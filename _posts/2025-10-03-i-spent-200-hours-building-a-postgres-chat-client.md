---
layout: post
title: I spent 200 hours building a Postgres chat client
date: 2025-10-03 15:19 -0700
excerpt_separator: <!--more-->
---

"Can you tell me how many..."

"Can you build me a report that shows..."

"Can you run another report for August..."

I know I'm not the only one who's received these requests. Most of the time they're not even sure what they want. Usually the answer to their question won't actually change anything. Does it matter whether we have 1000 or 1001 customers? Does it change our priorities? Our strategy?

<!--more-->

I can't really blame them though. Everyone has hunches they want to follow without any real outcome in mind. I do the same thing myself. The problem is that their curiosity is breaking my flow.

The answer to my problem was obvious: get out of the way. If I could build a way for non-technical users to access the same data then there wouldn't be any reason to interrupt me. I could set it up once and let them indulge their every guilty curiosity.

## Architecture

We can probably just skip this, right? It should be this easy:

<img src="{{ site.url }}/assets/images/so_easy.png" class="image"  style="max-height: 700px;">

Well, actually, no. It turns out that every great prompt has one critical piece: context. In this case, the database schema. Postgres's `pg_dump` can be used to generate a nice structured SQL schema that LLMs are already familiar with (I'm sure there are more than a few examples in their training data).

The problem is `pg_dump` is a CLI tool that we can't invoke with a database connection. Unless we want to have a user manually dump their schema every time it changes and paste it into the prompt, this won't work. Of course we could just build the schema by querying the database itself.

```ruby
def fetch_tables(conn)
  conn.exec(<<~SQL)
    SELECT schemaname, tablename, tableowner
    FROM pg_tables
    WHERE schemaname NOT IN ('pg_catalog', 'information_schema')
    AND schemaname NOT LIKE 'pg_temp%'
    AND schemaname NOT LIKE 'pg_toast%'
    ORDER BY schemaname, tablename;
  SQL
end
```

First, fetch all the tables, indexes, primary keys, foreign keys, and the Postgres version. Then fetch the columns for each table and format it just like `pg_dump` would. This took a while to get right but with enough SELECT statements we can make it work.

I can already hear somebody screaming though: "Okay but there's no way you should be giving an LLM access to the production database!" Agreed. That's a terrible idea. Unless...

## Guardrails

By far the biggest objection to doing something like this is that <a href="https://x.com/jasonlk/status/1946065483653910889">LLMs don't have a pristine track record when it comes to not wiping out production data</a>.

<img src="{{ site.url }}/assets/images/deleting_prod.png" class="image"  style="max-height: 700px;">

Really this is an [age](https://www.reddit.com/r/cscareerquestions/comments/6ez8ag/accidentally_destroyed_production_database_on/) [old](https://www.reddit.com/r/SQL/comments/dq40a3/help_deleted_a_table_in_production_seriously/) [problem](https://www.reddit.com/r/programming/comments/64idzl/digitalocean_accidentally_deleted_production/) remastered for modern audiences. Since this is a problem that people have had before, that means there are existing solutions.

> PROMPT: Pls don't delete anything.

Asking nicely doesn't hurt but this is basically thoughts and prayers for your data. Luckily, Postgres has a tidy solution for this: read-only transactions.

```sql
BEGIN TRANSACTION READ ONLY;
```

I know it's not perfect. A malicious user could probably bypass this by committing the transaction early and committing a second transaction that's destructive. But the user isn't malicious. They're on your team (presumably).

In any case, I think there's a much more credible threat: resource exhaustion. This is also a problem that's existed since databases were created so we have the tools to solve it. Setting a connection timeout is a simple way to prevent the LLM from hogging all of the resources. I also found this aptly named [connection_pool](https://github.com/mperham/connection_pool) gem that I configured to limit the number of concurrent connections to the database and reap idle ones.


```ruby
def connection_pool_for(database)
  @mutex.synchronize do
    @connection_pools[database.id] ||= create_connection_pool(database)
  end
end

def create_connection_pool(database)
  ConnectionPool.new(
    size: POOL_SIZE,
    timeout: POOL_TIMEOUT,
    check: ->(conn) { validate_connection(conn) },
    reaper: true,
    reaper_interval: 60  # Check for stale connections every 60 seconds
  ) { establish_connection(database) }
end

def validate_connection(conn)
  conn.exec("SELECT 1")
  true
rescue PG::Error => e
  Rails.logger.warn("Connection validation failed: #{e.message}")
  false
end
```

Since the connections needed to be shared between users from the same organization, there was the added complexity of managing shared resources across threads. A dedicated `DatabaseConnectionManager` class with a mutex was enough to handle this. Would this scale to thousands of connections? No, but premature optimization is a temptation best avoided.

## Improving the results

Look, if you guessed that language models trained on the entire corpus of human knowledge with boundless resources are good at turning English sentences into SQL statements you'd be correct. For all their shortcomings, this sort of task is something they're actually pretty good at. Not perfect, but at least as good as a junior developer. They still make mistakes though.

For more complex queries I found a little encouragement to be helpful. For some reason the models tried to hero shot queries without taking advantage of generating intermediate results. Anecdotally, this seemed to help quite a bit:

> If you need to join tables, do so. If you need to use a CTE to generate intermediate results, do so.

It also shouldn't be surprising that providing tools with explicit schema is critical to avoiding the response preamble. This was not the case with providing the schema, however. When asked to generate an answer using SQL the model will reasonably and without fail invoke the `get_schema` tool. This just slows down the response and with prompt caching it's actually detrimental.

## The Result

I won't drone on about all the other hurdles there were to clear (like handling streaming JSON responses with Hotwire updates) but I will say that creating a "ChatGPT wrapper" was more involved than I thought it would be. It turns out that building software with ✨AI✨ is just as fraught with pitfalls as building normal software.

Feel free to [try it out](https://mahoganychat.com) if you want. Let me know if you're able to break anything.
