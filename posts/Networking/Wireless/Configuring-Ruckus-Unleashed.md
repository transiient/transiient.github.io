---
title: Configuring Ruckus Unleashed on Used R510 APs
layout: post
grand_parent: Networking
parent: Wireless
---

# Configuring Ruckus Unleashed on Used R510 APs

I recently snagged a lot of three Ruckus R510 wireless access points for a grand total of A$130 including postage (around £70). Pretty happy with that.

They arrived a couple days ago, and I've finally got a chance to play with them!

***Image of the beautiful used R510s coming soon...***

Now before I begin, I must say that I've owned Ruckus APs in the past. I can't remember the model, and they weren't compatible with Unleashed, but they were absolutely fantastic and I got those for £10 each. I had one in the house and one in the shed. But I've moved around since then, and they didn't come with me. Their amazing performance left a lasting impression which is why I got more Ruckus equipment this time!

When I had those running, I'd actually configured a full home network with multiple VLANs, guest networks, virtual servers, and all the fancy stuff that enthusiasts over on r/homelab like to run. Yes, I was one of those people, and yes, I will be one of those people again.

## Why Am I Doing This Though?

There's a few reasons why I'm investing time into network equipment again. The main reason is: I love it. It interests me. The next reason along (and the reason I gave my partner), is that our WiFi currently *sucks*. It's terrible. Our actual internet connection isn't great (we have to use fixed wireless out here in rural Australia), but our WiFi drops out daily and the signal barely reaches 20m to the other side of the house. Rubbish.

# Firmware Updates

I'll be honest - while I'm writing this I've already gone through this bit once and configured the Unleashed "master" AP. But I will pretend that I haven't, so that we can do this together.

For starters, you'll need a firmware update. These access points I bought are on version 5 of the Solo firmware - this, as far as I understand, is the default firmware that allows these devices to connect to a controller or just run standalone.

Now, I don't have an Ethernet port on my laptop, and my PC is currently missing a graphics card (due to moving), which means I have to connect to my Ruckus APs a bit of a funny way. Usually, you would connect it to a power source (like a PoE switch), connect your computer to that network, assign your computer an IP on the 192.168.0.0/24 network, then configure through the admin portal at 192.168.0.1. However, my APs weren't playing nicely with this when I tried to do it through the "Configure.Me-XXXXXX" network that the APs broadcast by default.

Instead, I've hooked up my new PoE switch (TP-Link SG108PE, which I've *also* used in the past) to my ISP router, then plugged in a Ruckus R510, got the IP for the AP from the ISP router admin page, and logged in with the default credentials (super / sp-admin). And I'm in.

***I'll add a photo of this terrible setup soon too.***

![Ruckus R510 default admin page, shown after logging in](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 at 20-46-30 Ruckus Wireless Admin.png)

## Downloading the Firmware

The first step - downloading the firmware you need. To get these access points from their original firmware onto Unleashed, I first had to download and flash the latest version of the Solo firmware, then flash the Unleashed firmware. Some people say you need to flash a matching version of Unleashed, then upgrade it, but I didn't have to do that.

Start by browsing the (fantastic) [Ruckus Wireless website](https://support.ruckuswireless.com/software). It gives you a lot of search options. Search for your AP model in Software Downloads. Alternatively, just use Google. Up to you. Just try to find the latest version for your device!

For these R510's, I downloaded the **118.2.0.0.875** Solo firmware, and the **R510_200.15.6.212.14** firmware. Name them sensibly - put "UL" in the Unleashed file, or number them - otherwise you may get lost.

## Upgrading the Firmware

Once you've downloaded the firmware packages you need, you're ready to upgrade. It's easy, I promise.

I logged into an AP, the one I ended up using as the Unleashed "master". Then I navigated to the *Upgrade* page on the left-hand menu, selected **Local** (for local file), selected the first firmware file, then clicked **Perform Upgrade**.

![Before clicking Perform Upgrade...](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 at 20-47-03 Ruckus Wireless Admin.png)

![After clicking Perform Upgrade...](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 at 20-47-17 Ruckus Wireless Admin.png)

One of the scariest things you can do as a systems admin is click upgrade on working software (especially if it's Microsoft software), but I had to do this.

When it's done, it will tell you. It literally tells you it's done. It's fantastic.

![Great, it's all done!](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 204959.png)

## Upgrading the Firmware... Again

Now we'll do the same thing again with the Unleashed firmware package.

Before starting, just double check you're still able to connect to the AP. Its IP address might have changed - mine did, twice, which was a bit annoying.

If your original firmware was old enough, you may even see a new page asking you to change the login password! Remember that this is not the firmware you're keeping, so you can just do something silly like I did and use a password like "admin123" (which is literally what I used).

Once again, off to the *Upgrade* page. Click **Local**, select the file, click **Perform Upgrade**.

If you get an issue here, you probably need a later version of the Solo firmware before installing Unleashed - or you need an earlier version of Unleashed that you can then upgrade. But hopefully you won't get an issue - I didn't. In fact, this process is so ridiculously easy I wouldn't have even cared if it wanted me to do more work.

Anyway... once that's done, it will tell you again.

# Onto the Fun Part - Booting Unleashed for the First Time

As I said earlier, this isn't my first rodeo. But the configuration is pretty fresh in my brain.

I chose the advanced installation because I like exploring options (and making things more difficult than they need to be).

The wizard will ask you for some basic information. Enter an SSID (wireless network name), admin username/password, etc. Really simple stuff. In fact, the "Advanced" setup wasn't even that advanced - the *real* advanced stuff comes later when you've actually got Unleashed running.

Unleashed looks great, by the way. I haven't used it until now, but it's very exciting!

![The Unleashed login page in all its glory](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 at 20-59-47 Unleashed Login.png)

(Eagle-eyed readers may realise that the password on this page has the same number of characters as "admin123", however I can assure you I would not be so stupid as to write my password in a blog post. Or would I?)

Let's do another AP.

# AP Number Two...

Firmware upgrade... Done. Firmware upgrade two... Done. And now I log in... And what's this? It redirects to the "master" AP's IP address?

It seems that this software is pretty smart. I didn't have to do *anything* to link it to the master. In the admin portal, I can see two active access points:

![The Unleashed admin portal, showing two active APs](/assets/posts/Networking/Wireless/Configuring-Ruckus-Unleashed/Screenshot 2024-10-02 at 21-04-26 Unleashed Dashboard.png)

I only have two plugged in. That means the Unleashed software automatically picked up the second AP and added it to my Unleashed network.

# Conclusion

I will end this post with a numbered list of things I want to figure out how to do on my new wireless network:

1. Configure VLANs (which I'll have to do on my new MikroTik router and PoE switch first... I will write blog posts for those things too)
2. Mount them in the house - my partner will not be pleased about more holes being drilled, but I will enjoy doing it
3. Play around with the settings, probably turning a working WiFi network into a non-working network, then fixing it afterwards

I was actually going to go and buy a car today, but I tracked down a previous owner who told me the engine caught fire in 2021, so I decided to do this instead.

Til next time!