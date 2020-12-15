The sole purpose of this repository is to provide a 32-bit Linux binary of DOSBox-X. Its main repository only provides a 64-bit one and that is packed in .rpm format. Uses of Debian-derived systems can however download that, open it with an archive manager and extract the binary.

It took me a lot of blood, toil tears and sweat to get it working and having done so I may as well share the fruits of my labours with anybody else who wants it.  It is however offered strictly 'as is' with no promises that it will work for anybody else.  It has only been tested on Debian Buster so what versions of the Ubuntu family it be compatible with I don't know.  For all that it is 32-bit it can be used on a 64-bit system if you run the command `sudo dpkg --add-architecture i386` before installing it or have done so in the past.

Click __*release*__, then __*dosbox-x\_0.83.9\_i386\_buster.deb*__ to download it.  When the download has completed you can install it by double-clicking it or entering `sudo dpkg -i dosbox-x_0.83.9_i386_buster.deb` from a terminal.  


# Building DOSBox-X

The best thing is to really build DOSBox-X yourself as that has the greatest chance of the result being suitable for your computer.  Unfortunately Dosbox-x's documentation is still far from complete — not surprising as the people involved with it concentrate their efforts on the software itself — and they seem to assume people already know how to do this. It took me months to fathom how to build it successfully so here’s what I learnt.

Firstly you need the stuff to build anything. On Debian/Ubuntu you can get this by installing `build-essential`. Then you need dosbox-x’s build dependencies. These are installed by:—

	sudo apt-get install automake libncurses-dev nasm libsdl-net1.2-dev libpcap-dev libfluidsynth-dev libavformat-* libswscale-* libavcodec-* libfreetype6* libsdl2-dev libsdl2-net-dev

Now go to https://github.com/joncampbell123/dosbox-x and click the green *Clone* button and choose *Download ZIP*. When that’s arrived open it and extract the files to a directory of your choosing. Open a terminal and cd into it.

To build the full version of DOSBox-X type:—

	./build --enable-sdl2

There are other ./build-whatever scripts available for different situations.

Now a load of meaningless text will appear on your screen that's scrolling too quickly to read.  Some of it will look like error messages but don't matter.  A good while later that should finish and you’ll have a binary called `dosbox-x` in the `src` sub-directory. You can test that by typing:—

	src/dosbox-x

It will be very big though.  To get it down to a sensible size run:—

    strip --strip-all src/dosbox-x

If all is well you can move that to wherever you like, though the usual place is `/usr/bin.`  This is best done by running:— 

    sudo make install


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

DOSBox-X has a built-in virtual dot-matrix printer taken from DOSBox-Daum but it's somewhat rough 'round the edges as yet.  It was only from early May 2020 that it even worked at all on Linux and still can't actually print to a real USB  printer, only produce an image of what would have come out of one. 

The official guide to printing is here:  https://github.com/Wengier/dosbox-x-wiki/wiki/Guide%3ASetting-up-printing-in-DOSBox%E2%80%90X

My preferred way of printing is to set `parellel1=file` and then print the resulting file using `lpr`. That works fine if it’s plain text or PCL, such as is the case if the DOS program is set to print to an HP Laserjet printer.  If your DOS program can only operate with a dot-matrix printer you'll have to use the virtual one though.

Printing a file that has been created either by capture or the virtual dot-matrix printer can be automated by use of the two scripts `dbx-print` and `CapturePrint`.  dbx-print sets up CapturePrint, which actually does the printing,  then loads DOSBox-X and when you quit that switches off the printing mechanism.  Normal DOSBox-X parameters can be added after dbx-print.  **NOTE:  BOTH THESE SCRIPTS ARE LIKELY TO NEED AMENDING TO SUIT YOUR SET-UP**.  As they stand they assume that DOSBox-X sends files it has captured from LPT1 or created with the virtual dot-matrix printer to `~/capture`, that the CapturePrint script is in `~/config/dosbox-x` and that the DOSBox-X binary is somewhere in your `$PATH`.  You will also need to have installed `inotify-tools`, plus `ghostscript` if you want to print postscript files created with the virtual dot-matrix printer.

Besides the above you need to configure your DOS program.  These vary as to how much you can configure them though.  Ideally if you are using the virtual dot-matrix printer it would be set-up to print to an Epson SQ-860 but this may not be possible if the program pre-dates that model.  I've found LQ-800 to work as does IBM Pro Printer and IBM Graphics Printer.  The good old Epson FX-80 causes the lines to print too far apart meaning they come out five to the inch instead of six.  As previously said, if your program could print to an HP Laserjet the best way is to set it to that and use the `parellel1=file` method.

Really you'll have to experiment to find what works best with the soft- and hardware you have.
