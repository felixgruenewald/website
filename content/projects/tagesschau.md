---
title: "Topics of tagesschau"
date: 2022-08-01
images:
- /images/tagesschau_app_logo.png
description: "Online Tool to Analyze News Salience in Germany"
---

<b>For my [thesis at University of Amsterdam](/articles/thesis),</b> I collected subtitle data from [tagesschau](https://www.tagesschau.de), the biggest German TV news show. It shows the use of words (or better, wordstems) in tagesschau episodes, for each daily episode (mostly the 8pm ones) between 2014-2022. I never ended up using the data, so I decided to make it publicly available, in case someone else finds use in it. 

You can select up to three terms, choose the unit of time, visualize the use of the words and finally download the data for further analysis. If you want to know how this was made, scroll down. Have fun!

<b>Note:<b> Data for 2014 and 2022 is not complete, as you can see in the daily view. Their yearly sum is therefore not representative of the full year, of course.

<iframe height="1100" width="100%" frameborder="no" src="https://felixgruenewald.shinyapps.io/tagesschau_app/"></iframe>

<i><b>Disclaimer:</b> I can and do not want to guarantee for the correctness of these numbers. I used third-party packages to get the stems of words, and while in my experience, they work well, I do not have the resources to double check the numbers with the actual texts. To keep file sizes manageable (especially for my budget which doesn't allow for paid file hosting plans), the dataset here only includes nouns and proper names. Anything else, I only have locally but might make available at a later point.

For any questions, please [contact me](mailto:felixgruenewald@outlook.de).

If you decide to use my data for any kind of project, please cite to this website:</i>

---
**Grünewald, Felix**. "Online tool tagesschau”, August 1, 2023. _https://felixgruenewald.net_.

---

# Background

For my master's thesis at University Amsterdam, I wanted to write about the media's infuence on the perception of unemployment benefit recipients (broadly, the final topic was even more technical). To conduct a measure of salience, i.e., to get a sense of how much the media is talking about unemployment, benefits and the politics surrounding it, I contacted the NDR, producers of the biggest German TV news show about data. They were extremely helpful and cooperative. After a bit of discussion on the best approach, they sent me a total 11.976 subtitle files, of every tagesschau episode between November 2014 and April 2022. 

I wrote a script to read these files, split them up into the individual words and their wordstems, and to finally count the use of each term over time. In the meantime, however, I had to change my thesis plans due to a lack of data sources for a different part of my analysis. Nevertheless, a lot of my time went into making the subtitles analyzable, so I didn't just want to let it go to waste. So, while starting my Ph.D. at TU Chemnitz, I continued working on the data structure and visualisations as a hobby project until it now reached a point where I'm _kind of_ happy with the result. Therefore, instead of letting it sit on my computer, I wanted to make it accessible to other people. Who knows, maybe someone saves a lot of time for their own master's thesis, finds inspiration for their research or just likes to play around with the data. 

# Process

I will try to explain how I created this app, for everyone who wants to use the data and wants to be sure about its origins, or for those who are interested in these sorta things. My background is not in programming, so a lot of this is probably not as clean as it could be. Please let me know if you have tips!

Lastly, before I start: I have learned everything I needed for this mostly by searching questions on stackoverflow. However, my first steps in Python and the important knowledge about word processing comes from a great course at UvA by Damian Trilling, who fortunately made the teaching material [available online](https://github.com/damian0604/bdaca), for everyone who is interested.

## Preparing Data

When I received the subtitle files, and after decoding the format to .txt files, this is how the data looked like:

```         
1;00:00:00,040;00:00:03,720;Hier ist das Erste Deutsche Fernsehen mit der tagesschau.
2;00:00:04,240;00:00:09,040;Herzlich willkommen zur Live- Untertitelung des NDR (01.11.2014)
3;00:00:12,240;00:00:14,480;Heute im Studio: Susanne Daubner
4;00:00:14,920;00:00:17,920;Guten Abend, ich begrüße Sie zur tagesschau.
5;00:00:18,920;00:00:23,240;Bundespräsident Gauck äußerte sich in Thüringen skeptisch zu den Plänen
6;00:00:23,240;00:00:25,280;für eine rot-rot-grüne Regierung.
7;00:00:25,440;00:00:29,000;Dort könnte mit Bodo Ramelow zum ersten Mal ein Politiker
8;00:00:29,160;00:00:32,920;der Linkspartei Ministerpräsident eines Bundeslandes werden.
...
```

That's of course in no way a format that I could run data analyses on, so I had to write a few functions to extract the relevant information. First, by splitting the lines into two by the ';' after the timing information, and then removing the timing information from the dataset:

```         
Hier ist das Erste Deutsche Fernsehen mit der tagesschau. Herzlich willkommen zur Live- Untertitelung des NDR (01.11.2014) Heute im Studio: Susanne Daubner Guten Abend, ich begrüße Sie zur tagesschau. Bundespräsident Gauck äußerte sich in Thüringen skeptisch zu den Plänen für eine rot-rot-grüne Regierung. Dort könnte mit Bodo Ramelow zum ersten Mal ein Politiker der Linkspartei Ministerpräsident eines Bundeslandes werden. In einem ARD-Interview stellte Gauck die Frage, ob sich die Linkspartei weit genug von SED-Vorstellungen entfernt habe.
```

There are 11.976 of these text files at this point which is a lot more than one file per day. The tagesschau airs several times a day and in various lengths. To keep the word counts comparable across days, I keep only the longest episode per day, leaving 2717 episodes/days.

Next, I cleaned the texts by making everything lower space, removing certain terms that I defined (like, 'Herzlich Willkommen zur Tagesschau'. This is not strictly necessary, as I later on remove overly frequent words anyway, but it keeps these phrases that occur in every episode from skewing the data) and removing double spaces and punctuation. Lastly, I extracted meta information from the subtitle files, like the date, the moderator and the airtime of the episode.

I combined all these functions (find the full file under [My Functions](/supplementary/myfunctions)) in a `get_data` function which, for the example file above, gave me the following data with the full text, the meta information and a 'cleaned' version of the text:

<div class="table">

| Date | Text | Moderator | Cleaned Text | Time | Title | Length |
|:----:|:----:|:---------:|:------------:| :---:| :----:| :-------:|
| 2014-11-01 | Hier ist das Erste Deutsche Fernsehen mit der ... | Susanne Daubner | der bundespräsident gauck äußerte sich in thür... | 2000 | Das_Erste-2014-11-01-ts-2000.txt | 10102  |

</div>

This 'cleaned' text now looks like this:

```         
der bundespräsident gauck äußerte sich in thüringen skeptisch zu den plänen für eine rotrotgrüne regierung dort könnte mit bodo ramelow zum ersten mal ein politiker der linkspartei ministerpräsident eines bundeslandes werden in einem ardinterview stellte gauck die frage ob sich die linkspartei weit genug von sedvorstellungen entfernt habe
```

The next step is to *tokenize* the texts which, put simply, just means splitting them into their individual words. I used the `nltk` package for that, which has German language support. Afterwards, I sent each of these words to the [HannoverTagger](https://serwiss.bib.hs-hannover.de/frontdoor/deliver/index/docId/2457/file/wartena2023-HanTa_v1.1.0.pdf) to get the lemmata of these words. A lemma is basically a word reduced to its minimal form, stripped of its grammatical conjunctions etc. That way, for example, 'house' and 'houses' are not treated as different words, but as the same entry. The *HanTa* also provides more information on words, which allows to only keep nouns and proper names.

```         
Bundespräsident Gauck Thüringen Plan Regierung Bodo Ramelow Politiker Linkspartei Ministerpräsident Bundesland Ardinterview Gauck Frage Linkspartei Sedvorstellung
```

The last step is now to just transform the data that we have to count the number of words per day. You can find the full script for my processing [here](/process_subtitles.html).

<div class="table">

| Word               | 2014-11-01 | 2014-11-02 | 2014-11-03 | 2014-11-04 | 2014-11-05 | 2014-11-06 | 2014-11-07 | ... |
|--------|--------|--------|--------|--------|--------|--------|--------|--------|
| Überzahl           | 0          | 0          | 0          | 0          | 0          | 0          | 0          | ... |
| Überzeugung        | 0          | 0          | 0          | 0          | 0          | 0          | 0          | ... |
| Überzeugungsarbeit | 0          | 0          | 0          | 0          | 0          | 0          | 0          | ... |
| Übung              | 0          | 0          | 0          | 0          | 0          | 0          | 0          | ... |

</div>

Even after removing verbs, adjectives etc., this data set is still *large*. Putting it in a shiny application would have meant that every user would have to download the full thing, which is not only a waste of energy, but also too much for my shiny data limits. So, I exported this data and loaded it to a [MongoDB](https://www.mongodb.com/try/download/community-edition) that I created, using the `mongolite` R-package. This allows to access one line of the data at a time, using search queries:

```go {hl_lines=[],linenostart=0}
db$find('{'_row': 'Aachen'}')
```

With this in place, the data could be used in a `shiny` app. I wanted users to be able to scroll through all the choices and try out different words. To not reload the plot and data every time someone types a word, a list of words is loaded separately. The `selectizeInput` function keeps this list updated depending on the user input and all the other operations are only executed when the user chooses to click **Go**.

```go {hl_lines=[],linenostart=0}
// Loading the full list of words
word_list <- readRDS(file = 'data/word_list.rds')

// Input
fluidRow(
  column(4,wellPanel(selectizeInput(inputId = 'topic1',
                                    label = 'Topic 1',
                                    choices = NULL,
                                    multiple = FALSE))))

// Updating the list according to input
updateSelectizeInput(session,
                     inputId = 'topic1',
                     choices = word_list,
                     server = TRUE)

// Action button sends the signal to 
// send the search query and plot the data
column(width = 12,
       align = 'center',
       actionButton(inputId='action',
                    label='Go!')
```

After sending the data to an online database, it has now arrived on the user's computer in form of the three words they chose to display. The final step is plotting everything with [Plotly](https://plotly.com/r/).

```go {hl_lines=[],linenostart=0}
// Plotting the dates on x and each of the words on y
plot_ly(df, x = ~Date) %>% 
    add_trace(y = ~get(word1),
              type = 'bar',
              name = label1,
              hoverinfo = ~get(word1))
```

Thanks for reading through all this (granted you didn't skip over everything, of course). Please send me a text, if you have questions or remarks and I hope this was helpful or at least interesting!