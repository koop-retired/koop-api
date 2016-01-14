# Koop API

## Basic CRUD

### Import/Update dataset
`put :koop/:source_type/:host/:identifier`

### Delete dataset
`delete :koop/:source_type/:host/:identifier`

ArcGIS example

`put :koop/ArcGIS/production/1ef_2`

### Get info
`get :koop/:source_type/:host/:identifier`

## Source
A source exposes 2 functions
### `Source.fetchMetadata`

```javascript
async function fetchMetadata (options)
      return {
      name: String,
      updated: Number,
      retrieved: Number,
      data: Array, // pointers to raw data
      fields: Array // Map("string", "string")
    }
  )

```
ArcGIS example
```javascript
import 'portal'
import 'featureService'
async function fetchMetadata (host, identifier) {
  try {
    const itemId = identifier.split('_')[0]
    const layer = identifier.split('_')[1] || 0
    const item = await portal.getItem(options)
    const service = new FeatureService(item.url, {layer: layer})
    const pages = await service.getPages()
    return {
      name: service.name,
      updated: service.updated,
      retrieved: Date.now(),
      data: pages,
      fields: service.fields
    }
  } catch (e) {
    throw new Error('Request for resource metadata failed')  
  }
}
```
#### `Source.toGeoJSON`
Event Emitter
Source Data -> GeoJSON features || Errors

```javascript
function toGeoJSON (options) {
 const output = _.pipeline(stream => {
  return stream
  .pipe(transformation) // implement source specific logic
  .on('error', e => output.emit('error', e))
 })
 return output
}
```
## Global
The `global` `source_type` is reserved for actions that map across all resources

## Plugins
*A plugin exposes a set of functions that act on a resource*

Example: `get :koop/:source_type/:host/:identifier/:plugin/:action?:query`

Koop-Exporter
* Download file
  * `get :koop/:source_type/:host/export.format?:api:query_params`

Koop-Search
* Search across all resources
`get :koop/global/search?:query`

Koop-Geoservices
* Make a Feature Service style request
* Depends on `Cache` & `Index`
`get :koop/:source_type/:host/FeatureService/0/query`

Koop-Queue
* Manage a job queue
* Add a job onto the queue
`put :koop/:source_type/:host/:identifier/:queue/:job_type`
