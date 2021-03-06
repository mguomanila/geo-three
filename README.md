# Geo-Three

[![npm version](https://badge.fury.io/js/geo-three.svg)](https://badge.fury.io/js/geo-three)[![GitHub version](https://badge.fury.io/gh/tentone%2Fgeo-three.svg)](https://badge.fury.io/gh/tentone%2Fgeo-three)

- Library to display maps using `three.js`. Allows for full world scale visualization of geographic data using tile based chunks.
- Can generate 3D geometry for terrain from terrain height data using software generated tiles or using GPU displacement maps.
- Supports multiple maps service providers ([BingMaps](https://www.microsoft.com/en-us/maps), [GoogleMaps](https://developers.google.com/maps/documentation), [HereMaps](https://developer.here.com/), [MapBox](https://docs.mapbox.com/api/), [MapTiler](https://www.maptiler.com/), [OpenMapTiles](https://openmaptiles.org/), [OpenStreetMaps](https://www.openstreetmap.org/)).
  - Each one of these map providers require a developer account and a API configuration to be used.
  - Its possible to implement new provides using the `MapProvider` interface.
  - Providers should have a tile based map system to be supported by the library.
 - You can test the [live demo of the library running](https://tentone.github.io/geo-three/examples/index.html) from the GitHub page.

<img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/terrain_a.png" width="330"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/terrain_b.png" width="348">



### Usage

- To add the library to your project get the library trough NPM alongside with three.js and use it as a ES module.
- You can also include the library directly in you webpage but ensure first that THREE is available globally.

```javascript
// Create a map tiles provider object
var provider = new OpenStreetMapsProvider();

// Create the map view and add it to your THREE scene
var map = new MapView(MapView.PLANAR, provider);
scene.add(map);
```



### Tile Loading

- Tiles are fetched from the service API configured, each on of the services requires specific configuration using the specific `MapProvider` object.

- Base tiles are always loaded at the beginning of the process, then each frame a couple of rays are casted into the tile tree. The number of rays can be configured using the `MapView` `subdivisionRays` attribute.

- The distance of the ray to the camera is used to define if the node needs to simplified or sub-divided. These values can be configured using the `thresholdUp` and `thresholdDown` values.

  ​    

<img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/tiles.png" width="350">





### Data Providers

- The library as support for multiple data providers that have to be configured beforehand. Most of these data providers rely on external API that differ from service to service.
- Each one of them has its own provider object implementation of the `MapProvider` interface.

- The `DebugProvider` provides information about the tiles loaded, shows the zoom level and the coordinates of the tile relative to the origin in that specific level.

<img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/bing_sat.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/heremaps_sat.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/mapbox_sat.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/bing_vector.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/heremaps_vector.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/mapbox_vector.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/osm_vector.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/debug.png" width="270"><img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/providers/mapbox_height.png" width="270">



### Custom Data Providers

- It is possible to create new data providers to access data other tile sources. New data sources have to provide access to the rasterized tile as a compatible DOM element (e.g. [Image](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement), [Canvas](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement), ...)
- Custom providers have to extend the base `MapProvider` class and implement the `fetchTile(zoom, x, y)` method that returns a `Promise` with access to the tile data.
- Bellow is an implementation of a provider to access [OpenStreetMaps](https://www.openstreetmap.org/) tile data using the [Tile API](https://wiki.openstreetmap.org/wiki/Tiles) the provider simply loads the URL data into a image element.
- These methods are called directly by nodes being loaded into the scene. These should be always asynchronous and should avoid any blocking actions.

```javascript
export class OpenStreetMapsProvider extends MapProvider
{
	constructor(address) {super();}

	fetchTile(zoom, x, y)
	{
		return new Promise((resolve, reject) =>
		{
			var image = document.createElement("img");
			image.onload = function(){resolve(image);};
			image.onerror = function(){reject();};
			image.crossOrigin = "Anonymous";
			image.src = "https://a.tile.openstreetmap.org/" + zoom + "/" + x + "/" + y + ".png";
		});
	}
}
```

- Tiles coordinates for each zoom level are counted from the left-top corner sequentially across the tiles.
- Different API's might use different methods to index these tiles (e.g. [Bing maps](https://docs.microsoft.com/en-us/bingmaps/articles/bing-maps-tile-system) uses a different indexing method).
  - These coordinates need to be adapted to ensure correct loading when using this library.

<img src="https://raw.githubusercontent.com/tentone/geo-three/master/readme/coords.png" width="600">

### License

- Project uses a MIT license that allow for commercial usage of the platform without any cost.
- The license is available on the project GitHub page

