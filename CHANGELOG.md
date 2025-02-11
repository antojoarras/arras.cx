# Breaking Changes

## September 19th, 2022

If you've created a private server with this template before September 19th, 2022, the health bars will be weird on entities without shields.

Find the following in your server.js:
```
    return this.amount / this.max;
```
Replace it with
```
    return this.max > 0 ? this.amount / this.max : -1;
```


## November 24th, 2020

If you've created a private server with this template before November 24th, 2020, it will not show a class tree when you press T.

Open server.js, search for `position: positionInfo,`, and below it, add the line:
```
upgrades: e.upgrades.map(r => ({ tier: r.tier, index: r.index })),
```

Also, find the following in your server.js:
```
        if (set.UPGRADES_TIER_1 != null) { 
            set.UPGRADES_TIER_1.forEach((e) => {
                this.upgrades.push({ class: e, level: c.TIER_1, index: e.index,});
            });
        }
        if (set.UPGRADES_TIER_2 != null) { 
            set.UPGRADES_TIER_2.forEach((e) => {
                this.upgrades.push({ class: e, level: c.TIER_2, index: e.index,});
            });
        }
        if (set.UPGRADES_TIER_3 != null) { 
            set.UPGRADES_TIER_3.forEach((e) => {
                this.upgrades.push({ class: e, level: c.TIER_3, index: e.index,});
            });
        }
```
and replace it with
```
        if (set.UPGRADES_TIER_1 != null) { 
            set.UPGRADES_TIER_1.forEach((e) => {
                this.upgrades.push({ class: e, tier: 1, level: c.TIER_1, index: e.index });
            });
        }
        if (set.UPGRADES_TIER_2 != null) { 
            set.UPGRADES_TIER_2.forEach((e) => {
                this.upgrades.push({ class: e, tier: 2, level: c.TIER_2, index: e.index });
            });
        }
        if (set.UPGRADES_TIER_3 != null) { 
            set.UPGRADES_TIER_3.forEach((e) => {
                this.upgrades.push({ class: e, tier: 3, level: c.TIER_3, index: e.index });
            });
        }
```

## June 22nd, 2019

If you've created a private server with this template before April 10th, 2019, it will not support the new protocol which is now standardized!

Open the lib/fasttalk.js in your server. Copy the code from the lib/fasttalk.js in this project onto your project. This will let your server use the new encoding, which will save bandwidth.

## May 25th, 2019

If you've created a private server with this template before May 25th, 2019, the minimap and leaderboard will no longer work without this update!

Replace the entire `broadcast` function in your server.js, which you can find by searching `const broadcast = (() => {`. Replace it with the one in the template.

Find `needsFullLeaderboard: true` in your server.js and replace it with `needsNewBroadcast: true`

Find `let broadcastingGuiStuff = setInterval(() => broadcast(socket), 250)` and replace it with `broadcast.subscribe(socket)`

Find `clearTimeout(broadcastingGuiStuff)` and replace it with `broadcast.unsubscribe(socket)`

You may also remove this block of code in server.js
```
case 'z': { // leaderboard desync report
    if (m.length !== 0) { socket.kick('Ill-sized level-up request.'); return 1; }
    // Flag it to get a refresh on the next cycle
    socket.status.needsFullLeaderboard = true
} break
```

## April 10th, 2019

If you've created a private server with this template before April 10th, 2019, it will not support the new protocol which will be standardized in the future!

Open the lib/fasttalk.js in your server. Copy the code from the lib/fasttalk.js in this project onto your project. This will let your server use the new encoding, which will save bandwidth.

## December 30th, 2018

If you've created a private server with this template before December 30th, 2018, it will not support custom body shapes without this update!

Find the following in your server.js:
```
if (set.SHAPE != null) {
    this.shape = set.SHAPE;
}
```
Replace it with
```
if (set.SHAPE != null) {
    this.shape = typeof set.SHAPE === 'number' ? set.SHAPE : 0
    this.shapeData = set.SHAPE;
}
```
Then find
```
shape: e.shape
```
Replace it with
```
shape: e.shapeData
```
And you should be able to create custom bodies by putting an array of coordinates as the coordinates of the custom body. You can see an example of this in the TESTBED tank in definitions.js. It's also possible to put a SVG path data instead of simply an array, which can offer more customization.

## November 12th, 2018

If you've created a private server with this template before November 12th, 2018, the minimap and leaderboard will no longer work without this update!

Showing all entities on the minimap have been permanently removed due to lag issues. If you only want teammates and bosses to still be shown, however, you can replace the entire `broadcast` function, which you can find by searching `const broadcast = (() => {` and replacing it with the one in the template.

## July 2nd, 2018

If you've created a private server with this template before July 2nd, 2018, respawning will no longer work without this update:

Find the following in your server.js:
```
if (needsRoom !== 0 && needsRoom !== 1) { socket.kick('Bad spawn request.'); return 1; }
// Bring to life
socket.status.deceased = false;
// Define the player.
if (players.indexOf(socket.player) != -1) { util.remove(players, players.indexOf(socket.player));  }
// Free the old view
if (views.indexOf(socket.view) != -1) { util.remove(views, views.indexOf(socket.view)); socket.makeView(); }
socket.player = socket.spawn(name);     
// Give it the room state
if (needsRoom) { 
    socket.talk(
        'R',
        room.width,
        room.height,
        JSON.stringify(c.ROOM_SETUP), 
        JSON.stringify(util.serverStartTime),
        roomSpeed
    );
}
```
Replace it with
```
if (needsRoom !== -1 && needsRoom !== 0) { socket.kick('Bad spawn request.'); return 1; }
// Bring to life
socket.status.deceased = false;
// Define the player.
if (players.indexOf(socket.player) != -1) { util.remove(players, players.indexOf(socket.player));  }
// Free the old view
if (views.indexOf(socket.view) != -1) { util.remove(views, views.indexOf(socket.view)); socket.makeView(); }
socket.player = socket.spawn(name);     
// Give it the room state
if (!needsRoom) { 
    socket.talk(
        'R',
        room.width,
        room.height,
        JSON.stringify(c.ROOM_SETUP), 
        JSON.stringify(util.serverStartTime),
        roomSpeed
    );
}
```
and your server will be back to normal!
