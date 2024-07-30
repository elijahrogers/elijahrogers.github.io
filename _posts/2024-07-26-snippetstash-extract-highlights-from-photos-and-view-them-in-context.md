---
layout: post
title: 'SnippetStash: Extract highlights from photos and view them in context'
date: 2024-07-26 09:18 -0700
excerpt_separator: <!--more-->
---

I'm not sure exactly where I picked it up but I have a habit of highlighting books that I read. It's easy to forget some key insights after reading a book and highlighting provides a way to mark something that I want to revisit. The issue is I don't revist. At best I might remember some connection a few months later and flip through the entire book looking for the section I was thinking of.

After repeating this process several times it was clear that I was wasting my time. This shouldn't be so difficult. I realized that if I could digitize these highlights I could search, organize, and easily reference them whenever I wanted - No more flipping pages! I figured I should be able to build an app where I submit a photo and the text is OCR'd and any highlighted texts are extracted (that is at least once I learned the term OCR).

I knew I needed to begin by validating the key feature first before building an entire app. I built a small python script to perform some preprocessing on an image and OCR it using `opencv` to test it out. The basic process was:

1. Find the highlighted areas using some assumptions about the range of HSV values for a typical highlighter
2. OCR the text
3. Find any overlap between the bounding boxes of the highlights and text
4. Reconstruct the highlighted parts into strings with some fuzzing to account for inconsistent highlighting

Unfortunately, the initial results of step 2 were bad. Discouragingly bad:

<blockquote>
website that produces counts

gh time of search queries on the Internet beginning in 2004 REP:
DL O11). 2 UAE - ho » ivf... Sj Q be bble 7 1) : and t e ceare aa

sradually trailed off to almost nothing by 2014.
</blockquote>

OpenCV is really cool open source library but I was unable to twist it into doing what I wanted in this case. I decided to give the Google Vision API a shot and found that the output was high quality and allowed me to create a working demo. Since this seemed to work, I moved on to building a new Rails app from the ground up. There were a few key challenges at this phase:

1. Interoperability between Rails and my python scripts
2. Designing an intuitive mechanism for editing highlighted text
3. Improving accuracy

Challenge 1 ended up being easy to solve because I could pass the image path to my python script and receive JSON back.

```ruby
  def ocr_text(image)
    path = path_for(image)
    `python3 #{Rails.application.root}/app/services/extract.py "#{path}"`
  end
```

Challenge 2 was much more difficult, but I was able to create a form that tracked the offsets of text the user highlighted to update the position with a live preview.

Challenge 3 was - unsurprisingly - the most difficult. There were several sources of error that seemed to compound and few different edge cases. For example, a highlight doesn't always cover an entire word or skips one entirely.

![highlights]({{ site.url }}/assets/images/highlighted.jpg)
*The words "show" and "spike" are almost skipped entirely*

Adding some fuzzing (with the wonderful `fuzzy_match` gem) and word boundary detection to the matching algorithm helped quite a bit. This was particularly helpful for handling punctuation and whitespace related edge cases.

```ruby
def fuzzy_match(full_text, position, text)
  candidates = []
  start_indx = position
  end_indx = text.length

  while end_indx <= full_text.length
    adj_start = nearest_word_boundary(full_text[position..], start_indx)
    adj_end = nearest_word_boundary(full_text[position..], end_indx)
    candidates << full_text[adj_start...adj_end].strip # Don't match on spaces
    start_indx += 1
    end_indx += 1
  end

  match = FuzzyMatch.new(candidates).find(text, find_with_score: true)

  return unless match && match[1] > 0.95

  match[0]
end
```

This wasn't enough though. The results from the Vision API were still inconsistent and after a number of different updates and adjustments things weren't improving enough.

Eventually I stumbled upon [this reddit post](https://www.reddit.com/r/ChatGPTCoding/comments/1812r4n/vision_api_giving_much_worse_replies_than_the/). Apparently ChatGPT is really good at this sort of thing! I tested it out with the chat interface first and was astounded - ChatGPT-4o made the task look easy and it only required a single API call.

I quickly created a new backend service for this extraction method and finally got the accuracy I was looking for. The final step was adding some more polish and marketing before launching [the app](https://www.snippetstash.app/).
