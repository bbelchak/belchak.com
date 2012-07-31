---
comments: true
date: 2011-07-27 17:18:43
layout: post
slug: problems-with-ipv46-hosts-in-osx-lion
title: Problems with IPv4/6 hosts in OSX Lion
wordpress_id: 39
categories:
- Apple
tags:
- lion
- osx
---

If you're like me, you end up doing a lot of testing in virtual machines. I run several VMs that do various different tasks for me so that I don't have to sully my Mac with packages I rarely need, etc. So what I do is create a VM and give it a local hostname defined in my /etc/hosts file.

Recently, after my upgrade to OSX Lion, I started running into an issue where it would take over 5 seconds to connect to any of my VMs using any protocol (SSH, HTTP, etc). After several hours of troubleshooting, and several Wireshark sessions later, the truth of the matter was plain:

[![](http://www.belchak.com/wp-content/uploads/2011/07/kdxf5y8qtaxh-300x15.png)](http://www.belchak.com/wp-content/uploads/2011/07/kdxf5y8qtaxh.png)

OSX is now doing IPv6 by default with no way that I have found to disable it yet, and so that means that if you have hosts that you want to reference with locally defined hostnames, you are going to have to create an IPv6 entry for them as well.

Here is a great site that I found that helps with the conversion: [SubnetOnline.com](http://www.subnetonline.com/pages/converters/ipv4-to-ipv6.php) 

I am sure that there are better ways to do this, but I just wanted to share this quick little hack that I found with you. If you have a better way, please let me know!
