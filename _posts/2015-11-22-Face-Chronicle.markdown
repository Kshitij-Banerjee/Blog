---
layout: post
title:  "How to create a Face Chronical. Silently. Automatically"
description: "If you were to click a picture of yourself everyday, and see a rolling movie of yourself through a month.. What do you think you'll see ?"
author: Kshitij Banerjee
avatar: "img/authors/K_icon.png"
img: "take_that_wallpaper___faces_by_edr_emz-d4lbnu4.jpg"
date:   2015-11-22 12:12:12
color: ff1744 
---

How to create a Face Chronical.
=================================

Why?
---------

- If you were to click a picture of yourself everyday, and see a rolling movie of yourself through a month.. What do you think you'll see ?
Your very own face chronical helps you give that feedback to yourself about whats really going on with your body. The face tells it all.

- Gaining weight? Working out and losing it? Stressing yourself? You may know it, but when you see that feedback, you tend to register it and unconciously correct yourself.

- Suspect someone uses your laptop ?
If someone does. You have a record of it.

- And at the end of it.. It might just be fun :)

How to set it up? (Mac)
------------------

You'll need two softwares to do this. ImageSnap and Power Manager.

1. Create a new file (shell) with the following name capture.sh in your home directory (~/capture_pic.sh) with the below contents.

      `#!/bin/sh`
      `cd ~/Downloads/ImageSnap-v0.2.5;`
      `mkdir daily_pics;`
      `[ -f ./daily_pics/`date "+%d_%m_%Y"`.png ] && echo "File exists" || ./ImageSnap ./daily_pics/`date "+%d_%m_%Y"`.png -w 5;`

2. Open the Terminal. (Cmd + Space), type Terminal.
3. Run the command
      `crontab -e`
4. Enter the following to the contents of the file opened.  (You may have to enter insert mode by pressing i, if vim is opened)
      `@hourly ~/capture_image.sh`
5. Save and restart.
   Congratulations! Every time you open your computer, you'll see a green light turn up. Look up and Smile :)
6. In gist.. The above makes a directory called daily_pic under the `~/Downloads/ImageSnap-v0.2.5` directory. You can always attach that folder to a google photos backup link and automatically sync it to your google photos as well! [_Google Photos_](https://photos.google.com/)

