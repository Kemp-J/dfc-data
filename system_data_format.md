# Exported system data format

## Folder structure

The system data is exported to a folder named "System".

## System data index

Within the System folder is a file `_system.json` which provides an entry point into the system data. This is a json format file containing the following:

- "source_version": The version of the core rules PDF (taken from the PDF filename).
- "ship_rules": The filename of the data file containing ship special rules.
- "weapon_rules": The filename of the data file containing weapon special rules.

Example:

```json
{
    "source_version": "2.3.1",
    "admiral_abilities": "abilities.json",
    "ship_rules": "ship_rules.json",
    "weapon_rules": "weapon_rules.json"
}
```

## Admiral abilities data file

The generic admiral abilities are contained in a data file in json format.

The file contains a list of admiral abilities that may be used by Admirals in any fleet. Each entry consists of a dictionary containing "Cost" (string — see 'Ability cost format'), "Name" (string), and "Effect" (string).

```json
[
    {"Cost": "2AP", "Name": "Contain Reactor", "Effect": "When a player would roll for Explosion, instead of rolling, make the result of an Explosion roll (for you or your opponent) a 2."}
]
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
