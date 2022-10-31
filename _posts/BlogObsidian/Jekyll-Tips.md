---
title: "Jekyll Tips"
date: 2022-02-20
categories: [How To]
tags: [how-to]     # TAG names should always be lowercase
toc: true
comments: false
math: true
mermaid: true
---

## Publishing new posts
- cd /Users/nat/Desktop/Blog/nat-arslan.github.io
- git add .
- git commit -m "Initial commit"
- Do this step once. git remote add origin https://github.com/Nat-Arslan/nat-arslan.github.io.git
- git push 

## Categories and Tags
Tag names (in YAML) should always be lowercase.

The `categories` of each post are designed to contain up to two elements, and the number of elements in `tags` can be zero to infinity. For instance:

categories: [TOP_CATEGORIE, SUB_CATEGORIE]
categories: [Animal, Insect]

## Linking to other posts
I tried using Jekyll Wikilinks Plugin, but it didnt work for me.  
So the easy way is to:
	- Post on blog (lets say Post A)
	- Copy the blog URL (Post A URL)
	- Edit the other post (Post B) and paste Post A URL under a section/header 'Linked Posts' (or something like that)
  
## Attaching images
### Path
**Path: /attachments/images/seaotter.jpeg** 
![Otterly-Beautiful](/attachments/images/seaotter.jpeg)

### JPEG vs PNG
You cant publish PNG images so convert them to JPEG.

## Video
How to embed a video?
I tried embedding iframe (copy from youtube and paste into markdown editor). It works. (</iframe>)

## Publishing Jupyter notebook
1. If you have figures save them as PNG or JPEG
   `plt.savefig('XXX.jpg')`
   
2. Export your jupyter notebook to MD format. There are several methods.
   - Export from jupyter notebook
	- Can be problematic.`jupyter nbconvert --to markdown /path/to/example_notebook.ipynb`

3. Edit the resulting MD 
	- **DELETE**. Check if this conversion generated an html table of contents (toc). In my case it did and it was in the beginning of the file. Im guessing this might be due to jupyter toc plugin. It startes with "div class="toc". Delete this.
	- **DELETE**. If your jupyter notebook have some tables, the generated tables in the markdown file might have "<style". Obsidian displays these tables perfectly but smt happens when this files are pushed to github. So delete the styles in each table. 
	- To see the content of the table in Obsidian simply click on it. The tables html code will display.
	- **ADD**. Create a YAML for your file. You can copy-paste it from other posts and then edit the title, date, tags etc
	-  **DELETE** Remove or comment #plt.show() AND #plt.savefig

## Prompts
{: .prompt-tip }

## Chirpy Theme
Documentation github [page](https://github.com/cotes2020/jekyll-theme-chirpy#documentation). Chirpy's web [page](https://chirpy.cotes.page/posts/write-a-new-post/). 

