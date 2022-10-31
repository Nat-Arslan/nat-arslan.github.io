---
title: "Using Obsidian, PostgreSQL and Python to Analyse & Visualise Notes"
date: 2022-10-30
tags: [python, sql, notetaking] # TAG names should always be lowercase
toc: false
comments: false
image:
  src: /attachments/images/2022-10-31-Obsidian-PostgreSQL-Python.jpg
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: 
---

Github [repository](https://github.com/natarslan/Obsidian-PostgreSQL-Python) for this project.

---

I use [Obsidian](https://obsidian.md) to write down notes and my journal in markdown format. The app allows me to create relational notes. I can link these notes to each other, graph this relationship and use tags or dosens of plugins to make sense of my notes. But I always felt I needed to see more patterns and connections. And what is a better tool than Python to achieve this? 

There is an Obsidian [plugin](https://github.com/clouedoc/postgresql-obsidian) which makes it easy to send the metadata (YAML) of each file to PostgreSQL database. From there on I can connect to the database and analyse my journal entries with Python. Here is my brief workflow:

1. Keep taking notes in Obsidian
	1. Important to have consistent metadata (YAML) section. Some example things that can be tracked/recorded are: date, mood, weather, location etc.
2. Download the PostgreSQL [plugin](https://github.com/clouedoc/postgresql-obsidian) for Obsidian
	1. After taking a note (journal, meeting etc) use this plugin to send the files metadata to PostgreSQL database.
3. Use SQL in Python to analyse and visualise notes. In [this Github repo](https://github.com/natarslan/Obsidian-PostgreSQL-Python) you can follow all the steps. 

The steps in Python are briefly:
1. Connect to the PostgreSQL database in jupyter notebook (python):  
	> %sql postgresql://username:password@host:port/database

2. Create an SQL query. Example:
	> query =  ''' 
	> 	SELECT 
		    dataview_data -> 'date' as date,
		    dataview_data -> 'tags' as tags,
		    dataview_data -> 'mood' as mood, 
		    dataview_data -> 'geom' as geom
		    FROM obsidian.file; 
		    '''
	
3. Convert the data from the database into pandas using an SQL query and SQLAlchemy engine: 
   > df = pd.read_sql(query, engine)

4. Edit dataframe if necessary 
5. Convert dataframe to GeoPandas and map with IpyLeaflet
6. Plot data
7. (Optional) Use Streamlit to create a dashboard if you prefer

To reveal further connection between the notes, one can even use *dataframe.corr()* function to see the correlation between metadata fields. This can help answer questions like "Do I feel better on the days where I have a walk?".

I know there is a lot of benefit of keeping a hand written journal. But I need to be able to analyse the information that I'm documenting. Sending data to PostgreSQL and then to Python proved to be a very efficient way to do this.

