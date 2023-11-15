# riotapi-aramwins
Personal project to calculate the ARAM winrate compatibility between your friends!

# 0. Introduction
# 1. Documentation and Notes
league of legends riot API:
<br>https://developer.riotgames.com/apis
<br>All the information about their API's including required inputs, outputs and error codes are here.

<br>
data dragon:
<br>https://riot-api-libraries.readthedocs.io/en/latest/ddragon.html
<br>This is where riot stores most their their raw data (champion details, gamemode codes, lore, etc) 

<br>riot-watcher documentation
<br>https://riot-watcher.readthedocs.io/en/latest/
<br>We're only making a simple project and we don't want to handle rate-limiting, so we're using riot-watcher! 

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
apiKey = 'RGAPI-32df8dd9-f72c-422c-7f27-af8eaf770eb3' # looks something like this (never share your api key!)

# setup riot-watcher with our key
lol_watcher = LolWatcher(apiKey)
region = 'oc1'
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
<br> - myFriends: you can have as much (or as less) friends as you'd like in the list!
<br> - queueid: the type of gamemode to search from (you can search norms/ranked, using DataDragon in 1.1)
<br> - matches: how many games to search from (maxed at 100 per query)
<br> - index: where to start search (i.e. index=0, matches=50, gets your last 50 games / index=30, matches=50, gets your last 50 games not including your most recent 30)
<br> This might take a few minutes depending on how many games your searching through (the main bulk of the search time is in the function cacheAccount() and lol_watcher.match.by_id(region, gameid).
```python
# player name and friends
playerName = 'Fwoqqet'
myFriends = [
 playerName,
 'pinkfolder2',
 'XxElitePetexX',
 'Floppy Corn',
 'Elliechu',
 'kdng',
 'BirdBrainn',
 'HoChiLim',
 'PhotonBlade8'
]

# grab my puuid
me = lol_watcher.summoner.by_name(region, playerName)
puuid = me['puuid']

# grab all our friends' puuids- so we don't have to constantly search for them
statList = openStatList(myFriends)
puuidNameDict = cacheAccounts(myFriends)

# grab the matches
index = 0
matches = 100
queueid = 450 #this is ARAM
games = lol_watcher.match.matchlist_by_puuid(region, puuid, index, matches, queueid)

for gameid in games:
 gamedetails = lol_watcher.match.by_id(region, gameid)
 namesList = getNamesFromMatch(gamedetails, puuidNameDict)
 victory = hasWon(gamedetails, namesList)
 statList = updateStatList(statList, namesList, victory)

displayStats(statList)
```

