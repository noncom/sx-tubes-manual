
### Version 1.0.4, 2024-10-26

- Fix Teleport crash with a workaround of the PaperMC bug that crashes the server when sound playback is issued from an async task (https://github.com/noncom/sx-tubes/issues/5)
- Fix non-critical NPE in interaction with the `/backpack` (https://github.com/noncom/sx-tubes/issues/6)
- Fix a Tubes-disabling NPE that occurs sometimes when restoring from the database after a crash (https://github.com/noncom/sx-tubes/issues/4)
- Unassign the `SxTubeTrustTabCompleter` completer from `/tubes-template`, prevents some non-critical exceptions (https://github.com/noncom/sx-tubes/issues/1)
- Prevent `/tubes-trust` and `/tubes-untrust` from attempting to work when not enough args were passed (https://github.com/noncom/sx-tubes/issues/7)
- Add `/tubes-about`

### Version 1.0.3

- Fix the Bukkit/Paper exception when creating an ItemStack with amount 0

### Version 1.0.2

- Implement Tubes kit
- Frame recipe fix
- Optimize some code

### Version 1.0.1a

##### TODO Fix

- Player name on the grid

##### Fixes


### Version 1.0.1

##### Fixes

- Item Filter Frame is now found properly when it's on the far end of a Double Chest
- Make the absence of grid notification optional in withGrid. This now is used to prevent the "No grid" messages when working with non-grid Item Frames.
- Removed the "No grid" notification message when clicking on a sign with a sign
- Rename now correctly works only with line 2 with of the sign
