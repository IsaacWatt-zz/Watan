# Watan

A C++ Implementation of the popular board game Settlers of Catan
This project has recieived academic credit through the University of Waterloo, and thus the code is only available upon request. 

The executable is provided, feel free to read through the following documentation and play Watan!

## Interface

<table align="center">
    <tr>
        <td>
            <img src="https://github.com/IsaacWatt/Watan/blob/master/docs/board1.png" width="400px">
        </td>
        <td>
            <img src="https://github.com/IsaacWatt/Watan/blob/master/docs/command-line2.png" width="400px">
        </td>
    </tr>
</table>

## Table of Contents

- [How to Play](#how-to-play)
  - [Basic Setup](#basic-setup)
  - [Resources and Buildables](#resources-and-buildables)
  - [Rolling and Results](#rolling-and-results)
  - [The Goose](#the-goose)
  - [Trading](#trading)
  - [Commands](#commands)
  
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
Basement: 1 Brick, 1 Energy, 1 Glass, 1 Wifi (worth 1 victory point)
House: 2 Glass, 3 Heat (worth 2 victory points)
Tower: 3 Brick, 2 Energy, 2 Glass, 1 Wifi, 2 Heat (worth 3 victory points)
Dev Card: 1 Heat, 1 Energy, 1 Wifi
```

A `road` can only be placed adjacent to either a House, Basement or Tower of the same colour, or connected to another road of the same colour. A `Basement` can only be build on corner tiles or `Properties` if there is not another Basement, House or Tower already there or adjacent to it. Also a `Basement` can only be build if it is attached to a road of the same colour (with the exception of the first 2 that are placed at the beginning of the game). Thus, each Building must be a minimum path of length 2 apart. A `Basement` can be upgraded to a `House` (giving 2 resources of that type as opposed to 1). A `House` can be upgraded to a `Tower` (3 resources as opposed to 2). A Dev Card is a "mystery" card which may be one of the following: 

```
vp: gives you one additional victory point
monopoly: to be played with the name of a resource. Once played, every player must give you all of their cards of that resource type. 
knight: allows you to move the Goose, and steal from a player adjacent to the tile where you placed the Goose. 
```

#### Rolling and Results
At the beginning of each turn a `Builder` chooses between `loaded` and `fair` dice, and rolls. The role result follows basic probability if the die are `fair`, however if `loaded` die are chosen, the `Builder` gets to choose the result of the roll (an int from 2 to 12 inclusive). If a 2, 3, 4, 5, 6, 8, 9, 10, 11, or 12 is rolled, all `Builders` with a `Basement`, `House`, or `Tower` adjacent to that property recieve x resources depending on the type of propery of that titles type. If a 7 is rolled, every player with > 7 cards must discard half their deck (rounding down on odd numbers). Also, the `Builder` whom rolled the 7 gets to place the `Goose` whose significance is described below. 

#### The Goose
The goose is a piece that is placed on the board (initally on the `Park` tile, and thus has no effect on the game). When either a 7 is rolled, or a `knight` card is placed, that player gets to move the `Goose` piece. The tile in which the player places the `Goose` on, robs all player adjacent to that tile from receiving resources from it. Thus, if the `Goose` is on a `Wifi` tile associated with the die roll of 5, then if as 5 is rolled no one adjacent to that tile recieves any `Wifi`. Also, the player whom placed the `Goose` on that tile gets to steal exactly 1 random resource card from any `Builder` who is adjacent to that tile. The `Goose` then stays on that tile until it is moved by another `Builder`. 

#### Trading
on each `Builders` turn, they are allowed to propose trades with other `Builders`, or trade with the bank for a 4:1 ratio. That is, they would have to give 4 of one resource type to recieve 1 of any resource. When trading with another `Builder` you can run the command `trade <colour> <give> <take>` where `<colour>` is the colour of the builder whom you want to trade, `<give>` is the resource you are giving, and `<take>` is the resource you are taking. The table is then turnt to the Builder with colour `<colour>` who can acceot or deny your trade. 

#### Commands
Each `Builder` can run any of the following commands on their turn as many times as they would like, with their turn ending when they enter `next`. The commands are as follows: 
```
board
status  
residences
build-road <path#>
build-res <housing#>
improve <housing#>
trade <colour> <give> <take>
trade-bank <give> <take>
buy-dev
devs
play-dev <card (vp, monopoly (card), knight)>
next
save <file>
help
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
