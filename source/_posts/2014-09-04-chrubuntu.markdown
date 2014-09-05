---
layout: post
title: "Tips, Tricks, and Advice for installing ChrUbuntu on an Acer C720"
date: 2014-09-04 14:38:49 -0400
comments: true
categories: ChrUbuntu Ubuntu Chromebooks Acer&nbsp;C720 Chrome&nbsp;OS
---

##Some of this may even be useful for other models!!!

For the past couple of weeks my job has had me working on an internal project to create a shell script for installing Ubuntu with a completely furnished development environment for the Acer C720 Chromebooks.  That meant that each computer would need Ruby, RVM, Postgres, Sublime, Github credentials, customized bashrc, and whatever else we could think of.  

I started with [this awesome install script](https://github.com/codestarterorg/ubuntu-chromebook-installer) by [Codestarter](https://codestarter.org/), which was a great basis since many of the largest hurdles were well hurdled.  I've also played around witht Jay Lee's [install script](http://chromeos-cr48.blogspot.ca/), the guy who created ChrUbuntu.  And this blog post is the result of such tinkering.

##Tips
###Helpful Resources:
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

####Need more SSD space? 
This [guide](http://www.androidcentral.com/how-upgrade-ssd-your-acer-c720-chromebook) to upgrading the SSD on an Acer C720. Fair warning, I haven't done this myself yet. But I've read through it. It seems easy, and the page's comments echo this sentiment.

##Tricks
####Default booting into Chrubuntu:
  **NOTE** This should only be done after you have completely installed Ubuntu and must be reverted if you intend on reformatting the computer.  It can also *only* be done if you have removed your write protect screw in order to change the SeaBIOS.
  **NOTE** Doing this will void the warranty on the computer and potentionally brick the computer.  **I AM NOT RESPONSIBLE FOR ANY DAMAGE TO YOUR MACHINE IF YOU *CHOOSE* TO DO THIS**
  
  *If your write protect screw is already removed skip to step 6.*  One of the refurbished computers we used already so it's possible. You can check by starting at line 6 if you'd like

  1. Turn off the computer.
  2. Flip the computer over and remove all 13 screws from the bottom (there is one beind the sticker that reads, "Warranty Void If Seal Is Broken").
  3. Gently, unsnap the bottom of the case using your finger nails or a flathead screw driver.  These snaps should easily seperate so no need to force it.
  4. Once the case is removed locate the write protect screw, which is number 7 in [this photo](http://www.chromium.org/_/rsrc/1381990807648/chromium-os/developer-information-for-chrome-os-devices/acer-c720-chromebook/c720-chromebook-annotated-innards.png) and remove it.
  5. Replace the bottom case, making sure it snaps back into place.  Now put back the 13 screws you removed.
  6. Reboot the computer and press [CTRL + d].
  7. Go into a shell terminal [CTRL + ALT + t] and then enter `shell`.
  8. Finally enter `sudo -s set_gbb_flags.sh 0x00000489`.

<br>
####Switching back to developer mode boot:
  1. Boot into Chrome OS [CTRL + d]\ (You'll have like 3 seconds after starting the computer, so act fast.)
  2. Open the command line via [CTRL + ALT + t] then `shell`
  3. Enter `sudo -s set_gbb_flags.sh 0x00000000`

<br>
####Revert back to Factory Settings:
  1. Make sure you default boot into developer mode first (see above).
  2. Run the Chrome OS recovery image (see below). This step isn't necessary, but I'd play it safe to make sure the paritions are erased correctly.
  3. After it reboots press [SPACE BAR] then wait for the screen to refresh and press [ENTER]

<br>
##Trouble Shooting


####Developer Mode:
How the hell do I get into developer mode?
  1. Enter [ESC + REFRESH + POWER]
  2. On reboot your screen should read "Chrome OS is missing or damaged"
  3. Ignore it; it's lying. Enter [CTRL + d] then [ENTER]

<br>
####USB or SD card not working, prompt says device is 'read-only':
  - Likely the USB or SD card is not properly formatted
  - Use an application like Disk Utility on Macs to make sure the USB or SD card is formatted in FAT 32
  - If it is not erase and reformat the USB or SD card as FAT 32

<br>
####Chrome OS is Damaged:
  1. Download the [Chromebook Recovery Utility](https://chrome.google.com/webstore/detail/chromebook-recovery-utili/jndclpdbaamdhonoechobihbbiimdgai?hl=en) app.
  **NOTE** Google has two recovery utilties, but this is the only one that works.You will need to switch to [Chrome Beta](https://www.google.com/chrome/browser/beta.html?platform=mac&extra=betachannel) if you are using a Mac as your primary computer.
  2. Create a new image of Chrome OS specific to your machine.  The model number is at the bottom of the scary screen you should see your machines model number.
  3. After the Chromebook Recovery Utility is finished creating a recovery USB or SD simply plug it into the machine and let it do it's thing. It shouldn't take more than 5 minutes

<br>
####Stuck in Ubunutu reboot:
  1. Restart your computer
  2. As soon as you see the screen with "OS verification off" press [CTRL + d]
  3. Login as guest and open a shell with [CTRL + ALT + t] and enter `shell`
  4. Next enter `sudo -s set_gbb_flags.sh 0x00000000`
  5. Restart your computer and you will now stay on the "OS verification off". From here switch to Ubuntu [CTRL + l] or Chrome OS [CTRL + d]

<br>
####Stuck as a Guest in ChrUbuntu:
  - The source of this bug remains a mystery as of right now, but it is likely something went wrong with installing the patches for the Ubuntu OS.
  - To fix this you need to wipe your computer using the Chrome OS Recovery Image and reinstall ChrUbuntu.