# Exported fleet data format

## Folder structure

There is a folder for each fleet PDF. The fleets are:

- UCM
- Scourge
- PHR
- Shaltari
- Resistance
- Bioficer

## Fleet data index

Within each fleet folder is a file `_fleet.json` which provides an entry point to that fleet's data. This is a json format file containing the following:

- "source_version": The version of the fleet PDF (taken from the PDF filename). This is a date formatted as YYMMDD.
- "abilities": The abilities available to Fleet and Famous Admirals. This is a list of dictionaries with keys of "Cost" (string - see 'Ability cost format'), "Name" (string), and "Effect" (string).
- "fleet_admirals": The Fleet Admirals available to the fleet. This is a list of dictionaries with keys of "Name" (string), "Level" (integer), "Cost" (integer), "FleetAbilities" (integer), and "Abilities" (list of dictionaries with content as per the "abilities" key above).
- "launch_assets": A list of Launch Asset stat line dictionaries. Each dictionary has one of two sets of keys. Fighter type launch assets have keys of "Load" (string), "Thrust" (integer), and "Rerolls" (integer). All other launch assets have keys of "Load" (string), "Thrust" (integer), "Att" (integer), "Lock" (string: either "X+" where X is a number between 1 and 6, or "*"), "DMG" (integer), "Type" (string: one of "K", "E", or "C"), and "Special" (list of strings).
- "systems": A list of filenames for the data files containing ship systems lists. See 'Systems list' for more details.
- "deployable_features": A list of filenames for the data files containing Deployable Feature data.
- "famous_admirals": A list of filenames for the data files containing Famous Admiral data.
- "heroes": A list of filenames for the data files containing Hero ship data.
- "ships": A list of filenames for the data files containing ship data.

Example:

```json
{
    "source_version": "260327",
    "abilities": [
        {"Cost": "2AP", "Name": "Colonial Legions", "Effect": "After the Battalion Combat step of the Asset Phase, target a Dropsite or Feature. If any enemy Battalions completely removed any friendly Battalions from that Dropsite or Feature, place 1 Battalion on that Dropsite or Feature."}
    ],
    "fleet_admirals": [
        {"Name": "UCM Captain", "Level": 1, "Cost": 25, "FleetAbilities": 1, "Abilities": [{"Cost": "2AP", "Name": "Dedicated Survey Teams", "Effect": "When you activate a Group of a single Ship, that Ship may Survey a Dropsite and attack with a single weapon this round (but still cannot launch Assets)."}]}
    ],
    "launch_assets": [
        {"Load": "Light Torpedo", "Thrust": 6, "Att": 4, "Lock": "2+", "DMG": 1, "Type": "K", "Special": ["Penetrator"]},
        {"Load": "Fighters", "Thrust": 13, "Rerolls": 1}
    ],
    "systems": [],
    "deployable_features": [
        "aegis_platform.json"
    ],
    "famous_admirals": [
        "granite_halsey.json"
    ],
    "heroes": [
        "rhiannon_major.json"
    ],
    "ships": [
        "santiago.json",
        "lysander.json"
    ]
}
```

## Deployable Feature data file

Each Deployable Feature has its own data file in json format. The file is named the same as the Deployable Feature name, in lowercase with spaces replaced by underscores and all characters that are invalid for filenames removed.

The file contains the following:

- "profile": A dictionary containing "PTS" (integer or null), "Type" (string, the name of the Deployable Feature), "ES" (string: "X+" where X is a number), "KS" (string: "X+" where X is a number), and "Special" (list of strings).
- "weapons": A list of dictionaries, each containing "Name" (string), "Scan" (integer or null), "Att" (integer), "Lock" (string: either "X+" where X is a number between 1 and 6, or "*"), "DMG" (integer), "Type" (string: one of "K", "E", or "C"), and "Special" (list of strings). Example: `[{"Name": "Electrowave Caster", "Scan": 8, "Att": 3, "Lock": "2+", "DMG": 1, "Type": "E", "Special": ["Close Action", "Escape Velocity", "Status"]}]`.
- "load": A list of dictionaries, each containing "Load" (string), "Launch" (integer), and "Special" (list of strings). Example: `[{"Load": "Medium Torpedo", "Launch": 1, "Special": ["Limited-4"]}]`.
- "rules": A string containing the additional rules for the Deployable Feature.

Example Deployable Feature data file for the UCM Torpedo Platform:

```json
{
    "profile": {
        "PTS": null,
        "Type": "Torpedo Platform",
        "ES": "5+",
        "KS": "5+",
        "Special": []
    },
    "weapons": [],
    "load": [
        {
            "Load": "Medium Torpedo",
            "Launch": 1,
            "Special": [
                "Limited-4"
            ]
        }
    ],
    "rules": "When an attacking Group or Asset/s attack would cause this feature to be removed, if it has any remaining Torpedoes, the attacking opponent may remove one additional Feature and any Battalions assigned to it from this Dropsite."
}
```

## Ship data file

Each ship class has its own data file in json format. The file is named the same as the ship class name, in lowercase with spaces replaced by underscores and all characters that are invalid for filenames removed.

The file contains the following:

- "class": String, the name of the ship class.
- "type": String.
- "points": Integer.
- "tonnage": One of "L", "M", "H", "C".
- "base_size": Integer giving the size in mm.
- "profile": A dictionary containing "Thrust" (integer, unit: inches), "Scan" (integer, unit: inches), "Sig" (integer, unit: inches), "Hull" (integer), "ES" (string), "KS" (string), "BS" (string), "G" (string, "X" or "X-Y", where X and Y are integers), and "Special" (list of strings). Example: `{"Thrust": 12, "Scan": 6, "Sig": 0, "Hull": 2, "ES": "6+", "KS": "6+", "BS": "-", "G": "2-4", "Special": ["Descent", "Cloak-1", "Rare", "Vanguard-6\""]}`.
- "weapons": A list of weapon entries. Each entry is one of:
  - A **single-profile weapon**: a dictionary containing "Name" (string), "Arc" (string: one or more of "F", "S", "R", "SL", "SR", "FN", "RN", and "B", separated by slashes), "Att" (integer), "Lock" (string: either "X+" where X is a number between 1 and 6, or "*"), "DMG" (integer), "Type" (string: one of "K", "E", or "C"), and "Special" (list of strings). Example: `{"Name": "Barracuda Missile Bays", "Arc": "F/S/R", "Att": 2, "Lock": "4+", "DMG": 1, "Type": "K", "Special": ["Close Action"]}`.
  - A **multi-profile weapon**: a dictionary containing "Name" (string) and "Profiles" (a list of two or more profile dictionaries). Each profile dictionary contains "Name" (string), "Arc" (string), "Att" (integer), "Lock" (string), "DMG" (integer), "Type" (string), and "Special" (list of strings) as per a single-profile weapon.
  
  Both forms may optionally include "Rule" (string, present when the ship has a rule named the same as the weapon), "Cost" (integer, present on optional/refit weapons), and "Replaces" (list of strings, present on optional/refit weapons specifying which weapons are replaced if this one is taken).
- "load": A list of dictionaries, each containing "Load" (string), "Launch" (integer), and "Special" (list of strings). Example: `[{"Load": "Dropships", "Launch": 1, "Special": []}]`. Load entries may optionally have "Cost" and/or "Replaces" attributes. Cost specified a cost to take that load and Replaces specifies the names of load that it replaces if taken.
- "upgrades": An optional list of upgrade entries. Each entry is one of:
  - A plain upgrade item: a dictionary containing "Name" (string), "Cost" (integer), and "Effects" (list of profile modifiers, see 'Profile modifiers'). The ship may independently choose whether to take this item.
  - A choice group: a dictionary containing "min" (integer), "max" (integer), and "options" (list of plain upgrade item dictionaries). The ship must pick at least "min" and at most "max" options from the group. Example: `{"min": 0, "max": 1, "options": [{"Name": "Cloaking Keel", "Cost": 15, "Effects": [...]}, {"Name": "Engine Upgrade", "Cost": 25, "Effects": [...]}]}` means the ship may take at most one of the listed options.
- "rules": An optional list of dictionaries, each containing "Name" and "Text", both strings. Example: `[{"Name": "Stealth Drop", "Text": "When this Group launches its Dropships, 2 Dropships are needed to place 1 Battalion. If this Group contains a single ship, each Dropship only places a Battalion on a roll of a 4+."}]`.
- "systems": An optional dictionary specifying the systems list and selection constraints for this ship. Only present on ships that have purchaseable systems (e.g. Bioficer Dreadnoughts, Resistance Cruisers). See 'Systems list' for more details.
- "admiral_abilities": An optional list of dictionaries, each containing "Cost" (string — see 'Ability cost format'), "Name" (string), and "Effect" (string). Example: `[{"Cost": "2AP", "Name": "Telemetry Link", "Effect": "When you activate another friendly Group in Orbit, that Group increases its total movement (after orders) by 4\" this round."}]`.
- "usable_in": An optional list of dictionaries defining the fleets that this ship is usable in. The ship is assumed to be usable as given in the fleet the ship belong to regardless of this list, and the owning fleet will not be present in the list. Each dictionary has "Fleet" (the name of a fleet it can be used in, can also be "*" to indicate all fleets) and "Modifiers" (a list of profile modifiers that apply when used in the named fleet, see 'Profile modifiers'). If the list contains a "*" entry as well as entries naming specific fleets, it should be interpreted as available for all fleets with additional modifiers applicable to the named fleets. Example: `[{"Fleet": "UCM, "Modifiers": [{"Target": "Special", "Type": "append", "Value": "Rare"}]}]`.
- "known_ships": A list of strings.
- "flavour": A string.

The following is an example ship data file for the Lysander class:

```json
{
    "class": "Lysander",
    "type": "Stealth Lighter",
    "points": 25,
    "tonnage": "L",
    "base_size": 30,
    "profile": {
        "Thrust": 12,
        "Scan": 6,
        "Sig": 0,
        "Hull": 2,
        "ES": "6+",
        "KS": "6+",
        "BS": "-",
        "G": "2-4",
        "Special": ["Descent", "Cloak-1", "Rare", "Vanguard-6\""]
    },
    "weapons": [
        {"Name": "Barracuda Missile Bays", "Arc": "F/S/R", "Att": 2, "Lock": "4+", "DMG": 1, "Type": "K", "Special": ["Close Action"]}
    ],
    "load": [
        {"Load": "Dropships", "Launch": 1, "Special": []}
    ],
    "upgrades": [],
    "rules": [
        {"Name": "Stealth Drop", "Text": "When this Group launches its Dropships, 2 Dropships are needed to place 1 Battalion. If this Group contains a single ship, each Dropship only places a Battalion on a roll of a 4+."}
    ],
    "admiral_abilities": [],
    "known_ships": ["Hope's Spark", "Blue Shade", "Eternal Darkness", "Azure Night", "Leprechaun", "Silent Blade"],
    "flavour": "The Lysander class Lighter has been a lynchpin in the UCM’s covert ops since it entered service in 2669. It is the UCMF’s first fully cloaked warship, pushing multiple 1st gen classified stealth systems to their current limits of scale. The class is equipped for extended infiltrations including limited surface landings to scout potential invasion sites and support Resistance groups.\nInitially the class was Level-6 classified and generally reserved for Marine Force Black - the UCM’s most elite troops under the Office of Naval Intelligence. Rumours circulated among the admiralty, where demands for access to such a tool grew from whispers into shouts. As the Battle for Earth loomed, the Lysander’s existence was confirmed and production ramped up. While still precious assets, limited numbers are now available to support regular frontline operations and first strike incursions."
}
```

## Famous Admiral data file

Each Famous Admiral has its own data file in json format. The file is named the same as the Admiral's name, in lowercase with spaces replaced by underscores and all characters that are invalid for filenames removed.

The file is structured the same as for ships, with the following additions to the top-level dictionary:

- "admiral": The Admiral name as a string.
- "admiral_level": The Admiral's level as an integer.
- "admiral_points": The cost of the Admiral (combined with the ship cost for "points").
- "ship_points": The cost of the ship (combined with the Admiral cost for "points").
- "fleet_admiral_abilities": The number of fleet Admiral abilities the Admiral may take.

Additionally, the "known_ships" key is not present.

The following is an example data file for the Admiral named "Granite" Halsey:

```json
{
    "admiral": "\"Granite\" Halsey",
    "admiral_level": 5,
    "class": "Washington",
    "type": "Supercarrier",
    "admiral_points": 105,
    "ship_points": 530,
    "points": 635,
    "tonnage": "C",
    "base_size": 60,
    "profile": {
        "Thrust": 6,
        "Scan": 12,
        "Sig": 14,
        "Hull": 24,
        "ES": "3+",
        "KS": "3+",
        "BS": "5+",
        "G": "1",
        "Special": ["Aegis-6", "Command Ship-2", "Reinforced Armour"]
    },
    "weapons": [
        {"Name": "UF-9000 Mass Driver Turret", "Arc": "F/S", "Att": 2, "Lock": "2+", "DMG": 2, "Type": "K", "Special": ["Critical-1", "Penetrator"]},
        {"Name": "UF-9000 Mass Driver Turret", "Arc": "F/S", "Att": 2, "Lock": "2+", "DMG": 2, "Type": "K", "Special": ["Critical-1", "Penetrator"]},
        {"Name": "UF-4200 Mass Driver Turret Core Battery", "Arc": "F/S", "Att": 6, "Lock": "4+", "DMG": 1, "Type": "K", "Special": ["Fusillade-3", "Volley-2"]},
        {"Name": "UF-4200 Mass Driver Turrets", "Arc": "F/S", "Att": 4, "Lock": "4+", "DMG": 1, "Type": "K", "Special": ["Fusillade-2", "Volley-2"]},
        {"Name": "Leviathan Missile Bays", "Arc": "F/S/R", "Att": 8, "Lock": "3+", "DMG": 1, "Type": "K", "Special": ["Close Action"]}
    ],
    "load": [
        {"Load": "Fighters & Bombers", "Launch": 6, "Special": ["Alt-1"]},
        {"Load": "Heavy Bombers", "Launch": 4, "Special": ["Alt-1"]}
    ],
    "upgrades": [],
    "rules": [
        {"Name": "Fighter Command", "Text": "Fighters, Bombers, & Heavy Bombers launched from this ship may be placed up to 8\" away instead of 6\"."}
    ],
    "fleet_admiral_abilities": 2,
    "admiral_abilities": [
        {"Cost": "4AP", "Name": "Master Tactician", "Effect": "At the end of this Group’s activation, discard a Pass Token and target another friendly Group of L or M Tonnage. If you do, that Group may turn up to 45 degrees, then move up to a quarter of its Thrust, and then each Ship in it may attack with a single weapon."}
    ],
    "flavour": "Head of the most powerful institution mankind has ever known, Supreme Admiral Jacob 'Granite' Halsey is famous for his steely, brisk, borderline-offensive, flare. His rank grants him a permanent seat on the High Council, but his record there is of extensive truancy, not that anyone dares raise that. A career-sailor, his career began as a teenage rating aboard the ageing grand battleship UCMS Virginia, which he would, thirty-two years later, captain. His remaining on the bridge to the last as the venerable ship was wrecked by Shaltari privateers earned him a promotion to commodore, along with facial scarring and a lifetime's hatred for the 'filthy rotten stinking echidnas'. After another two decade's service as the UCMF's most battlehardened, pre-Reconquest commander, he attained mastery of the entire UCMF.\nPreferring to lead from the front, Halsey was present in the opening hours of the Battle for Earth, where, for the ninth time in his career, he had his CIC destroyed around him. Forced to command from the Shield of Aurum's auxiliary CIC, he remained a pivotal force in that titanic engagement, personally scoring nineteen capital ship kills in the first forty-eight hours.\nHalsey is a fixture on whichever frontline is hottest, and although he's burst some more blood vessels on his pockmarked face, he's at the razor's edge of command."
}
```

## Hero data file

Each Hero has its own data file in json format. The file is named the same as the Hero's name, in lowercase with spaces replaced by underscores and all characters that are invalid for filenames removed.

The file is structured the same as for ships, with the following additions to the top-level dictionary:

- "hero": The Hero name as a string.
- "ship": The ship name as a string.

The "class" key is not present and is replaced by the above two keys.

The following is an example data file for the Hero named Rhiannon Major:

```json
{
    "hero": "Rhiannon Major",
    "ship": "Leaden Triad",
    "type": "Heavy Destroyer",
    "points": 71,
    "tonnage": "L",
    "base_size": 30,
    "profile": {
        "Thrust": 8,
        "Scan": 6,
        "Sig": 4,
        "Hull": 6,
        "ES": "3+",
        "KS": "4+",
        "BS": "6+",
        "G": "1",
        "Special": [
            "Hero",
            "Unique"
        ]
    },
    "weapons": [
        {"Name": "Artillery Cannon", "Arc": "F", "Att": 1, "Lock": "4+", "DMG": 2, "Type": "K", "Special": ["Calibre-H/C", "Fusillade-1", "Low Power"]},
        {"Name": "Artillery Cannon", "Arc": "F", "Att": 1, "Lock": "4+", "DMG": 2, "Type": "K", "Special": ["Calibre-H/C", "Fusillade-1", "Low Power"]},
        {"Name": "Artillery Cannon", "Arc": "F", "Att": 1, "Lock": "4+", "DMG": 2, "Type": "K", "Special": ["Calibre-H/C", "Fusillade-1", "Low Power"]}
    ],
    "load": [],
    "upgrades": [],
    "rules": [],
    "admiral_abilities": [],
    "known_ships": [
        "- Leaden Triad"
    ],
    "flavour": "Leaden Triad is a new, experimental vessel designed and built by the restored Eden Drive Yards Corporation. This pre-war colossus was ended by the Scourge invasion, but this reincarnation is based in its recaptured shipyards, and the new corporation has all the recovered tech of its predecessor, including its work on the largest chemical guns ever built. Though they never got a chance to enter service, three prototypes were recovered and have been installed aboard a unique heavy destroyer hull.\nCaptain Rhiannon Major, already the UCM's finest naval gunnery officer at just 34 years of age, has been given command. Though not a natural leader, her icy bravery and uncanny intelligence are the natural fit to shepherd this project into combat testing. Already, she has demonstrated the devastating power of this antique technology when writ large by deftly annihilating two Bioficer battlecruisers. These guns hurl house-sized shells at vast muzzle energy with negligible power drain, allowing full manoeuvrability and systems use while firing weapons far exceeding this ship's tonnage grade. However, despite Major's calculations and brilliant heat management, frequent and ultra-costly barrel changes seem inevitable."
}
```

## Ability cost format

The `"Cost"` field on ability dictionaries (in both `"abilities"` and `"admiral_abilities"`) is always a string. The possible forms are:

| Value | Meaning |
|---|---|
| `"NAP"` (e.g. `"2AP"`) | Fixed cost of N points |
| `"NAP*"` (e.g. `"2AP*"`) | Base cost of N points with a conditional modifier described in the effect text |
| `"*AP"` | Variable cost defined in the fleet abilities table (used for abilities whose cost depends on context) |

## Profile modifiers

A profile modifier is a dictionary consisting of "Target" (string), "Type" (string, one of "add", "subtract", "replace", "append"), and "Value" (integer or string). "Target" is the name of a profile field ("Thrust", "Scan", "Sig", "Hull", "ES", "KS", "BS", "G", "Special"). Integer values are compatible with the "add", "subtract", and "replace" types. String values are compatible with the "replace" and "append" types.

ES, KS, and BS are treated numerically without the "+" suffix for the purpose of "add" and "subtract". Thrust, Scan, and Sig are treated numerically without the inches suffix for the purposes of "add" and "subtract". For example, if the ship has an ES stat of "5+" and a profile modifier of `{"Target": "ES", "Type": "subtract", "Value": 1}` then the new ES value is "4+".

The Special field should be treated as if it were a comma-separated list of values for the purpose of "append", with a value of "-" indicating an empty list. This means that if Special were "-" and an "append" is applied then the appended value would become the only value in Special. For example, given a Special of "-" and a modifier that appends "Stealth", the new Special would be "Stealth", not "-, Stealth".

## Systems list

### Systems list files

The fleet index gives the filenames of systems lists available to the fleet. Each file contains one list of systems (for example, Bioficer Dreadnought systems or Resistance Cruiser systems).

The file contains the following:

- "name": The name of this list of systems. This usually indicates what general category of ship it is intended for.
- "groups": The names of the groups of systems. The different classes of ship will indicate how many of each group they may take.
- "systems": The systems that are available.

The "systems" value is a dictionary that has a key for each name in "groups" (and only those names). The content for each group is a list of system entries. Each system entry is a dictionary containing:

- "SubGroup": An optional string value giving the name of a sub-grouping. For example, the "Broadside Hardpoint" group in the Resistance Cruiser Systems list has sub-groups of "Weapons" and "Launch".
- "Pts": The points cost of the system.
- "Type": The type of system. This will be one of "Weapon", "Load", or "Structure".
- "Profile": The profile of the system. For weapon and load systems, this follows the usual stat lines associated with those (as used, for example, in the "weapons" and "load" entries for ships). For structure systems, this has "Name" and "Effect" keys (both with string values).
- "Effects": This is only present for structure systems. This is a list of profile modifiers (see 'Profile modifiers') in the same way as for a ship upgrade.
- "Max": An optional value. This is an integer giving the maximum number of times this system may be selected. For example, Resistance ships may only take each structure system once.

Example of file content:

```json
{
    "name": "Dreadnought Systems",
    "groups": ["Secondary Hardpoint", "Tertiary Hardpoint", "Launch Hardpoint"],
    "systems": {
        "Secondary Hardpoint": [
            {"Pts": 15, "Type": "Weapon", "Profile": {"Name": "Grand Bisector", "Arc": "FN", "Att": 5, "Lock": "3+", "DMG": 3, "Type": "E", "Special": ["Bloom-2", "Calibre-H/C", "Crippling", "Focused", "Penetrator"]}},
            {"Pts": 20, "Type": "Weapon", "Profile": {"Name": "Gravitic Hyperlance", "Arc": "FN", "Att": 6, "Lock": "3+", "DMG": 2, "Type": "C", "Special": ["Arrest-2", "Bloom-2"]}}
        ],
        "Tertiary Hardpoint": [
            {"Pts": 15, "Type": "Weapon", "Profile": {"Name": "Thermator", "Arc": "F", "Att": 3, "Lock": "4+", "DMG": 2, "Type": "E", "Special": ["Overcharge", "Scald-1"]}}
        ],
        "Launch Hardpoint": [
            {"Pts": 20, "Type": "Load", "Profile": {"Load": "Torpedo", "Launch": 2, "Special": ["Limited-4"]}}
        ]
    }
}
```

### Systems list on ships

The "systems" key in ship data files specifies the type of systems that can be fitted to that ship and the minimum/maximum quantities. It is a dictionary that contains the following:

- "Name": The name of the systems list to use. This must match the "name" value in the systems list data, not the filename.
- "Min": An integer stating the minimum number of systems that must be taken across all groups.
- "Max": An integer stating the maximum number of systems that can be taken across all groups.
- "GroupLimits": A dictionary giving the limits for each group. Each entry has the name of a group (defined in the "groups" value in the systems list), with the value being a dictionary with "Min" and "Max" keys. Min and max are both optional with a default Min of 0 and default Max of unlimited (in practice, this will be the same as the max systems). If a group is not present then the default values are assumed. 

Below is an example for the Resistance Heavy Cruiser. This must take 5 systems from the "Cruiser Systems" list, with a maximum of two from the "Broadside Hardpoint" group, two from the "Structures" group, and an unlimited number from the "Turret Hardpoint" group (capping at 5 for the max number of systems that can be taken).

```json
{
    "Name": "Cruiser Systems",
    "Min": 5,
    "Max": 5,
    "GroupLimits": {
        "Broadside Hardpoint": {"Max": 2},
        "Structures": {"Max": 2}
    }
}
```
