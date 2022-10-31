---
title: "Using Obsidian, PostgreSQL and Python to Visualise Notes"
date: 2022-10-31
future: true
categories: 
tags: [python, sql, notetaking] # TAG names should always be lowercase
toc: false
comments: false
image:
  path: /attachments/images/2022-10-31-Obsidian-PostgreSQL-Python.jpeg
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: 
---

I use Obsidian to write down notes and my journal. The Obsidian plugin makes it easy to send the files' metadata to PostgreSQL database. From there on I can connect to the database and analyse my journal entries. Nice thing is that, this can be done to any kind of notes (not restricted to journal files). Say for instance I can analyse meeting notes etc. Here is the brief workflow:

1. Keep taking notes in Obsidian
	1. Important to have consistent metadata (YAML) section. Some example things that can be tracked/recorded are: date, mood, weather, location etc
2. Download the PostgreSQL [plugin](https://github.com/clouedoc/postgresql-obsidian) for Obsidian
	1. After taking a note (journal, meeting etc) use this plugin to send the files metadata to PostgreSQL database.
3. Use SQL in Python to analyse and visualise notes. In [this Github repo](https://github.com/natarslan/Obsidian-PostgreSQL-Python) you can follow all the steps. Briefly:
	1. Connect to the PostgreSQL database in jupyter notebook (python):  
	   %sql postgresql://username:password@host:port/database
	2. Convert the data from the database into pandas using an SQL query and SQLAlchemy engine: 
	   df = pd.read_sql(query, engine)
	3. Edit dataframe if necessary
	4. Map with GeoPandas and IpyLeaflet
	5. Plot data
	6. Use Streamlit to create a dashboard if you prefer
