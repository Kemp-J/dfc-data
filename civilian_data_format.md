# Exported civilian ships data format

## Folder structure

The civilian ship data is exported to a folder named "Civilian".

## Fleet data index

Within the Civilian folder is a file `_civilian.json` which provides an entry point into the civilian ship data. This is a json format file containing the following:

- "source_version": The version of the civilian ship PDF (taken from the PDF filename). This is a date formatted as YYMMDD.
- "ship_rules": A list of rules. Each rule consists of a dictionary containing "Name" and "Text" keys, along with an optional "Example" key. All have string values.
- "ships": A list of filenames for the data files containing ship data.

Example:

```json
{
    "source_version": "260501",
    "ship_rules": [
        {"Name": "Blooming", "Text": "Other friendly Ships within 6” of this Ship are Hidden within its Bloom. Enemy Ships attacking these Hidden Ships ignore 2 of their Group’s Spikes when measuring weapons range.\nThis special rule ceases to function while this Ship is not controlled."}
    ],
    "ships": [
        "hyperyacht_somniferum.json"
    ]
}
```

## Ship data file

Each ship class has its own data file in json format. The file is named the same as the ship class name, in lowercase with spaces replaced by underscores and all characters that are invalid for filenames removed.

The data files are identical in structure to those described in the "Ship data file" section in fleet_data_format.md. The only difference is that the points field may be null for scenario-only ships.
