# YAML Introduction

## Overview

- Ansible Playbooks are written in YAML.
- YAML (YAML Ain't Markup Language) is a human-readable data serialization format.
- YAML is used for configuration data in Ansible.

## YAML Basics

- Key-value pairs are defined with a colon, separating the key and value.
- Use a space followed by a colon to differentiate between key and value.
- Arrays are represented using a dash (-) for each element.
- Dictionaries group properties under an item with aligned spaces.
- Spaces are crucial in YAML to represent data correctly.

## Key Concepts

- Lists contain dictionaries, and dictionaries can contain lists.
- Use dictionaries for single objects with properties.
- Use lists for multiple items of the same type of object.
- A list of dictionaries represents multiple objects with different properties.
- Understanding when to use a dictionary, list, or list of dictionaries is crucial.

## Differences Between Dictionary and List

- Dictionaries are unordered collections, order of properties doesn't matter.
- Lists are ordered collections, order of items matters.
- Comments in YAML start with a hash (#) symbol.

## Notes

- Dictionaries are unordered, lists are ordered.
- Order of properties in dictionaries doesn't matter.
- Order of items in lists matters.
- Lines starting with a hash are comments in YAML.

```yaml
# Example of a Dictionary
car_properties:
  color: "blue"
  model:
    name: "sedan"
    year: 2022
  transmission: "automatic"
  price: 25000

# Example of a Dictionary within Another Dictionary
detailed_car_properties:
  banana:
    calories: 105
    fat: 0.4g
    carbs: 27g
  apple:
    calories: 95
    fat: 0.3g
    carbs: 25g

# Example of a List of Strings
car_names:
  - "blue_sedan"
  - "red_convertible"
  - "black_suv"

# Example of a List of Dictionaries
cars_information:
  - color: "blue"
    model: "sedan"
    transmission: "automatic"
    price: 25000
  - color: "red"
    model: "convertible"
    transmission: "manual"
    price: 30000
  - color: "black"
    model: "suv"
    transmission: "automatic"
    price: 35000

```