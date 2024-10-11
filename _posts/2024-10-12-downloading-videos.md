---
layout: post
title: "Downloading videos"
---

Sometimes it's not simple as right clicking on the video and hit "Save video as..."

In the case, I usually go to network tab in the dev tools and find the video link, maybe make it a little bit easier by filtering by media tag.

Recently I face some challenge where the video chunks I downloaded can't be opened. The binary data is in png image format. I quickly change the extension to mp4 but for most of the videos, it doesn't work.

<!-- more -->

I go for some research and figure out that they prepend [8 identical bytes of png format](http://www.libpng.org/pub/png/spec/1.2/PNG-Structure.html) into the chunk. So I need to strip it out before doing the concatenation.

```
# the video is combined by chunks in m3u8 playlist format
curl https://proxy.playvideo.xyz/videos/2647585af6b9ca7a497e9aa731fda6a8/index.m3u8 -o index.m3u8
```

Using `grep` to remove all the lines with `#` and store the urls in to a file called `links`.

```
cat links
| xargs -P 24 -I{} bash -s <<-EOF
	name=$(echo {} | sed "s/https:\/\/sf16-scmcdn-sg.ibytedtos.com\/obj\/ad-site-i18n-sg\///");
	curl {} -s -o - | dd of=$name.mp4 bs=1 skip=8
EOF
```

To speed up downloading process, I use `xargs -P 24` which creates a pool of 24 processes to run `curl` simultaneously.

The urls have incremental integer paths which are used for filenames and it helps to concat the chunks in order later.

And the most important command is `curl {} -s -o - | dd of=$name.mp4 bs=1 skip=8`. Instead of saving the file to disk first, I pipe it to stdin and feed to `dd` to cut of the first 8 bytes before it saved.

And the last thing is to create a whole video file.

```
ls -1 *.mp4 | sort | xargs -I{} bash -c 'cat {} >> video.mp4'
```
