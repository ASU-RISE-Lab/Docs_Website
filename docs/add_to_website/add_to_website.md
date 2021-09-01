---
title: How to Add Content to This Website
layout: tutorial
toc: true
exclude: true
permalink: /tutorials/add_to_website
---

## Get the Source Code

Before adding any new content to this website, you need to get the source code. 
Clone the `gh-pages` from `PX4-Autopilot` using

```
git clone --branch gh-pages https://github.com/ASU-RISE-Lab/PX4-Autopilot.git
```

## Required Software

### Ruby

Follow the instructions [here](https://www.ruby-lang.org/en/documentation/installation/).

### Bundler

Follow the instructions [here](https://bundler.io)

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
---
```

- The `---` above and below the text is essential.
- `title` - is the title.
- `layout` - determines where things are placed. Alternatives are `page`, `post`, `home`, `default`.
- `toc` - controls whether there is a table of contents or not. It doesn't seem to be doing anything right now.
- `exclude` - SET TO TRUE. This excludes the page from having a link in the header bar of every page.
- `permalink` - modifies the link that goes in the URL bar of the browser. 

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