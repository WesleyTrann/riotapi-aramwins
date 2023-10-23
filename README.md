# riotapi-aramwins
Personal project to calculate the ARAM winrate compatibility between your friends!

# 0. Introduction
# 1. Documentation and Notes

# 2. Setup
<b>2.1 Install riotwatcher</b>
```python
pip install riotwatcher
```

<b>2.2 Import what we need</b>
```python
from riotwatcher import LolWatcher, ApiError
```

<b>2.3 Initialise a few variables..</b>
```python
# Make sure to register/renew apiKey at: https://developer.riotgames.com/ 
apiKey = 'RGAPI-XXX'
region = 'oc1'
playerName = 'HoChiLim'

# these are my friends!
myFriends = [
    playerName, # add any amount of names after
    'pinkfolder2', 
    'XxElitePetexX',
    'Floppy Corn',
    'Player5',
    'Player6'
]
```

<details>
  <summary><b>2.4 Make sure to create and run these functions we'll use below...</b></summary>
  
  ```python
  def openStatList(myFriends):
    players = []
    for name in myFriends:
        addPlayer(players, name)
    return players

def addPlayer(players, name, gamesPlayed = 0, gamesWon = 0):
    player = {
        'name': name,
        'gamesPlayed': gamesPlayed,
        'gamesWon': gamesWon,
    }
    players.append(player)
    
def cacheAccounts(friendList):
    puuidNameDict = {}
    for name in friendList:
        puuid = getPuuidFromName(name)
        puuidNameDict.update({puuid: name})
    return puuidNameDict

def getPuuidFromName(name):
    summonerdto = lol_watcher.summoner.by_name(region, name)
    return summonerdto['puuid']

def getNamesFromMatch(gamedetails, accountsDict):
    nameList = []
    puuidList = gamedetails['metadata']['participants'] # gets list of all 10 players
    for puuid in puuidList:
        if puuid in accountsDict:
            nameList.append(accountsDict.get(puuid))
    return nameList
        
def hasWon(gamedetails, teamList):
    for i in range(10):
        if gamedetails['info']['participants'][i]['summonerName'] in teamList:
            return (gamedetails['info']['participants'][i]['win'])
    return

def updateStatList(statList, players, victory):
    for player in players:
        for i in range(len(statList)):
            if player == statList[i]['name']:
                statList[i]['gamesPlayed'] += 1
                if (victory):
                    statList[i]['gamesWon'] += 1
                break
    return statList

def displayStats(statList):
    finaltext = ""
    for i in range(len(statList)):
        winratetext = str(round((statList[i]['gamesWon']/statList[i]['gamesPlayed']*100),0))
        gamesPlayedtext = str(statList[i]['gamesPlayed'])
        if statList[i]['gamesPlayed'] != 0:
            if statList[i]['name'] != playerName:
                finaltext += statList[i]['name']+": "+winratetext+"% of "+gamesPlayedtext+" games. \n"
            else:
                print(playerName+" has a winrate of "+winratetext+"% of "+gamesPlayedtext+" games, and have the following winrates with:")
    print(finaltext)
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

