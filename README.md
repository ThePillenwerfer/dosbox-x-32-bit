The sole purpose of this repository is to provide a 32-bit Linux binary of dosbox-x.  It’s main repository only provides a 64-bit one and that is packed in .rpm format.  Uses or Debian-derived systems can however download that, open it with an archive manager and extract the binary.

My binary is compiled without the AV codec.  As far as I am aware this is only needed for playing audio CDs, MP3s and the like.  Compiling it in adds 100MBs to the binary so really doesn’t seem worth it.


# Know Bugs

I have never been able to make the virtual dot-matrix printer work.  This isn’t a very big deal as capturing the LPT1 output to a file does work and that can then be sent to a printer using lpr.  That works fine if it’s plain text or PCL, such as is the case if the DOS program is set to print to an HP Laserjet printer.  If the program is setup for a dot-matrix printer such as an Epson FX80 see:

If using the UK kepyboard layout you can't type a # or ~.


# Building dosbox-x

Dosbox-x is poorly documented — not surprising as the people involved with it concentrate their efforts on the software itself.  It took me months to fathom how to build it successfully so here’s what I learnt.

Firstly you need the stuff to build anything.  On Debian/Ubuntu you can get this by installing `build-essential`.  Then you need dosbox-x’s build dependancies.  These are installed by:—

`sudo automake apt-get install automake libncurses-dev nasm libsdl-net1.2-dev libpcap-dev libfluidsynth-dev libavformat-* libswscale-* libavcodec-* libfreetype6-dev`

You also need `fmpeg` and `libavdevice58` if you want to compile in the audio codec but the Stretch versions are too old.  If you really want them, download, build and install the latest ffmpeg from: https://ffmpeg.org/releases/ffmpeg-4.2.2.tar.bz2  That is an easy build but takes ages.  Open ffmpeg-4.2.2.tar.bz2 and extract the files to a directory of your choosing then open a terminal and cd into it.  Then type

  `./configure`
  `make`
  `sudo make install`

Now go to https://github.com/joncampbell123/dosbox-x and click the green Clone or Download button and choose `download zip`.  Then that’s arrived open it and extract the files to a directory of your choosing.  Open a terminal and cd into it.

To build the full version of dosbox-x type:—

  `./build`

To build it without the AV coded it’s:—

  `./build-no-avcodec`

There are other ./build-whatever scripts available for different situations.

A good while later that should finish and you’ll have a binary called `dosbox-x` in the `/src` sub-directory.  You can test that by typing:—

  `src/dosbox-x`

If all is well you can move that to wherever you like, though the usual place is `usr/bin`.


# Configuration

When you first run dosbox-x it will create a directory `~/.dosbox` which contains a blank configuration file called...  You can edit that to suit your needs.  This repository contains the reference version which gives details of ALL the possible settings, though you’re unlikely to need most of them.  For instance the configuration file I actually use only consists of:—

`# This is the configuration file for DOSBox 0.82.18. (Please use the latest version of DOSBox)`
`# Lines starting with a # are comment lines and are ignored by DOSBox.`
`# They are used to (briefly) document the effect of each option.`
`# To write out ALL options, use command 'config -all' with -wc or -writeconf options.`
` `
`[sdl]`
`output=opengl`
`mapperfile=~/.dosbox/mapper-0.83.2.map`
` `
`[dosbox]`
`#machine=cga`
`captures=~/capture`
` `
`[parallel]`
`parallel1=file`
` `
`[dos]`
`keyboardlayout=uk`
` `
`[autoexec]`
`# Lines in this section will be run at startup.`
`# You can put your MOUNT lines here.`
` `
`mount c: ~/.dos`
`c:`
