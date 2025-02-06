# MapCommandV1

|Sender| Receiver | Triggered by | 
|---|---|--|
|`Client` | `Map Service` |  Runtime |

## Message attributes

| Member          | Req. Level | Type          | Description  |
|---------------|-----------|--------------|-------------|
| `Method`     | shall     | string | Shall be set to Get |
| `Scope`  | shall     | string  | ´Summary`if summary is requested, ´/´if getAll is requested |


## Example of MapCommandV1 Summary
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-02-06T19:45:22.123Z",
  "MapCommandV1": {
    "Method": "GET",
    "Scope": "/summary"
  }
}

```
