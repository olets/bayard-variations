# bayard-variations

These [abcm2ps](http://moinejf.free.fr/) "decos" add support for above the staff variation-marking notation to [abc](http://abcnotation.com/).

[Click here to download a zip of these files.](https://github.com/olets/bayard-variations/archive/master.zip)

###Contents
  
- Introduction
- [Usage](#usage)
- [Variation decoration types](#variation-decoration-types)
- [Customization](#customization)
- [Included decorations](#included-decorations)
- [Design your own](#design-your-own)
- [Architecture](#architecture)
- [If something goes wrong](#if-something-goes-wrong)
- [License](#license)

--

&nbsp;

For years in my fiddle transcriptions I marked variations with a superscripted letter (in abc, this was `"^a..."`). Finally I came across that exact idea in print in Jeff Todd Titon's [*Old Time Kentucky Fiddle Tunes*](http://www.kentuckypress.com/live/title_detail.php?titleid=1074#.V-Mch5MrKHo). Titon says he "and other scholars" trace the idea back to [Samuel Bayard](https://en.wikipedia.org/wiki/Samuel_Preston_Bayard), in his 1944 *Hill Country Tunes: Instrumental Folk Music of Southwestern Pennsylvania*. Bayard's notation was actually nicer, as it showed exactly which notes were varied (my crop):

<img src="images/bayard.jpg" height="150px"/>

After I'd been working on PostScript decorations to emulate Bayard for a while, Beáta Salamon's [*Magyar népzenei dallamgyűjtemény*](http://www.hagyomanyokhaza.hu/page/716/) came out… and what did I find in it but beautiful Bayard-type variation notation!

These **bayard-variations** are my own expansion on Bayard's and Salamon's designs.


## Usage

Download at least `bayard-variations.fmt`. Run abcm2ps with

	abcm2ps INPUTFILE.abc -F bayard-variations.fmt

(See the abcm2ps readme for additional options.)
	
Use `hbv-deco-chords.fmt` to support writing guitar chords (e.g. `"A"`) with stacked variation decorations (see [below](#var-x_stack)). Simply download the file in addition to `bayard-variations.fmt`, and uncomment (remove the line-initial `%` from) the first line of `bayard-variations.fmt`.

## Variation decoration types

1. ###var-x
 
	```
	!var-x(! y0 AAA BBB !var-x)!
	```
 prints `x-------,` over the notes AAA BBB
 
 <kbd><img src="images/var-x.png" height="90px"/></kbd>
 
1. ###var-xy
 
	```
	!var-xy(! y0 AAA BBB !var-xy)!
	```
 prints `x, y----,` over the notes and so on for `var-xyz`, `var-xyz1`, and `var-xyz12`
 
 <kbd><img src="images/var-xy.png" height="90px"/></kbd>
 
1. ###var
	```
	!var(! y0 AAA BBB !var)!
	```
 prints `,-------,` over the notes
 
 <kbd><img src="images/var.png" height="90px"/></kbd>
 
1. ###varcontinues-x
	```
	!varcontinues-x(! y0 AAA BBB !varcontinues-x)!
	```
 prints `x---------` over the notes (e.g. for use when a variation extends beyond the end of a staff). *varcontinues-x is intended to pair with varcontinued*
 
 <kbd><img src="images/varcontinues-x.png" height="90px"/></kbd>
 
1. ###varcontinues
	```
	!varcontinues(! y0 AAA BBB !varcontinues)!
	```
 prints `,---------` over the notes (e.g. for use when a variation extends beyond the end of a staff). *varcontinues is intended to pair with varcontinued*
 
 <kbd><img src="images/varcontinues.png" height="90px"/></kbd>
 
1. ###varcontinued
	```
	!varcontinued(! y0 AAA BBB !varcontinued)!
	```
 prints `--------,` over the notes (e.g. for use when a variation started before the current staff. *varcontinued is intended to pair with either varcontinues-x or varcontinues*
 
 <kbd><img src="images/varcontinued.png" height="90px"/></kbd>

 --
 
 **Those decorations don't stack nicely with each other or with repeats** (e.g. `[1 ... ]`)**. If you need to mark overlapping variations, or need to use these decorations within a repeat, use the `_stack` variations:**

1. ###var-x\_stack

	```
	!var-x_stack! !var-y_stack! y0 AAA !var-y_stack)! BBB !var-x_stack)!
	```
  prints `y----,` over `AAA` and, above that, `x---------,` over `AAA BBB`
 
 <kbd><img src="images/var-x_stack.png" height="90px"/></kbd>
  
1. ###varcont\_stack
	```
	!varcont_stack(! !var-x_stack! AAA !var-x_stack)! BBB !varcont_stack)!
	```
 prints `x----,` over `AAA` and, over that, prints `-----------,` over `AAA BBB`
 
 <kbd><img src="images/varcont_stack-and-chord.png" height="100px"/></kbd>



Notes:
<ol type="A">

<li> **guitar chords** (in `""` quotations) **don't look good with stacked variation
markers. Use the included `hbv-deco-chords.fmt` to get around this** - include that `.fmt` and replace any

	!var..._stack(! y0 "X" ... !var..._stack)!
	
with
 
	!var..._stack(! y0 !X! y0 ... !var..._stack)!
	
The first line of `bayard-variations.fmt` imports `hbv-deco-chords.fmt`, commented out. The easiest way to add `!chord!` support, if you don't already have a base `.fmt` file to import both files into, is to download both `.fmt`s, put them in the same folder, and uncomment that first line of `bayard-variations.fmt`.
	
</li>

<li>I recommend always putting a `y0` after the opening deco of a long decoration,
e.g. after `!var-x(!`. It's sometimes a good idea to put one after the closing
deco `!var-x)!` and, in the case of variation markers stacked above guitar chords, after the `!X!` stackable guitar deco.
</li>
</ol>

## Customization

I use Monaco 9 for the label and a thin 60%-black dashed line

-	The font is defined before every occurrence of the postscript command `show`
-	The thickness of the line is defined before every `SLW` (abcm2ps shorthand for
	the postscript command `setlinewidth`) (default: 0.5)
-	The darkness of the line is defined before every non-zero `setgray` (default: 0.6)
-	The dash is defined before every non-`[]` `setdash` (default: [3 5], which is 3px
	of line followed by 5px of space)

The line could be made e.g. solid and black by commenting out all `setdash` and `setgray`.



## Included decorations

I've included a lot of decorations, which should cover most needs:

- single-letter decorations `var-a` to `var-z`
- single-number decorations `var-0` to `var-9`
- an asterisk decoration `var-*`
- two-letter decorations `var-ab` to `var-yz` for sequential pairs (e.g. `var-bc`, not `var-ac`)
- assorted other two-letter decorations I've needed
- three-letter decorations `var-abc` to `var-klm` (again, in sequential triplets)
- assorted other three-letter decorations I've needed
- the four-letter decoration `var-abcd`
- the five-letter decoration `var-abcde`
- single-letter open-ended decorations `varcontinues-a` to `varcontinues-f`
- the unnamed decoration `var`
- the open-ended unnamed decoration `varcontinues`
- the open-start decoration `varcontinued`
- single-letter stackable decorations `var-a_stack` to `var-z_stack`
- single-number stackable decorations `var-1_stack` to `var-3_stack`
- stackable unnamed open-ended decoration `varcons_stack`
- stackable open-start decoration `varcont_stack`

If that doesn't meet your needs, you can always

## Design your own

You'll need to add three lines per decoration, within `beginps...endps`:

	/var-[name]{/varname ([marker text] def [1var/[2-5]vars]}!
		deco var-[name]( [5 for normal, 7 for stacking] - 17 0 0
		deco var-[name]) ["] var-[name] 17 0 0


One-Character Example: `var-a`

	/var-a{/varname (a) def 1var}!
		deco var-a( 5 - 17 0 0
		deco var-a) 5 var-a 17 0 0

Two-Character Example: `var-ab`

	/var-a{/varname (a, b) def 2vars}!
		deco var-ab( 5 - 17 0 0
		deco var-ab) 5 var-ab 17 0 0

Three-Character Example: `var-abc`

	/var-abc{/varname (a, b, c) def 3vars}!
		deco var-abc( 5 - 17 0 0
		deco var-abc) 5 var-abc 17 0 0

Four-Character Example: `var-abcd`

	/var-abcd{/varname(a, b, c, d) def 4vars}!
		deco var-abcd( 5 - 17 0 0
		deco var-abcd) 5 var-abc 17 0 0

Five-Character Example: `var-abcde`

	/var-abcde{/varname (a, b, c, d, e) def 5vars}!
		deco var-abcde( 5 - 17 0 0
		deco var-abcde) 5 var-abcde 17 0 0

Custom Example: `var-custom`

	/var-custom{/varname (a. bc!!) def 3vars}!
		deco var-custom( 5 - 17 0 0
		deco var-custom) 5 var-custom 17 0 0

Custom Stackable Example: `var-custom2`

	/var-custom_stack{/varname (a. bc!!) def 3vars}!
		deco var-custom_stack( 7 - 17 0 0
		deco var-custom_stack) 7 var-custom_stack 17 0 0

Note: **for custom variables, by default your marker text can be one, four
seven, ten, or thirteen characters** long (using 1var, 2vars, 3vars, 4vars, and
5vars, respectively). For marker text of other lengths you'll need to customize the
variable `<adjust>`, which determines the horizontal placement of the marker line's start.


## Architecture

Here's the logic

1. Named, bracketing at end  
&emsp;deco `var-[x[y[z[1[2]]]]][_stack](`  
&emsp;and  
&emsp;deco `var-[x[y[z[1[2]]]]][_stack])`  
&emsp;&emsp;depend on  
&emsp;&emsp;`/var-x`  
&emsp;&emsp;&emsp;depends on  
&emsp;&emsp;&emsp;`/[#]vars`  
&emsp;&emsp;&emsp;&emsp;depends on  
&emsp;&emsp;&emsp;&emsp;`/variation`

1. Unnamed, bracketing at start and end  
&emsp;deco `var(`  
&emsp;&emsp;depends on  
&emsp;&emsp;`\unnamedvar`

1. Open, named, extending a little further right than the default  
&emsp;deco `varcontinues-[x[y[z[1[2]]]]](`  
&emsp;and  
&emsp;deco `varcontinues-[x[y[z[1[2]]]]])`  
&emsp;&emsp;depend on  
&emsp;&emsp;`/varconts-a`  
&emsp;&emsp;&emsp;depends on  
&emsp;&emsp;&emsp;`/1contvar`

1. Open, unnamed, extending a little further right than the default  
&emsp;deco `varcontinues(`  
&emsp;and  
&emsp;deco `varcontinues)`  
&emsp;&emsp;depend on  
&emsp;&emsp;`/varcontsunnamed`

1. Open at left, extending a little further left than the default  
&emsp;deco `varcontinued(`  
&emsp;and  
&emsp;deco `varcontinued)`  
&emsp;and  
&emsp;deco `varcont_stack(`  
&emsp;and  
&emsp;deco `varcont_stack)`  
&emsp;&emsp;depend on  
&emsp;&emsp;`/varcontd`

## If something goes wrong

**bayard-variations** not working for you? Check to see if it's a [known issue](https://github.com/olets/bayard-variations/issues). If it isn't, add it!

I'll mention one bug in particular: sometimes the decos may draw below the staff instead of above it. Just stick a `w:` field at the end of the tune. I do this in the bayard-variations demo, and it's documented in [issue #1](https://github.com/olets/bayard-variations/issues/1).


&nbsp;


## License

**bayard-variations** is licensed under the MIT License, with the following additional request: if you make use of it in any publication, commercial or otherwise, let the author know: oletswrite@gmail.com. For commercial projects, please credit the author as well.
