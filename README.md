# squadmortar
This is the repository for the website 
[squadmortar.xyz](squadmortar.xyz), a mortar/rocket calculator for the game 
[Squad](https://joinsquad.com/). After Squad patch 6.0 the maker of this software took the repository and the forementioned site down. However the project had been saved before the takedown by some squad players of community [Farssi.net](https://farssi.net). It is by far the best calculator there is for squad. All credits for the orginal author of the calculator. Currently the calculator is being hosted at [sqmortar.farssi.net](https://sqmortar.farssi.net)

### Community Guides
- [Written guide](https://www.reddit.com/r/joinsquad/comments/q1e2la/squadmortarxyz_a_mortar_calculator_guide/) by /u/MENA_Conflict
- [Video guide](https://www.youtube.com/watch?v=QvGjmXwB6xc) by /c/ZonkyTheDonkey 
  - Green grid elevation described incorrectly and wrong Hell Cannon kill radius value given (it's 10m not 20m), but otherwise good.


### Resources and similar projects

- The Squad SDK available from the [Epic Games Launcher](https://store.epicgames.com/en-US/)
- [Squad Game Mechanics Post](https://www.reddit.com/r/joinsquad/comments/7xeh9g/squad_game_mechanics_post/) and the associated [Game Statistics](https://drive.google.com/drive/folders/1LMXaYgBLMxSoOCWiNNn6oy1TVKAQMbaH)
- http://squad.ujkl.ru/ - the calculator that earlier versions were based on


### Major features

- Unlimited weapons, unlimited number of targets: All info is displayed on the target element itself, making it easier to prepare and switch between a large number of targets.
- Uses heightmaps exported from Squad SDK
- Control via mouse and modifiers: 
  - double-click to create target
  - ctrl-click to delete targets
  - shift-double-click to create new weapon
  - shift-click to toggle weapon activation
  - click and drag to move either
- User settings saved in/loaded from local browser storage
- Weapon selection
  - standard mortar: using (US) milliradians, everything as expected
  - mortar technical: same general properties as the standard mortar, but using degrees instead of milrads
  - UB32 rocket pod technical: A precalculated firing table (distance x height -> angle) allows using this weapon effectively even with significant height differences, which is common on maps where it appears.
  - Hell Cannon: using degrees for both elevation and direction
- Weapon options
  - Weapon placement helper: Show grid index/keypads while dragging weapon
  - Weapon placement label: Shows the current location of the weapon marker
- List of placed Weapons
  - Weapon Identifier
  - Grid location
  - Extra weapon height: Add height over landscape to get accurate data when firing from tall buildings or bridges. Negative values translate to added target height i.e. to fire on top of roofs.
  - Pick weapon (^): Activates this weapon and deactivates all others
  - Toggle weapon (o): Toggles activation status of this weapon
- Target options
  - Aim grid (#): The green grid shows 1° bearing and 5 mil elevation lines, giving you an idea how much you have to change aim in order to get good spacing on rounds.
  - Spread circle (o): The single blue circle shows an approximation of the spread due to inaccuracy (50 MOA). It likely follows normal distribution, though this is unconfirmed.
  - Splash radii ((o)): The red circles show 100 and 25 damage radii, which are instant kills and guaranteed bleed, respectively. If spread is turned on, it shows these from the edge of spread, giving you the worst case damage spread.
  - Weapon to target distance (m) 
  - Compact mode: Shows only last three digits for elevation and no direction (and only high arc solution for Hell Cannon)
  - Target placement helper: Show grid index/keypads while dragging target
  - Target placement label: Shows the current location of the target marker
  - Font size: Adjustable size of target text
- Basic touch options and extra buttons
  - Show extra buttons in collapsed mode (m.)
  - Deletion mode (-T): When toggled on, click or touch (including the first instance in a double-click/-touch) will now remove targets. Replacement for modifier on touch input devices.
  - Remove all targets (-∀T): Removes all targets on click.
- Server
  - Experimental multi user session that synchronizes weapon and target locations. It does not synchronize map or weapon selection.
  - Currently no default server offered due to low benefit
  - Session Id (only shown after creating/joining)
  - Session Id input field to join an existing session created by another user
  - Controls for creating/joining/leaving a session.
  - click on your own name (prefixed with '> ') to change it



### Limitations
- Most extra target features assume the ground around the target is flat and some are (close) geometric simplifications.
- Several properties of the rocket pod calculations add up to make the target area quite inaccurate in edge cases - a good understanding of the ballistics largely alleviates this issue.
- Large asset sizes are potentially prohibitive for slow/traffic limited connections.

## Development
In order to work on this you need to have [node](https://nodejs.org/en/) installed and available on PATH

    npm install
    npm run dev
    npm run server

## Architecture

#### Outer Layer (html/ts/css)
- canvas and most assets in plain html
- holding asset and DOM references
- canvas event handling
- canvas rendering
- setup of react state machine
- standard css used for styling and to support some simple layout patterns

#### Inner Layer
- react: multiple instances for UI overlays
- react-redux
- redux
  - thunks
  - middleware for message replication
- ECS style handling of world state within redux
    - properties split into self-contained components
    - message types align with components
    - simplified from a full ECS due to limited scope

Redux is the core of this latest rewrite and was chosen as a state machine and message queue for use with replication features with the significant benefit of integrating well with react. The entry point is still a classic HTML/CSS/JS website and an 'SPA' by mere coincidence. The canvas and related assets are left out of react to maintain fine-grained control over side-effects and performance. 
