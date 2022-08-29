---
title: "Interactive Webmap with Django, Leaflet and PostGIS"
date: 2022-08-21
categories: [Coding, Interactive Maps]
tags: [python, webdev, leaflet, django, postgis, earthquake, javascript] # TAG names should always be lowercase
toc: true
comments: false
image:
  src: /attachments/images/2022-08-29-Django-Webapp1.jpeg
  width: 1000   # in pixels
  height: 400   # in pixels
  alt: 
---

This is my first Django web app where I created an interactive map of earthquake data. In the clickable map you can display the location and magnitude information in the popups.

In this [jupyter notebook](https://github.com/natarslan/Simple-Django-App-With-Leaflet-/blob/main/SimpleDjangoApp-Github.ipynb) you can see the steps I followed and all the notes I took in order to understand the process of :

- Downloading and activating PostgreSQL and PostGIS
- Creating a Django web app
- Uploading an excel file to the server
- Displaying this data on a map
- Adding popup on the markers
- Styling the map marker
	- Change icon style to circle
	- Display the circle size dependent on earthquake magnitude

Here is the [github page](https://github.com/natarslan/Simple-Django-App-With-Leaflet-/blob/main/README.md) for this project
