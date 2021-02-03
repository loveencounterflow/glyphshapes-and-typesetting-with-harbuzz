


# Glyphshapes and Typesetting with HarfBuzz (and NodeJS &c)


<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [`FTGL`](#ftgl)
- [FontKit (NodeJS, Browser)](#fontkit-nodejs-browser)
- [PyCairo](#pycairo)
- [Install HarfBuzz](#install-harfbuzz)
  - [APT Packages (Would Not Recommend)](#apt-packages-would-not-recommend)
- [Install HarfBuzz with Homebrew](#install-harfbuzz-with-homebrew)
- [Command Lines](#command-lines)
    - [Size](#size)
  - [Short Descriptions](#short-descriptions)
- [HarfBuzzJS](#harfbuzzjs)
- [OpenType Glyf Names and SVG Element IDs](#opentype-glyf-names-and-svg-element-ids)
- [Links](#links)
- [Tests and Benchmarks](#tests-and-benchmarks)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## `FTGL`

```sh
libftgl-dev
libftgl2
```

???

## FontKit (NodeJS, Browser)

* [`fontkit`](https://github.com/foliojs/fontkit)

* [`fontkit-next`](https://github.com/Hopding/fontkit)—used in
  [`pdf-lib`](https://github.com/Hopding/pdf-lib) (see also [here](https://pdf-lib.js.org/#examples))
  **NOTE** `pdf-lib` can draw SVG paths to PDF, so...

* [`fonteditor-core`](https://github.com/kekee000/fonteditor-core)
  * sfnt parse
  * read, write, transform fonts
    * ttf (read and write)
    * woff (read and write)
    * woff2 (read and write)
    * eot (read and write)
    * svg (read and write)
    * otf (only read)
  * ttf glyph adjust
  * svg to glyph
  * also see [`fonteditor` (在线字体编辑器)](https://github.com/ecomfe/fonteditor)


## PyCairo

https://pycairo.readthedocs.io/en/latest/reference/glyph.html



## Install HarfBuzz

### APT Packages (Would Not Recommend)

While there are fairly recent packages for HarfBuzz in APT, on my Linux Mint 19.3 machine (which is
arguably a bit outdated as of January, 2021), all I get with `sudo apt install libharfbuzz-bin` is version
1.7.2 which is from December, 2017. Not only is this 3 years old software, it also has had a *lot* of
updates in the meantime.

Sadly, this state of affairs is par of the course for the APT/DPKG (or whatcha may call it) ecosystem.
Fortunately, there other options, for which see below.

## Install HarfBuzz with Homebrew

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo 'eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)' >> /home/flow/.zshenv
eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
brew install gcc
brew install harfbuzz
```

This gives you the following executables; the version is 2.7.4 as of January, 2021:

```sh
/home/linuxbrew/.linuxbrew/bin/hb-ot-shape-closure
/home/linuxbrew/.linuxbrew/bin/hb-shape
/home/linuxbrew/.linuxbrew/bin/hb-subset
/home/linuxbrew/.linuxbrew/bin/hb-view
```

Observe there's even a new executable here, `hb-subset`, which isn't even included if you were to `sudo apt
install libharfbuzz-bin`.

## Command Lines

```sh
dpkg --listfiles libharfbuzz-bin
less /usr/share/doc/libharfbuzz-bin
man hb-shape
hb-shape --help
hb-view --help
hb-view DejaVuSansCondensed.ttf 'helo world'
hb-view EBGaramond12-Regular.otf 'helo world'
hb-view EBGaramond12-Regular.otf 'agffix'
hb-shape EBGaramond12-Regular.otf 'agffix'
hb-shape --help-output
hb-shape --output-format=text/json EBGaramond12-Regular.otf 'agffix'
hb-shape --output-format=text/json EBGaramond12-Regular.otf 'agffix' | less
hb-shape --help-output-syntax
hb-shape --output-format=text/json --no-glyph-names EBGaramond12-Regular.otf 'agffix' | less
hb-shape --output-format=text/json --no-glyph-names --verbose EBGaramond12-Regular.otf 'agffix' | less
hb-shape --output-format=text/json --no-glyph-names --show-extents --verbose EBGaramond12-Regular.otf 'agffix' | less
hb-shape --output-format=json --no-glyph-names --show-extents --verbose EBGaramond12-Regular.otf 'agffix' | less
```



```sh
dpkg --listfiles libharfbuzz-bin
/usr/bin/hb-ot-shape-closure
/usr/bin/hb-shape
/usr/bin/hb-view
```

#### Size

```sh
hb-view --annotate --output-file=rendered.svg --font-size=1000 EBGaramond12-Italic.otf 'abcabc'
```

Results in an SVG with glyphs whose design size is 1000 and whose coordinates are (apparently) all integers,
which makes this a good choice for readability and post-processing.



### Short Descriptions


* `hb-ot-shape-closure`—gives the set of characters contained in a string, represented as single characters
  and/or single character names. Example: hb-ot-shape-closure /usr/share/fonts/dejavu/DejaVuSans.ttf "Hello
  World.".

* `hb-shape`—is used for the conversion of text strings into positioned glyphs.

* `hb-subset`—is used to create subsets of fonts, and display text using them.

* `hb-view`—displays a graphical view of a string shape using a particular font as a set of glyphs. The
  output format is automatically defined by the file extension, the supported ones being
  ansi/png/svg/pdf/ps/eps. For example: hb-view --output-file=hello.png
  /usr/share/fonts/dejavu/DejaVuSans.ttf "Hello World.".

See [*Usage Notes for `hb-shape`, `hb-view`, `hb-subset`, and
`hb-ot-shape-closure`*](hb-command-line-reference.md) for details on command line arguments.

## HarfBuzzJS

Make sure you have the most recent `wasm-ld`; clone https://github.com/harfbuzz/harfbuzzjs` and build:

```sh
brew install llvm
git clone https://github.com/harfbuzz/harfbuzzjs
cd harfbuzzjs
./build.sh
```

The above will produce `hb.wasm`, the HarfBuzz library as WebAssembly.


## OpenType Glyf Names and SVG Element IDs

This section tries to answer the question whether any kind of processing / escaping /substitution should be
performed on OpenType Glyf Names in order to make them usable as SVG Element IDs (needed to reference glyph
outlines as SVG symbols). Previously it had been planned to use (numerical) Glyf IDs (GIDs) as these are
syntactically more predictable than Glyf Names; the current thinking, however, is to prefer Glyf Names since
these are deemed to be more informative, more legible, and somewhat better standardized across fonts than
Glyph IDs (which are always specific to a given font file and meaningless outside that context).

* Syntactically speaking, OpenType Glyf Names are rather restricted as per [the
  spec](https://adobe-type-tools.github.io/afdko/OpenTypeFeatureFileSpecification.html#2.f.i):

  > A glyph name may be up to 63 characters in length, must be entirely comprised of characters from the
  > following set:
  >
  >
  >     ABCDEFGHIJKLMNOPQRSTUVWXYZ
  >     abcdefghijklmnopqrstuvwxyz
  >     0123456789
  >     .  # period
  >     _  # underscore
  >
  > and must not start with a digit or period. The only exception is the special character “.notdef”.
  >
  > “twocents”, “a1”, and “\_” are valid glyph names. “2cents” and “.twocents” are not.

* Likewise, per the [spec](https://svgwg.org/svg2-draft/struct.html#IDAttribute), allowable values for SVG Element IDs must be
  valid [XML 1.0 Name Tokens](https://www.w3.org/TR/xml/#NT-Name), which are given as:

  >     [4]     NameStartChar ::= ":" | [A-Z] | "_" | [a-z] | [#xC0-#xD6] | [#xD8-#xF6] | [#xF8-#x2FF] | [#x370-#x37D] | [#x37F-#x1FFF] | [#x200C-#x200D] | [#x2070-#x218F] | [#x2C00-#x2FEF] | [#x3001-#xD7FF] | [#xF900-#xFDCF] | [#xFDF0-#xFFFD] | [#x10000-#xEFFFF]
  >     [4a]    NameChar      ::= NameStartChar | "-" | "." | [0-9] | #xB7 | [#x0300-#x036F] | [#x203F-#x2040]
  >     [5]     Name          ::= NameStartChar (NameChar)*

* Observe that in HTML5 (but not earlier versions), [most restrictions on ID values have been
  removed](https://html.spec.whatwg.org/multipage/dom.html#the-id-attribute); to quote: "There are no other
  restrictions on what form an ID can take; in particular, IDs can consist of just digits, start with a
  digit, start with an underscore, consist of just punctuation, etc."

* The [W3C Validator](https://validator.w3.org/) does indeed complain if an SVG document contains IDs such
  as `---` or `:---:`; however, Chromium and the Linux Mint Image Viewer seems to be unfazed and both
  display the SVG just fine. *Do not use whitespace in IDs though, as these have been observed to cause
  errors in applications.*

**Conclusion** OpenType Glyf Names are largely a subset of allowable XML IDs, with the sole exception of
`.notdef`. But since this value nor any of the more questionable ones like `---` and `:---:` caused display
errors in viewers or browsers tested, we will assume that **OpenType Glyf Names may be used as-is in SVG ID
attributes; no escaping need be done, and, assuming that reasonable fonts stick to the OT spec, no
validation will be needed either**.


## Links


* https://lfs-hk.koddos.net/blfs/view/systemd/general/harfbuzz.html has info about
  `hb-ot-shape-closure`, `hb-shape`, `hb-subset`, and `hb-view`
* [*HarfBuzz OT+AAT "Unishaper"* by Behdad Esfahbod, Aug. 23,
  2020](https://prezi.com/view/THNPJGFVDUCWoM20syev/)
* [Homepage of Behdad Esfahbod, author of HarfBuzz](http://behdad.org/)
* [Blog of Behdad Esfahbod](https://medium.com/@behdadesfahbod)
* [*HarfBuzz* on Wikipedia](https://en.wikipedia.org/wiki/HarfBuzz)
* [*HarfBuzz Manual*](https://harfbuzz.github.io/)
* [`uharfbuzz` for Python/Cython](https://github.com/harfbuzz/uharfbuzz)
* [*The journey of a word: how text ends up on a page* by Simon Cozens @ linux conf au
  2017](https://www.youtube.com/watch?v=Is4PW6f4Pk4)
* Language bindings:
  * https://github.com/rougier/freetype-py
  * https://github.com/RazrFalcon/rustybuzz, A complete harfbuzz's shaping algorithm port to Rust
  * https://github.com/foliojs/fontkit, An advanced font engine for Node and the browser

## Tests and Benchmarks

* relevant business logic code implemented in this repo, tests and benchmarks in
  [𐌷𐌴𐌽𐌲𐌹𐍃𐍄](https://github.com/loveencounterflow/hengist/tree/master/dev/glyphshapes-and-typesetting-with-harfbuzz)
* tested libraries:
  * [HarfBuzz](https://github.com/harfbuzz/harfbuzz) is considered the Gold Standard for correctness and the
    Base Line for performance
  * [HarfBuzzJS](https://github.com/harfbuzz/harfbuzzjs)
    * apparently there are two variants, `harfbuzzjs/examples/nohbjs.html` and
      `harfbuzzjs/examples/hbjs.example.html`
    * see https://github.com/harfbuzz/harfbuzzjs/issues/10
  * [opentype.js](https://github.com/opentypejs/opentype.js)
  * [Fontkit](https://github.com/foliojs/fontkit)
  * (future): generate interface to HarfBuzz using [`ffi-napi`](https://github.com/node-ffi-napi/node-ffi-napi)
    and call into C libraries from JS.—Also see
    * https://www.sysleaf.com/nodejs-ffi/
    * https://medium.com/@koistya/how-to-call-c-c-code-from-node-js-86a773033892
    * https://medium.com/jspoint/a-simple-guide-to-load-c-c-code-into-node-js-javascript-applications-3fcccf54fd32
* test for
  * glyf selection correctness
  * OTF features
  * performance
  * outline matching



