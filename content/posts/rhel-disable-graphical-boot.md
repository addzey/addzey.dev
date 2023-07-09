+++
title = "RHEL Disable Graphical Boot"
date = "2023-07-09T16:23:54+09:30"
author = "Adam"
authorTwitter = "" #do not include @
cover = ""
tags = ["RHEL", "CentOS", "Fedora", "Troubleshooting"]
keywords = ["graphical boot", "troubleshooting"]
description = "Disable graphical boot on RHEL, CentOS Stream, Fedora Server"
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
+++

# Why disable graphical boot?

If I've connected a monitor to a physical server or consoled into a virtual machine during boot, I'd like to see what's going on without having to hit any key combos etc. I want the info the be right there scrolling past!

## How to:

Using the tool **grubby** we can see what the current kernel boot arguments are

```grubby --info=ALL```

![Grubby](../../Grubby-2023-07-09_165643.png)

We can see the options "quiet rhgb" listed in the arguments line  
"*quiet*" reduces the verbose output to console  
"*rhgb*" stands for RedHat Graphical Boot which puts a nice display while booting but no info as to what's happening  

To remove the options "quiet" and "rhgb" from all installed kernels:  

```grubby --update-kernel=ALL --remove-args="rhgb quiet"```

