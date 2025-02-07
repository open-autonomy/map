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


# Map Service Concept
## Map Objects

- A **map** consists of **roads** and **areas**.
- A **road** is represented as a **line** where the start and end points are different.
- An **area** is modeled as a **closed loop** where the first and last points are the same.
- A **line** serves as a way element, defined by an ordered sequence of points.
- A **point** is modeled as a node element.

---

## Map object rules

Each map object must have a **unique identification number** within its category.  
Object IDs remain **constant throughout their lifecycle**, even if attributes change.  
A unique ID is assigned upon creation and only removed when the object is deleted.

---

## Map Object ID Constraints

| Object  | Restrictions |
|---------|-------------|
| `node id` | `0` is not allowed. Each node must have a **unique** identifier across all nodes within the map server. |
| `way id` | `0` is not allowed. Each way must have a **unique** identifier across all ways within the map server. |
| `user id` | `0` is not allowed. Each user must have a **unique** identifier across all registered users in the map system. |

---

# Identification Numbers in Map Updates

## Way Identification Numbers

Way identification numbers **must remain unchanged** after a map update (set of edits) **unless**:

- The map feature remains unmodified.
- Node references within the way are updated (moved, added, or deleted), but the feature still represents the same object (e.g., lane, road, or shape).
- Tags related to the way are modified, added, or removed.

### When Do Way IDs Change?
The list of way IDs in the map is updated when:

- A **new feature** (lane, road, or area) is added, introducing a **new way ID** to the map and incrementing `ChangeSetWay`.
- A **way is removed**, meaning the corresponding **way ID is deleted**, and `ChangeSetWay` is updated.

---

## Node Identification Numbers

Node identification numbers **must remain unchanged** after a map update **unless**:

- The node is not modified at all.
- The **latitude (`Lat`) or longitude (`Lon`) attributes** are changed.
- Tags related to the node are modified, added, or removed.

### When Do Node IDs Change?
The list of node IDs in the map is updated when:

- A **new node** is added to the map.
- A **node is removed** from the map.

---

# Map format
The map format for the map service is Open Street Map (OSM). The version supported is 0.6. 

## Encoding
The map document shall be in XML document, and not wrapped into a JSON object. 
- Version shall be 0.6
  -  Generator attribue naming the software that generated the map
  -  Sequience is the number in order of a splitted map
  -  OSM element shall aggregate ´node´ and ´way´ element 
  

# Map Chunk Encoding and Structure

## General Requirements
- Each **map chunk** must comply with the **map document encoding** as defined in **Encoding**.
- A **map document** must contain at least **one chunk**.

## XML Integrity
- When **splitting a map into chunks**, XML integrity must be preserved.
- Each chunk must remain **grammatically valid** when parsed by a standard XML parser.

## Chunk Size Limitations
Each chunk must adhere to the following upper limits:
- **Maximum of 10,000 elements per chunk**, including **points, roads, and areas**.
- **Maximum of 2,000 nodes per way**, meaning no road or area should exceed this number of points.

## Sequence Numbering
- Each **map chunk** must contain a **sequence number**, stored as a `sequence` attribute in the `osm` element.
- The **first chunk** must have the sequence number **'1'**.
- Every subsequent **ordered chunk** must have a sequence number **incremented by +1** from the previous chunk.

## Managing Concurrent Updates
- The **map service** must handle concurrent **map changesets**.
- The sequence of **map chunk transfers** must remain consistent with the latest **Map Summary** provided to the client application.

---

This ensures a **structured, scalable, and reliable** handling of map data in a way that maintains both **data integrity and performance**.

## Table: A 'node' element
A **node** represent a point in the map service. 

| Attribute   | Requirement Level | Format | Description |
|------------|------------------|--------|-------------|
| `id`       | **shall**         | 64-bit integer (not equal to zero) | Each node has a unique identifier within the map service's numbering system. Negative node IDs should be used for local files that have not been merged with the map service. The map service only serves positive node IDs. |
| `lat`      | **shall**         | Decimal number (7 decimal places) **≥ -90.0000000 and ≤ 90.0000000** | Latitude coordinate in degrees (North of the equator is positive), following the standard **WGS84 coordinate system**. |
| `lon`      | **shall**         | Decimal number (7 decimal places) **≥ -180.0000000 and ≤ 180.0000000** | Longitude coordinate in degrees (East of Greenwich is positive), using the **WGS84 coordinate system**. |
| `ele`      | **should**        | Decimal number (meters) | Elevation above mean sea level, defined by the **EGM96 geoid model**. |
| `timestamp` | **should**       | Timestamp (ISO 8601) | Records the UTC Date Time when the node was last modified. |
| `changeset` | **should**       | 64-bit integer | Identifies the batch transaction when a user submits a changeset to the map service. |
| `version`   | **should**       | 64-bit integer | Tracks the version of the node. New nodes start at version **1**, and the value increments each time a new version is uploaded. |
| `uid`       | **should**       | 64-bit integer | Identifies the user who last edited and posted the changeset. **UID management is outside the scope of this document.** |
| `user`      | **may**          | String | The **human-readable username** of the last editor who submitted the changeset. |
| `visible`   | **should**       | `'true'` \|\| `'false'` | Specifies whether the node should be displayed on the map. |

---

## Table: A ‘way’ element

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
# Standard Tags

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

## Tag with Attribute `k = 'name'`

### Purpose  

The **name** attribute is the **primary identifier** used by people to reference map objects in conversations. Assigning names to significant objects is a standard practice in mapping.  

All **roads and areas** named within the map authoring tool should be included in the **map document**. However, a name **does not need to be unique** across the map—it is common to assign the **same name to multiple objects**.  

---

###  Name Usage on Map Objects  

| Object Type    | Description  |
|---------------|-------------|
| `node`        | **Supported**. Represents a point of interest with meaningful information for users. The name provides clarity about what the node represents. |
| `way as road` | **Supported**. Roads and road segments are often named to allow users to refer to them easily. |
| `way as area` | **Supported**. Important areas are commonly named so that people can reference them in discussions or navigation. |

---

## Tag with Attribute `k = 'default_task'`

### Purpose  

The **default_task** attribute is primarily used when **third-party software** interacts with the map. It helps software systems identify how different **areas** are meant to be used in a **mining context**, allowing them to enable appropriate **behaviors and attributes** for each area.  

This attribute acts as a **guideline** rather than a restriction—it does **not enforce or limit** the types of tasks that can be assigned to a machine in the area.  

---

###  Default Task Usage on Map Objects  

| Object Type    | Description  |
|---------------|-------------|
| `node`        | **Not Supported** |
| `way as road` | **Not Supported** |
| `way as area` | **Supported** – Defines the primary mining purpose of an area. |

---

### Default Task Key Values  

| Key Value | Description  |
|-----------|-------------|
| `load`    | An area where the machine will primarily **pick up** a load. |
| `offload` | An area where the machine will primarily **unload** its cargo. |
| `supply`  | An area where the machine can either **pick up or offload** a load. |
| `park`    | An area where the machine **parks and shuts down** for an extended period. |
| `wait`    | An area where the machine **stops and waits** for further instructions. Unlike **park**, the machine remains on standby and does not shut down. |
| `drive`   | An area where the machine **drives through** to another road or area. Unlike a designated **road**, a drive area lacks a fixed path, requiring the machine to plan its own freeform route. |
| `fuel`    | An area where the machine **re-fuels**, either at a fuel island or where a fuel truck is present. |

---

## Tag with Attribute `k = 'autonomy'`

### Purpose  

The **autonomy** attribute is used by **third-party software** to determine which areas are designated for **autonomous machine operations** and which areas they are **excluded from**.  

When a **map service** covers both **autonomous** and **manned** areas, this attribute helps client applications distinguish where **autonomous machines** can be **dispatched without exceptions**.  

---

### Autonomy Usage on Map Objects  

| Object Type    | Description  |
|---------------|-------------|
| `node`        | **Not Supported** |
| `way as road` | **Supported** – Can be used to indicate whether autonomous machines are allowed on a specific road. |
| `way as area` | **Supported** – Can be used to specify whether autonomous machines are allowed to plan paths within a designated area. |

---

### Autonomy Key Values  

| Key Value   | Description  |
|------------|-------------|
| `excluded`  | **Autonomous machines are NOT allowed** to use this map object. Only human-operated vehicles are permitted. |
| `exclusive` | **ONLY autonomous machines** are allowed to use this map object. Human-operated vehicles are prohibited. |
| `mixed`     | **Both autonomous and human-operated vehicles** are permitted to use this map object. |

---

##Tag with Attribute `k = 'autonomy:area'`

### Purpose  

The **autonomy:area** attribute is used by **third-party software** to define **perimeters** that indicate whether **autonomous machines** are **allowed or restricted** within a specific zone.  

These areas typically **overlap drivable surfaces**, but they **are not intended** to be directly navigable paths.  

---

### Autonomy: Area Usage on Map Objects  

| Object Type    | Description  |
|---------------|-------------|
| `node`        | **Not Supported** |
| `way as road` | **Not Supported** |
| `way as area` | **Supported** – Defines whether a perimeter is an **inclusionary or exclusionary** boundary for autonomous machines. |

---

### Autonomy: Area Key Values  

| Key Value   | Description  |
|------------|-------------|
| `AOZ`       | **Autonomous Operating Zone (AOZ).** This perimeter encompasses all roads and areas where **autonomous machines** are allowed to operate. Machines **cannot** function autonomously **outside of an AOZ**. The use of AOZ is **optional**, but when implemented, it must be a **permanent object** in the map. |
| `exclusion` | **Restricted Zone.** Autonomous machines **must not enter** an exclusion perimeter. These perimeters are **temporary**, usually lasting for **hours or days**. |
| `obstacle`  | **Obstacle Zone.** Autonomous machines **must not enter** the boundary of an obstacle. These obstacles are **short-term**, with a lifespan measured in **minutes to a few hours**. |

---

## Tag with Attribute `k = 'oneway'`

### Purpose  

The **oneway** attribute is used by **third-party software** to identify **lanes** or **one-way roads**.  
- Lanes are **always modeled** as one-way roads.  
- The **order of points in the line** defines the **direction of travel**.  
  - **The first point is the entrance**  
  - **The last point is the exit**  

---

### Oneway Usage on Map Objects  

| Object Type    | Description  |
|---------------|-------------|
| `node`        | **Not Supported** |
| `way as road` | **Supported** – Specifies if a road is intended for **one-way traffic**. |
| `way as area` | **Not Supported** |

---

### Oneway Key Values  

| Key Value     | Description  |
|--------------|-------------|
| `yes`        | Indicates that **traffic flows in a single direction**, from the **first** to the **last point**. |
| `no`         | Indicates that **traffic is bidirectional**. Roads are assumed to be bidirectional **unless explicitly tagged as one-way**. Omitting the `oneway` tag is equivalent to `<tag k='oneway' v='no'/>`. |
| `reversible` | Roads that **switch direction periodically but infrequently**.  
Example: A lane that changes direction **twice a day during rush hour**. |
| `alternating` | Roads that **alternate direction frequently or continuously**.  
Example: A **single-lane bridge** where traffic alternates between directions as needed. |
