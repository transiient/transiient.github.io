---
title: Install and Configure Webmin (Ubuntu Server 22.04)
layout: post
grand_parent: Linux
parent: Linux Server Administration
---

# Install and Configure Webmin Control Panel on Ubuntu Server (20.04 / 22.04)

Today I had a bright idea. I wanted to make it easier for Windows admins at my workplace to get up and running with our relatively small cluster of Linux machines. After a bit of Googling, I found two projects which looked quite helpful for this:

* [Cockpit](https://cockpit-project.org)
* [Webmin](https://webmin.com)

I had heard of Cockpit in the past but didn't really take much notice of its existence. I'd never heard of Webmin, but people online are happy to suggest it as a good alternative to Cockpit!

## So... Why Not Cockpit?

When I started toying with this idea, I actually went straight to installing Cockpit. It has a beautiful UI, which is, of course, an important deciding factor in this endeavour! But as I was about to confirm the `apt install` command I noticed something - *Cockpit uses NetworkManager under the hood*.

Now this is good for some people, and may even be fine for you, but parts of our network configuration are done through Ansible Playbooks which work directly with Netplan configuration files. Upon installation, NetworkManager replaces your Netplan configuration and makes itself the go-to solution for your networking needs.

{: .note }
NetworkManager isn't necessarily a bad thing, but it's not something I am prepared to refactor my existing code for.

So, rather than refactor my Ansible playbooks to play nice with NetworkManager, and risk breaking some servers in the process, I decided to look for an alternative - and that's when I discovered Webmin.

Webmin doesn't use NetworkManager under the hood. It has more dependencies/recommended packages, but NetworkManager isn't one of them, which is nice.

# Installing Webmin

Installing Webmin doesn't require much prep beforehand. In fact, it was pretty seamless.

{: .docs }
To save copying-and-pasting information that's already out there, I'll just link the official documentation for [installing Webmin](https://webmin.com/download/).

In short, you just download and execute their repo configuration script, then install Webmin with Apt.

I ran the below commands in a root terminal:

```
curl -o setup-repos.sh https://raw.githubusercontent.com/webmin/webmin/master/setup-repos.sh
sh setup-repos.sh

apt-get install webmin --install-recommends
```

After a couple minutes, Webmin was ready to go!

# Initial Configuration and Usage

When you install some new software, the first thing you want to do is *use it*, right?

In this case, I've got three goals to accomplish with Webmin:

1. Change the UI port from `10000` to `4500`
2. Configure a new Webmin user that *doesn't* have a Unix account on the system
3. Find installed software packages that need updating, and update them (if necessary)

I discovered pretty quickly that I could use my existing local account to log into Webmin, and then I found out why:

{: .warning}
> By default, any user who can run any command with *sudo* can log into Webmin.
>
> You can change this behaviour by visiting the **Webmin -> Webmin Users** blade and then navigating to **Unix User Authentication**. There, you'll find an option called *Allow users who can run all commands via sudo to login as root*.

That's fine for now. The same page offers various options for authenticating Unix users, but I'm happy to leave it set to default for the time being.

## Change Webmin Interface Port

Changing the port that Webmin binds to is surprisingly simple, and can be done directly within the Webmin UI (as you'd expect).

It's pretty intuitive - I navigated to the **Webmin -> Webmin Configuration** blade, then navigate to **Ports and Addresses**.

In the **IP addresses and ports** table, the first entry contained fields that were set to listen on all IP addresses, on port `10000`.

I changed this to `4500`, then clicked Save at the bottom of the page.

Webmin automatically redirected to the new port, saving me from editing the URL myself - it's a nice touch, as some services wouldn't do this.

## Configure a New Webmin User

So, goal number two. I wanted to create a new Webmin user.

I wanted this user to be able to be able to view System Logs, update Software Packages, and manage SSH Server. That means I had to create a Privileged Webmin user.

The **Webmin -> Webmin Users** blade is where Webmin users can be created, modified, and removed. The options to create new users are a little bit small, but at the top and bottom of the **Webmin Users** table, there are options to:

* Create a new privileged user
* Create a new safe user

Because I wanted this user to be able to edit the SSH Server configuration and update Software Packages, it had to be a privileged user.

On the **Create Webmin User** page, there are various options for configuring the new user. A "Username", "Real name", and "Password" are pretty self-explanatory.

Under **Available Webmin modules**, there's a large list of modules which can be made available to the new user. There are lots of other options on this page so I recommend going through them (you can even limit logon dates/times!), but for now I just set up a basic user who can change a couple things.

I selected the *System Logs Viewer*, *Software Packages*, and *SSH Server* modules, then clicked Create.

Again, pretty seamless. The new user popped up in the table.

## Update Software Packages

On the left-hand panel, there's a small red button which you can use to - you guessed it - log out. So I did just that, and then logged in with my new user.

Then I navigated to the **System -> Software Packages** blade, and selected the **Update packages** tab.

Leaving the settings at their default values, I hit **Upgrade** and let Webmin do its work under the hood. It downloaded package lists, and then... did nothing. I'd already upgraded all my packages prior to installing Webmin.

{: .note }
Webmin pipes the output of most commands directly to the web UI, so you can follow what it's doing and trace errors in real-time.

# Still to Do...

Some of our Linux servers use SSSD to enable admins to log in with Active Directory accounts (I wrote an Ansible Role to set all this up for me, and will share it soon).

If I choose to deploy Webmin, then I'd like to test if users can log in directly with their AD credentials through the Webmin interface. It'll also be nice to know if this automatically creates a home directory for them - though as it's using Unix authentication under the hood, it's safe to assume it will.

# Conclusion

So far, Webmin is looking promising, and it seems to be what I want. It's fast, doesn't hog resources, is quick and simple to get started with, and looks nice enough to present to colleagues without feeling like I'm sending them on a mission to find .

I've got to do some more testing to determine how "safe" it'll be to integrate alongside my existing automations for configuring and maintaining our servers, but it could be a great way to integrate our Windows admins into the work of Linux admin, which is usually full of scary Command Line Interfaces and text-formatted configuration files.