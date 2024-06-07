# Commander X16 to Glasgow adapter

This is an expansion card for the [Commander
X16](https://x16community.github.io/faq/faq.html) retro PC, which
exposes one memory mapped IO region (at a time) to a [Glasgow
Interface Explorer](https://glasgow-embedded.org/latest/intro.html).

With it, the Glasgow can emulate nearly any IO card you might plug
into the X16. So far I've [got an
applet](https://github.com/jkominek/glasgow) which half-assedly
emulates a 16550, and hooks it up to a pseudoterminal on a Linux
system. With David Murray's ROMTERM, you can then use the X16 as
a "serial" terminal to log into the Linux system.

(To be super clear, that's all done without the [Commander X16 Serial
& ESP32 Network
card](https://texelec.com/product/commander-x16-serial-network-card/).)

![KiCad render of the card](https://raw.githubusercontent.com/jkominek/x16glasgow/master/imgs/3dpcb.jpg)

And here's a quick little video of it in operation: https://www.youtube.com/watch?v=3OkkUSMPfc0

# how it works

The Glasgow has 16 IO pins. To act as an IO device, you need to handle:
- 8 data lines
- RWB
- PHI2
- At least one of the IO selection lines
- An IRQ
- Five address lines (for the full 32 byte space)

That's 17 lines. You could drop the IRQ line, but that'd be boring.
Instead, we decode RWB, PHI2 and a jumper-chosen IO selection line
using some 74-series logic, and condense the three of them into
/RD and /WR lines. That fits everything into 16 pins.

# how do i get a getty on a pseudoterminal

```
mgetty -r -b -m '""' /dev/pts/X
```

not actually sure if the `-b` is necessary. The `-m` bit was the secret
sauce.
