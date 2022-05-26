### Sx-Tubes

A Spigot/Bukkit plugin for PaperMC that enables using configurable pipe systems for managing storage.

The storage and logistics aspect in Minecraft is lacking a lot. The SxTubes system aims to fix that. 

Heavily inspired by Craftbook Pipes but aims to be lighter on the CPU and provide more features. Also some inspiration
was taken from ChestShop in terms of working with signs.

### Core concepts

The Tubes system is setup via building networks made of connecting tubes and various other elements. Such a network
is called "Grid".

There are 4 main types of components required for a Grid to operate.

1. Connecting glass -- all tubes are built from glass and serve their only true purpose -- linking the various parts
of the system together and controlling scanflow (see scanflow)
2. Control blocks -- devices that serve as configurable control terminals for the Grid
3. Senders -- devices that send items
4. Receivers -- devices that receive items
5. Requesters -- devices that allow requesting a particular item from the grid.

There are also variations of these.

Creating a Grid consists of:
- Building it
- Configuring it

All the in-game configuration of a Grid is performed via signs with various commands written on them. See the section
on Grid Elements and Their Commands for more insight into what blocks support what commands.

#### Building a Grid

1. Build the Senders and the Receivers where you want them, connect with glass Tubes.
2. Determine where you'd want the control center of your network to be. At minimum it needs to be able to accommodate
at least one Control Block that is exposed from at least 3 sides. Plan for more if it's a major network because you'd
likely want to have access to more commands than just the basic ones.
3. Configure the Grid via signs.
4. Initialize the Grid. This scans the Grid starting from the executing Control Block and actually initializes all the
devices

Note: When the Grid is enabled, it protects all its elements from unauthorized destruction so all these parts can be
exposed to public places. If the Grid is shut down, all its elements lose protection and can be broken/changed by anyone.

Certain Grid Devices allow being reconfigured dynamically, without switching the whole Grid off (see device descriptions).

#### Configuring and Controlling a Grid

There are two ways to configure and control a Grid:
1. Control Signs -- this method is used for configuring Grid Elements (blocks) in-place
2. Console Commands -- this method is used for various administrative commands

#### Permissions

The owner of a Grid automatically possesses all permissions. However, to any other player the permissions must
be given separately either by the owner or by another player who has the permission to alter permissions.

Currently there are the following permissions:

---

| Permission                          | Meaning                                                                                                                                                                           |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ALLOW_SCAN`                        | allow to re-scan the Grid                                                                                                                                                         |
| `ALLOW_REMOVE`                      | allow to remove the Grid                                                                                                                                                          |
| `ALLOW_RENAME`                      | allow to rename the Grid                                                                                                                                                          |
| `ALLOW_CONFIGURE`                   | allow the player to execute `[tube:config]` signs (NOT IMPLEMENTED)                                                                                                               |
| `ALLOW_GET_INFO`                    | allow the player to get grid info via the `/tube-grid-info <grid-id>` console command                                                                                             |
| `ALLOW_MAKE_OWNER`                  | pass ownership (NOT IMPLEMENTED)                                                                                                                                                  |
| `ALLOW_ALTER_PERMISSIONS`           | allow the player to change permissions                                                                                                                                            |
| `ALLOW_CONFIGURE_SEND`              | allow the player to execute `[tube:send]` configuration signs                                                                                                                     |
| `ALLOW_SEND`                        | (NOT IMPLEMENTED)                                                                                                                                                                 |
| `ALLOW_REQUEST`                     | allow the player to use `[tube:request]` signs                                                                                                                                    |
| `ALLOW_OPEN_SEND_INVENTORIES`       | (NOT IMPLEMENTED)                                                                                                                                                                 |
| `ALLOW_OPEN_RECEIVE_INVENTORIES`    | (NOT IMPLEMENTED)                                                                                                                                                                 |
| `ALLOW_OPEN_OVERFLOW_INVENTORIES`   | (NOT IMPLEMENTED)                                                                                                                                                                 |
| `ALLOW_DYNAMIC_DEVICE_MODIFICATION` | allow the player to break signs on certain devices for re-programming them. For this permission to work, the allowance must also be enabled on the Grid via `[tube:config] ddm:t` |
| `ALLOW_DYNAMIC_TUBE_EXTENSION`      | (NOT IMPLEMENTED)                                                                                                                                                                 |

------------------------------------------------------------------------------------------------------------------------

## Grid Elements and Their Commands on Control Signs

------------------------------------------------------------------------------------------------------------------------

#### Glass

Serves for constructing the connecting Tubes. Glass does not currently accept any commands but Stained Glass Tubes
can be used for separating Grids -- differently colored Glass Tubes do not intersect. Use this feature to create
compact buses of different Grids.

------------------------------------------------------------------------------------------------------------------------


#### Control Sign

A Minecraft Sign that is slapped on one of the Grid Elements and contains one of the valid commands for that type of
Element. A Sign must be a part of one of the Grid Devices, it doesn't do anything on its own.

Control Signs are usually interacted with with a Stick. Such interaction might either execute the command written on
the Control Sign or toggle it on/off, depending on the particular command.

Many of the commands have additional parameters that allow for flexible granularity in setting up how your Grid works.

The lines on a sign usually serve the following functions:

1. Command, all SX-Tube commands have the following format: `[tube:<command>]` where `<command>` is one of the commands
applicable to the device in question.
2. Command parameters.
3. One of the following, depending on the command:
    - Item description for item filter commands
    - Command output
4. Command output.

See each command description for details.

------------------------------------------------------------------------------------------------------------------------

#### Control Device

Serves as the main port of interaction with the Grid. A Grid requires at least one Control Block to be created.

Structure: a Control Block with a Control Sign on it.

A Control Block supports up to 6 Control Signs on it, if you're bare on resources or space, but one Control Block per
one Control Sign is recommended for clarity. In general SX-Tubes leans more toward having dedicated control rooms with the whole set
of needed Control Blocks to control the Grid. In-field quick setups are of course also possible and the ability to use
a single Control Block for executing the basic commands helps with that.

The base block that represents the Control Block can be configured, but the default suggested variants are Netherite Block,
Crying Obsidian Block or Iron Block. Selecting the block type allows to naturally gate the ability of a player to build
and expand Grids depending on their progress in the game.

##### Control Device Sign Commands

#### Control Device: Scan

The first ever command you execute for a Grid. It effectively creates the grid and initializes it.

```
[tube:scan]
<params>
<short grid info>
<owner>
```

The following parameters are supported:

`id` -- an integer to serve as a fixed ID for this grid. If the ID is unavailable, the Grid will not initialize. If this
parameter is not specialized then every time upon Grid creation it is going to get a new ID.

Output:

- `<short grid info>` -- Some technical info about the Grid, its id, the length of the glass tubes and the amount of receivers
- `<owner>` -- Who's the Grid owner

Scanflow:

Grid scanning is implemented with an algorithms that walks the tubes from the Control Block it was executed on.
This phenomenon is nicknamed "Scanflow" and it influences the order in which devices are added to the Grid.
For Receivers that means establishing an order in which they attempt to receive an item that is being sent.

------------------------------------------------------------------------------------------------------------------------

#### Control Device: Name

Gives a name to your grid. The name is a hint that can be used for an easier identification of your Grid. But otherwise Grid id is superior.

```
[tube:name]
<empty-line>
<name>
<empty-line>
```



------------------------------------------------------------------------------------------------------------------------


#### Control Device: Remove

Executing this command removes the associated Grid. If the command block is associated with any Grid, the ID of the Grid
is going to be displayed in the info line. Upon a successful removal execution the info line is going to display the "OK" status.

```
[tube:remove]
<empty-line>
<info>
<empty-line>
```

Output:

`<info>` describes either the currently linked Grid or a status message.

#### Control Device: Config

An advanced command used for detailed parametrization of grid workings.

```
[tube:config]
<params>
<params>
<params>
```

All lines take in parameters.

Currently implemented parameters:

- `ddm:<t|f>` -- Enables or disables Dynamic Device Modification. When that setting is on some devices can be added, removed
and modified without turning the Grid off or re-scanning it.

------------------------------------------------------------------------------------------------------------------------

#### Sender Device 

Senders are Grid Elements that are responsible for sending items into the Grid.
The central block of a Sender tube device is the Sender Piston, which is just a normal Minecraft Sticky Piston. It is recognized
as a tube Sender device if it is:
1. Connected to a Grid by a Glass Tube
2. Has an inventory block in front of it, for example a Chest.
3. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Sender Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

##### Sender Device Sign Commands

```
[tube:send]
<params>
<status>
<status-details>
```

Params:

- `t:<int>` -- Enables auto-send in the specified amount of game ticks. The amount can't be lower than what's specified in the plugin config to prevent spam sends.
- `i:<t|f>` -- Enables or disables inventory-triggered sends. Used for executing sends when a player puts something into the associated inventory.
- `s:<int>` -- Enables spread action where the associated number is the amount of items in each spread batch for a single receiver

For example params `t:10000,i:t` would make it so that the Sender automatically sends once in 10000 ticks, but at the same time also
instantly reacts to any events of putting the stuff manually into it.

Also Sender device can be activated by powering it with a Redstone signal.

------------------------------------------------------------------------------------------------------------------------

#### Receiver Device

Receivers are Grid Elements that are responsible for receiving items from the Grid.
The central block of a Receiver tube device is the Receiver Piston, which is just a normal Minecraft Piston. It is recognized
as a tube Receiver device if it is:
1. Connected to a Grid by a Glass Tube
2. Has an inventory block in front of it, for example a Chest.
3. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Receiver Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

##### Receiver Device Sign Commands

All Receiver Piston Control Signs first line must be one of the following:

```
[tube]
<empty-line>
<item-name>
<empty-line>

```

The command is simply `[tube]` because in this case the Receiver Device is automatically configured with the simplest configuration possible.
This option is provided for simplicity because the amount of receivers in a system is assumed to be high.
In this case only the line 2 plays any role, and it must contain the filter for which items to receive.


Lines:

- `<item-name>` -- the name of the item that's intended to be put into this receiver

------------------------------------------------------------------------------------------------------------------------

#### Overflow Device

Receivers are Grid Elements that are responsible for receiving items from the Grid.
The central block of a Receiver tube device is the Receiver Piston, which is just a normal Minecraft Piston. It is recognized
as a tube Receiver device if it is:
1. Connected to a Grid by a Glass Tube
2. Has an inventory block in front of it, for example a Chest.
3. Has a Control Sign on it that describes and configures the device.


##### Overflow Device Sign Commands

All Receiver Piston Control Signs first line must be one of the following:

```
[tube]
<empty-line>
<item-name>
<empty-line>

```

The command is simply `[tube]` because in this case the Receiver Device is automatically configured with the simplest configuration possible.
This option is provided for simplicity because the amount of receivers in a system is assumed to be high.
In this case only the line 2 plays any role, and it must contain the filter for which items to receive.


Lines:

- `<item-name>` -- the name of the item that's intended to be put into this receiver

------------------------------------------------------------------------------------------------------------------------

#### Requester Device

Requesters are Grid Elements that are responsible for requesting particular items from the whole Grid.
The central block of a Requester tube device is the Requester Piston, which is just a normal Minecraft Piston. It is recognized
as a tube Requester device if it is:
1. Connected to a Grid by a Glass Tube
2. Has an inventory block in front of it, for example a Chest.
3. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Requester Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

##### Requester Device Sign Commands

```
[tube:request]
<params>
<item-name>
<amount:int>
```

The following parameters are available:

- `p:<tags>` -- where "<tags>" is any combination of Grid element type tags that controls the order in which the Grid elements get polled
  for the request. The following tags are available:
  - `r` -- receivers
  - `s` -- senders
  - `o` -- overflow
  - `q` -- other requesters
  
  If the `p` argument is not specified for the Requester then the default one from the config is used. The default value is `rso`.
  If one of the tags is not specified, the corresponding type of elements does not get polled during the request. For example, specifying
  the `p` parameter like `p:qo` would ensure that the poll is first performed among the other Requesters and then among the Overflows, while
  no Senders or Receivers are getting polled.

The following additional configuration lines are possible:

- `<item-name>` is the item name to poll for.
- `<amount>` is the amount of the item to poll for.
