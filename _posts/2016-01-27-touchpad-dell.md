---
layout:     post
title:      Toggling DELL 9350's touchpad on Ubuntu 15.10
date:       2016-01-27 23:32:11
summary:    How to set up a hotkey to toggle DELL 9350's touchpad on Ubuntu 15.10
---

Running Ubuntu on this laptop is still a bit tricky.

This isn't one of the worst problems, but it can be a little unnerving since the driverless touchpad can be super sensitive and bring chaos while you're typing.

In my case, trying to disable it from Ubuntu's settings panel was useless, so I had to resort to this script:

```bash
#!/bin/bash

declare -i ID
ID=`xinput list | grep -Eo 'DLL0704:01\s06CB:76AE\sUNKNOWN\s*id\=[0-9]{1,2}' | grep -Eo '\=[0-9]{1,2}' | grep -Eo '[0-9]{1,2}'`
declare -i STATE
STATE=`xinput list-props $ID|grep 'Device Enabled'|awk '{print $4}'`
if [ $STATE -eq 1 ]
then
	xinput disable $ID
	echo "Touchpad disabled."
else
	xinput enable $ID
	echo "Touchpad enabled."
fi
```

The device name could be different for you, so run `xinput` in a terminal.
In my case the name was: `DLL0704:01 06CB:76AE UNKNOWN`.

If it's the same, you can keep the script as it is, otherwise you'll have to edit the line that fetches the ID changing the regular expression.

Save the file somwhere (for example `~/bin/toggle_touchpad.sh`), and make it executable (`chmod +x ~/bin/toggle_touchpad.sh`).

Now go to the settings *panel -> keyboard -> shourtcuts -> custom shortcuts* and add a new shortcut pointing to the script (I used CTRL+F7).