layout: blog
title: 'Reading:How to build a Lite app in low-end Android Phone'
date: 2016-10-08 12:43:57
tags: Android, Mobile Optimization
---
[How we built Facebook Lite for every Android phone and network](https://code.facebook.com/posts/1365439333482197/how-we-built-facebook-lite-for-every-android-phone-and-network?utm_source=wanqu.co&utm_campaign=Wanqu+Daily&utm_medium=social)
## The golds
1.	The size of APK <= 1 MB
2. 	Minimize Data ussage and optimize for 2G Network

The architecture:
![Architecture](https://scontent.xx.fbcdn.net/t39.2365-6/12504175_1685387308400777_1475859244_n.png "Architecture")

To reduce the APK size, the client side does not have any production code, instead it contains only a simple VM and a rendering engine to Android UI. 
The production code is written on the server side. The server fetches data from backend server. 

## How to Achieve 
1.	Small APK 
	The client is a geneic VM and production code is one server.Elements,like strings and PNG resources are sent form the server on demand and cached rather than built from APK. Use Unicon Symbol instead of Icon.

2.	Slow connection and data efficiency
	-	Over TCP instead of HTTPS
	-	Use LZMA2 compression for server to client(great compression rate and low resource usage in decompression)
	- 	DEFLATE for client to server
	-  control the image size because the Lite server knows the exact the size of screen and DPI and can adjust the quality of image and format.
	-  Diff mechanism: only push the changes to the client.
	-  Tools like Augmented Traffic Control and the Internet.org Innovation Lab to simulate the real conditions.


