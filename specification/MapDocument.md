# MapDocument

|Sender| Receiver | Triggered by | 
|---|---|--|
|`Map Service` | `Client` |  After triggering by sending [MapCommandV1](https://github.com/open-autonomy/map/blob/feat-add-map-from-iso/specification/MapCommandV1.md#example-of-mapcommandv1-get-all) to Map Service |



## Example of Simple single lane example
```xml
<?xml version="1.0" encoding="UTF-8"?>
<osm version="0.6" generator="AHS Authoring" sequence="1">
  <node id="8012345678" changeset="88654321" timestamp="2025-02-06T15:30:45Z" uid="20083456"
        lat="-23.1956789" lon="118.8012345"/>
  <node id="8012345679" changeset="88654321" timestamp="2025-02-06T15:30:45Z" uid="20083456"
        lat="-23.1934567" lon="118.8123456"/>
  <node id="8012345680" changeset="88654321" timestamp="2025-02-06T15:30:45Z" uid="20083456"
        lat="-23.1912345" lon="118.8156789"/>
  <node id="8012345681" changeset="88654321" timestamp="2025-02-06T15:30:45Z" uid="20083456"
        lat="-23.1867890" lon="118.8115678"/>
  <node id="8012345682" changeset="88654321" timestamp="2025-02-06T15:30:45Z" uid="20083456"
        lat="-23.1804567" lon="118.8098765"/>
  <way id="854987654" changeset="89234567" timestamp="2025-02-06T16:00:30Z" uid="20083456">
    <nd ref="8012345678"/>
    <nd ref="8012345679"/>
    <nd ref="8012345680"/>
    <nd ref="8012345681"/>
    <nd ref="8012345682"/>
    <tag k="highway" v="service"/>
  </way>
</osm>
```
