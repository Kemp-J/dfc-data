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
    "ship_rules": "ship_rules.json",
    "weapon_rules": "weapon_rules.json"
}
```

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
