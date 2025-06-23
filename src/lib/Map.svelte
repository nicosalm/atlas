<script>
  import { onMount } from 'svelte';
  import { browser } from '$app/environment';
  import 'maplibre-gl/dist/maplibre-gl.css';

  let {
    imagePath,
    width = 1920,
    height = 1080,
    initialZoom = 1,
    maxZoom = 6,
    minZoom = 0,
    center = [0, 0],
    containerHeight = '80vh',
    padding = 20,
    renderWorldCopies = false
  } = $props();

  let mapContainer = $state();
  let map = $state();

  let aspectRatio = $derived(width / height);
  let coordinateWidth = $derived(96); // Base width
  let coordinateHeight = $derived(coordinateWidth / aspectRatio);
  let bounds = $derived([
    [-coordinateWidth, coordinateHeight],   // top-left
    [coordinateWidth, coordinateHeight],    // top-right
    [coordinateWidth, -coordinateHeight],   // bottom-right
    [-coordinateWidth, -coordinateHeight]   // bottom-left
  ]);
  let fitBounds = $derived([
    [-coordinateWidth, -coordinateHeight],
    [coordinateWidth, coordinateHeight]
  ]);

  onMount(async () => {
    if (browser) {
      const maplibregl = (await import('maplibre-gl')).default;

      map = new maplibregl.Map({
        container: mapContainer,
        style: {
          version: 8,
          sources: {},
          layers: [],
          glyphs: 'https://demotiles.maplibre.org/font/{fontstack}/{range}.pbf'
        },
        center: center,
        zoom: initialZoom,
        maxZoom: maxZoom,
        minZoom: minZoom,
        renderWorldCopies: renderWorldCopies,
      });

      map.on('load', () => {
        map.addSource('map-image', {
          type: 'image',
          url: imagePath,
          coordinates: bounds
        });

        map.addLayer({
          id: 'map-background',
          type: 'raster',
          source: 'map-image'
        });

        // fit to show the entire map
        map.fitBounds(fitBounds, {
          padding: padding
        });
      });

      return () => map?.remove();
    }
  });

  export function getMap() {
    return map;
  }

  export function flyTo(coordinates, zoom = initialZoom + 1) {
    if (map) {
      map.flyTo({
        center: coordinates,
        zoom: zoom,
        duration: 2000
      });
    }
  }
</script>

<div
  bind:this={mapContainer}
  class="map-container"
  style="height: {containerHeight};"
></div>

<style>
  .map-container {
    width: 100%;
    position: relative;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  }
</style>
