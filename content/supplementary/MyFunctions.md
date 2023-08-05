---
title: "MyFunctions.py"
subtitle: Functions for subtitle processing
date: 2022-08-01
---

```go {hl_lines=[],linenostart=0}
// import modules:
import os
import re

import pandas as pd

from string import punctuation

from datetime import datetime

regex = [
    "hier ist das erste deutsche fernsehen mit",
    r"herzlich willkommen zur live- untertitelung des ndr.+\)",
    "heute im studio: [a-zäöüß-]+( zur)?( malte)? [a-zäöüß-]+",
    "guten abend",
    "guten tag",
    "willkommen zur tagesschau",
    "ich begrüße sie zu[a-z]?",
    r"copyright untertitel: ndr \d{4}",
    "tagesschau",
    "tagesthemen"
]
// a couple of terms to be removed from the texts, including weather announcements

dicmods = {
    "Caren Misoga": "Caren Miosga",
    "Caren Mioasga": "Caren Miosga",
    "Carmen Miosga": "Caren Miosga",
    "Julia Niharika-Sen": "Julia-Niharika Sen",
    "Pina Atalay": "Pinar Atalay",
    "Pinar Ataly": "Pinar Atalay",
    "Torsten Schröder": "Thorsten Schröder",
    "Tarek Yousbachi": "Tarek Youzbachi"
}


// correcting moderators' names

def replace_mod(moderator):
    if pd.isna(moderator) == False:
        for i, j in dicmods.items():
            moderator = moderator.replace(i, j)
    return moderator


def convert_date(datestring):
    if pd.isna(datestring) == False:
        try:
            datestring = datetime.strptime(datestring, '%Y-%m-%d')
        except ValueError:
            datestring = "No Date found"
    return datestring


def meta(file):
    '''
    Extract Meta Information from Subtitles
    '''

    m = re.search("Heute im Studio: ([a-zA-ZäÄöÖüÜß-]+( zur)?( Malte)? [a-zA-ZäÄöÖüÜß-]+)", file)
    if m:
        moderator = m.group(1)
    else:
        moderator = None

    moderator = replace_mod(moderator)
    // correcting typos in moderator names with a prev. defined dictionary

    moderators.append(moderator)


def process(file):
    '''
    split the lines by line breaks, then the lines into time data and text
        '''

    file = file.replace("\ufeff", "").split("\n")

    file = [line.split(";") for line in file]

    files.append(file)


def extract_text(file):
    '''
    separate the text from its time and line data
    '''
    lines = []
    for line in file:
        if len(line) == 4:
            lines.append(line[3])
    texts.append(lines)


def clean_text(dataset):
    '''
    clean the texts for further analysis
    '''
    dataset = [text.lower() for text in dataset]  // lower case

    for reg in regex:
        dataset = [re.sub(reg, " ", text) for text in dataset]
        // use a list of terms to remove from texts

    dataset = ["".join([l for l in text if l not in punctuation]) for text in dataset]  // remove punctuation
    dataset = [" ".join(text.split()) for text in dataset]  // remove double spaces

    return dataset

def get_date(title):
    '''
    get date and time from filename
    '''

    d = re.search(r"(?<=-)([0-9-]+)(?=-)", title)
    if d:
        date = d.group(1)
    else:
        date = "No Date found"

    t = re.search(r"(?<=-)([0-9]+)(?=\.txt)", title)
    if t:
        time = t.group(1)
    else:
        time = "No Time found"

    date = convert_date(date)
    // converting dates to date format

    times.append(time)
    dates.append(date)

def get_data(path):
    '''
    processes the subtitle files

    Parameters
    ----------
    path: name of the subfolder containing the files

    Returns
    -------
    rawfiles (simply the txt file),
    files (processed file, line by line information),
    moderators (list of commentator for every episode, if available),
    dates (date of every episode),
    fulltexts (full text for every episode),
    texts (processed text)
    '''

    global rawfiles, files, moderators, dates, texts, stopwords, times, titles

    rawfiles = []
    files = []
    moderators = []
    dates = []
    times = []
    texts = []
    titles = []

    for file in os.listdir(path):
        if file.endswith(".txt"):
            f = os.path.join(path, file)
            title = os.path.basename(f)
            titles.append(title)
            with open(f, "r") as fd:
                rawfiles.append(fd.read())

    // open each file and add raw text and filename to list

    for title in titles:
        get_date(title)
    // get date and time from filename

    for file in rawfiles:
        meta(file)
    // extract meta information from each file

    for file in rawfiles:
        process(file)
    // separate subtitle formatting from text

    for file in files:
        extract_text(file)
    // save text to different list

    fulltexts = [" ".join(text) for text in texts]
    // join each line of text to one full text

    texts = clean_text(fulltexts)
    // clean text and save to different list

    df = pd.DataFrame(fulltexts)
    df.columns = ["Text"]
    df["Date"] = dates
    df["Moderator"] = moderators
    df["Cleaned Text"] = texts
    df["Time"] = times
    df["Title"] = titles
    // create dataframe with all the information

    return df

    // return dataframe (and individual lists for easier analysis

```