---
title: How to Add Content to This Website
layout: tutorial
toc: true
exclude: true
permalink: /tutorials/add_to_website
usemathjax: true
---

## Get the Source Code

Before adding any new content to this website, you need to get the source code. 
Clone the `gh-pages` from `PX4-Autopilot` using

```
git clone --branch gh-pages https://github.com/ASU-RISE-Lab/PX4-Autopilot.git
```

It is highly recommended that you clone this branch separately from your actual PX4 firmware to keep shenanigans
with submodules and irrelevant folders from messing with the repository.

## Required Software

### Ruby

Follow the instructions [here](https://www.ruby-lang.org/en/documentation/installation/).

### Bundler

Follow the instructions [here](https://bundler.io)

The command is likely `sudo gem install bundler`. Don't worry about specifying dependencies yet.

## Adding a New Page/Tutorial

### New Folder and Markdown File

Below is a subset of the directory tree:

```
└── PX4-Autopilot
    ├── assets
    └── docs
        ├── add_to_website
        │   └── add_to_website.md
        └── index_tutorials.md
```

If you are adding a new tutorial about something, create a new folder under `docs`
and then the name of your new web page inside that. For this tutorial, the source file
is `/PX4-Autopilot/docs/add_to_website/add_to_website.md`.

For this tutorial, I will create an [example page](../example_page/example_page.md). 
The new folder is `PX4-Autopilor/docs/example_page`, and the new file is `example_page.md`.
The directory tree will now include this:
```
.
└── PX4-Autopilot
    ├── assets
    └── docs
        ├── add_to_website
        │   └── add_to_website.md
        ├── example_page
        │   └── example_page.md
        └── index_tutorials.md
```

### Front Matter

The front matter of your new page (the stuff that goes on top of the new file) should be as follows: 
```
---
title: <TITLE OF WEBPAGE/TUTORIAL>
layout: tutorial
toc: true
exclude: true
permalink: /tutorials/<FOLDER NAME>
usemathjax: <true/false>
---
```

- The `---` above and below the text is essential.
- `title` - is the title.
- `layout` - determines where things are placed. Alternatives are `page`, `post`, `home`, `default`.
- `toc` - controls whether there is a table of contents or not. It doesn't seem to be doing anything right now.
- `exclude` - SET TO TRUE. This excludes the page from having a link in the header bar of every page.
- `permalink` - modifies the link that goes in the URL bar of the browser. 
- `usemathjax` - set to `true` if you want to embed $$\LaTeX$$ equations like $$e=mc^2$$. Note that you will need to use double dollar signs `$$<EQUATION>$$` for equations to show.

### Adding Actual Content

Type things in here.

Inline code is done by surrounding your text with single backticks `` `<CODE>` ``.

Block code is done by having triple backticks above and below the code block:

````
```
<CODE>
```
````

IDK how to insert images yet.

IDK how to insert videos yet.

Links are added via `[text](web-url)`.

## Creating Link from [Tutorials](..)

Before deploying your new page, you need to make it accessible from the [home page](../../index.markdown).
To do this, add the following to `PX4-Autopilot/docs/index_tutorials.md`:
```
- [<PAGE_TITLE>](<PAGE_FOLDER>/<PAGE_FILE>)
```

For the [Example Page](../example_page/example_page.md), it would be
```
- [Example Page](example_page/example_page.md)
```

## Local Testing

To locally test your new content, run
```
bundle exec jekyll serve
```
in your terminal. Launch a browser and go to [your locally hosted version of the website](http://127.0.0.1:4000/PX4-Autopilot/).

This way, you can quickly make changes to your page, check for broken links, and make sure everything is working
properly before making your page public.

## Publishing Your New Content

For your new stuff to show up on the internet, you have to push the page you wrote to the repository `gh-pages` branch.
The steps should follow as:
```
git add .
git commit -m "<MESSAGE HERE>"
git push
```

Wait for a few minutes, then you should see the changes on the website.

Please only push to `gh-pages`. Don't overwrite other branches, they contain valuable code.