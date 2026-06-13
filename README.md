# Overview

This is a website I've made for my Duke of Edinburgh Award (Bronze) skills 
section which is website design. You can learn more about the Duke of 
Edinburgh's Award [here](duke-of-edinburgh-website).

**DISCLAIMER:** This is not finished yet; although, I would like to finish it as 
soon as possible so I can get this section signed off as quickly as possible.

[duke-of-edinburgh-website]: https://www.dofe.org/about/

# Implementation

This section is mainly intended to by read by my DofE Bronze Skills section 
assessor for the purposes of assessing my skills in website design, but can be 
read by other people if they want to.

## Current implementation

### General structure

This website is built using [Jekyll](jekyll), a static site generator. A static
site generator is a piece of software that takes in a form of a website's
source code which is easy to make edits to and for humans to understand. In
this form, there is (preferably) no duplicated code. It outputs a form of the
source code which can be understood by web browsers. But this form may include
duplicated code and may be harder to understand properly, so you wouldn't have
wanted to write and maintain that code by hand.

For the content of each page in the website, I use Liquid with HTML. Liquid is 
a templating language which allows me to dynamically generate content based on 
the data available from `_config.yml` and the YAML frontmatter in each page. 
For example, this allows me to dynamically generate breadcrumbs bars based on 
the url of the page and the title of the page and all of the page's parent 
directories. You don't dynamically generate content for the entire page though, 
only the parts that need it.

For my HTML styling, I use Sass, which is a CSS extension language and compiles
to normal CSS. (The built CSS is what is actually deployed.) It adds features 
not found in vanilla CSS like nesting, which makes styling (especially complex 
styling) more readable. More about this later.

#### Directory structure

Because this website is built using Jekyll, it follows the directory structure
that is required for Jekyll websites:
- **[_includes/](_includes-dir)** – Where resuable snippets of HTML go. For
  example, I have an include file which has the HTML code for the header, which
  I can reuse in all my layouts.
- **[_layouts/](_layouts-dir)** – Where reusable [layouts](jekyll-layouts) go.
  These define the structure of a page in the site.
- **[_sass/](_sass-dir)** – Where Sass and SCSS modules go
- **[assets/](assets-dir)**
  - **[css/](assets/css-dir)** – Where top-level main SCSS stylesheets go. 
    These top-level SCSS modules get compiled into CSS stylsheets in the same 
    directory. It is the CSS stylesheets which are what are actually referenced 
    in the layout for a page.
  - **[img/](assets/img-dir)** – Where images and videos supplementary to the
    site are stored.
- **[_config.yml](_config.yml-file)** – Configures Jekyll and is the source of
  site-wide variables and data
- **[Gemfile](gemfile-file)** – File which controls the version of Jekyll and
  the Jekyll plugins used and other Ruby-based dependencies

[jekyll-layouts]: https://jekyllrb.com/docs/layouts/

[_includes-dir]: https://github.com/polyagonal1/DofE-website/tree/main/_includes
[_layouts-dir]: https://github.com/polyagonal1/DofE-website/tree/main/_layouts
[_sass-dir]: https://github.com/polyagonal1/DofE-website/tree/main/_sass
[assets-dir]: https://github.com/polyagonal1/DofE-website/tree/main/assets
[assets/css-dir]: https://github.com/polyagonal1/DofE-website/tree/main/assets/css
[assets/img-dir]: https://github.com/polyagonal1/DofE-website/tree/main/assets/img
[_config.yml-file]: https://github.com/polyagonal1/DofE-website/blob/main/_config.yml
[gemfile-file]: https://github.com/polyagonal1/DofE-website/blob/main/Gemfile

As well as the Jekyll-specific files/directories, it also has files and folders
specific to itself:
- **[.github/](.github-dir)**
    - **[workflows/](.github/workflows-dir)** – Where GitHub Actions workflows
      are stored, including my custom workflow which I use to build and deploy to
      GitHub Pages.
- **[catalogue/](catalogue-dir)** – Directory for where the source code for the
  catalogue and all origami models in the catalogue are.
- **[scripts/](scripts-dir)** – Directory for where utility scripts used for
  local development of the website go
- **[doc_assets/](doc_assets-dir)** – Directory for where images and videos and 
  other assets supplementary to this README and other documentation separate 
  from the actual website go.
- **[package.json](package.json-file)** – Configuration file which controls the
  Node.js-based dependencies of the website, including the SCSS compiler

[.github-dir]: https://github.com/polyagonal1/DofE-website/tree/main/.github
[.github/workflows-dir]: https://github.com/polyagonal1/DofE-website/tree/main/.github/workflows
[catalogue-dir]: https://github.com/polyagonal1/DofE-website/tree/main/catalogue
[scripts-dir]: https://github.com/polyagonal1/DofE-website/tree/main/scripts
[doc_assets-dir]: https://github.com/polyagonal1/DofE-website/tree/main/doc_assets
[package.json-file]: https://github.com/polyagonal1/DofE-website/blob/main/package.json

### SCSS compilation

There are 3 releases of Sass:
- **Dart Sass** – The newest and fastest and most 'current' release of Sass 
  written in Node.js (JavaScript). It has all of the latest and greatest 
  features of Sass. This release of Sass is recommended for new projects.
- **LibSass** – An obsolete and unmaintained release of Sass written in C and
  requires a wrapper to actually compile anything.
- **Ruby Sass** – The original implementation of Sass written in Ruby. It is no
  longer supported and reached it's end of life in early 2019. It does not
  support a lot of the newest features supported in Dart Sass.

Jekyll already supports SCSS/Sass compilation out of the box, however, the
release of Sass Jekyll uses is Ruby Sass, which is not at all ideal. To
overcome this issue, I've disabled Jekyll's compilation of SCSS/Sass by making
Jekyll not look at any of the SCSS files. (What files Jekyll looks at and 
ignores is configured in the `config.yml` file.) Instead, the SCSS gets 
compiled before the Jekyll build step and Jekyll only looks at the built CSS, 
so to Jekyll, it looks like they were CSS files written by hand and therefore 
are included in the output.

The actual stylsheets that are compiled are all of the SCSS files in 
[`/assets/css/`](assets/css-dir). These get compiled into a CSS file with the 
same basename but with a `.css` extension instead of `.scss`. For example, an 
SCSS file at `/assets/css/foo.scss` will get compiled to `/assets/css/foo.css`.

[jekyll]: https://jekyllrb.com

### Local development

To build and serve my site locally, I use a shell script at 
[`/scripts/build_and_serve.sh`](build_and_serve-sh-file). This shell script has 
2 parts to it:
```sh
npx sass --watch --load-path=./_sass ./assets/css/:./assets/css/ &
```
and
```sh
bundle exec jekyll serve --force-polling --livereload
```

The first part runs Dart Sass, which will compile all the SCSS/Sass files in 
`/assets/css/` into CSS files in the same directory. It will then watch the 
files for changes to them, and when it sees a change in any of the files, it 
will recompile that stylesheet so the resulting CSS output matches. The 
`--load-path=./_sass` part of it is necessary because the SCSS stylsheets which 
are being compiled reference SCSS modules in the `/_sass/` directory and Dart 
Sass wouldn't know where to look for them otherwise. The `&` at the end of it 
means that what happens next will run in parallel with it (at the same time).

The next part – being run in parallel as Dart Sass – builds the rest of the 
website and serves it locally using Jekyll. By 'serves it locally', I mean that 
if you were to open a new tab on a browser while this shell script is running, 
and typed in 'localhost:4000', and pressed enter, it would look like an actual 
website. By default, this command will also watch for any changes to the 
website and rebuild it when necessary. The`--livereload` part of the command 
means that I don't have to reload the tab whenever I want to see the changes to 
the website on the browser; it just reloads automatically.

These 2 things running in parallel (because of the `&`) means that if I change 
some of the styling (one of the SCSS files) while this shell script is running, 
the first part (Dart Sass) will see this change and recompile it as necessary. 
The second part (Jekyll) won't see that any of the SCSS files have changed 
because I've configured Jekyll to ignore any SCSS files. But Jekyll will see 
the output of Dart Sass and notice that that file has changed so it still 
rebuilds the website and serves the new, changed, version and still makes the 
browser reload itself.

[build_and_serve-sh-file]: https://github.com/polyagonal1/DofE-website/blob/main/scripts/build_and_serve.sh

### Publishing

This website is published to GitHub Pages, which is a service provided by
GitHub which allows you to host a website from a GitHub repository.

The benefit of using GitHub Pages is that it's free: I don't need to
pay to keep my website published. It is also a lot more convenient hosting my
website with GitHub Pages because it just takes the code directly from my
repository – I don't need to pipe the code from this repository to any external
services when using GitHub Pages.

**GitHub Pages has 2 possible publishing sources (ways to use it):**
- **Deploy from a branch:** Use GitHub's pre-chosen version of Jekyll
  to build your site with pre-chosen plugins you're allowed to use and the
  build pipeline is also pre-chosen.
- **GitHub Actions:** Use a [GitHub Actions workflow](github-actions) to build
  and deploy your site.

Currently, I am using GitHub Actions to build and deploy using a custom 
workflow which can be found [here](custom-workflow). This workflow first 
installs the Ruby and Node.js toolchain. This is required because Jekyll is 
written in Ruby and can't run without it. Dart Sass is written in Node.js 
JavaScript so therefore requires the Node.js toolchain to run as well. After 
that, Jekyll and Dart Sass themselves are installed. Then, once everything we 
need is installed, Dart Sass compiles all the SCSS into browser-readable CSS; 
then Jekyll builds the rest of the site into the `_site/` directory. Then the 
contents of the `_site/` directory (the built site) is deployed (the changes 
are incorporated into the actual published website).

It would be impossible to do this using the 'Deploy from a branch' option 
because the build pipeline with that option is fixed: I can't customise it. It 
would instead just skip the first SCSS compilation step and try building the 
website using Jekyll without any styling and the output would look horrendous.

[github-actions]: https://github.com/features/actions
[custom-workflow]: https://github.com/polyagonal1/DofE-website/blob/main/.github/workflows/build-and-deploy.yml

## Implementation history

### At the start

When I first started making my website, I used the 'Deploy from a branch' 
option to build my website. But, I didn't use any of the features Jekyll had to 
offer. Instead, I just directly wrote the code that would be published. There 
was no config file or any Jekyll-specific folders, so Jekyll just copied the 
contents of the root directory into _site, which was what was actually 
published.

Before long, I had lots of duplicated code across all the pages in the website. 
This was partially because I wanted to have a header that would be on every 
page and would allow you to navigate to specific parts of the site. However, 
this required copying the header code from one file to all the others, which 
would have got very hard to maintain as I add more and more pages. When I did 
some research about this issue, I found the only way to overcome this and still 
use GitHub Pages was through using a static site generator.

### Switching to Jekyll

As well as realising that I was going to have to use a static site generator, I 
also found out that GitHub Pages automatically builds your site with Jekyll, a 
static site generator. (That's if you are using GitHub Pages by deploying from 
a branch, which I was.) The trade-off was that I couldn't control the version 
of Jekyll or any of the build process.

So I started switching my website to use Jekyll and put all the duplicated code 
in a [layout](jekyll-layouts), which can be reused across all the pages that 
use that layout.

[jekyll-layouts]: https://jekyllrb.com/docs/layouts/

### Switching from CSS to SCSS

By that time, I had also found out about what [SCSS and Sass](sass) is, and 
found it was basically a version of CSS which compiles to normal CSS and is 
easier to maintain.

So I decided to convert all of my CSS into SCSS.

[sass]: https://sass-lang.com

### Adding breadcrumbs

After that, I also decided I wanted to add breadcrumbs to the header, which is 
a little bar usually below the main navigation bar which tells you exactly 
where in the site you are. For example, say your site has this structure:
```
/
├─ about/
├─ blog/
│  ├─ 01-06-2026--mysecondpost.html
│  └─ 20-05-2026--myfirstpost.html
└─ 404.html
```
If you were in the post `/blog/01-06-2026--mysecondpost.html`, the breadcrumbs 
would say:
```
Home → Blog → My second post
```

Implementing this was pretty difficult, partially because of the Liquid 
implementation used by Jekyll, and partially because at the time, I didn't have 
very much knowledge of the best way to use Liquid and what you could and 
couldn't do with Liquid.

My original approach was to split up the page URL into its segments by 
splitting by the occurance of a `/` in the URL. Unfortunately for me, if the 
URL had a trailing slash, it would split into an emty string, and then whatever 
was in front of the slash. There was no way I could prevent this which meant I 
had to filter out all of the empty strings out of it before actually processing 
it. At the time, I couldn't figure out a way to do this that wasn't super 
verbose and unnecessarily long and complicated. This is the actual code I used:
```liquid
{% assign url_segments_unfiltered = page.url | split: "/" | compact %}

{% assign clean_url = "" %}

{% for s in url_segments_unfiltered %}
    {% unless s == "" %}
        {% if clean_url == "" %}
            {% assign clean_url = s %}
        {% else %}
            {% assign clean_url = clean_url | append: "/" | append: s %}
        {% endif %}
    {% endunless %}
{% endfor %}

<!-- clean_url = {{ clean_url | inspect }} -->

{% assign url_segments = clean_url | split: "/" %}
```
As you can see, that is very long. Later I would need to do the same thing in 
somewhere else which would have resulted in a lot of duplicated, complex code 
if I had not thought of a better solution.

#### Better solution

This solution does the same thing but is just one line of code:
```liquid
{% assign url_segments = page.url | replace: "/", " " | strip | split: " " %}
```

This method instead replaces all slashes in the string with spaces 
(`page.url | replace: "/", " "`), then strips any whitespaces (includes spaces) 
from the start and end of the string (`| strip`). This means any slashes at the 
start and end of the page's URL (without the 'https://polyagonal1.github.io' 
bit) essentially get removed. After that, the resulting string is split into 
segments but using a space as a separator, and because any spaces at the start 
and end of the string are removed, no empty strings can be made in the 
resulting array [^1] and it's just left with the actual segments.

[^1]: Except if there is a slash and then another slash in the string but 
    because it is a URL, that should never happen.

### Separating styling into multiple output stylesheets

Before we talk about what this section is about, I would like to clarify the 
difference between 'main stylesheet' and 'output stylesheet' in this context. 
'Main stylesheet' means an SCSS stylesheet which is compiled by Dart Sass to 
become the output stylesheet. The output stylesheet is the compiled CSS; the 
main stylesheet is the SCSS stylesheet that is compiled to the output 
stylesheet.

At this point, my only output stylesheet (`/assets/css/main.css`) was becoming 
pretty big and had a lot of styling that is only used in one page, but is still 
being given to all the other pages. Because of this, I decided to take the part 
of the main stylesheet which is only used in the catalogue and put it in a new 
main stylsheet: `/assets/css/catalogue.scss`. Because I am now compiling 2 
main stylesheets, I need to compile both of them instead of just the original. 
To allow for more main stylesheets being added, I changed my 
`build_and_deploy.sh` script to compile all SCSS files in `/assets/css` to 
output stylesheets in the same directory but with the `.css` file extension 
instead of `.scss`. Before, it was only compiling the `main.scss`.

### Adding a favicon

A favicon is the icon that appears on the tab of the page you are currently 
looking at in a web browser. Here is an example:

![Favicon example](https://raw.githubusercontent.com/polyagonal1/DofE-website/refs/heads/main/doc_assets/favicon-example.png)

I wanted to add one to make my website seem more professional. After doing 
some research, found that the best way to do it to make it work with all 
browsers is to have an ICO favicon at `/favicon.ico` and then link to it from 
every page because ICO favicons are supported on basically every browser in 
existence. The issue with ICO favicons is that they are hard to edit easily 
because they store the data as a bitmap, not as contours and vectors.

SVG on the other hand does store it's data as contours and vectors so if I want 
to make any changes, it is nice and easy. Another benefit of SVG is that it 
works optimally on whatever resolution it is being displayed at. Have you ever 
had it when you zoom in too much on a PNG or JPG or ICO file (they all store 
their data as a bitmap) and it all becomes blurry? Well that never happens in 
SVG image [^2] because whatever you are using to display the SVG can easily 
upscale the resolution to be high enough, because the data is stored as vectors.

So, I decided to design a logo in SVG for my site in general and then use that 
as a favicon for [browsers that support SVG favicons](svg-favicon-support), and 
have an ICO favicon for browsers that don't support SVG favicons.

Here is the logo/favicon design I made:

![Logo/favicon design](https://raw.githubusercontent.com/polyagonal1/DofE-website/refs/heads/main/favicon.svg)

[svg-favicon-support]: https://caniuse.com/link-icon-svg

[^2]: Well, technically, if you have a bitmap image embedded into the SVG, it 
    can still cause the SVG to become blurry if you zoom in too much in to the 
    part of the SVG which is the bitmap image