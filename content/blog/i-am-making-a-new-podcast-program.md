+++
date = 2025-04-13
title = "i am making a new podcast program"
+++
If you want to listen to podcasts, you have a few options: Spotify, YouTube, Apple Podcasts, or RSS. RSS is great, but it lacks the quality of life features that these other providers have. For me the biggest two things that are missing are ordering episodes in order of oldest to newest (I listen to alot of fiction podcasts) and being able to resume a podcast *across devices*.

I am hoping to resolve this by making my own program that has everything *I* personally want, and maybe what alot of other people are missing. I  want it to be super easy to make a new app for any platform, and to store all of its data in easy to parse files, so its not too hard to move to a different platform if it does not work for you.

The basic idea is to store two things, Your list of subscribed podcasts, weather you have watched or not watched an episode, and the point where you left an episode off.

This is to just give you a better idea of what I mean, this will change.

```rust
#[derive(Debug)]
pub struct Feed {
    url: String,
    name: String,
    episodes: Vec<Episode>,
}

#[derive(Debug)]
pub struct Episode {
    url: String,
    media_url: String,
    title: String,
    date: DateTime<Utc>,
    duration: time::Duration,
    resume_time: time::Duration,
}
```

Initially, I will make a tui application, and an andriod app after that, maybe later I can make a gui and ios app, but thats only if I can make those first two into a useable program.

If you are intrested, take a at the repo on [github](https://github.com/skoove/undersea), and considor contributing or giving it a star.

