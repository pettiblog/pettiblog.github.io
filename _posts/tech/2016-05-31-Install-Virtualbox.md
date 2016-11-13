---
title: "How to install Linux on Windows using VirtualBox"
author: nicolas
excerpt: "Install Linux on Windows using VirtualBox"
modified: 2016-05-31
category: tech
header: 
  image: virtualbox/virtualbox.png
  caption: "Image credit: **[Oracle VirtualBox](https://www.virtualbox.org)**"
  teaser: virtualbox/02.JPG
image:
  feature:
  credit:
comments: true
---

Desktop virtualization software such as **[VMware][vmware]** and **[Oracle VirtualBox][oracle]** lets you install and run multiple Operating Systems (OS) on your PC while your host OS remains undisturbed. If you have a Windows OS but you want to use Linux or Mac without having to go through the fuss of setting up [dual boot][dualboot], a virtual environment may be the best choice for you. It's actually very quick and easy!

But before we get started, if you want to learn more about the advantages of using desktop virtualisation software head over to [Dinesh's article on sysprobs.com][sysprobs].

## Download VirtualBox and the Linux iso image

The first thing you want to do is **download VirtualBox**:

[VirtualBox](http://download.virtualbox.org/virtualbox/5.0.20/VirtualBox-5.0.20-106931-Win.exe){: .btn .btn--inverse}

Now you can **download the Linux OS ISO file**, I would suggest downloading the 64 bit version. Take your pick (I would recommend Linux Mint Cinnamon):

[Linux Mint](http://www.linuxmint.com/download.php){: .btn .btn--inverse}	[Ubuntu](http://www.ubuntu.com/download/desktop){: .btn .btn--inverse}

Downloading might take a while as the ISO file is more than 1Gb in size. 
{: .notice}

## Enable "Intel Virtualisation Technology" in BIOS

"What the heck is BIOS?" If you're asking yourself that question and want to know more, here's a video from [Linus][techquikie]:

<iframe width="560" height="315" src="https://www.youtube.com/embed/zIYkol851dU?start=72&end=236" frameborder="0"> </iframe>
*Video by [Techquickie][techquikie]*

So when your PC first turns on there is usually a message that lasts for a couple of seconds that says: *Press [key] to enter boot menu or BIOS*. In case you don't get a message you might want to Google search your PC model and find out how to enter the BIOS. The key is usually F1, F12 or Del and this will open your boot menu or BIOS, if it opens the boot menu just look for BIOS settings and open it. Once that's sorted **follow these steps**:

* Enter BIOS settings
* Search for "Intel Virtualisation Technology" or "Intel Virtualisation".
 * It may be under advanced features, advanced configuration or security.
* **Enable it**
* Exit BIOS and reboot the PC

Sorted

## Install VirtualBox

* Go to where you saved the VirtualBox.exe file and double click.
* Click "Run" and then "Next".

<figure class="half">
	<img src="/images/virtualbox/01.JPG">
	<img src="/images/virtualbox/02.JPG">
</figure>

* Click next on the following two windows.

<figure class="half">
	<img src="/images/virtualbox/03.JPG">
	<img src="/images/virtualbox/04.JPG">
</figure>

* Click "Yes" and "Install".

<figure class="half">
	<img src="/images/virtualbox/05.JPG">
	<img src="/images/virtualbox/06.JPG">
</figure>

* When prompted to allow to install on your computer, click yes.
* When asked “Install this device software?” check the box and select “Install”.
* Finally, check the box to start Virtual Box and click “Finish”.

## Creating a Virtual Hard-Drive with VirtualBox

Once you've got VirtualBox installed you will need to give your new OS some memory, both RAM and Hard-Drive.

* Open VirtualBox and click on "New".

<figure>
	<img src="/images/virtualbox/07.JPG">
</figure>

* Give the virtual disk a name (e.g. Virtual Linux or Johnny).
* Make sure linux is selected for "Type".
* Make sure you select the correct version (32 or 64 bit).
* If Linux Mint is not displayed select "other Linux".

<figure class="half">
	<img src="/images/virtualbox/08.JPG">
	<img src="/images/virtualbox/09.JPG">
</figure>

**WTF? Why is there no 64bit version on the drop-down menu?** If this has happened to you don't worry, it happens sometimes. You will have to restart your PC until it shows up. If the problem persist, this [article][no64] might help.
{: .notice}

* For memory size I recommend a minimum of 2GB. Although if you only have 2GB on your PC then select 1GB.
* Do not set the memory to the maximum on your system!
* Create a virtual hard drive by selecting the hard drive and folder, and select the size (a minimum of 10GB).
* Select "VDI".
* Select “Dynamically allocated”.
* Click “Create”.

## Installing Linux on your Virtual Hard Drive

* Select your “Virtual Linux” and click on “Settings” (next to "New").
* Click on "Storage".
* Click "Empty" and then click on the small CD icon on the far right. Click “Choose a virtual CD/DVD disk file...”
* Navigate to where you downloaded the ISO file and open it.

<figure class="half">
	<img src="/images/virtualbox/11.JPG">
	<img src="/images/virtualbox/13.JPG">
</figure>

* Make sure the ISO is listed under "Controller: IDE" and click OK.
* Additionally you could go into other settings and tweak to your liking.
* Click "OK" and then "Start" Linux.

<figure>
	<img src="/images/virtualbox/15.JPG">
</figure>

* After a few seconds this should appear (image above).
* Double click on "Install Linux Mint" or "Install Ubuntu".
* Make sure you are connected to the internet.

<figure class="half">
	<img src="/images/virtualbox/16.JPG">
	<img src="/images/virtualbox/17.JPG">
</figure>

* Select your language.
* Check the box to “Erase disk and install Linux Mint/Ubuntu". Don't worry, it won't affect your Windows OS.
* Select your time zone and "Continue"

<figure class="half">
	<img src="/images/virtualbox/18.JPG">
	<img src="/images/virtualbox/19.JPG">
</figure>

* Tell Linux who you are and what password you want to use.
* Install!

<figure>
	<img src="/images/virtualbox/20.JPG">
</figure>

* Once it finishes you will want to restart Linux. You might have to click the X on the top right and select "Power Off".
* Click on "Start" to turn Linux on again.

<figure class="half">
	<img src="/images/virtualbox/23.JPG">
	<img src="/images/virtualbox/24.JPG">
</figure>

* To enable full screen click “Devices” and select “Insert Guest Additions CD image”.
* Click "Run".
* Enter your password when prompted.

<figure class="half">
	<img src="/images/virtualbox/25.JPG">
	<img src="/images/virtualbox/26.JPG">
</figure>

* When finished hit Return.
* Shut Linux down again to finalise the additional features.
* Now by hitting right (right)Ctrl + F you can make it completely fullscreen.

## Done!

<figure>
	<img src="/images/virtualbox/mintdone.JPG">
</figure>

I hope this has helped and everything worked out smoothly. 

Feel free to leave a comment to let me know if you liked it or have any queries.

[vmware]: http://www.vmware.com/products/player
[oracle]: https://www.virtualbox.org
[dualboot]: http://www.howtogeek.com/187789/dual-booting-explained-how-you-can-have-multiple-operating-systems-on-your-computer/
[sysprobs]: http://www.sysprobs.com/why-to-use-vmware-virtualbox-virtual-pc-other-desktop-virtualization-software-advantages
[techquikie]: https://www.youtube.com/channel/UC0vBXGSyV14uvJ4hECDOl0Q
[no64]: http://www.fixedbyvonnie.com/2014/11/virtualbox-showing-32-bit-guest-versions-64-bit-host-os/#.V02OLXUrKto

