# readme2tex
Renders LaTeX for Github Readmes

<p align="center"><img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/d27ecd9d6334c7a020001926c8000801.svg" align=middle width=174.20741999999998pt height=33.73722pt/></p>

<sub>*Make sure that pdflatex is installed.*</sub>

----------------------------------------

`readme2tex` is a Python script that "texifies" your readme. It takes in Github Markdown and
replaces anything enclosed between dollar signs with rendered <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/c068b57af6b6fa949824f73dcb828783.svg" align=middle width=45.88146pt height=24.443640000000013pt/>.

In addition, unlike the other Github TeX renderers, `readme2tex` ensures that inline mathematical expressions
are properly aligned with the rest of the text to avoid giving a "jumpy" look to the document.

### Examples:

Here's a display level equation
<p align="center"><img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/32737e0a8d5a4cf32ba3ab1b74902ab7.svg" align=middle width=139.51836pt height=42.87816pt/></p>

The code that was used to render this equation is just

    $$
    \frac{n!}{k!(n-k)!} = {n \choose k}
    $$

<sub>*Note: you can escape \$ so that they don't render.*</sub>

Here's an inline equation. 

> It is well known that if <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/15b9e78f3a7cb11ea59b95c9553fb928.svg" align=middle width=129.83094pt height=29.134080000000015pt/>, then <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/2b1f70f6a49aea806b0a5f021e843447.svg" align=middle width=122.57262pt height=36.22391999999999pt/>.

The code that was used to render this is:

    It is well know that if $ax^2 + bx + c = 0$, then $x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$.

Notice that the equations line up with the baseline of the text, even when the height of these two images are different.

Sometimes, you might run into equations that are bottom-heavy, like <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/4cb4ead947a07837121937c807973436.svg" align=middle width=51.96996pt height=40.39883999999998pt/>. Here, `readme2tex`
can compute the correct offset to align this equation to the baseline of your paragraph of text as well.

### Installation

Currently, you just need to have `readme2tex` on your python-path, and you can call

### Usage

    python -m readme2tex --output README.md

It will then look for a file called `readother.md` and compile it down to a readable Github-ready
document.

In addition, you can specify other arguments to `render.py`, such as:

* `--readme READOTHER.md` The raw readme to process. Defaults to `READOTHER.md`.
* `--output README.md` The processed readme.md file. Defaults to `README_GH.md`.
* `--packages ...` A list of addition packages to use during <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/c068b57af6b6fa949824f73dcb828783.svg" align=middle width=45.88146pt height=24.443640000000013pt/> compilation. This is optional.
* `--svgdir svgs/` The directory to store the output svgs. The default is `svgs/`
* `--branch master` *Experimental* Which branch to store the svgs into, the default is just master.
* `--username username` Your github username. This is optional, and `render.py` will try to infer this for you.
* `--project project` The current github project. This is also optional.
* `--nocdn True` Ticking this will use relative paths for the output images. Defaults to False.
* `--htmlize False` Ticking this will output a `X.md.html` file so you can preview what the output looks like. Defaults to False.
* `--valign False` Ticking this will use the `valign` trick (detailed below) instead. See the caveats section for tradeoffs.

### Technical Tricks

#### How can you tell where the baseline of an image is?

By prepending every inline equation with an anchor. During post-processing, we can isolate the anchor, which
is fixed at the baseline, and crop it out. It's super clowny, but it does the job.

#### Caveats

Github does not allow you to pass in custom style attributes to your images. While this is useful for security purposes,
it makes it incredibly difficult to ensure that images will align correctly to the text. `readme2tex` circumvents this
using one of two tricks:

1. In Chrome, the attribute `valign=offset` works for `img` tags as well. This allows us to shift the image directly.
Unfortunately, this is not supported within any of the other major browsers, therefore this mode is not enabled by
default.
2. In every (reasonably modern) browser, the `align=middle` attribute will vertically center an image. However, the
definition of the vertical "center" is different. In particular, for Chrome, Firefox, (and probably Safari), that center
is the exact middle of the image. For IE and Edge however, the center is about 5 pixels (the height of a lower-case character)
above the exact center. Since this looks great for non-IE browsers, and reasonably good on Edge, this is the default
rendering method. The trick here is to pad either the top or the bottom of the image with extra spaces until the
baseline of the equation is at the center. For most equations, this works great. However, if you have a tall equation,
like <img src="https://rawgit.com/leegao/readme2tex/svgs/svgs/bdd0f9b91b7fff7fe5a2b1b7684a96ef.svg" align=middle width=61.272540000000006pt height=78.20711999999999pt/>, you'll notice that there might be a lot
of slack vertical spacing between these lines. If this is a deal-breaker for you, you can always try the `--valign True`
mode. For most inline equations, this is usually a non-issue.
