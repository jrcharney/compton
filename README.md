# Pasadena

__Compton__ is a lightweight standalone compositor for X, forked from __compton__ which was forked from __xcompmgr-dana__.  I wanted transparency in [Fluxbox](http://fluxbox.org/) when using [URXVT](http://software.schmorp.de/pkg/rxvt-unicode.html).

## What makes Pasadena different from Compton?

I believe [Christopher Jeffrey](github.com/chjj) has a great project going for him.  It's really great.
The fact that he took the time to fork, debug, improve, and refactor Dana Jansens' for of xcompmgr is an outstanding achievement.

However, it is not complete.  And the efforts made to refactor that software are in vain if the documentation isn't as svelte as the software itself.  I've decided to fork it and toss out a lot of junk.  Trust me, there is junk here.

The problem: Chris' fork uses [ASCIIDoc](http://asciidoc.org/).  With a name like that you'd thikin "Oh, this documentation is written in plain text."  Actually, it isn't.  It's completely bloated.  It's like [Doxygen](http://www.stack.nl/~dimitri/doxygen/).

Sure you can have a great piece of software with documentation, but if that software requires you to install a gigabyte of other software for a file that can just use that one file, you doing something wrong.

If you want to write documentation for Linux, BSD, or Unix, the general format is to use [man pages](https://en.wikipedia.org/wiki/Man_page).  These are easy to write.  They don't really require extra software, and the developer can produce them as part of software package.  I'll attempt to replace all of that.

The other problem: Chris is still on the fence about making a [CMake](http://www.cmake.org/) file for installing Compton.  Pasadena should fix that.  We're just going to use `make`. (K.I.S.S.!)

Why `make` over `cmake`?

The rise of Mac users writing code on their MacBooks has brought on an unplesant change in how to make programs from source for non-OSX users.  Namely the part where more commands are added.

* Making with `make` is simple: `./configure && make && sudo make install`
* Making with `cmake` is complex: `mkdir build && cd build` followed by `cmake .. && make && sudo make install`. (You can't streamline the first two commands and the last three commands.)

Personally, I add `sudo ldconfig` just to finish this process in either method.

## Changes from Compton:

* Better documentation. (No more installing an entire webserver for something that is just 2MB large.)  I recomend reading about [creating man pages on TLDP](http://www.tldp.org/HOWTO/Man-Page/) and [this page](http://www.linuxhowtos.org/System/creatingman.htm).
* Completed installation instruction. (`./configure.sh` FTW!) See [TLDP](http://www.tldp.org/HOWTO/Software-Building-HOWTO.html).

## Changes from xcompmgr:

* OpenGL backend (`--backend glx`), in addition to the old X Render backend.
* Inactive window transparency (`-i`) / dimming (`--inactive-dim`).
* Titlebar/frame transparency (`-e`).
* Menu transparency (`-m`, thanks to Dana).
* shadows are now enabled for argb windows, e.g. terminals with transparency
* removed serverside shadows (and simple compositing) to clean the code,
  the only option that remains is clientside shadows
* configuration files (see the man page for more details)
* colored shadows (`--shadow-[red/green/blue]`)
* a new fade system
* VSync support (not always working)
* Blur of background of transparent windows, window color inversion (bad in performance)
* Some more options...

## Fixes from the original xcompmgr:

* fixed a segfault when opening certain window types
* fixed a memory leak caused by not freeing up shadows (from the freedesktop repo)
* fixed the conflict with chromium and similar windows [many more](https://github.com/chjj/compton/issues) (Talk to Chris about those issues.)
* fixes to make it more simpatico with Fluxbox. [I'll take a shot at that.](https://github.com/jrcharney/pasadena/issues)

## Building

### Dependencies:

__B__ for build-time

__R__ for runtime

* libx11 (B,R)
* libxcomposite (B,R)
* libxdamage (B,R)
* libxfixes (B,R)
* libXext (B,R)
* libxrender (B,R)
* libXrandr (B,R)
* libXinerama (B,R) (Can be disabled with `NO_XINERAMA` at compile time)
* pkg-config (B)
* make (B)
* xproto / x11proto (B)
* sh (R)  (although Bash will do.)
* xprop,xwininfo / x11-utils (R)
* libpcre (B,R) (Can be disabled with `NO_REGEX_PCRE` at compile time)
* libconfig (B,R) (Can be disabled with `NO_LIBCONFIG` at compile time)
* libdrm (B) (Can be disabled with `NO_VSYNC_DRM` at compile time)
* libGL (B,R) (Can be disabled with `NO_VSYNC_OPENGL` at compile time)
* libdbus (B,R) (Can be disabled with `NO_DBUS` at compile time)
* ~~asciidoc (B) (and docbook-xml-dtd-4.5, libxml-utils, libxslt, xsltproc, xmlto, etc. if your distro doesn't pull them in)~~

### How to build

To build, make sure you have the dependencies above.

The old way that Chris used was this:
```bash
# Make the main program
$ make
# Make the man pages
$ make docs
# Install
$ make install
```

The new way that I use, is complete:
```bash
# Configure the file
./configure.sh
# make it
make
# install it
sudo make install
```

TLDR: `./configure.sh && make && sudo make install`

(Compton does include a `_CMakeLists.txt` in the tree, but we haven't decided whether we should switch to CMake yet. The `Makefile` is fully usable right now.)

## Known issues

* Chris's [FAQ](https://github.com/chjj/compton/wiki/faq) covers some known issues with the Compton stuff.
* My [FAQ](https://github.com/jrcharney/pasadena/wiki/faq) covers the more Pasadena stuff.
* VSync does not work too well. You may check the Chris' [VSync Guide](https://github.com/chjj/compton/wiki/vsync-guide) for how to get (possibly) better effects.  I'll need to read into this a little more as I clean house here.
* If `--unredir-if-possible` is enabled, when compton redirects/unredirects windows, the screen may flicker. According to Chris, using `--paint-on-overlay` appears to minimizes that problem for the moment, yet neither of us know what causes that or how to fix it yet.
* compton may not track focus correctly in all situations. The focus tracking code is experimental. `--use-ewmh-active-win` might be helpful.  However, Fluxbox might have a solution from what I can tell.
* The performance of blur under X Render backend might be pretty bad. OpenGL backend could be faster.
* With `--blur-background` you may sometimes see weird lines around damaged area. `--resize-damage YOUR_BLUR_RADIUS` might be helpful in the case.

## Usage

~~Please refer to the Asciidoc man pages (`man/compton.1.asciidoc` & `man/compton-trans.1.asciidoc`) for more details and examples.~~ New documentation coming soon.

Note a sample configuration file `compton.sample.conf` is included in the repository.  I'll probably includ using `pasadena.sample.conf` as an alternate filename to use.

## Support
* Bug reports and feature requests should go to the "Issues" section above.
* Chris' (semi?) official IRC channel is #compton on FreeNode.  Although I've been chatting on #fluxbox a bit too about this issue since most everyone there is intersted in Compton.  This is important considering the Fluxbox Wiki still talks about Xcompmgr, which Compton was designed to replace.
* Some information is available on the wikis, including
  * [FAQ](https://github.com/chjj/compton/wiki/faq),
  * [VSync Guide](https://github.com/chjj/compton/wiki/vsync-guide),
  * [Performance Guide](https://github.com/chjj/compton/wiki/perf-guide).
  * Eventually I'll clone his wiki on my wiki.

## License

Pasadena, like Compton, is distributed unter MIT License.  See [LICENSE](https://github.com/jrcharney/pasadena/LICENSE) for more info.

From what Chris can tell, this seems to be the genealogy of xcompmgr development.

* Keith Packard (original author)
* Matthew Hawn
* ...
* Dana Jansens
* chjj and richardgv
* Jason Charney

Not counting the tens of people who forked it in between.

## Software Recommendations and Shout Outs
* Fluxbox (A new release was released this year! Now if only they could produce some new screenshots.)
* URXVT
* Tmux
* Vim
* Spf13-Vim  (Don't forget the Airline fonts, I'll have to get a dot-file repo going!)
* RVM.io  (for making a better Ruby installation than what most Distros have)
* Git for being awesome
* Chris, Richard, Dana, Matthew, and Keith
* AND YOU for reading this file and/or possibly download this repo!
