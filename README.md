# Maps
Proposed API for building maps compatible with the loot ecosystem. 

**Objective:** Define a common format for maps so game designers and developers can build maps that are compatible with Loot.

## Principles
We aim to follow the principles set down by Loot:
1. This is a primitive. It is designed to play nicely with many other primitives.
2. Let game developers/dungeon masters make as many important decisions as possible, for example.. 
  a. Monsters 
  b. Visual style 
  c. Spawn points
3. Expose everything via Solidity API (vs proprietary formats or focusing on the art)

## NFT Structure
A Map NFT is made up of three parts:

* ID (Number) - Each map has a unique identifier (integer)
* Image (svg) - Each map has a unique image representing its layout.
* Metadata - Each map has metadata representing it which can be accessed from getters on the smart contract: 
* a. id (int) - The ID of the map 
* b. layout (string) - A string representing walls, walkable floor, interactable objects (e.g. doors), and points of interest. 
* c. environment (int) - An integer depicting an environment for the map
* d. name (string) - The name of the map

Developers can choose to ignore any of these.

## Map Structure
Maps are always square (e.g. 10x10) and can be any size > 4x4.

Maps must contain at least one 'walkable' area that is at least 2 tiles.

Maps contain a list of 'tiles' which have different characteristics:
* Walls: Represented by '0'
* Floors: Represented by '1' (where players can walk and move around)
* Points: Represented by '2'
* Encounters: Represented by '3'

Maps have a name (e.g. 'Den of the Twins') to give each one a sense of permanence. Some names will be repeated across the ecosystem, which is ok.

Maps have an environment (e.g. '2') to give a sense of ambiance. This is akin to a 'tileset' in 2D RPG's.

Developers can choose to ignore any of the above attributes and use whatever is useful.

All other attributes (e.g. monsters, spawn point, etc) to be defined by the developer or game designer via separate entities.

## What could I build with this ?
We focused on a simple API (uint256) so that developers/designers can easily copy it and start building their own maps. 

This format supports both generative maps (see Dungeons) and manually/offline created maps. It is purposefully simple to allow for tons of interpretation on the format.

Other entities could lay metadata on top of this map to indicate where players can find water, where they can ride mounts, etc. 

We hope that people add Encounters, Monsters, Rewards, Traps, Locks, Environmental Art, Flying, Running, Walking... even full 2D and 3D environments! 

We hope that incentives are built over time to reward people that hold maps (e.g. a proceeds of the rewards could go to the holders).

This is just the beginning.

## Usage
We expect most developers will want to create a 2D array containing to represent the dungeon: For example:
```
[
  ['0', '0', '0', '0', '0'],
  ['0', '1', '1', '0', '1'],
  ['0', '1', '0', '2', '1'],
  ['0', '2', '1', '1', '0'],
  ['0', '1', '3', '1', '0'],
  ['0', '0', '0', '0', '0']
]
```
Would generate:

![Example map](https://github.com/threepwave/loot-maps/raw/main/images/array-example.png)

You can generate a 2D array from the getLayout() int by looping through the integer, character-by-character.

For example (javascript):
````
// Assume you start with the integer API call
let map = String(getLayout())
const size = Math.sqrt(map.length)

let mapArray = []
let count = 0

for(let y = 0; y < size; y++) {
  let row = []
  for(let x = 0; x < size; x++) {
    row.push(map[count])
    count++
  }
  mapArray.push(row)
}
````

For more details, see **API - getLayout()** below.

## API 

The dungeon contract exposes the following endpoints to allow developers to use and query dungeons in their games and applications.

We'll use this dungeon for all examples below:
````
[
  ['0', '0', '0', '0', '0', '0', '0', '0'],
  ['0', '0', '0', '0', '1', '1', '1', '0'],
  ['0', '1', '1', '0', '1', '3', '1', '0'],
  ['0', '1', '1', '0', '1', '1', '1', '0'],
  ['0', '1', '1', '0', '1', '1', '1', '0'],
  ['0', '1', '1', '1', '2', '1', '0', '0'],
  ['0', '1', '1', '0', '0', '0', '0', '0'],
  ['0', '0', '0', '0', '0', '0', '0', '0'],  
  ['0', '0', '0', '0', '0', '0', '0', '0']
]
````

![API Example](https://github.com/threepwave/loot-maps/raw/main/images/api-example.png)


### Map Geometry

**getLayout()** (int) - Returns a uint256 representing all tiles in the map as integers. The top left corner of the map starts at (0, 0), which corresponds to the first number in the integer.

Using our example map:
"00000000000013100110111001101110011011001112100011000000000000000000000"

Each character represents a 'tile' which have different charactertics:
* Walls: Represented by '0'
* Floors: Represented by '1' (where players can walk)
* Interactables: Represented by '2'
* Points: Represented by '3'


### Dungeon Attributes

**getName()** (string) - Returns the name of the map. In our example: `Den of the Twins.`

**getEnvironment()** (int) - Returns the environment of the map. There are six total environments and each affects the color of the original SVG. Developers can choose to embrace or ignore these environments. In our example: `0`

**numInteractables()** (int) - Returns the number of 'interactables'' present in the dungeon. The definition of interactable is purposefully left vague to be interpreted by the developer. In our example: `1`.
*Useful for: Querying for dungeons with a specific number of doors.*

**numPoints()** (int) - Returns the number of points present in the map. In our example: `1`.
*Useful for: Querying for dungeons with a specific number of points, determining how many spawn points are listed by default.*

**numFloors()** (int) - Returns the number of accessible floor tiles in the map. This includes interactables and points of interest. In our example: `26`
*Useful for: Determining probabilities of objects based on the amount of floor space. Making sure a given map is suitable for a given party size.*

**size()** (int) - Returns the overall size of the map. In this case, we have an 8x8 dungeon, so in our example: `64`
*Useful for: Querying for maps based on size*

*Note: Maps are always square. Developers can also determine the size of the maps by taking the sqrt of the length of the int*


# License

The Maps API is licensed under a CC-0 (Free use) license (see: LICENSE.md).

Anyone can take this API and produce as many interesting maps as they see fit.

Everyone has read access to every map. Therefore, maps can be used by anyone, in any app/game/context. However they want. 

I hope that over time, people will build incentives for maps holders but that is not baked into the contract or the design.