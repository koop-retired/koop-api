# Koop API

## Basic CRUD

### Import dataset
`put :koop/:source_type/:host/:uuid`

ArcGIS example

`put :koop/ArcGIS/production/1ef_2`

### Get info
`get :koop/:source_type/:host/:uuid`

## Source Type
A source exposes 3 functions
1. `fetchMetadata`

```javascript
function fetchMetadata (options)
  Promise.resolve(
    {
      "name": name,
      "updated": timestamp,
      "data": [URI...URI] // pointers to raw data
    }
  )

```
2. `toGeoJSON`
Event Emitter
Source Data -> GeoJSON features || Errors

```javascript
function toGeoJSON (options) {
 const output = _.pipeline(stream => {
  return stream
  .pipe(transformation) // impliment source specific logic
  .on('error', e => output.emit(e)
 })
 return output
}
```
## Global
The `global` `source_type` is reserved for actions that map across all resources

## Plugins
*A plugin exposes a set of functions that act on a resource*

Example: `get :koop/:source_type/:host/:uuid/:plugin/:action?:query`

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
`put :koop/:source_type/:host/:uuid/:queue/:job_type`
