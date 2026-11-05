# Exported Fleet Space Stations Data Format

## Folder Structure

The station data is exported to a folder named `Stations`.

## Stations Index

Within the `Stations` folder is a file `_stations.json` providing an entry point to all station data. It contains:

- "SourceVersion": The version of the stations PDF (taken from the PDF filename). A date formatted as YYMMDD.
- "Features": The filename of the Space Station Features data file (`space_station_features.json`).
- "GenericStations": A list of filenames for generic Space Station data files.
- "Armaments": The filename of the Space Station Armaments data file.
- "StationUpgrades": A list of filenames for the generic Space Station Upgrade data files, e.g. Astrobotanical Lab and Defence Grid.
- "FleetStations": A list of filenames for fleet-specific station data files.

Example:

```json
{
    "SourceVersion": "261104",
    "Features": "space_station_features.json",
    "GenericStations": [
        "small_space_station.json",
        "medium_space_station.json",
        "large_space_station.json"
    ],
    "Armaments": "space_station_armaments.json",
    "StationUpgrades": [
        "astrobotanical_lab.json",
        "defence_grid.json"
    ],
    "FleetStations": [
        "ucm_defence_hangar.json",
        "ucm_munitions_platform.json"
    ]
}
```

## Space Station Features Data File

The Space Station Features file (`space_station_features.json`) is a list with one entry per row of the PDF's priced Features table, in PDF order. Each entry contains:

- "Name": The Feature's name (the table's Type column), e.g. "Launch Control".
- "Pts": The points cost (integer). A Feature a station has automatically is free; this is the price of buying one more.
- "ES": The Feature's energy save (string, e.g. "4+"), or "*" when the Feature uses its Space Station's best available saves (Launch Control, Weapons Control).
- "KS": The Feature's kinetic save (string, e.g. "4+"), or "*" as for "ES".
- "Hull": The Feature's hull (integer).
- "Effect": The Effect text, with wrapped lines joined. "None" when the PDF prints None.
- "Effects": A list of profile modifiers (see 'Profile modifiers' in fleet_data_format.md) applied to the station that has the Feature. Always present; an empty list when the Effect changes no profile stat. For example, Redundant Systems ("This Space Station gains a 6+ Backup Save.") has `[{"Target": "BS", "Type": "replace", "Value": "6+"}]`.

Example:

```json
[
    {"Name": "Launch Control", "Pts": 5, "ES": "*", "KS": "*", "Hull": 5, "Effect": "This feature uses its Space Station's best available saves. When all Launch Control Features are removed from a Space Station, that Station cannot Launch Assets", "Effects": []},
    {"Name": "Reactor Core", "Pts": 0, "ES": "5+", "KS": "5+", "Hull": 6, "Effect": "When this Feature or a Space Station with this feature is destroyed, it explodes with a Reactor Overload Explosion Effect with a range of D6+3\".", "Effects": []},
    {"Name": "Redundant Systems", "Pts": 10, "ES": "4+", "KS": "4+", "Hull": 7, "Effect": "This Space Station gains a 6+ Backup Save.", "Effects": [{"Target": "BS", "Type": "replace", "Value": "6+"}]},
    {"Name": "Defensive Systems", "Pts": 20, "ES": "2+", "KS": "2+", "Hull": 3, "Effect": "None", "Effects": []}
]
```

## Generic Station Data File

Each generic station has its own data file in JSON format, named after the station in lowercase with spaces replaced by underscores.

The file contains:

- "Name": The name of the station.
- "Type": The type of the station.
- "Points": The points cost.
- "BaseSize": The base size in mm.
- "Profile": A dictionary containing "Scan" (integer), "Sig" (integer), "Hull" (integer), "ES" (string), "KS" (string), "BS" (string), "G" (string), and "Special" (list of strings).
- "Hardpoints": What the station has and may take. See 'Hardpoints' below.
- "MaxUpgrades": The maximum number of upgrades (e.g. Astrobotanical Lab or Defence Grid) the station may take.
- "UpgradeOptions": A list of the upgrades the station may take. Each entry is either the "Name" field of a station upgrade data file, or the special entry "Space Station Features", meaning one option from the Space Station Features list. An option taken from that list counts as one of the station's "MaxUpgrades".

Example:

```json
{
    "Name": "Small Space Station",
    "Type": "Orbital Satellite",
    "Points": 30,
    "BaseSize": 30,
    "Profile": {
        "Scan": 6,
        "Sig": 4,
        "Hull": 10,
        "ES": "4+",
        "KS": "4+",
        "BS": "-",
        "G": "1",
        "Special": []
    },
    "Hardpoints": {
        "Features": [
            {"Name": "Weapons Control", "Count": 1},
            {"Name": "Life Support", "Count": 1}
        ],
        "AdditionalFeatures": {"Min": 0, "Max": 0},
        "Armaments": {"Min": 0, "Max": 1}
    },
    "MaxUpgrades": 1,
    "UpgradeOptions": ["Astrobotanical Lab", "Defence Grid", "Space Station Features"]
}
```

## Hardpoints

"Hardpoints" appears on generic stations (after "Profile") and on fleet stations (after "Load"). It always has all three parts:

- "Features": A list of `{Name, Count}` entries for the Features the station has automatically (for free), e.g. two Weapons Control. Each "Name" matches an entry in the Space Station Features file.
- "AdditionalFeatures": `{Min, Max}`, how many extra Features from the Space Station Features list the station must (Min) and may (Max) take, bought at their listed points. "Must take three" is 3/3, "may take one" is 0/1, and "must take two and may take one" is 2/3. `{"Min": 0, "Max": 0}` when none.
- "Armaments": `{Min, Max}`, how many options from the Space Station Armaments list the station may take. `{"Min": 0, "Max": 0}` when none.

## Space Station Armaments Data File

The Space Station Armaments file (`space_station_armaments.json`) is formatted identically to a fleet systems list file (see `fleet_data_format.md` — 'Systems list files').

Weapon Systems entries never carry a "Max" field (multiple identical options may be taken). Structure entries always carry a "Max" of 1 (up to one of each may be taken).

Two Structures, **Defensive Garrison** and **Forward Operating Base**, depend on the fleet's scenario role. They have "Pts" 0 (the PDF has "*") and carry "ScenarioRole" ("Defender" or "Attacker", the role the taking player must have) and "ExcludesGroup" (`true` means a station that takes the Structure cannot take any other Structures), both after "Effects" and only when set.

## Station Upgrade Data File

Each station upgrade (Astrobotanical Lab, Defence Grid) has its own data file in JSON format, named after the upgrade in lowercase with spaces replaced by underscores.

The file contains:

- "Name": The name of the upgrade (e.g. "Astrobotanical Lab"). Must match the name used in "UpgradeOptions" on generic station files.
- "Type": The type/flavour name shown in the PDF (e.g. "Exo-Greenhouse", "Military Space Station").
- "Cost": The additional points cost.
- "Weapons": A list of weapon entries in the same format as fleet station weapons (see 'Fleet Station Weapon' below). May be empty.
- "Rules": A list of `{Name, Text}` rule dictionaries. May be empty.

Example:

```json
{
    "Name": "Astrobotanical Lab",
    "Type": "Exo-Greenhouse",
    "Cost": 30,
    "Weapons": [],
    "Rules": [
        {"Name": "Signature Bloom", "Text": "Friendly Ships within this Space Station's unmodified Signature are Hidden within its Bloom. Enemy Ships targeting a Hidden Ship's Group ignore a Spike for each Hidden Ship in that Group.\nThis rule has no effect while this Space Station is controlled by an enemy player."}
    ]
}
```

## Fleet Station Data File

Each fleet-specific station has its own data file in JSON format, named after the full station name in lowercase with spaces replaced by underscores and invalid filename characters removed.

The file contains:

- "Fleet": The fleet this station belongs to.
- "Name": The station name (e.g. "Defence Hangar") with the fleet name prefix removed.
- "Size": The size class of the station. One of "Small", "Medium", or "Large".
- "Points": Points cost.
- "BaseSize": Base size in mm.
- "Profile": Same format as generic station profiles (Scan, Sig, Hull, ES, KS, BS, G, Special).
- "Weapons": A list of weapon entries. Each entry is either a single-profile weapon or multi-profile weapon, following the same structure as fleet ship weapons (see `fleet_data_format.md` — 'Ship data file'), except:
  - The "Arc" field may be "*" in addition to the standard arc values. ("*" is used by the Shaltari Shuriken's Disintegrator Bank weapon due to it not using standard arcs.)
  - The optional "Rule" field on a weapon may be present.
- "Load": A list of load entries in the same format as fleet ship load entries.
- "Hardpoints": As described in 'Hardpoints' above. The PDF's Hardpoints rule is not repeated in "Rules".
- "Rules": An optional list of `{Name, Text}` rule dictionaries. Omitted when empty.

Example:

```json
{
    "Fleet": "UCM",
    "Name": "Defence Hangar",
    "Size": "Small",
    "Points": 50,
    "BaseSize": 30,
    "Profile": {
        "Scan": 6,
        "Sig": 4,
        "Hull": 8,
        "ES": "3+",
        "KS": "5+",
        "BS": "-",
        "G": "1",
        "Special": []
    },
    "Weapons": [],
    "Load": [
        {"Load": "Fighters & Bombers", "Launch": 2, "Special": []}
    ],
    "Hardpoints": {
        "Features": [
            {"Name": "Life Support", "Count": 1},
            {"Name": "Launch Control", "Count": 2}
        ],
        "AdditionalFeatures": {"Min": 0, "Max": 0},
        "Armaments": {"Min": 0, "Max": 0}
    }
}
```
