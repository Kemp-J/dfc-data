# Exported system data format

## Folder structure

The system data is exported to a folder named "System".

## System data index

Within the System folder is a file `_system.json` which provides an entry point into the system data. This is a json format file containing the following:

- "source_version": The version of the core rules PDF (taken from the PDF filename).
- "game_sizes": The filename of the data file containing game size specifications.
- "admirals": The filename of the data file containing generic Admirals and Admiral abilities.
- "ship_rules": The filename of the data file containing ship special rules.
- "weapon_rules": The filename of the data file containing weapon special rules.

Example:

```json
{
    "source_version": "2.3.1",
    "game_sizes": "game_sizes.json",
    "admirals": "admirals.json",
    "ship_rules": "ship_rules.json",
    "weapon_rules": "weapon_rules.json"
}
```

## Game sizes data file

The game size definitions are contained in a data file in json format.

The file contains a list of dictionaries containing game size definitions. Each dictionary contains "Name" (string), "MinPts" (integer), "MaxPts" (integer, or null for no limit), and "MaxGroups" (integer).

Example:

```json
[
    {"Name": "Clash", "MinPts": 1001, "MaxPts": 2000, "MaxGroups": 20}
]
```

## Admirals data file

Details of the generic Admirals and Admiral abilities are contained in a data file in json format.

The file contains a dictionary with keys:

- "admirals": A list of dictionaries containing generic Admiral details. Each dictionary contains "Level" (integer), "Cost" (integer), and "UnavailableIn" (list of strings giving game sizes that Admiral cannot be used in).
- "abilities": A list of generic Admiral abilities that may be used by Admirals in any fleet. Each entry consists of a dictionary containing "Cost" (string — see 'Ability cost format'), "Name" (string), and "Effect" (string).

Example:

```json
{
    "admirals": [
        {"Level": 3, "Cost": 40, "UnavailableIn": ["Skirmish"]}
    ],
    "abilities": [
        {"Cost": "2AP", "Name": "Contain Reactor", "Effect": "When a player would roll for Explosion, instead of rolling, make the result of an Explosion roll (for you or your opponent) a 2."}
    ]
}
```

### Ability cost format

The `"Cost"` field on ability dictionaries is always a string. The possible forms are:

| Value | Meaning |
|---|---|
| `"NAP"` (e.g. `"2AP"`) | Fixed cost of N points |
| `"NAP*"` (e.g. `"2AP*"`) | Base cost of N points with a conditional modifier described in the effect text |
| `"*AP"` | Variable cost defined in the fleet abilities table (used for abilities whose cost depends on context) |

## Ship and weapon rule data files

The ship and weapon rules are contained in data files in json format.

Each file contains a list of rules. Each rule consists of a dictionary containing "Name" and "Text" keys, along with an optional "Example" key. All have string values.

Example weapon rules data file:

```json
[
    {"Name": "Corruptor-X", "Text": "When this Weapon successfully inflicts damage, place X Battalions on the target."},
    {"Name": "Critical-X", "Text": "Each of this Weapon's criticals increases the damage of that hit by X.", "Example": "A Critical-2 weapon rolls 2 dice to attack, one is a hit and the other is a critical. The hit deals its normal damage while the critical increases the damage it causes by 2."}
]
```
