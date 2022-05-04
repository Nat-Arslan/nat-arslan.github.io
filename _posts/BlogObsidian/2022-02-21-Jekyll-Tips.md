---
title: "Jekyll Tips"
date: 2022-02-20
categories: [testcategory]
tags: [how-to]     # TAG names should always be lowercase
toc: true
comments: false
math: true
mermaid: true
---

## Publishing new posts
- git add .
- git commit -m "Initial commit"
- Do this once. git remote add origin https://github.com/Nat-Arslan/nat-arslan.github.io.git
- git push 

## Tagging
Tag names (in YAML) should always be lowercase
  
## Attaching images
### Path
**Path: /attachments/images/seaotter.jpeg** 
![Otterly-Beautiful](/attachments/images/seaotter.jpeg)

### JPEG vs PNG
You cant publish PNG images so convert them to JPEG.

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