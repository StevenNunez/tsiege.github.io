---
layout: post
title: "Tips, Tricks, and Advice for installing ChrUbuntu"
date: 2014-09-04 14:38:49 -0400
comments: true
categories: ChrUbuntu, Ubuntu, Chromebooks, Acer C720, Chrome OS
---

#Tips, Tricks, and Trouble Shooting for the Acer C720
##Some of this may even be useful for other models!!!

For the past couple of weeks my job has had me working on an internal project to create an install script for installing Ubuntu with a completely furnished development environment for the Acer C720 Chromebooks.  That meant that each computer would need Ruby, RVM, Postgres, Sublime, Github credentials, customized bashrc, and whatever else we could think of.  

I started with [this awesome install script](https://github.com/codestarterorg/ubuntu-chromebook-installer) by [Codestarter](https://codestarter.org/), which was a great basis since many of the largest hurdles were well hurdled.  I've also played around witht Jay Lee's [install script](http://chromeos-cr48.blogspot.ca/), the guy who created ChrUbuntu.  And this blog post is the result of such tinkering.

##Tips
Helpful Resources:
  - Reddit's [ChrUbuntu Thread](http://www.reddit.com/r/chrubuntu/)
    Lot's of answers to common problems as well as general advice.
  - Ask Ubuntu's [Chromebook Section](http://askubuntu.com/questions/tagged/chromebook)
    Ask Ubuntu is Stack Exchange's site devoted to all things Ubuntu, so it's it great resource if you have general Ubuntu questions too.
  - Jay Lee's [Chromebook Blog](http://chromeos-cr48.blogspot.ca/)
    While it hasn't been active for about a year, it is still the website of the guy who made ChrUbuntu, so reading some posts give you a better grounding about ChrUbuntu.
  - Google's [Chromebook Support site](https://support.google.com/chromebook/answer/1080595?hl=en)
    This website has a lot of helpful information about trouble shooting Chrome OS.
    **NOTE** The Chrome OS Recovery tool on this, at least for Macs, is **outdated**.
    See below for more information.  You **MUST** use this [version](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai?hl=en) of the recovery utility.

Need more SSD space? This [guide](http://www.androidcentral.com/how-upgrade-ssd-your-acer-c720-chromebook) to upgrading the SSD on an Acer C720. Fair warning, I haven't done this myself yet. But I've read through it. It seems easy, and the page's comments echo this sentiment.

##Tricks
Default booting into Chrubuntu:
  1. Boot into Chrome OS [CTRL + d]
  2. Open the command line via [CTRL + ALT + t] then `shell`
  3. Enter `sudo -s set_gbb_flags.sh 0x00000489`
  **NOTE** This should only be done after you have completely installed Ubuntu and must be reverted if you intend on reformatting the computer

Switching back to developer mode boot:
  1. Boot into Chrome OS [CTRL + d]\ (You'll have like 3 seconds after starting the computer, so act fast.)
  2. Open the command line via [CTRL + ALT + t] then `shell`
  3. Enter `sudo -s set_gbb_flags.sh 0x00000000`

Revert back to Factory Settings:
  1. Make sure you default boot into developer mode first (see above).
  2. Run the Chrome OS recovery image (see below).
  3. After it reboots press [SPACE BAR] then wait for the screen to refresh and press [ENTER]

##Trouble Shooting
###This section is based on my own experiences when working on this.  These questions accurately reflect my mood when I first encountered them.

Developer Mode:
How the hell do I get into developer mode?
  1. Enter [ESC + REFRESH + POWER]
  2. On reboot your screen should read "Chrome OS is missing or damaged"
  3. Ignore it; it's lying. Enter [CTRL + d] then [ENTER]

USB or SD card not working:
I keep trying to run the install script off the USB, but I keep getting an error saying it's read only and sudo doesn't work and chmod doesn't either. I swear it just worked.

  - Likely the USB or SD card is not properly formatted
  - Use an application like Disk Utility on Macs to make sure the USB or SD card is formatted in FAT 32
  - If it is not erase and reformat the USB or SD card as FAT 32

Chrome OS is Damaged:
Something went wrong, and now my screen is stuck with a menacing exclamation point and reads "Chrome OS is missing or damaged"!!! What now!?!?!?!

  1. Download the [Chromebook Recovery Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai?hl=en) app.
  **NOTE** Google has two recovery utilties, but this is the only one that works.You will need to switch to [Chrome Beta](https://www.google.com/chrome/browser/beta.html?platform=mac&extra=betachannel) if you are using a Mac as your primary computer.
  2. Create a new image of Chrome OS specific to your machine.  As of 9/3/2014 the model number for the Acer c720 is PEPPY C6A-N3C-A70 but double check by the looking at the bottom of the scary screen you should see your machines model number
  3. After the Chromebook Recovery Utility is finished creating a recovery USB or SD simply plug it into the machine and let it do it's thing. It shouldn't take more than 5 minutes

Stuck in Ubunutu reboot:
My computer is stuck booting into Ubuntu; how do I stop it?
  1. Restart your computer
  2. As soon as you see the screen with "OS verification off" press [CTRL + d]
  3. Login as guest and open a shell with [CTRL + ALT + t] and enter `shell`
  4. Next enter `sudo -s set_gbb_flags.sh 0x00000000`
  5. Restart your computer and you will now stay on the "OS verification off". From here switch to Ubuntu [CTRL + l] or Chrome OS [CTRL + d]

Stuck as a Guest in ChrUbuntu:
I've run the install script, ChrUbuntu was installed, but I'm stuck being logged in as guest...
  - The source of this bug remains a mystery as of right now, but it is likely something went wrong with installing the patches for the Ubuntu OS.
  - To fix this you need to wipe your computer using the Chrome OS Recovery Image and reinstall.

I will add to this post if I think of anything else.