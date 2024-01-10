---
title: Predicting professional Counter-Strike Global Offensive matches using Data Science
---
# Trying to predict the outcome of pro CSGO Matches

_First a little bit of background:_

I am an avid Counter-Strike Player and i also very much enjoy watching professional matches.

There is also a vivid betting scene in Counter-Strike eSports, where predicting the winner of professional matches will net you quite a lot of money.

So being a computer science student and having lost considerable sums of money betting, i thought to myself:

**Could i build a Machine-Learning based solution to automatically predict the winner of professional Counter-Strike Matches?**

But first i had to gather some historical data. Fortunately the site HTLV.org keeps track of every single professional Counter-Strike match played.

There was a small problem tho, they had no official API to request said data. So i had to build a fairly complex Webscraper to extract the necessary data from the site.

I chose to use Python, the requests module, and BeautifulSoup4 as a basis for the Webscraper.

Very basically, i first use requests to load the HTML of a single match page, such as [this](https://www.hltv.org/matches/2349930/g2-vs-natus-vincere-iem-cologne-2021), i then ingest the raw HTML into BS4 to parse it.

```python
def getRawData(url, useragent=_UAGENT, waittime=16):
    """
    returns a bs4.soup-Object of the given url
    @Params: url: a string-url for a HLTV-Match page
    @returns a bs4.soup-Object
    """
    try:
        # Connect and Save the HTML Page
        # Check if Proxy Settings are available
        # User Agent Mozilla to Circumvent Security Blocking
        page_html = proxies.proxiedRequest(url)

    except Exception as e:
        print(e)
        print("HTTPError 429 Too many requests, waiting for " + str(waittime) + " Seconds.")
        time.sleep(waittime)
        return getRawData(url, waittime=waittime * 2)

    # Parse HTML
    page_soup = soup(page_html, "html.parser")
    return page_soup
```

I then had to extract all the information from the HTML, fortunately BS4 makes this very easy.

Heres an example of how i extracted the date and time when a match was played:

```python
def _getMatchDate(page_soup):
    """
    returns relevant Date Information for a given soup_Object
    @Params: page_soup: bs4.soup-Object of an HTLV-Result Page
    @returns a datetime.datetime Object
    """
    time = page_soup.find("div", {"class": "time"}).text
    date = page_soup.find("div", {"class": "date"}).text
    year = date[-4:]
    day = int((date.split(" ")[0])[0:-2])
    month = month_string_to_number(date.split(" ")[2])
    return datetime.datetime(int(year), month, int(day), hour=int(time.split(":")[0]), minute=int(time.split(":")[1]))
```

Having extracted all the interesting information from the HTML, i then had to store it.

I chose to use SQLite for its ease of use and speed.

Here's a snapshot of my Database-Layout:

![Database Layout](DatabaseLayout.png)

Now all thats left to do is run my Scraper for every match listed on HLTV.org, lets hope they dont have DDOS-Protection :)

Using Elo to calculate win-percentages.

After i had collected all the Data i needed, i started by trying to calculate Elo-Values for every player, so that i could theoretically predict their Winrates against each other.

Heres the Code i used to calculate the Elo after a match.

```python
def calcRoundElo(team0, team1, winner):
    """
    @Param: team0, team1 is a List of all Players on the Team.
     *** The List team0 has to be a List of 5 PlayerIDs! ***
    @Param: winner is either a 0 or a 1 depending on which Team won the round.
    This Method then calculates the Elo Values for every player after the Round and returns them in Two Lists
    """
    K = 128

    averageEloTeam0 = -1
    averageEloTeam1 = -1
    for player in team0:
        averageEloTeam0 += getEloForPlayer(player)
    averageEloTeam0 = averageEloTeam0 / len(team0)
    for player in team1:
        averageEloTeam1 += getEloForPlayer(player)
    averageEloTeam1 = averageEloTeam1 / len(team0)

    for player in team0:
        elo = getEloForPlayer(player)
        transformedElo = math.pow(10, elo / 400)
        expectedScore = transformedElo / (transformedElo + math.pow(10, averageEloTeam1 / 400))
        if winner == 1:
            newRating = elo + (K * (0 - expectedScore))
        elif winner == 0:
            newRating = elo + (K * (1 - expectedScore))
        updateEloRating(player, newRating)

    for player in team1:
        elo = getEloForPlayer(player)
        transformedElo = math.pow(10, elo / 400)
        expectedScore = transformedElo / (transformedElo + math.pow(10, averageEloTeam0 / 400))
        if winner == 0:
            newRating = elo + (K * (0 - expectedScore))
        elif winner == 1:
            newRating = elo + (K * (1 - expectedScore))
        updateEloRating(player, newRating)
```

**Work in Progess, i’ll continue this whenever i have the Time.**