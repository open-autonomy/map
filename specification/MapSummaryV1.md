# MapSummaryV1

|Sender| Receiver | Triggered by | 
|---|---|--|
|`Map Service` | `FMS` |  At Connection |
|`Map Service` | `FMS` |  On Map change |
|`Map Service` | `FMS` |  On client request |

## Message attributes

| Member          | Req. Level | Type          | Description  |
|---------------|-----------|--------------|-------------|
| `Timestamp`     | shall     | DateTime | The UTC time the map was last modified |
| `ChangeSetMap`  | shall     | Integer  | The serial number of the post (set of changes) to the map. Each map commit will increment the map set number by 1. |
| `ChangeSetWay`  | shall     | Integer  | The serial number of changes to the Ways only. Ways are deemed changed in the map if WayIds are removed or added and if tags to a way are modified, added, or removed. |
| `uid`          | should    | Integer  | A User ID that modified it |
| `NodeCount`    | shall     | Integer  | The number of `<node />` objects (Points) |
| `WayCount`     | shall     | Integer  | The number of `<way />` objects (Roads or Area) |
| `DocumentCount` | shall     | Integer  | The number of XML documents (map chunks) that will be sent when a full update is sent. |

## Example of MapSummaryV1
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-02-06T19:45:22.123Z",
  "MapSummaryV1": {
    "Timestamp": "2025-02-05T18:20:15.456Z",
    "ChangeSetMap": 92345678,
    "ChangeSetWay": 678901,
    "uid": 20083456,
    "NodeCount": 47239,
    "WayCount": 4892,
    "DocumentCount": 6
  }
}

```
