---
layout: blog-post
permalink: undersea-devlog-1
---
It has been two days since I started working on undersea, and its going well!

I have implemented the basics of the library, you can now add shows, retrive data about your shows and their episodes! I have started doing some basic work on learning ratatui to create the tui, which will now be developed along side the library. This allows me the focus in the scope of the library as I add the features that the tui provides.

## things missing from library (that i can think of)
- Retriving episode duration.
  - This is quite hard! it is what most of the devlopment time was actually spent on for the past two days. It is shockingly difficult to get the duration of an episode, as you need to decode the mp3 file up until the point where things like images stop to get a frame to get the bitrate, and that is upwards of 3mb. For the hundreds and likely thousands of episodes in all podcasts someone would have added, that is multiple gigabytes of data transfer just to get the durations. I plan to work around this by having duration calculation just be something that app implementations call, probably when you attempt to play an episode, or if there is a resume duration stored, but no total duration.
- Saving to file
  - This is a core feature, but not one I think is difficult, I will probably regret this
- Removing podcasts
- Sorting podcasts
  - Sorting will be done by sorting the vector that episodes are stored in. The vector will be ordered oldest first, and then app implementations can have a button that swaps the display order
- Marking episodes as finished
- Setting resume duration of episodes

## things missing from tui
- everything
