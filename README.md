# riotapi-aramwins
Personal project to calculate the ARAM winrate compatibility between your friends!

# 0. Introduction
# 1. Documentation and Notes

# 2. Setup
<b>2.1 Install riotwatcher</b>
```python
pip install riotwatcher
```

<b>2.2Import what we need</b>
```python
from riotwatcher import LolWatcher, ApiError
```

<details>
  <summary><b>2.4 Make sure to create and run these functions we'll use</b></summary>
  
  ```python
  hello
  ```
</details>


# 3. Implementation and Usage
<b>3.1 Simply just run the below and you'll get your results!</b>
<br> Notes:
<br> - queueid: the type of gamemode to search from (you can search norms/ranked, using DataDragon in 1.1)
<br> - matches: how many games to search from
<br> - index: where to start search (i.e. index=0, matches=50, gets your last 50 games / index=30, matches=50, gets your last 50 games not including your most recent 30)
```python
# grab the matches
queueid = 450 #this is ARAM
matches = 5
index = 0
games = lol_watcher.match.matchlist_by_puuid(region, puuid, 0, matches, queueid)

for gameid in games:
    for gameid in games:
    gamedetails = lol_watcher.match.by_id(region, gameid)
    namesList = getNamesFromMatch(gamedetails, puuidNameDict)
    victory = hasWon(gamedetails, namesList)
    statList = updateKDA(gamedetails, statList, namesList)
    statList = updateStatList(statList, namesList, victory)
    
displayStats(statList)
```

