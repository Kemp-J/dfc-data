# Exported Fleet Space Stations Data Format

## Folder Structure

The station data is exported to a folder named `Stations`.

## Stations Index

Within the `Stations` folder is a file `_stations.json` providing an entry point to all station data. It contains:

- "SourceVersion": The version of the stations PDF (taken from the PDF filename). A date formatted as YYMMDD.
- "Features": A list of `{Name, Text}` dictionaries (identical structure to ship rules) representing the Fleet Space Station Features.
- "GenericStations": A list of filenames for generic Space Station data files.
- "Armaments": The filename of the Space Station Armaments data file.
- "StationUpgrades": A list of filenames for the generic Space Station Upgrade data files, e.g. Astrobotanical Lab and Defence Grid.
- "FleetStations": A list of filenames for fleet-specific station data files.

Example:

```json
{
    "SourceVersion": "250818",
    "Features": [
        {"Name": "Launch Control", "Text": "When all Launch Control features are removed from a Station, that station cannot launch Assets."},
        {"Name": "Weapons Control", "Text": "When all Weapons Control features are removed from a Station, that station cannot use its Weapon Systems."}
    ],
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

## Generic Station Data File

Each generic station has its own data file in JSON format, named after the station in lowercase with spaces replaced by underscores.

The file contains:

- "Name": The name of the station.
- "Type": The type of the station.
- "Points": The points cost.
- "BaseSize": The base size in mm.
- "Profile": A dictionary containing "Scan" (integer), "Sig" (integer), "Hull" (integer), "ES" (string), "KS" (string), "BS" (string), "G" (string), and "Special" (list of strings).
- "Hardpoints": A dictionary with "Armaments" (integer — number of options required from the Space Station Armaments list) and "Features" (integer — number of additional Fleet Space Station Features required beyond the base two).
- "MaxUpgrades": The maximum number of upgrades (e.g. Astrobotanical Lab or Defence Grid) the station may take.
- "UpgradeOptions": A list of upgrade names the station may take. Each name corresponds to the "Name" field in a station upgrade data file.

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
        "Armaments": 1,
        "Features": 0
    },
    "MaxUpgrades": 1,
    "UpgradeOptions": ["Astrobotanical Lab", "Defence Grid"]
}
```

## Space Station Armaments Data File

The Space Station Armaments file (`space_station_armaments.json`) is formatted identically to a fleet systems list file (see `fleet_data_format.md` — 'Systems list files').

Weapon Systems entries never carry a "Max" field (multiple identical options may be taken). Structure entries always carry a "Max" of 1 (up to one of each may be taken).

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
    ]
}
```
