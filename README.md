# xmonad-pulsevolume
Scripts for setting and visualizing pulseaudio volume with XMonad+xmobar (and maybe others).

## Purpose

 * Easily change pulseaudio volume from xmonad
 * Provide a very simple starting point for customization.
 * Visualize volume in xmobar.  Here are some snapshots of the representation in increasing volume:

```
     [  (mute)  ]
     [          ]
     [.         ]
     [-         ]
     [/         ]
     [/.        ]
     [/-        ]
     [//        ]
     [//.       ]
     [///-      ]
     [////.     ]
     [//////////]
```

## Install & Use

0. Dependencies: working xmonad, pulseaudio.  For visualization: python, xmobar.
1. Add pulse-volume.sh and show-volume.py somewhere on your $PATH.
2. Update xmonad.hs for media buttons.  I add the following keybindings:
   ```
    -- mute button
    , ((0                 , 0x1008FF12), spawn "pulse-volume.sh toggle")

    -- volumeup button
    , ((0                 , 0x1008FF13), spawn "pulse-volume.sh increase")

    -- volumedown button
    , ((0                 , 0x1008FF11), spawn "pulse-volume.sh decrease")
   ```
3. Update xmobar configuation to run python script.  The important thing is to add to the 'commands' array, and to the template.  Here's a full example:
   ```
     Config { font = "xft:Droid Sans Mono:size=9:antialias=true"
           , bgColor = "#181818"
           , fgColor = "green"
           , position = TopW L 95
           , commands = [ Run Cpu ["-L","3","-H","50","--normal","green","--high","red"] 5
                        , Run Memory ["-t","Mem: <usedratio>%"] 5
                        , Run Date "%a %b %d %Y %H:%M:%S " "date" 1
                        , Run Com "python" ["/path/to/show-volume.py"] "vol" 1
                        , Run StdinReader
                        ]
           , sepChar = "%"
           , alignSep = "}{"
           , template = "%StdinReader% }{ %cpu% | %memory%   %vol%  <fc=#e0e0e0>%date%</fc>"
           }
   ```
   
4. (Optional) Upon boot, I like to reset the volume to what I consider a reasonable volume and unmute.  In my .xsession file I run `pulse-volume.sh reset`.  This also ensures that ~/.mute and ~/.volume are correct (see below).
5. Restart xmonad.

## Under the hood and future work

The pulse-volume.sh script maintains two files in your home directory: ~/.volume and ~/.mute.  Whenever you run this script it reads the current volume from the file and updates it appropriately (it takes a parameter "increase", "decrease", "mute", "unmute", "toggle").  It also updates pulse-volume.  If this file is changed between uses of the script, the volume can jump dramatically; hence the "reset" feature to go back to a fixed volume.  Obviously, it would be better to get the current volume from pulse audio itself instead of replicating state... contributions welcome!

./show-volume.py generates the graphical representation from these files.  It takes no parameters -- run it from the command line to see what it does.  It's very easy to hack on this to make a new visualization... let me know if you do something cool!

I expect you can use these scripts with other window managers or status bars, like dzen2.  I somehow doubt there's much need -- but if you find it useful, feel free to contribute additions.

## Bugs

I wrote this years ago and never really intended to release it.  Yet, it's stood the test of time for me, and I automatically deploy it on several linux systems without issues.  

As of March 2017, 2 years later, no significant bugs have come up (we only had issue #1 and that's been patched) and a few people have given the project a "star".  So I suppose that suggests it works fine in the common case.  We've yet to see any compatibility issues come up.

Use the issue tracker.  For security bugs, email berkeley@berkeleychurchill.com.  You can get a gpg key off my webpage at https://www.berkeleychurchill.com if you like.
