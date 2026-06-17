# Exported Fleet Space Stations Data Format

## Folder Structure

The station data is exported to a folder named `Stations`.

## Stations Index

Within the `Stations` folder is a file `_stations.json` providing an entry point to all station data. It contains:

- "source_version": The version of the stations PDF (taken from the PDF filename). A date formatted as YYMMDD.
- "features": A list of `{Name, Text}` dictionaries (identical structure to ship rules) representing the Fleet Space Station Features.
- "generic_stations": A list of filenames for generic Space Station data files.
- "armaments": The filename of the Space Station Armaments data file.
- "station_upgrades": A list of filenames for the generic Space Station Upgrade data files, e.g. Astrobotanical Lab and Defence Grid.
- "fleet_stations": A list of filenames for fleet-specific station data files.

Example:

```json
{
    "source_version": "250818",
    "features": [
        {"Name": "Launch Control", "Text": "When all Launch Control features are removed from a Station, that station cannot launch Assets."},
        {"Name": "Weapons Control", "Text": "When all Weapons Control features are removed from a Station, that station cannot use its Weapon Systems."}
    ],
    "generic_stations": [
        "small_space_station.json",
        "medium_space_station.json",
        "large_space_station.json"
    ],
    "armaments": "space_station_armaments.json",
    "station_upgrades": [
        "astrobotanical_lab.json",
        "defence_grid.json"
    ],
    "fleet_stations": [
        "ucm_defence_hangar.json",
        "ucm_munitions_platform.json"
    ]
}
```

## Generic Station Data File

Each generic station has its own data file in JSON format, named after the station in lowercase with spaces replaced by underscores.

The file contains:

- "name": The name of the station.
- "type": The type of the station.
- "points": The points cost.
- "base_size": The base size in mm.
- "profile": A dictionary containing "Scan" (integer), "Sig" (integer), "Hull" (integer), "ES" (string), "KS" (string), "BS" (string), "G" (string), and "Special" (list of strings).
- "hardpoints": A dictionary with "armaments" (integer — number of options required from the Space Station Armaments list) and "features" (integer — number of additional Fleet Space Station Features required beyond the base two).
- "max_upgrades": The maximum number of upgrades (e.g. Astrobotanical Lab or Defence Grid) the station may take.
- "upgrade_options": A list of upgrade names the station may take. Each name corresponds to the "name" field in a station upgrade data file.

Example:

```json
{
    "name": "Small Space Station",
    "type": "Orbital Satellite",
    "points": 30,
    "base_size": 30,
    "profile": {
        "Scan": 6,
        "Sig": 4,
        "Hull": 10,
        "ES": "4+",
        "KS": "4+",
        "BS": "-",
        "G": "1",
        "Special": []
    },
    "hardpoints": {
        "armaments": 1,
        "features": 0
    },
    "max_upgrades": 1,
    "upgrade_options": ["Astrobotanical Lab", "Defence Grid"]
}
```

## Space Station Armaments Data File

The Space Station Armaments file (`space_station_armaments.json`) is formatted identically to a fleet systems list file (see `fleet_data_format.md` — 'Systems list files').

Weapon Systems entries never carry a "Max" field (multiple identical options may be taken). Structure entries always carry a "Max" of 1 (up to one of each may be taken).

## Station Upgrade Data File

Each station upgrade (Astrobotanical Lab, Defence Grid) has its own data file in JSON format, named after the upgrade in lowercase with spaces replaced by underscores.

The file contains:

- "name": The name of the upgrade (e.g. "Astrobotanical Lab"). Must match the name used in "upgrade_options" on generic station files.
- "type": The type/flavour name shown in the PDF (e.g. "Exo-Greenhouse", "Military Space Station").
- "cost": The additional points cost.
- "weapons": A list of weapon entries in the same format as fleet station weapons (see 'Fleet Station Weapon' below). May be empty.
- "rules": A list of `{Name, Text}` rule dictionaries. May be empty.

Example:

```json
{
    "name": "Astrobotanical Lab",
    "type": "Exo-Greenhouse",
    "cost": 30,
    "weapons": [],
    "rules": [
        {"Name": "Signature Bloom", "Text": "Friendly Ships within this Space Station's unmodified Signature are Hidden within its Bloom. Enemy Ships targeting a Hidden Ship's Group ignore a Spike for each Hidden Ship in that Group.\nThis rule has no effect while this Space Station is controlled by an enemy player."}
    ]
}
```

## Fleet Station Data File

Each fleet-specific station has its own data file in JSON format, named after the full station name in lowercase with spaces replaced by underscores and invalid filename characters removed.

The file contains:

- "fleet": The fleet this station belongs to.
- "name": The full station name (e.g. "UCM Defence Hangar").
- "size": The size class of the station. One of "Small", "Medium", or "Large".
- "points": Points cost.
- "base_size": Base size in mm.
- "profile": Same format as generic station profiles (Scan, Sig, Hull, ES, KS, BS, G, Special).
- "weapons": A list of weapon entries. Each entry is either a single-profile weapon or multi-profile weapon, following the same structure as fleet ship weapons (see `fleet_data_format.md` — 'Ship data file'), except:
  - The "Arc" field may be "*" in addition to the standard arc values. ("*" is used by the Shaltari Shuriken's Disintegrator Bank weapon due to it not using standard arcs.)
  - The optional "Rule" field on a weapon may be present.
- "load": A list of load entries in the same format as fleet ship load entries.
- "rules": An optional list of `{Name, Text}` rule dictionaries. Omitted when empty.

Example:

```json
{
    "name": "UCM Defence Hangar",
    "fleet": "UCM",
    "size": "Small",
    "type": "Small Space Station",
    "points": 50,
    "base_size": 30,
    "profile": {
        "Scan": 6,
        "Sig": 4,
        "Hull": 8,
        "ES": "3+",
        "KS": "5+",
        "BS": "-",
        "G": "1",
        "Special": []
    },
    "weapons": [],
    "load": [
        {"Load": "Fighters & Bombers", "Launch": 2, "Special": []}
    ]
}
```
