# Sx-Tubes

A Spigot/Bukkit plugin for PaperMC that enables using configurable pipe systems for managing storage.

The storage and logistics aspect in Minecraft is lacking a lot. The SxTubes system aims to fix that. 

Heavily inspired by Craftbook Pipes but aims to be lighter on the CPU and provide more features. Also some inspiration
was taken from ChestShop in terms of working with signs.

## Core concepts

The tube system is set up via building a network of devices connected by glass. Such a network is called "Grid".

There are 4 main types of device required for a Grid to operate:

1. **Control blocks** -- Configurable control terminals for the Grid
2. **Senders** -- send items
3. **Receivers** -- receive items
4. **Requesters** -- request a particular item from the Grid

## Managing a Grid

Creating a Grid consists of:
- Building it
- Configuring it

Use signs with special commands to configure the devices.

## Building a Grid

In general the simplest setup goes like this:

1. Determine where you'd want the Grid control center of your network to be.
   At least one Control Block exposed from at least 3 sides is required.
2. Build the Senders and the Receivers
3. Connect all the devices with glass tubes.
4. Configure the devices with signs.
5. Initialize the Grid.
   This scans the Grid starting from the executing Control Block and actually initializes all the devices.

Note: When a Grid is enabled, it's indestructible. If the Grid is shut down, all its elements lose protection.
Note: Some Grid Devices can be reconfigured dynamically, without switching the whole Grid off (see device descriptions).

## Configuring and Controlling a Grid

There are two ways to configure and control a Grid:
1. Control Signs -- used for configuring Grid Elements (blocks) on-site
2. Console Commands -- used for various administrative commands

## Control Signs

A Minecraft Sign attached to a Grid Device defines and configures it.

Interacting with such a Sign with a Stick either assembles the device and turns it on or performs another function according
to the command written on the Sign.

Many of the commands have additional parameters that allow for flexible granularity in setting up how your Grid works.

The lines on a Sign usually serve the following functions:

1. The command itself, all SX-Tube commands have the following format: `[tube:<command>]` where `<command>` is one of the commands
   applicable to the device in question.
2. Command parameters.
3. One of the following, depending on the command:
  - Item description for item filter commands
  - Command output
4. Command output.

See each command description for details.

## Permissions

The owner of a Grid automatically possesses all permissions.
All other players must be explicitly given the permissions.

Altering permissions for a particular player is possible with `/tubes-trust` and `/tubes-untrust` commands.
Initially only the Grid owner can do that.

Currently, there are the following permissions:

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
| `ALLOW_CONFIGURE_SEND`              | allow the player to configure `[tube:send]` signs                                                                                                                                 |
| `ALLOW_SEND`                        | allow the player to execute `[tube:send]` signs                                                                                                                                   |
| `ALLOW_REQUEST`                     | allow the player to use `[tube:request]` signs                                                                                                                                    |
| `ALLOW_OPEN_SEND_INVENTORIES`       | allow the player to open inventories of Senders                                                                                                                                   |
| `ALLOW_OPEN_RECEIVE_INVENTORIES`    | allow the player to open inventories of Receivers                                                                                                                                 |
| `ALLOW_OPEN_REQUEST_INVENTORIES`    | allow the player to open inventories of Requesters                                                                                                                                |
| `ALLOW_OPEN_OVERFLOW_INVENTORIES`   | allow the player to open inventories of Overflows                                                                                                                                 |
| `ALLOW_DYNAMIC_DEVICE_MODIFICATION` | allow the player to break signs on certain devices for re-programming them. For this permission to work, the allowance must also be enabled on the Grid via `[tube:config] ddm:t` |
| `ALLOW_DYNAMIC_TUBE_EXTENSION`      | (NOT IMPLEMENTED)                                                                                                                                                                 |

------------------------------------------------------------------------------------------------------------------------

## Grid Elements and Their Commands on Control Signs

------------------------------------------------------------------------------------------------------------------------

## Glass

Glass is the tubes material. Build tubes to extend your grid.
Colored Glass only connects to its own color. Hint: use this feature to create compact buses of different Grids.

------------------------------------------------------------------------------------------------------------------------

## Control Device

Serves as the main port of interaction with the Grid. A Grid requires at least one Control Block to be created.

Structure: a Control Block with a Control Sign on it.

A Control Block supports up to 6 Control Signs on it, but one Control Block per one Control Sign is recommended for clarity.

Hint: You may want to group you Control Devices together in one room for the comfort of access.
Hint: On-spot quick setups are of course also possible and the ability to use a single Control Block for executing the basic commands helps with that.
Hint: The material of Control Block can be set in the config.

### Control Device Sign Commands

------------------------------------------------------------------------------------------------------------------------

#### Control Device: Scan

This is the first ever command you execute for a Grid. It effectively creates the grid and initializes it.

```
[tube:scan]
<params>
<short grid info>
<owner>
```

Parameters:

`id` (optional) -- an integer to serve as a fixed ID for this grid. If the ID is unavailable, the Grid will not initialize. Example: `id:42`. 
If this parameter is not specified then every time upon Grid creation it is going to get a new ID by adding +1 to the last one.

Output:

- `<short grid info>` -- Some technical info about the Grid, its id, the length of the glass tubes and the amount of receivers
- `<owner>` -- Who's the Grid owner

!!!NOTE!!!

Scanflow:

Grid scanning is implemented with an algorithm that walks the tubes from the Control Block it was executed on.
This phenomenon is nicknamed "Scanflow" and it influences the order in which devices are added to the Grid.
For Receivers that means establishing an order in which they attempt to receive an item that is being sent.
If the maximum Grid size limit is set in the config, the Scanflow stops upon reaching it.

------------------------------------------------------------------------------------------------------------------------

#### Control Device: Name

Gives a name to the Grid. The name is a just a hint that can be used for an easier identification. It is not required
to be unique.

```
[tube:name]
<empty-line>
<name>
<empty-line>
```



------------------------------------------------------------------------------------------------------------------------

#### Control Device: Remove

Executing this command removes the associated Grid. If the command block is associated with any Grid, the ID of the Grid
is going to be displayed in the info line. Upon successful removal the info line is going to display the "OK" status.

```
[tube:remove]
<empty-line>
<info>
<empty-line>
```

Output:

`<info>` describes either the currently linked Grid or a status message.

------------------------------------------------------------------------------------------------------------------------

#### Control Device: Config

An advanced command used for detailed parametrization of various grid workings.

```
[tube:config]
<params>
<params>
<params>
```

Note: All lines take in parameters.

Currently implemented parameters:

- `ddm:<t|f>` -- Enables or disables Dynamic Device Modification. When this setting is on, some devices can be added, removed
and modified on-spot, without turning the Grid off or re-scanning it.

------------------------------------------------------------------------------------------------------------------------

## Sender Device 

Sends items into the Grid.

Structure:

1. Is a __Minecraft Sticky Piston__
2. Connected to a Grid by a Glass Tube
3. Has an inventory block in front of it, for example a Chest.
4. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Sender Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

### Sender Device Sign Commands

```
[tube:send]
<params>
<status>
<status-details>
```

Params:

- `t:<int>` -- Enables auto-send in the specified amount of game ticks. The amount can't be lower than what's specified in the plugin config to prevent spam sends.
- `i:<t|f>` -- Enables or disables inventory-triggered sends. Sends when a player puts something into the associated inventory.
- `s:<int>` -- Enables spread action where the associated number is the amount of items in each spread batch for a single Receiver.

For example params `t:10000,i:t` would make it so that the Sender automatically sends once in 10000 ticks, but at the same time also
instantly reacts to any events of putting the stuff manually into it.

Also, Sender device can be activated by powering it with a Redstone signal.

------------------------------------------------------------------------------------------------------------------------

## Receiver Device

Receive items from the Grid.

Structure:
1. Is a __Minecraft Piston__
2. Connected to a Grid by a Glass Tube
3. Has an inventory block in front of it, for example a Chest.
4. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Receiver Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

### Receiver Device Sign Commands

All Receiver Piston Control Signs first line must be one of the following:

```
[tube]
<empty-line>
<item-name>
<empty-line>

```

The command is simply `[tube]` because in this case the Receiver Device is automatically configured with the simplest configuration possible.
This option is provided for simplicity because the amount of receivers in a system is assumed to be high.

Lines:

- `<item-name>` -- the name of the item that's intended to be put into this receiver

------------------------------------------------------------------------------------------------------------------------

## Overflow Device

Collects everything that doesn't fit into other specified Receivers.

Structure:
1. Is a __Minecraft Piston__ 
2. Connected to a Grid by a Glass Tube
3. Has an inventory block in front of it, for example a Chest.
4. Has a Control Sign on it that describes and configures the device.

### Overflow Device Sign Commands

All Overflow Piston Control Signs first line must be one of the following:

```
[tube:overflow]
<empty-line>
<empty-line>
<empty-line>

```


------------------------------------------------------------------------------------------------------------------------

## Requester Device

Requesting particular items from the whole Grid.

Structure:
1. Is a __Minecraft Piston__
2. Connected to a Grid by a Glass Tube
3. Has an inventory block in front of it, for example a Chest.
4. Has a Control Sign on it that describes and configures the device.

If Dynamic Device Modification is on, Requester Device can be reconfigured in a working Grid. Just destroy the Control Sign and re-program it.

### Requester Device Sign Commands

```
[tube:request]
<params>
<item-name>
<amount:int>
```

The following parameters are available:

- `p:<tags>` (optional) -- specifies the Grid element types and the order in which they are polled. The following tags are available:
  - `r` -- receivers
  - `s` -- senders
  - `o` -- overflow
  - `q` -- other requesters
  
  If the `p` argument is not specified for the Requester then the default one from the config is used. The default value is `rso`.
  If one of the tags is not specified, the corresponding type of elements does not get polled during the request.
  
  Example: `p:qo` ensures that the poll is performed first among the other Requesters and then among the Overflows, Senders or Receivers are not polled.

The following additional configuration lines are possible:

- `<item-name>` (required) -- the item name to poll for.
- `<amount>` (required) -- the amount of the item to poll for.
