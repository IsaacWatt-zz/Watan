# Watan

A C++ Implementation of the popular board game Settlers of Catan
This project has recieived academic credit through the University of Waterloo, and thus the code is only available upon request. 

The executable is provided, feel free to read through the following documentation and play Watan!

## Table of Contents

- [How to Play](#how-to-play)
  - [Basic Setup](#basic-setup)
  - [Resources and Buildables](#resources-and-buildables)
  
- [How to run Watan](#how-to-run-watan)
  - [Command Line Interface](#command-line-interface)
  - [Game file format](#game-file-format)
  - [Board file format](#board-file-format)

### How to Play

#### Basic Setup 
Watan is a grid structure made up of 19 tiles. Each corner of a hex is refered to as a `Property` and each edge is
a `Path`. In Watan, there are 4 players, called `Builders` that take turn rolling virtual dice to determine which players will be given resources for that turn. `Builders` can trade amongst each other to gain advantages, and complete their goals. The first `Builder` to obtain 10 victory points wins the game. 

#### Resources and Buildables
In Watan their are 5 resources, 
`Brick`, `Energy`, `Glass`, `Heat`, `Wifi`, and `Park`. Each of these are present on the board, and thus if a `Builder` is on one of these tiles they will recieve that respective resource. There is also a `Park` tile, however it is resourceless and any Builder adjacent to it will not receive any resource from it. There are also items for `Builders` to make, called `Buildables`. These include (with their associated price), 

```
Road: 1 Heat, 1 Wifi
Basement: 1 Brick, 1 Energy, 1 Glass, 1 Wifi
House: 2 Glass, 3 Heat
Tower: 3 Brick, 2 Energy, 2 Glass, 1 Wifi, 2 Heat
Dev Card: 1 Heat, 1 Energy, 1 Wifi
```

### How to run Watan

#### Command Line Interface

To Play Watan, run the command `./Watan <cmd> <cmd> ...` in the terminal. `<cmd>` can be any of:
- `-seed xxx` sets the random number generation seed to `xxx`. 
- `-random-board` which generates a random board. 
- `-load f` loads the game saved in file `f`. 
- `-board b` loads the board saved in file `b`. 

#### Game file format

Each game stores which players turn it is, each of the 4 builder's data, the board, and the tile in which the goose is on. This looks as follows:
```
<turn>
<builder0>
<builder1>
<builder2>
<builder3>
<board>
<goose>
```
Where `<turn>` is the number of the builder whos turn it is, 

`<builderX>` is the data associated with the builder X. This includes their resources, owned roads, and owned properties (along with their respecitve locations). 
Thus each builder has a set of data which looks as follows: 
```
<Brick> <Energy> <Glass> <Heat> <Wifi> r <Roads> h <Properties>
```
where `<Brick>` is the number of bricks the builder owns (similarly with `<Energy` and so on). `<Roads>` is a list of numbers that represent the locations of where that builder owns roads, and `<Properties>` is a list of key value pairs of the form 
`n T` where `n` is the location of the propery and `T` is the type of property. 

`<board>` is the [board](#board-file-format) that is being used. 
`<goose>` is a number that represents which tile the goose is on. 

#### Board file format

Each board stores the order of tiles and their respective types, along with their associated probabilities. The format is as follows: 

```
0 represents Brick
1 represents Energy
2 represents Glass
3 represents Heat
4 represents Wifi
5 represents Park
```
Each resource is followed by its probablity of being rolled from top to bottom. A sample board may look as such: 

`1 10 0 3 3 5 1 4 5 7 3 10 2 11 1 3 3 8 0 2 0 6 1 8 4 12 1 5 4 11 3 4 4 6 3 9 3 9`

This would have the very top tile as an Energy with probability 10, followed by a Brick with probability 3, ect. 
