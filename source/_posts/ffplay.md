---
title: ffplay
date: 2019-11-14 13:58:06
tags:
     -ffplay
     -ffmpeg

---

## play  audio

 `mp3`

```bash
 ffplay -ar 8000 -ac 1 -i dest_transcode.mp3
```
`pcm_alaw`

```bash
 ffplay -ar 8000 -ac 1 -f alaw -i test.g711a
```
