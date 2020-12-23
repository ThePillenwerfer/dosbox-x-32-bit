The sole purpose of this repository is to provide a 32-bit Linux binary of DOSBox-X. Its main repository only provides a 64-bit one and that is packed in .rpm format. Uses of Debian-derived systems can however download that, open it with an archive manager and extract the binary.

It took me a lot of blood, toil, tears and sweat to get it working and having done so I may as well share the fruits of my labours with anybody else who wants it.  It is however offered strictly 'as is' with no promises that it will work for anybody else.  It has only been tested on Debian so what versions of the Ubuntu family it may be compatible with I don't know.  Two versions are available, one built on Buster and one on Stretch.  If one doesn't work, try the other and if both fail see next section.  For all that it is 32-bit it can be used on a 64-bit system if you run the command `sudo dpkg --add-architecture i386` before installing it or have done so in the past.

Click __*releases*__, then __*dosbox-x\_0.83.9\_i386\_<version\>.deb*__ to download it.  When the download has completed you can install it by double-clicking it or entering `sudo dpkg -i dosbox-x_0.83.9_i386_<version>.deb` from a terminal.  To launch it type dosbox-x in a termninal or add it to the menu.


# Building DOSBox-X

The best thing is to really build DOSBox-X yourself as that has the greatest chance of the result being suitable for your computer.  Unfortunately Dosbox-x's documentation is still far from complete — not surprising as the people involved with it concentrate their efforts on the software itself — and they seem to assume people already know how to do this. It took me months to fathom how to build it successfully so here’s what I learnt.

Firstly you need the stuff to build anything. On Debian/Ubuntu you can get this by installing `build-essential`. Then you need dosbox-x’s build dependencies. These are installed by:—

	sudo apt-get install automake libncurses-dev nasm libsdl-net1.2-dev libpcap-dev libfluidsynth-dev libavformat-* libswscale-* libavcodec-* libfreetype6* libsdl2-dev libsdl2-net-dev

Now go to https://github.com/joncampbell123/dosbox-x and click the green *Code* button and choose *Download ZIP*. When that’s arrived open it and extract the files to a directory of your choosing. Open a terminal and cd into it.

To build the full version of DOSBox-X type:—

	./build --enable-sdl2

There are other ./build-whatever scripts available for different situations.

Now a load of meaningless text will appear on your screen that's scrolling too quickly to read.  Some of it will look like error messages but don't matter and it may appear to freeze-up at times.  A good while later (ten to fifteen minutes on my computers) that should finish and you’ll have a binary called `dosbox-x` in the `src` sub-directory. You can test that by typing:—

	src/dosbox-x

It will be very big though.  To get it down to a sensible size run:—

    strip --strip-all src/dosbox-x

If all is well you can move that to wherever you like, though the usual place is `/usr/bin.`  This is best done by running:— 

    sudo make install

After that you can launch it by typing `dosbox-x` in a terminal or adding it to the Menu.


# Configuration

Settings are stored in the file `~/.config/dosbox-x/dosbox-x-0.83.9.conf`.  This repository contains the reference version which gives details of ALL the possible settings, though you’re unlikely to need most of them. For instance the configuration file I actually use only consists of:—

```
[sdl]
output=opengl
mapperfile=~/.config/dosbox-x/mapper-dosbox-x.map

[dosbox]
captures=~/capture

[parallel]
parallel1=file

[autoexec]
# Lines in this section will be run at startup.
# You can put your MOUNT lines here.

keyb uk 437
mount c: ~/.dos
c:
```

There is a Wiki for DOSBox-X at https://github.com/Wengier/dosbox-x-wiki/wiki which is currently actively being updated/expanded.


# UK Keyboard

Selecting the UK keyboard layout in either the `[DOS]` section of the `.conf` file or by running `keyb uk` causes slight distortion of the text on screen.  This can be avoided by running `keyb uk 437` instead.  This can be added to the `[autoexec]` section of the `.conf` file, as in the example above.


# Printing

DOSBox-X has a built-in virtual dot-matrix printer taken from DOSBox-Daum but it's somewhat rough 'round the edges as yet.  It was only from early May 2020 that it even worked at all on Linux though there have recently been big improvements. 

The official guide to printing is here:  https://github.com/Wengier/dosbox-x-wiki/wiki/Guide%3ASetting-up-printing-in-DOSBox%E2%80%90X

If the DOS program could print to an HP Laserjet printer set `parallel1=file file:output1.prn timeout:1000 openpcl:lpr` in the `[parallel]` section of the `.conf` file.  Similar settings can be used if your program could print to a PostScript printer or if you are only printing plain text.

If your program is outputting printer data with ESC/P coding, as used by dot-matrix printers, you'll have to use the virtual one.

To use it you need to set `parallel1=printer` in the `[parallel]` section of the `.conf` file.

To work it needs some fonts.  On Linux it tries to use Liberation Mono, Roman and Sans but may have trouble finding them depending on where your version of Linux stores them.  You can tell it in the `[printer]` section with `fontpath=`, eg `fontpath=/usr/share/fonts/truetype/liberation`.  In practice you're unlikely to need anything other than the monospaced one.  If you prefer something else to Liberation Mono make a directory `~/.config/dosbox-x/FONTS` — note the capital letters — and put a copy of your chosen font in it and rename it `courier.ttf`.  You can also use links, eg `ln /usr/share/fonts/truetype/dejavu/DejaVuSansMono.ttf ~/.config/dosbox-x/FONTS/courier.ttf` or `ln ~/.fonts/<something you've downloaded> ~/.config/dosbox-x/FONTS/courier.ttf`.

My suggested version of the `[printer]` section is:—

```
[printer]
printer     = true
dpi         = 360
width       = 82
height      = 117
font_path   = /usr/share/fonts/truetype/liberation
printoutput = ps
openwith    = lpr
multipage   = false
docpath     = /home/<USER>/capture
timeout     = 1000
```

The defaults are for printing on American ‘Letter’ paper (8½” x 11”) so I’ve changed it to A4 (8¼” x 11¾”).  Besides that I have changed the `timeout` from `0`.  If that’s left it will only ‘print’ when it receives a form feed and not all software sends one.  Now if no data comes for one second it assumes that's all there is and 'prints'.

In `docpath`, `<USER>` needs changing to your user name.  Another 'quirk' is that `~/` won't work here; it has to be given in full.

According to the reference `.conf` file if `printoutput=printer` it will print to your actual printer but this **doesn't work on Linux**.  The `openwith:lpr` tells it to open the file it has created with `lpr`, ie print it.

Besides the above you need to configure your DOS program.  These vary as to how much you can configure them though.  If it can print to an HP Laserjet or postscript printer it's best to set it to that and use the `parellel1=file` method.  If it can only print to dot-matrix printers and you are having to use the virtual one ideally tell the program it is connected to an Epson SQ-860.  This may not be possible though if the program pre-dates that model.  I've found LQ-800 to work as does IBM Pro Printer and IBM Graphics Printer.  The good old Epson FX-80 causes the lines to print too far apart meaning they come out five to the inch instead of six.  

Really you'll have to experiment to find what works best with the soft- and hardware you have.






