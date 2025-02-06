# Map Interface definition
This section will explain the Map service requirements, use cases and specify the messaging format.

### [Message specification](./specification/README.md)
### [Message flow diagram](./diagram/README.md)
### [State diagrams](./diagram/README.md)

<br>

# Introduction
This repo is dedicated to documenting the Map service, one of the services used in the open-autonomy family

<br>

# Map Service Purpose
The map service handles maps in the interaction between AHS & FMS systems. 

<br>

# Audience
- Autonomy integrators ( typically miners )
- Autonomous truck suppliers ( Engineering )
- Fleet Management suppliers ( Engineering )

<br>

# What is a Map ?
A map in a mine for autonomous trucks is a digital representation of the mining environment that defines roads, intersections, loading and dumping areas, and restricted zones. It enables the trucks to navigate safely, optimize routes, and interact with other equipment while following predefined operational rules.

<br>

# Format
The map format that is used to share information is the Open Street Map (OSM) format (https://www.openstreetmap.org). 


# General rules
# Elements in Mapping

## Table: Attributes of a ‘Way’ Element

A **way** represents a path or road in the map service. The attributes associated with a way define its properties and how it is managed in the system.

| Attribute   | Requirement Level | Format          | Description |
|------------|------------------|----------------|-------------|
| `id`       | **shall**         | integer | A unique identifier for the way within the map service. Temporary (local) files can use negative IDs for unmerged ways, while the map service only uses positive IDs. |
| `timestamp` | **should**       | Timestamp | The date and time when the way was last modified. |
| `changeset` | **should**       | integer | Identifies the batch of changes in which this way was modified. |
| `version`   | **should**       | integer | Tracks the version history of the way. Starts at 1 and increments when a new version is uploaded. |
| `uid`       | **should**       | integer | Identifies the user who last modified the way. |
| `user`      | **may**          | String         | A human-readable username of the last editor. |
| `visible`   | **should**       | `true` or `false` | Determines if the way should be displayed on the map. |

---

## Roads in the Map Service

A **road** is represented as a **line** made up of an ordered sequence of points.

### **Road Requirements**
- The **first and last point** must be different.
- A road must contain **at least 2 points** and at most **2,000 points**.
- A road **cannot connect or cross itself**, but different roads may cross each other.

**Use Case:** Roads guide machines in normal driving conditions. The specific way in which autonomous machines follow these roads depends on the autonomy system.

---

## Lanes

Lanes are a **specialized type of road** that are always **unidirectional**.

- Lanes should be marked with the attribute:  
  ```xml
  <tag k="oneway" v="yes"/>

## Areas

An area is also modeled as a line, but:
	- 	The first and last point must be the same to form a closed shape.
	- 	Must have at least 3 points, with a maximum of 2,000 points.
	- 	Areas cannot cross themselves, but they can overlap other areas.

### Use Cases of Areas

Areas define functional spaces on a map, such as:
	- Drivable Surfaces – where autonomous machines can plan their own paths.
	- 	Mining Operations – defining areas for:
	- 	Active mining zones
	- 	Waste dumps
	- 	Stockpile areas
	- 	Permanent Facilities – such as:
	- 	Parking lots
	- 	Fuel stations
	- 	Autonomous Zones – defining operational limits for autonomous vehicles.
	- 	Restricted Areas – where autonomy is not allowed.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<osm version='0.6' generator='MappingTool'>
  <node id='1001' timestamp='2025-02-06T12:00:00Z' uid='2001' user='JohnDoe'
        visible='true' version='1' changeset='5001' lat='-23.20035' lon='118.78111' />
  <node id='1002' timestamp='2025-02-06T12:00:00Z' uid='2001' user='JohnDoe'
        visible='true' version='1' changeset='5001' lat='-23.20030' lon='118.78113' />
  <node id='1003' timestamp='2025-02-06T12:00:00Z' uid='2001' user='JohnDoe'
        visible='true' version='1' changeset='5001' lat='-23.20027' lon='118.78118' />
  <way id='9001' timestamp='2025-02-06T12:05:00Z' uid='2001' user='JohnDoe'
        visible='true' version='2' changeset='5002'>
    <nd ref='1001' />
    <nd ref='1002' />
    <nd ref='1003' />
    <tag k='highway' v='service' />
    <tag k='oneway' v='yes' />
  </way>
</osm>
```
## Standard Tags

| Key              | Values                                              | Purpose |
|-----------------|-----------------------------------------------------|---------|
| `name`          | Any text                                           | Assigns a human-readable name to objects. |
| `default_task`  | `load`, `offload`, `supply`, `park`, `wait`, `drive`, `fuel` | Defines the primary function of an area. |
| `autonomy`      | `excluded`, `exclusive`, `mixed`                   | Determines where autonomous vehicles can operate. |
| `autonomy:area` | `AOZ`, `exclusion`, `obstacle`                      | Defines specific autonomy zones. |
| `oneway`        | `yes`, `no`, `reversible`, `alternating`            | Defines road directionality. |
| `highway`       | `service`, `track`, `escape`, `construction`        | Classifies road types. |
| `amenity`       | `parking`, `fuel`                                   | Defines special-use areas. |
| `landuse`       | `quarry`, `construction`                            | Classifies land usage. |
