---
title: "Paintfuck: Brainfuck for art"
date: 2023-10-27 00:00:00 +0000
categories: [esolang]
tags: [esolang, brainfuck, creative]
---
[Susam Pal](https://susam.net/)'s minimal drawing language
[CRF\[\]](https://susam.net/cfr.html) recently got some attention on
[r/programming](https://www.reddit.com/r/programming/comments/17d216o), quickly
followed by the slightly more interactive CFRS[] variant
([website](https://susam.net/cfrs.html),
[reddit](https://www.reddit.com/r/programming/comments/17gt7ae)).

I found it very enjoyable to play around with, but while I did, I constantly
thought about how much the heavy reliance on `[]` as a looping construct
makes it look like [Brainfuck](https://en.wikipedia.org/wiki/Brainfuck), and how
it could be very fun to have the same interface available for drawing in Brainfuck.

So I forked her code and made [Paintfuck (try here)](/assets/pages/paintfuck.html)!

I knew from the beginning that I wanted the canvas itself to be the tape, but I
considered a bunch of options for how color would work. Each cell could have
three data pointers, for HSV or RGB? But then you'd need a lot of different
instructions. Expose just hue or just lightness via the tape value? Maybe
combine three independent cells on the tape to select a color? Add an extra bit
to determine whether the cell is rendered at all?

In the end, I went with a very simple approach, that I found visually satisfying:
Just use the same base colors present in CRF[], and cycle through them mod 8.
This enables high contrast and funky patterns, that still give a nice visual
impression for what is happening computationally. I might add other color modes
later, if I feel the need to encode a jpeg in Paintfuck.


## First Impression

Unlike CRF[], it is hard to get Paintfuck to do something that looks cool and
interesting. For a while, I even struggled to just flood a line with color
(it's just `-[>+]+` btw), but I kept thinking, reasoning and experimenting until
I had a pretty decent grasp of some "primitive" shapes and operations.

One thing I absolutely still want to do, but haven't quite managed at time of
writing, is implementing a [Rule 110](https://en.wikipedia.org/wiki/Rule_110)
run. It is definitely possible, but I have real-life obligations upcoming, so I
decided to publish it without that particular demo.

Here are pictures of my current demo programs:


## Demos

![A pattern of red and blue dots](/assets/images/paintfuck/demo-0.png)
A simple dotted pattern, created by `<----[>+>>>>>>>>V++++]>-`

![A pattern of diagonal colored lines](/assets/images/paintfuck/demo-1.png)
Diagonal colored stripes, intentionally finite, created by

    VVVV>+++++++[<++++++++>-]<[>>>+++++++[<++++++++>-]^[V+^-]V+[[>]<[->+>+<<]>>[-<<+>>]<+[<]>-]<<-[-V+^]V]

![Three simple colored lines, vertical, horizontal, diagonal](/assets/images/paintfuck/demo-2.png)
Simple lines, mostly showing off how to "fill" a complete line.

    VVVV--[>+]++V>>>>^^^^-----[V+]---<<<<^-[>V+]+

![A patterned mess of colors](/assets/images/paintfuck/demo-3.png)
Hey, this kinda looks like the pattern on a bus seat from the 90's.

    +++[>>>+++++++[<++++++++>-]<[[>]<[->+>][<]>-]<<-[-V+^]V]

![A face with angry eyebrows](/assets/images/paintfuck/demo-4.png)
A simple face, disappointed by the lack of detail and care taken in the drawing of itself.

    VVVVVVVVVVVVVVVVVVVV>>>>>>>>>
    ++++++++++++[>+>+<<-]>>[[>]+[<]>-][<]>[-<+<+>>]<<[>>+<<-]>>[[V]+[^]V-]<[>V[V]<+>^[^]<-]>V[V]<[->+<]>[[>]+[<]>-]>[>]^+[^+<[>-]>]
    >>>>>>>>>>>>>>VVV
    ++++++++++++[>+>+<<-]>>[[>]+[<]>-][<]>[-<+<+>>]<<[>>+<<-]>>[[V]+[^]V-]<[>V[V]<+>^[^]<-]>V[V]<[->+<]>[[>]+[<]>-]>[>]^+[^+<[>-]>]
    >>>++++++++[-[^+V-]^]
    <<<++++++++++++[--<+<+>>[V<<+>>^-]<<V]
    <<<<<<<<<<<<<<<<<<<^^^^
    ++++++++++++[--<+<+>>[^<<+>>V-]<<^]
    <<<<<<+++++++[>++++++<-]>[-[V+^-]V]
    >>>++++++[<++++++++>-]<-[[>]+[<]>-]


![Three empty rectangle of different sizes](/assets/images/paintfuck/demo-5.png)
Three boxes, showing off the 'rounded rectangle' function

    ++++++[>+>+<<-]>>[[>]++++++[<]>-][<]>[-<+<+>>]<<[>>+<<-]>>[[V]++++++[^]V-]<[>V[V]<+>^[^]<-]>V[V]<[->+<]>[[>]++++++[<]>-]>[>]^+[^+<[>-]>]>>>VVV+++++++++[>+>+<<-]>>[[>]++[<]>-][<]>[-<+<+>>]<<[>>+<<-]>>[[V]++[^]V-]<[>V[V]<+>^[^]<-]>V[V]<[->+<]>[[>]++[<]>-]>[>]^+[^+<[>-]>]>>>>>VVVVV+++++++++++++[>+>+<<-]>>[[>]++++[<]>-][<]>[-<+<+>>]<<[>>+<<-]>>[[V]++++[^]V-]<[>V[V]<+>^[^]<-]>V[V]<[->+<]>[[>]++++[<]>-]>[>]^+[^+<[>-]>]

![Another mess of colors](/assets/images/paintfuck/demo-6.png)
Another colored mess, except I created this one with intent. If the timelapse
function were properly implemented, it would look really cool to watch this grow.

```>>>>>+<<<<<<-[-[>-<+>]+V>+V>+V<[>-<+>]+V<+V<+]```

![Thick rectangles of color](/assets/images/paintfuck/demo-7.png)
Technical issues, please hold. The code for this could definitely be shortened,
by seeding the color for each row first and then copying that into a full line.

    VVVV
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]+[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]++[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]+++[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]++++[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]+++++[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]++++++[<]>-]<<-[-V+^]V]
    ++++++++[>>>+++++++[<++++++++>-]<-[[>]+++++++[<]>-]<<-[-V+^]V]


## Potential Improvements

The current state of Paintfuck is definitely both usable and fun, but there are
some improvements that could be made:

### Customisation

The color mode, canvas size and limits such as the maximum number of operations
and maximum code length are all arbitrary and have been selected to work for me,
in my early stages of experimentation. Technically, there's no reason that I
shouldn't expose customisation for this.

### Animation and debugging tools

I experimented with letting the interpreter run slowly, and changing the state
of the canvas in an observable way. This resulted in some really cool animation,
but didn't immediately play nice with changing the code mid-animation. It would
be cool to offer an observable interpretation (with customisable delays and
stepping, akin to [Brainfuck Visualizer](https://ashupk.github.io/Brainfuck/brainfuck-visualizer-master/index.html#)).

Since this kind of falls into the category of "debugging/dev tools", why not
expose the entire GridTape state on demand?

Versions of CRF[] created after my fork also include the `?` command, which
outputs a lot of helpful information right on page. I might want to steal that.

Technically, there's no reason to not allow inline comments by ignoring all
non-instructional input.

### Image (and video?) export

While you can export the resulting image by just right-clicking the canvas, this
might not be obvious to everyone (I didn't realise it until I wanted to save
pictures for the demo section of this blog post).

Offering a simple 'Download'-button would be nice. And while we're at it, why
not combine it with the animation mentioned in the previous section and
directly offer a download of the animation of the image being drawn?


### Optimisation

In theory, repeated position shifts and additions can easily be 'grouped' for
a quick win in the performance department. This will probably not be necessary
until I offer custom resolution, where bigger canvases have ever-increasing
portions of their operations dedicated to just traversing the data pointer
across space.

## Closing Thoughts

Thanks for reading! If you've made it this far, I would love it if you went to
actually [try Paintfuck](/assets/pages/paintfuck.html) and maybe showed me some
of the cool things you made and/or gave me feedback on the whole thing.
