---
title:  "How to create a Face Chronical. Silently. Automatically"
subtitle: "If you were to click a picture of yourself everyday, and see a rolling movie of yourself through a month.. What do you think you'll see ?"
author: "Kshitij Banerjee"
avatar: "img/authors/wferr.png"
image: "http://pre00.deviantart.net/44ce/th/pre/f/2012/004/b/7/take_that_wallpaper___faces_by_edr_emz-d4lbnu4.jpg"
date:   2015-11-22 12:12:12
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

1. Download ImageSnap from [__here__](http://prdownloads.sourceforge.net/iharder/ImageSnap-v0.2.5.tgz)
2. Decompress it if you have to using
         tar -xvf ~/Downloads/ImageSnap-v0.2.5.tgz
3. Download PowerManager from [__here__](https://www.dssw.co.uk/powermanager/)
4. Launch Power Manager.
5. Create an empty event
![Empty Event](https://www.dssw.co.uk/blog/2014-10-09-run-script-on-mac-power-on-or-wake/1-power-manager-create-empty.png)
6. Double click the event created (Untitled) to edit it.
7. Add a power on trigger. ![Power on](https://www.dssw.co.uk/blog/2014-10-09-run-script-on-mac-power-on-or-wake/4-power-manager-power-on-trigger.png)
8. Add a Run Script > Shell Script action
9. Edit the Run Script step by clicking on the __Cog__ icon.
10. Copy and paste the following script.

      `#!/bin/sh`

      `cd ~/Downloads/ImageSnap-v0.2.5;`
      `mkdir daily_pics;`
      `[ -f ./daily_pics/`date "+%d_%m_%Y"`.png ] && echo "File exists" || ./ImageSnap ./daily_pics/`date "+%d_%m_%Y"`.png -w 5;`
11. Save and restart.
   Congratulations! Every time you open your computer, you'll see a green light turn up. Look up and Smile :)
12. In gist.. The above makes a directory called daily_pic under the `~/Downloads/ImageSnap-v0.2.5` directory. You can always attach that folder to a google drive link and sync it up on the cloud.
(you will probably need to symlink your drive folder to the daily_pic folder.)

