---
layout: post
title: Postmark Gaslit Me
date: 2025-10-01 09:18 -0700
excerpt_separator: <!--more-->
---

RTFM. I'm sure you've heard it before. Usually it serves me well. When something isn't working it's best to start from the assumption that you've overlooked something simple.

```ruby
>> TestMailer.hello.deliver_now
=> true
```

This was my assumption when I ran into this issue with Postmark. A 200 response but no email sent. If Postmark said the message was sent then it must be somewhere, right? I tried different streams, used different to and from addresses, triple checked the dashboard but my messages were nowhere to be found.

<!--more-->

<img src="{{ site.url }}/assets/images/unused_streams.png" alt="Unused Streams" style="margin: 2rem auto;">

Maybe the docs were outdated? I dug into the source code for the Postmark gem and found nothing.<br />
Maybe it was an ActionMailer configuration issue? I read the docs and checked that everything was set up exactly as it should be.

Finally, I decided the best option was to simplify. cURL always works right?

```bash
MESSAGE_ID=$(curl "https://api.postmarkapp.com/email" \
  -X POST \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "X-Postmark-Server-Token: $POSTMARK_API_TOKEN" \
  -d '{
    "From": "hello@mahoganychat.com",
    "To": "hello@mahoganychat.com",
    "Subject": "Hello from Postmark",
    "HtmlBody": "Hello dear Postmark user.",
    "MessageStream": "outbound"
    }' \
  | jq -r '.MessageID'); echo "MessageID: $MESSAGE_ID"; \
  curl "https://api.postmarkapp.com/messages/outbound/$MESSAGE_ID/details" -s -H "Accept: application/json" -H "X-Postmark-Server-Token: $POSTMARK_API_TOKEN"

=> MessageID: 1c075232-1f9a-4b99-bd79-7be6cc3a2499
=> ErrorCode 701: Message doesn’t exist
```

Wrong. The message you created doesn't exist. Huh?

After fighting this for hours I caved and bugged support. To their credit, they resolved it immediately:

>
> Hi Elijah,
>
> Thanks for reaching out and happy to help here.
>
> Your Postmark account had been inactive for a handful of months, not sending out any outbound messages.
>
> As a measure to help prevent abuse from being sent from the account, **we paused sending and queued any new messages.**
>
> To help here, I've now gone ahead and restored sending to your Postmark account. Any queued messages have now been sent out.

Surely I just missed something in the dashboard that could've saved me the hassle.

> Just curious, did I miss something in Postmark that shows this status?

Nope.

> Hi Elijah --
> No you did not miss anything! We are working on updating this so you and other customers are informed in the future about inactive accounts. It's high on our list as something to enable. Apologies for the confusion in the meantime!

I figured it's worth documenting in case anyone else runs into this issue. Sometimes reading the manual isn't enough. Sometimes the API gaslights you.
