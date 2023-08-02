---
title: "Topics of Tagesschau"
date: 2022-08-01
images:
- /images/screenshot_plot.png  
description: "Online Tool to Analyze News Salience in Germany"
---

<b>For my [thesis at University of Amsterdam](https://scripties.uba.uva.nl/search?id=c7012523),</b> I collected subtitle data from [Tagesschau](https://www.tagesschau.de), the biggest German TV news show. It shows the use of words(or better, wordstems) in Tagesschau episodes, for each daily episode (the 8pm ones) between 2014-2022. I never ended up using it, so I decided to make it publicly available, in case someone else finds use in it. 

You can select up to three terms, choose the unit of time, visualize the use of the words and finally download the data for further analysis. Have fun!

<iframe height="1100" width="100%" frameborder="no" src="https://felixgruenewald.shinyapps.io/tagesschau_app/"></iframe>

<i><b>Disclaimer:</b> I can and do not want to guarantee for the correctness of these numbers. I used third-party packages to get the stems of words, and while in my experience, they work well, I do not have the ressources to check the numbers with the actual texts. To keep file sizes manageable (especially for my budget which doesn't allow for paid file hosting plans), the dataset here only includes nouns and proper names. Anything else I only have locally but might make available at a later point.

For any questions, please [contact me](mailto:felixgruenewald@outlook.de).

If you decide to use my data for any kind of project, please cite to this website.</i>

# Background

For my master's thesis at University Amsterdam, I wanted to write about the media's infuence on the perception of unemployment benefit recipients (broadly, the final topic was much more teechnical). To conduct a measure of salience, i.e., to get a sense of how much the media is talking about unemployment, benefits and the politics surrounding it, I contacted the NDR, producers of the biggest German TV news show about data. They were extremely helpful and cooperative. After a bit of discussion on the best approach, they sent me a total 11.976 subtitle files of every Tagesschau episode between November 2014 and April 2022. 

I wrote a script to read these files, split them up into the individual words and their wordstems, and to finally count the use of each term over time. In the meantime, however, I had to change my thesis plans due to a lack of data sources for a different part of my analysis. Nevertheless, a lot of my time went into making the subtitles analyzable, so I didn't just want to let it go to waste. So, while starting my Ph.D. at TU Chemnitz, I continued working on the data strcuture and visualisations as a hobby project until it now reached a point where I'm kind of happy with the result. So, instead of letting it sit on my computer, I wanted to make it accessible to other people. Who knows, maybe someone saves a lot of time for their own master's thesis, finds inspiration for their research or just likes to play around with the data. 