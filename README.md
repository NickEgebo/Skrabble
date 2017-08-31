# Skrabble
The repository contains the API definition for the Skrabble server, as well as client implementations in various languages.

## Table of content

- [Rules](#rules)
- [API](#api)
    - [Get Game State](#get-game-state)
    - [Join Game](#join-game)
    - [Delete Game](#delete-game)
    - [Play Word](#play-word)
    - [Swap Tiles](#swap-tiles)
    - [Skip Turn](#skip-turn)
- [FAQ](#faq)
- [Example Board JSON](#example-board-json)

## Rules
- The first word played must cross the starting tile
- All words other than the first must be adjacent to, or intersect, an existing tile
- Players may perform on of the following actions on their turn:
  - play a word, 
  - skip their turn
  - swap any number of tiles
- The game ends when ????


## API

### Get Game State 

  Returns a JSON object that represent the current state of the supplied game, including:
  - Game Status
  - Board State
  - Player Status
  - Tile Bag
  - Starting Tile Distribution
  
**URL**
```
[GET] http://SkrabbleHost:8888/game/:gameId
```
**Params**
```
gameId=[string]    // Game Name
```
**Example**
Requests the current state of TestGame
```url
[GET] http://SkrabbleHost:8888/game/TestGame
```

----  
### Join Game
  Returns a Board JSON object that represent the current state of the game AFTER joining. Updated fields may include:
  - Game Status
  - Player Status
  - Tile Bag
  
**URL**
```url
[GET] http://SkrabbleHost:8888/game/:gameId/join/:playerId
```
**Params**
```
gameId=[string]      // Game Name
playerId=[string]    // Player Name
```
**Example**
Player JohnDoe joins the game TestGame
```url
[GET] http://SkrabbleHost:8888/game/TestGame/join/JohnDoe
```  

----
### Delete Game
  Deletes an existing Skrabble game
  
**URL**
```
[DELETE] http://SkrabbleHost:8888/:gameId
```
**Params**
```
gameId=[string]      // Game Name
```
**Example**
Deletes the game TestGame
```url
[DELETE] http://SkrabbleHost:8888/TestGame
```

----  
### Play Word
  Play a word onto the board with a set of parameters. Requires the full word, including tiles already present on the board.
  
**URL**
```
[GET] http://SkrabbleHost:8888/game/:gameId/:playerId/playword/:x/:y/:direction/:word
```
**Params**
```
gameId=[string]      // Game Name
playerId=[string]    // Player Name playing the word
x=[integer]          // X position of the start of the word
y=[integer]          // Y position of the start of the word
direction=[string]   // Direction the word will be played. Valid values are "HORIZONTAL" or "VERTICAL"
word=[string]        // Word to play. Must include tiles already on board.
```
**Example**
Player JohnDoe plays the word QUAKER starting at tile (7,5) horizontally in TestGame.
```url
[GET] http://SkrabbleHost:8888/game/TestGame/JohnDoe/playword/7/5/HORIZONTAL/QUAKER
```

----  
### Skip Turn
  Skip a turn.
  
**URL**
```
[GET] http://SkrabbleHost:8888/game/:gameId/:playerId/skipturn
```
**Params**
```
gameId=[string]      // Game Name
playerId=[string]    // Player Name who is skipping
```
**Example**
Player JohnDoe skips their turn in TestGame.
```url
[GET] http://SkrabbleHost:8888/game/TestGame/JohnDoe/skipturn
```

----
### Swap Tiles
  Swap tiles from your shelf with tiles in the tile bag. Tiles are drawn before the tiles to be swapped are added back to the bag.
  
**URL**
```
[GET] http://SkrabbleHost:8888/game/:gameId/:playerId/swaptiles/:tiles
```
**Params**
```
gameId=[string]      // Game Name
playerId=[string]    // Player Name who is skipping
tiles=[string]       // Tiles which the player wishes to swap
```
**Example**
Player JohnDoe swaps the tiles "EAZ" for three random tiles in the tile bag in game TestGame.
```url
[GET] http://SkrabbleHost:8888/game/TestGame/JohnDoe/swaptiles/EAZ
```

## FAQ
* How does Double/Triple Letter/Word score get calculated?
  Double/Triple Letter score tiles are always calculated before Double/Triple word score tiles. 

* If I play a word that intersects a tile that already had Double/Triple Letter/Word score, does it get counted again?
  No. Score modifiers are only applied for new tiles. If a new tile is on a modifier, and creates  multiple words, the modifier will be applied to both words. 
  

## Example Board JSON
  ```
  {
  "_id":"test200",
  "state":"Starting",
  "turn": {
    "number":1,
    "player":"Player1"
    },
  "skipCounter":0,
  "playerOne":{
    "playerId":"Player1",
     "tileShelf":["U","R","I","O","K","E","I"],
     "score":0,
     "turns":[
      {
        "turn": 1,
        "pos": "(7,7)",
        "dir": "HORIZONTAL",
        "action": "AAEE",
        "score": 4
     ]
    }
  "playerTwo":{...},
  "board":[
      [
        {"coords":"(0,0)","modifier":"tripleWordScore","text":"TRIPLE WORD SCORE","tile":""}, ...],
        {"coords":"(0,1)","modifier":"","text":"","tile":"X"}, ...],
        ...
      ],
      [ ... ],
      ...
    ],
  "tileBag":["E","E", ... ,"X","Q","Z"],
  "tileSetProperties":[
      {"letter":"E","score":1,"count":12},
      {"letter":"A","score":1,"count":9},
      ...
    ]
  }
  ```
