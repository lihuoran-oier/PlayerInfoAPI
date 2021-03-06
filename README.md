# PlayerInfoAPI
-------------

[中文](https://github.com/TISUnion/PlayerInfoAPI/blob/master/README_cn.md)

a MCDReforged plugin to provide API for getting player information

Now support 1.14+ new JSON string format

(Thank Pandaria for providing the regular expression)

## Usage

Use imp.load_source() to load PlayerInfoAPI in your plugin first

```
from imp import load_source
PlayerInfoAPI = load_source('PlayerInfoAPI','./plugins/PlayerInfoAPI.py')
```

for MCDReforged use `server.get_plugin_instance()` to get PlayerInfoAPI instance

```
PlayerInfoAPI = server.get_plugin_instance('PlayerInfoAPI')
```

### PlayerInfoAPI.convertMinecraftJson(text)

Convert Minecraft style json format into a dict

Minecraft style json format is something like these:

- `Steve has the following entity data: [-227.5d, 64.0d, 12.3E4d]`
- `[-227.5d, 64.0d, 231.5d]`
- `Alex has the following entity data: {HurtByTimestamp: 0, SleepTimer: 0s, ..., Inventory: [], foodTickTimer: 0}`

It will automatically detect if there is a `<name> has the following entity data: `. If there is, it will erase it before converting

Args:
- text: A data get entity or other command result that use Minecraft style json format

Return:
- A parsed json result. It can be a `dict`, a `list`, a `int` or a `None`

Samples:

- Input `Steve has the following entity data: [-227.5d, 64.0d, 231.5d]`, output `[-227.5, 64.0, 123000.0]`

- Input `{HurtByTimestamp: 0, SleepTimer: 0s, Inventory: [], foodTickTimer: 0}`, output `{'HurtByTimestamp': 0, 'SleepTimer': 0, 'Inventory': [], 'foodTickTimer': 0}`

### PlayerInfoAPI.getPlayerInfo(server, name, path='', timeout=5)

Call `data get entity <name> [<path>]` and parse the result

If it's in MCDReforged and rcon is enabled it will use rcon to query

Args:
- server: The Server Object
- name: Name of the player who you want to get his/her info
- path: An optional `path` parameter in `data get entity` command
- timeout: The maximum time to wait the data result if rcon is off. Return `None` if time out

Return:
- A parsed json result. It can be a `dict`, a `list`, a `int` or a `None`

Please refer to the Player.dat page on minecraft wiki
[player.dat format](https://minecraft.gamepedia.com/Player.dat_format)

## Example

```
def onServerInfo(server, info):
  if info.content.startswith('!!test'):
    result = PlayerInfoAPI.getPlayerInfo(server,info.player)
    server.say("Dim:"+str(result["Dimension"])+"Pos:"+str(result["Pos"][0])+","+str(result["Pos"][1])+","+str(result["Pos"][2]))
```

you can also refer to the demo of Here plugin with this API(in newapi branch)

[Here(Demo)](https://github.com/TISUnion/Here/tree/newapi)
