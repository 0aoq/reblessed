# reblessed

Because the original [blessed repo](https://github.com/chjj/blessed) is kinda _dead_, (last commit in **2016**) i decided to revive it.

![reblessed-stars](https://img.shields.io/github/stars/kenan238/reblessed)
![reblessed-forks](https://img.shields.io/github/forks/kenan238/reblessed)
![reblessed-stars](https://img.shields.io/github/license/kenan238/reblessed)
![reblessed-issues](https://img.shields.io/github/issues/kenan238/reblessed)

<p align="center">
  <img src="https://github.com/kenan238/reblessed/blob/master/reblessed-logo.png" alt="Logo"/>
</p>

A curses-like library with a high level terminal interface API for node.js.

![reblessed](https://raw.githubusercontent.com/chjj/blessed/master/img/v0.1.0-3.gif)

Blessed is over 16,000 lines of code and terminal goodness. It's completely
implemented in javascript, and its goal consists of two things:

1. Reimplement ncurses entirely by parsing and compiling terminfo and termcap,
and exposing a `Program` object which can output escape sequences compatible
with _any_ terminal.

2. Implement a widget API which is heavily optimized for terminals.

The blessed renderer makes use of CSR (change-scroll-region), and BCE
(back-color-erase). It draws the screen using the painter's algorithm and is
sped up with smart cursor movements and a screen damage buffer. This means
rendering of your application will be extremely efficient: blessed only draws
the changes (damage) to the screen.

Blessed is arguably as accurate as ncurses, but even more optimized in some
ways. The widget library gives you an API which is reminiscent of the DOM.
Anyone is able to make an awesome terminal application with blessed. There are
terminal widget libraries for other platforms (primarily [python][urwid] and
[perl][curses-ui]), but blessed is possibly the most DOM-like (dare I say the
most user-friendly?).

Blessed has been used to implement other popular libraries and programs.
Examples include: the [slap text editor][slap] and [blessed-contrib][contrib].
The blessed API itself has gone on to inspire [termui][termui] for Go.

## Install

``` bash
$ npm install reblessed
```

## Example

This will render a box with line borders containing the text `'Hello world!'`,
perfectly centered horizontally and vertically.

__NOTE__: It is recommend you use either `smartCSR` or `fastCSR` as a
`blessed.screen` option. This will enable CSR when scrolling text in elements
or when manipulating lines.

``` js
var blessed = require('reblessed');

// Create a screen object.
var screen = blessed.screen({
  smartCSR: true
});

screen.title = 'my window title';

// Create a box perfectly centered horizontally and vertically.
var box = blessed.box({
  top: 'center',
  left: 'center',
  width: '50%',
  height: '50%',
  content: 'Hello {bold}world{/bold}!',
  tags: true,
  border: {
    type: 'line'
  },
  style: {
    fg: 'white',
    bg: 'magenta',
    border: {
      fg: '#f0f0f0'
    },
    hover: {
      bg: 'green'
    }
  }
});

// Append our box to the screen.
screen.append(box);

// Add a png icon to the box
var icon = blessed.image({
  parent: box,
  top: 0,
  left: 0,
  type: 'overlay',
  width: 'shrink',
  height: 'shrink',
  file: __dirname + '/my-program-icon.png',
  search: false
});

// If our box is clicked, change the content.
box.on('click', function(data) {
  box.setContent('{center}Some different {red-fg}content{/red-fg}.{/center}');
  screen.render();
});

// If box is focused, handle `enter`/`return` and give us some more content.
box.key('enter', function(ch, key) {
  box.setContent('{right}Even different {black-fg}content{/black-fg}.{/right}\n');
  box.setLine(1, 'bar');
  box.insertLine(1, 'foo');
  screen.render();
});

// Quit on Escape, q, or Control-C.
screen.key(['escape', 'q', 'C-c'], function(ch, key) {
  return process.exit(0);
});

// Focus our element.
box.focus();

// Render the screen.
screen.render();
```

## Documentation
Go to the WIKI tab

#### Testing

Most tests contained in the `test/` directory are interactive. It's up to the
programmer to determine whether the test is properly displayed. In the future
it might be better to do something similar to vttest.


#### Examples

Examples can be found in `examples/`.


#### FAQ

1. Why doesn't the Linux console render lines correctly on Ubuntu?
  - You need to install the `ncurses-base` package __and__ the `ncurses-term`
    package. (#98)
2. Why do vertical lines look chopped up in iTerm2?
  - All ACS vertical lines look this way in iTerm2 with the default font.
3. Why can't I use my mouse in Terminal.app?
  - Terminal.app does not support mouse events.
4. Why doesn't the OverlayImage element appear in my terminal?
  - The OverlayImage element uses w3m to display images. This generally only
    works on X11+xterm/urxvt, but it _may_ work on other unix terminals.
5. Why can't my mouse clicks register beyond 255 cells?
  - Older versions of VTE do not support any modern mouse protocol. On top of
    that, the old X10 protocol it _does_ implement is bugged. Through several
    workarounds we've managed to get the cell limit from `127` to `255`. If
    you're not happy with this, you may want to look into using xterm or urxvt,
    or a terminal which uses a modern VTE, like gnome-terminal.
6. Is blessed efficient?
  - Yes. Blessed implements CSR and uses the painter's algorithm to render the
    screen. It maintains two screen buffers so it only needs to render what
    has changed on the terminal screen.
7. Will blessed work with all terminals?
  - Yes. Blessed has a terminfo/termcap parser and compiler that was written
    from scratch. It should work with every terminal as long as a terminfo
    file is provided. If you notice any compatibility issues in your termial,
    do not hesitate to post an issue.
8. What is "curses" and "ncurses"?
  - ["curses"][curses] was an old library written in the early days of unix
    which allowed a programmer to easily manipulate the cursor in order to
    render the screen. ["ncurses"][ncurses] is a free reimplementation of
    curses. It improved upon it quite a bit by focusing more on terminal
    compatibility and is now the standard library for implementing terminal
    programs. Blessed uses neither of these, and instead handles terminal
    compatibility itself.
9. What is the difference between blessed and blessed-contrib?
  - blessed is a major piece of code which reimplements curses from the ground
    up. A UI API is then layered on top of this. [blessed-contrib][contrib] is
    a popular library built on top of blessed which makes clever use of modules
    to implement useful widgets like graphs, ascii art, and so on.
10. Are there blessed-like solutions for non-javascript platforms?
  - Yes. There are some fantastic solutions out there.
    - Perl: [Curses::UI][curses-ui]
    - Python: [Urwid][urwid]
    - Go: [termui][termui] & [termbox-go][termbox]


## Contribution and License Agreement

If you contribute code to this project, you are implicitly allowing your code
to be distributed under the MIT license. You are also implicitly verifying that
all code is your original work. `</legalese>`


## Original License

Copyright (c) 2013-2015, Christopher Jeffrey. (MIT License)

See LICENSE for more info.

[slap]: https://github.com/slap-editor/slap
[contrib]: https://github.com/yaronn/blessed-contrib
[termui]: https://github.com/gizak/termui
[curses]: https://en.wikipedia.org/wiki/Curses_(programming_library)
[ncurses]: https://en.wikipedia.org/wiki/Ncurses
[urwid]: http://urwid.org/reference/index.html
[curses-ui]: http://search.cpan.org/~mdxi/Curses-UI-0.9609/lib/Curses/UI.pm
[termbox]: https://github.com/nsf/termbox-go
[ttystudio]: https://github.com/chjj/ttystudio#choosing-a-new-font-for-your-terminal-recording
