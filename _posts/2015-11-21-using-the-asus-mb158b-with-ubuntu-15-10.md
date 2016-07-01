---
layout:     post
title:      Using the Asus MB168B+ with Ubuntu 15.10
date:       2015-11-21 23:32:11
summary:    How to make the ASus MB168B+ work with Ubuntu
---

The **Asus MB168B+** is a very nice portable monitor, and it would be a shame if it weren't possible to use it with Ubuntu ;)

Download the Ubuntu [driver](http://www.displaylink.com/downloads/ubuntu.php) and extract it somewhere.\n\nOpen up a terminal, and navigate to that folder. Then launch these commands:

```bash
./displaylink-driver-1.0.138.run --keep --noexec\ncd displaylink-driver-1.0.138/
```

Then type `nano displaylink-installer.sh` to edit the installer file (if you don't like nano, use your favourite text editor!).

Change this:

```bash
detect_distro()
{
  if which lsb_release >/dev/null; then
      local R=$(lsb_release -d -s)
      echo \"Distribution discovered: $R\"
      if [ -z \"${R##Ubuntu 14.*}\" ]; then
        SYSTEMINITDAEMON=upstart
      elif [ -z \"${R##Ubuntu 15.04*}\" ]; then
        SYSTEMINITDAEMON=systemd
        fi
  else
    echo \"WARNING: Unknown distribution, assuming defaults - this may fail.\" >&2
    fi
  }
```
                                          
 To this:
                                      
```bash
detect_distro()
{
  if which lsb_release >/dev/null; then
    local R=$(lsb_release -d -s)
    echo \"Distribution discovered: $R\"
    SYSTEMINITDAEMON=systemd
    fi
}
```

And then run:

`sudo ./displaylink-installer.sh install`

Your monitor should be working now!

*p.s. this procedure should work to install the driver in distros other than Ubuntu!*
