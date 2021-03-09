# STAC Spatio-Temporal Periodic Extent Extension

- **Title: Spatio-Temporal Periodic Extent**
- **Identifier: periodic-extent**
- **Field Name Prefix: periodic-extent**
- **Scope: Collection**
- **Extension [Maturity Classification](hhttps://github.com/radiantearth/stac-api-spec/blob/master/extensions.md#extension-maturity): Proposal**

This document explains the fields of the STAC Spatio-Temporal Periodic Extent Extension to a STAC Collection. 
STAC Collection spatio temporal extents does not allow to encode information about the temporal resolution.

STAC Spatio-Temporal Periodic Extent Extension extension allows to overcome this limitation and allows to specifiy temporal resolutions in the STAC Collection temporal extent.

Extensions proposes `bbox` field changes.

## Examples

- [Classic STAC Collection extent example](examples/classic.json)
- [Periodic STAC Collection extent example](examples/periodic.json)

## Schema

- [JSON Schema](json-schema/schema.json)

### Extent Object

The object describes the spatio-temporal extents of the Collection. Both spatial and temporal extents are required to be specified.

| Element  | Type                                              | Description                                                           |
| -------- | ------------------------------------------------- | --------------------------------------------------------------------- |
| spatial  | [Spatial Extent Object](#spatial-extent-object)   | **REQUIRED.** Potential *spatial extents* covered by the Collection.  |
| temporal | [Temporal Extent Object](#temporal-extent-object) | **REQUIRED.** Potential *temporal extents* covered by the Collection. |

#### Spatial Extent Object

The object describes the spatial extents of the Collection.

| Element | Type         | Description                                                          |
| ------- | ------------ | -------------------------------------------------------------------- |
| bbox    | \[\[number]] | **REQUIRED.** Potential *spatial extents* covered by the Collection. |

**bbox**: Bounding Boxes of the assets represented by this Collection using either 2D or 3D geometries. Each outer array element can be a separate bounding box, but it is recommended to only use multiple bounding boxes if a union of them would then include a large uncovered area (e.g. the union of Germany and Chile).

The length of the inner array must be 2*n where n is the number of dimensions. The array contains all axes of the southwesterly most extent followed by all axes of the northeasterly most extent specified in Longitude/Latitude or Longitude/Latitude/Elevation based on [WGS 84](http://www.opengis.net/def/crs/OGC/1.3/CRS84). When using 3D geometries, the elevation of the southwesterly most extent is the minimum depth/height in meters and the elevation of the northeasterly most extent is the maximum.

The coordinate reference system of the values is WGS 84 longitude/latitude. Example that covers the whole Earth: `[[-180.0, -90.0, 180.0, 90.0]]`.  Example that covers the whole earth with a depth of 100 meters to a height of 150 meters: `[[-180.0, -90.0, -100.0, 180.0, 90.0, 150.0]]`.

#### Temporal Extent Object

The object describes the temporal extents of the Collection.

| Element  | Type               | Description                                                           |
| -------- | ------------------ | --------------------------------------------------------------------- |
| interval | \[\[string\|[Periodic Interval](#periodic-interval)\|null]] | **REQUIRED.** Potential *temporal extents* covered by the Collection. |

**interval**: Each outer array element can be a separate temporal extent, but it is recommended to only use multiple temporal extents if a union of them would then include a large uncovered time span (e.g. only having data for the years 2000, 2010 and 2020).

Each inner array consists of exactly two dates and times. Each date and time MUST be formatted according to [RFC 3339, section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6). The temporal reference system is the Gregorian calendar.

Open date ranges are supported by setting either the start or the end time to `null`. Example for data from the beginning of 2019 until now: `[["2009-01-01T00:00:00Z", null]]`. 

###### Interval example

##### Periodic Interval

| Element | Type         | Description                                                          |
| ------- | ------------ | -------------------------------------------------------------------- |
| range   | \[\[string\|null]] | **REQUIRED.** Potential *temporal extents* covered by the Collection. |
| period  | string | **REQUIRED.** ISO 8601:1988(E) compilant period: PnYnMnD |

**range**: Behaves like the interval field in the non periodic case.
**period**: Is a required field. Period is represented by ISO 8601:1988(E) compilant string.

#### Spatio-Temporal Extent examples

##### Periodic interval example:

```javascript
{
  "spatial": {
    "bbox": [
      [
        -101.40824987104652,
        37.79718802132125,
        -73.94863288954222,
        41.41061537114088
      ]
    ]
  },
  "temporal": {
    "interval": [
      {
        "range": [
          "2018-05-01T00:00:00Z",
          "2018-08-01T00:00:00Z"
        ],
        "period": "P1M"
      }
    ]
  }
}
```


##### Interval example (original STAC Collection extent):

```javascript
{
  "spatial": {
    "bbox": [
      [
        -78.32015745740821,
        37.79447027981874,
        -73.92530589507936,
        41.41037947529183
      ]
    ]
  },
  "temporal": {
    "interval": [
      [
        "2018-05-01T00:00:00Z",
        "2018-08-01T00:00:00Z"
      ]
    ]
  }
}
```

## Implementations

[GeoTrellis Server](https://github.com/geotrellis/geotrellis-server/) can use the [STAC API](https://github.com/radiantearth/stac-api-spec) server (it was tested with [Franklin](https://github.com/azavea/franklin)) as the input source of rasters. And it uses the STAC Collection periodic metadata to advertise it though OGC Endpoints.

[Franklin](https://github.com/azavea/franklin) uses STAC4S and as a consequence ingest and query of such collections.

[STAC4S](https://github.com/azavea/stac4s) that supports collection with this extension.
