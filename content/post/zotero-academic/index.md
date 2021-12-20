---
title: Extract publications to hugo-academic / wowchemy from Zotero-BetterBibTex
subtitle:

# Summary for listings and search engines
summary: This post explains how to automatically extract publications stored in Zotero (or in any reference manager exportable to Bibtex) and publish them as a publication record in hugo-academic using blogdown/R

# Link this post with a project
projects: []

# Date published
date: "2021-12-13T00:00:00Z"

# Date updated
lastmod: "2021-12-13T00:00:00Z"

# Is this an unpublished draft?
draft: true

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com/photos/CpkOjOcXdUY)'
  focal_point: ""
  placement: 2
  preview_only: false

authors:
- admin

tags:
- Academic

categories:
- Demo

---

## Overview

After developing and maintaining a website generated vía Rmarkdown/Jekyll for some years, I decided to look for an alternative that allowed me a better organization of my work in terms of projects, publications, courses, and the like. I wanted to be able to establish links between many works that where connected but in my site appeared as unrelated, and I also wanted a smoother navigation and searching of content, hopefully using some R tools. After looking for a while the best choice I found was the static site generator [hugo](https://gohugo.io/) via [R/blogdown](https://bookdown.org/yihui/blogdown/), which I was already using for websites of some of my courses. After trying a couple of themes I decided to choose the more traditional way for this kind of objective, which is the academic theme.

Using the [academic-hugo](https://academic-demo.netlify.app/) theme for a personal website where to organize and share academic activities has several advantages. Despite some [critics](https://spectrum.chat/academic/general/the-deadly-rabbit-hole-of-the-academic-theme~c9d51eb8-f2af-4ec8-8ee6-cea4f175a572) regarding the difficulties in setting up a website which is constantly characterized as "simple" by their actual maintainers and promoters (wowchemy), still has many features that make it the first option for many scientist. One of the reasons for this is its excellent capacity for displaying, tagging and searching **publications**, which is something quite unique and appealing as it can observed in this [example](https://solomonkurz.netlify.app/publication/). So, I wanted that, **but** ... it requires to create one file with the information of each publication, which seemed like a lot of work.

The academic-wowchemy people of course are aware of that limitation and created an engine that generates publication records from references stored in Bibtex format, which is great as it is an standard format to export references. This engine is called [hugo-academic-cli](https://github.com/wowchemy/hugo-academic-cli), but unfortunately did not worked well for me. I could have tried a bit more but the fact that it required python (and I prefer R) and with a limited customization made me look for a different alternative based on R. I thought it would be a known topic but I could not find many sources, the closest one being the script from [Lorenzo Busetto](https://www.r-bloggers.com/2018/03/automatically-importing-publications-from-bibtex-to-a-hugo-academic-blog-2/) which is a great job. But ... I tried it and it didn't work properly from scratch, probably because it was outdated from previous versions of Zotero and R and its dependencies and packages. So I decided to attempt to tweak it a bit and adapt it to my needs and to generate a wokflow that includes tools as blogdown, Zotero and BetterBibTex.

# 1. Getting your publications from a reference manager

(if you already have a bib file and you do not intend to automatize this process, proceed to step 2)

One of the things that academics hate is to deliver information from publications in different formats all the time (university forms, project reports, CVs, etc), very annoying and time consuming. If populating a website means to write a different record for any publication AGAIN ... NO WAY! Therefore, I believe the best way to do this is to keep your own references saved just once in a reference manager and then link the information from there. I recommend [Zotero](https://www.zotero.org/) for its excellent quality, for being open source, and because it allows to keep collections/subcollections files synced in any place in your computer thanks to the plug in Better BibTex.

I won't cover here the use of Zotero or BetterBibTex (BBT) in detail, there are excellent tutorials for this. I will just focus on some workflow recommendations and options of BBT in order to automatize the incorporation of publication records on your academic website. The final goal is that:

- you add a bibliographic reference to Zotero
- you run a simple script (see below)
- you get your publications rendered in your academic website

## Save your academic references in a folder/subfolder

- Zotero allows to organize your references in folders, which then can be exported independently to other formats. I recommend to create a folder called `academic-publications` where you can save or move bibliographic records when you are one of the authors. You can choose another name for the folder but I recommend this because to keep coherence with the following workflow. This is the folder that contain the information that will be published on your website.

## Export a synced `bibtex` file from your Zotero folder.

The academic-website requires a bibtex file as an input for generating publication records. In order to keep this file synced with your Zotero collection you can use the Zotero add-in [BetterBibTex](https://retorque.re/zotero-better-bibtex/). Once installed, a bit of customization in Tools > Preferences > BetterBibTex tab

- in Export tab
  - Bibtex tab uncheck "Export unicode ...", otherwhise there are problems with special characters
  - Add URLs to Bibtex export: select in the URL field

- in Miscellaneous tab (within Export, not the other one)
  - uncheck "Apply case-protection to capitalized words by enclosing them in braces"

Now we are ready to export the collection:

  - right click over your Zotero folder academic-publications
  - format: Better BibTex
  - select "Keep updated"
  - ok, and save it under content/publication

These steps create an `academic-publications.bib` file in your website folder. Any reference that you add afterwards, as well as any modification to any of your record, it will updated with the bibtex file available for your website.

Finally, this assumes that you have a relatively ordered and completed set of references. Please check, in my case sometimes authors' name appear written differently in different references, which for a bibtex file means two different authors. I also recommend to add an abstract to each reference, as it is the body of the publication record in your website. There is no need to do this at the beginning as further updates on Zotero will be reflected in your website after the following step.

So far I have tried this with the followig entries: journal, books, chapters and reports.

# 2. `bibtex_2academic` R script

This R script consists of an R function developed by Busetto and that basically extracts information from Bibtex records stored in a .bib file. For each bibliographic record it generates a markdown file that serves as a publication record for the academic theme. You can download a copy of a slightly modified version [here]().

If you want to have a look at it, the first part of the script creates the function `bibtex_2academic` and the second runs the script specifying a) the bib file, and b) the folder where the publication records (as markdown files) are saved. The script uses the `pacman` package to install/load libraries, so if it is not installed the script does it and it might take a while just the first time.

The default way to do this is:

- save the "bibtex_2academic.R" file in the content/publication folder (part of the default folders)
- save your bib file in the same folder with the name `academic-publications.bib` (details in previous step)
- run the script from the terminal. Assuming that your working directory is the root folder of the website project, run: `source("content/publication/bibtex_2academic.R")`

After running the script, the publication folder should have as many markdown files as the number of references in your bib file. After rendering the site with blogdown (`blogdown::serve_site()` from terminal) you should be able to see the list of publications rendered in the default widget "Recent publications" and in `[your website address]/publications/`

# About overwriting option

There are two approaches for the citations-academic workflow: 1) to generate the markdown files and then to add additionals details and customization there, or 2) make all the modifications within Zotero and do not touch the markdown files generated by this script. ALtough the first one allows to add some more fancy stuff manualle, I prefer to use the second one and only add information and modifications within Zotero. But, if you go for the first one you should change the "overwrite" option in the script to false, otherwise any change you make locally in the file will be lost.




- overwrite option

-
