# Export format changes for Dropfleet Commander 2.4

The changes to the exported JSON between the 2.3.1-era exports and the 2.4 exports.

Breaking changes are marked **(breaking)**. Everything else is additive or a new value in an existing field.

## System (rulebook)

### `_system.json`

- `SourceVersion` is always three parts. The rulebook calls itself `2.4`, and it is exported as `"2.4.0"`.

### `group_limits.json` (breaking)

`Tonnage` changes from an object keyed by tonnage to a **list of limits**. 2.4 adds a combined cap: Heavy and Colossal points together may not exceed Medium points. One tonnage can now be covered by more than one limit.

Before:

```json
"Tonnage": {
    "Light":    {"MaxPointsFrom": ["Medium", "Heavy"], "Scope": "Fleet"},
    "Heavy":    {"MaxPointsFrom": ["Medium"], "Scope": "Fleet"},
    "Colossal": {"MaxGroupsByGameSize": {"Skirmish": 0, "Clash": 1, "Battle": 2, "Reconquest": 3}, "Scope": "Fleet"}
}
```

After:

```json
"Tonnage": [
    {"Tonnages": ["Light"],             "MaxPointsFrom": ["Medium", "Heavy"], "Scope": "Fleet"},
    {"Tonnages": ["Heavy", "Colossal"], "MaxPointsFrom": ["Medium"],          "Scope": "Fleet"},
    {"Tonnages": ["Colossal"],          "MaxGroupsByGameSize": {"Skirmish": 0, "Clash": 1, "Battle": 2, "Reconquest": 3}, "Scope": "Fleet"}
]
```

- `Tonnages` is a non-empty list. The limit applies to the **combined** total of all the tonnages listed. For `MaxPointsFrom`, that means the summed points of Heavy and Colossal ships.
- Each entry has exactly one limit form (`MaxPointsFrom`, `MaxGroupsByGameSize` or `MaxGroups`), plus `Scope`. These forms and their meanings are unchanged.
- Every limit applies independently. Colossal is covered by both the pooled points cap and its group-count limit.

### New file: `features.json`

`_system.json` gains `"Features": "features.json"`. The file lists the five standard rulebook Features (section 11.3): Military Outpost, Orbital Defence Gun, Comms Station, Power Plant and Hangar. Each has the same layout as a fleet Deployable Feature, except there is no `PTS`:

```json
{
    "Profile": {"Type": "Hangar", "ES": "4+", "KS": "4+", "Hull": null, "Special": []},
    "Weapons": [],
    "Load": [{"Load": "Fighters & Bombers", "Launch": 2, "Special": []}],
    "Rules": "This Feature's Fighters & Bombers use the Controlling players' Fighters & Bombers."
}
```

- `Hull` is `null` where the rulebook shows `-` (Hangar).
- Comms Station and Power Plant print a bespoke rule in the Special column. That text is in `Rules` (`"Comms Uplink: …"`, `"Volatile: …"`), and `Special` is `[]`.
- A footnote on a Feature's table, such as the Hangar's, is added to its `Rules`.

## FeatureCarrier (all ships: fleet, Civilian, Misc)

Every ship with a `Feature Carrier` rule gains a `FeatureCarrier` object, directly after `Rules`. It is not present on other ships.

```json
"FeatureCarrier": {"Count": 1, "ValidDeployableFeatures": ["*"]}
"FeatureCarrier": {"Count": 2, "ValidDeployableFeatures": ["*"]}
"FeatureCarrier": {"Count": 1, "ValidDeployableFeatures": ["Gravitational Arc", "Ghost Orb Tower"]}
"FeatureCarrier": {"Count": 1, "Free": true, "ValidDeployableFeatures": ["Hangar"]}
```

- `Count` is how many Features the ship carries. Where `Count` is 2, duplicates are allowed.
- `Free` is only present when `true`. When it is present, the Features cost no points. When it is absent, the ship pays each Feature's `PTS`.
- `ValidDeployableFeatures` holds Feature names, matching a Feature's `Profile.Type`. Look each name up in the ship's own fleet's Deployable Features first, then in the System `features.json`.
- `["*"]` means any of the ship's own fleet's Deployable Features. It never includes the rulebook Features; those are only valid when named.

2.4 values:

| Ships | FeatureCarrier |
|---|---|
| PHR, Scourge, UCM carriers; Resistance Jutland, Midway, Trafalgar | 1 of `*` |
| Resistance Actium, Salamis; all Shaltari carriers | 2 of `*` |
| Bioficer Shade, Sierra, Sluice, Syntax, Synthesis | 1 of Gravitational Arc / Ghost Orb Tower |
| Civilian EX-7 Packet Runner | 1 Hangar, `Free` |
| Civilian M-Type Barge | 1 Military Outpost, `Free` |

## Fleets

### Deployable features

- `Profile` gains `Hull`, directly after `KS`. It is an integer, or `null` where the PDF shows `-`. No 2.4 Deployable Feature currently does.

UCM Torpedo Platform example:

```json
"Profile": {"PTS": 20, "Type": "Torpedo Platform", "ES": "5+", "KS": "5+", "Hull": 4, "Special": []}
```

### Ability costs

- A fleet ability or admiral ability `Cost` can now be a range, such as `"1-2AP"` (Shaltari Navigational Mastery). The full pattern is `^(\d+(-\d+)?AP\*?|\*AP)$`. The cost is exported as printed, and the ability text explains how the range is used.

### Repeated multi-profile weapons

- A ship can now carry several multi-profile weapons with the same `Name`. Romulus has three `Energy Glaives` weapons and Remus has two, and each one has `Pulse` and `Beam` profiles. Treat each weapon entry as a separate weapon.

### Systems lists

- No format change. Some Effect texts change with 2.4: Resistance Frigate `Sensor Dome` now only grants Detector.

## Mercenary and `UsableIn` (all ships: fleet, Civilian, Misc)

2.4 adds the ship rule `Mercenary (X)`, which appears as a profile **Special**: `Mercenary (All)`, `Mercenary (UCM, PHR, Resistance)` or `Mercenary (Non-Scourge, Non-Bioficer)`.

- **Specials can contain commas inside brackets.** `"Mercenary (UCM, PHR, Resistance)"` is a single Special. Code that matches Special names should treat `Mercenary (...)` as the rule `Mercenary (X)`.
- **New optional `Except` key on a `UsableIn` entry:**

  ```json
  "UsableIn": [
      {"Fleet": "*", "Except": ["Scourge", "Bioficer"], "Modifiers": [
          {"Target": "Special", "Type": "remove", "Value": "Boardable"},
          {"Target": "Special", "Type": "remove", "Value": "Civilian Transport"}
      ]}
  ]
  ```

  - `Except` only appears when `Fleet` is `"*"`, and it is left out rather than written as an empty list.
  - It holds short fleet names (`UCM`, `PHR`, `Shaltari`, `Scourge`, `Resistance`, `Bioficer`), the same names used in `Fleet`.
- `(All)` gives one entry with `Fleet` set to `"*"`. A named list gives one entry per fleet. A `Non-` list gives a single `"*"` entry with `Except`.
- Each entry removes `Boardable` and/or `Civilian Transport`, whichever of them the ship has, because the Mercenary rule removes them when the ship is taken.
- `Modifiers` is still always present, and can be `[]`.
- Ships whose eligibility comes from another rule (Civilian Argonaut's "Mind of its Own", Misc Palatine's "Remnant" and Pungari Thresher's "Subservient Mercenaries") keep their existing `UsableIn`. There is no change for these.

## Space Stations

### `_stations.json` (breaking)

- `Features` changes from an inline list of `{Name, Text}` to a **filename**: `"space_station_features.json"`.

### New file: `space_station_features.json`

The Features are now a priced table, like the armaments.

```json
[
    {"Name": "Launch Control",    "Pts": 5,  "ES": "4+", "KS": "4+", "Hull": 5, "Effect": "When all Launch Control Features are removed from a Space Station, that Station cannot Launch Assets", "Effects": []},
    {"Name": "Life Support",      "Pts": 5,  "ES": "5+", "KS": "5+", "Hull": 6, "Effect": "When this Feature is removed, each player removes a Battaion from this Space Station, …", "Effects": []},
    {"Name": "Reactor Core",      "Pts": 0,  "ES": "5+", "KS": "5+", "Hull": 6, "Effect": "When this Feature or a Space Station with this feature is destroyed, it explodes with a Reactor Overload Explosion Effect with a range of D6+3\".", "Effects": []},
    {"Name": "Redundant Systems", "Pts": 10, "ES": "4+", "KS": "4+", "Hull": 7, "Effect": "This Space Station gains a 6+ Backup Save.", "Effects": [{"Target": "BS", "Type": "replace", "Value": "6+"}]}
]
```

- `Pts` and `Hull` are integers, and `ES` and `KS` are strings, as in ship profiles.
- `Effects` is a list of profile modifiers applied to the station that has the Feature, as on the Space Station Armaments Structures. It is always present and is `[]` when the Feature changes no profile stat. In 2.4, only Redundant Systems has one: it sets the station's `BS` to `"6+"`.
- 2.4 has eight Features: Launch Control, Weapons Control, Life Support, Reactor Core, Redundant Systems, Firing Computer, Defensive Systems and Supercontrol Node.
- Defensive Systems has no effect: the PDF prints `None` in its Effect cell, and its `Effect` is the string `"None"` as printed, not `null`.

### `Hardpoints` on generic and fleet stations (breaking)

`Hardpoints` changes shape, and now also appears on **fleet stations**.

Before (generic stations only):

```json
"Hardpoints": {"Armaments": 1, "Features": 0}
```

After:

```json
"Hardpoints": {
    "Features":           [{"Name": "Life Support", "Count": 1}],
    "AdditionalFeatures": {"Min": 3, "Max": 3},
    "Armaments":          {"Min": 0, "Max": 2}
}
```

- `Features`: the Features the station has automatically. They are free, and each has a count, such as two Weapons Control. Each `Name` matches an entry in `space_station_features.json`.
- `AdditionalFeatures`: how many extra Features may be bought from `space_station_features.json`, at their listed points. "Must take three" gives 3/3, "may take one" gives 0/1, and "must take two… and may take one" gives 2/3.
- `Armaments`: how many options may be taken from `space_station_armaments.json`. "May take up to two" gives 0/2.
- The shape is always complete: absent parts are `[]` or `{"Min": 0, "Max": 0}`.
- On fleet stations, `Hardpoints` comes after `Load`, and the `Hardpoints` rule is **removed from `Rules`**. If that was the only rule, `Rules` is left out.

2.4 values:

| Station | Features | AdditionalFeatures | Armaments |
|---|---|---|---|
| Small Space Station | Weapons Control 1, Life Support 1 | 0/0 | 0/1 |
| Medium Space Station | Life Support 1 | 3/3 | 0/2 |
| Large Space Station | Life Support 1 | 4/4 | 0/3 |

Fleet stations vary. Most have Life Support plus two Launch Control or two Weapons Control, and some may or must take additional Features.

### `UpgradeOptions` on generic stations

- It can now contain the special entry `"Space Station Features"`. This means "one option from the Space Station Features list", counting as one of the station's `MaxUpgrades`. All three generic stations have it in 2.4.
- Every other entry is still the `Name` of a station upgrade file.

### Scenario-only Structures in `space_station_armaments.json`

The Structures group gains two entries: **Defensive Garrison** and **Forward Operating Base**. Which one can be selected depends on the scenario setup. Like the other Structures they have `Max` (1). They also carry two new optional keys after `Max`, each written only when it applies:

```json
{
    "Pts": 0,
    "Type": "Structure",
    "Profile": {"Name": "Defensive Garrison", "Effect": "This Structure does not cost points and can only be taken by a player that is the Defender in a scenario, …"},
    "Effects": [],
    "Max": 1,
    "ScenarioRole": "Defender",
    "ExcludesGroup": true
}
```

- `ScenarioRole` is `"Defender"` or `"Attacker"`. The entry can only be taken by the player in that role. The suggested presentation is to merge all `ScenarioRole` entries into one choice ("Defensive Garrison / Forward Operating Base") and apply whichever fits once the roles are known.
- `ExcludesGroup` set to `true` means taking this entry prevents taking any other entry in the same group (Structures).
- `Pts` is set to `0` where the PDF has `*`.
