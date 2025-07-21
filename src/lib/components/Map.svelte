<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import mapboxgl from 'mapbox-gl';
	import 'mapbox-gl/dist/mapbox-gl.css';
	import MapStylePicker from './MapStylePicker.svelte';

	let map: mapboxgl.Map | null = null;
	let mapContainer: HTMLElement | null = null;
	let lng: number, lat: number, zoom: number;
	const mapboxToken = import.meta.env.VITE_MAPBOX_TOKEN;
	let mapStyle = 'mapbox://styles/mapbox/light-v9';

	lat = 13.739861;
	lng = 100.534942;
	zoom = 9;
	let initialState = { lng, lat, zoom };

	function updateData() {
		lat = map?.getCenter()?.lat ?? lat;
		lng = map?.getCenter()?.lng ?? lng;
		zoom = map?.getZoom() ?? zoom;
	}

	function handleReset() {
		lng = initialState.lng;
		lat = initialState.lat;
		zoom = initialState.zoom;
		if (map) {
			map.setCenter([lng, lat]);
			map.setZoom(zoom);
		}
	}

	function handleStyleChange(event: Event) {
		const target = event.target as HTMLSelectElement;
		if (map) {
			map.setStyle(target.value);
		}
	}

	onMount(() => {
		if (mapContainer) {
			mapboxgl.accessToken = mapboxToken;
			map = new mapboxgl.Map({
				container: mapContainer,
				center: [initialState.lng, initialState.lat],
				zoom: initialState.zoom
			});

			map.on('move', () => {
				updateData();
			});
		} else if (mapContainer === null) {
			console.error('Map container is null');
		} else {
			console.error('Map container is not defined');
		}
	});

	onDestroy(() => {
		if (map) {
			map.remove();
		}
	});
</script>

<MapStylePicker currentStyle={mapStyle} on:change={handleStyleChange} />
<div class="map" bind:this={mapContainer}></div>

<div class="sidebar">
	Longitude: {lng.toFixed(4)} | Latitude: {lat.toFixed(4)} | Zoom:
	{zoom.toFixed(2)}
</div>

<button onclick={handleReset} class="reset-button">Reset</button>

<style>
	.map {
		position: absolute;
		width: 100%;
		height: 100%;
	}

	.sidebar {
		background-color: rgb(35 55 75 / 90%);
		color: #fff;
		padding: 6px 12px;
		font-family: monospace;
		z-index: 1;
		position: absolute;
		top: 50px;
		left: 0;
		margin: 12px;
		border-radius: 4px;
	}

	.reset-button {
		position: absolute;
		top: 100px;
		z-index: 1;
		left: 12px;
		padding: 4px 10px;
		border-radius: 10px;
		cursor: pointer;
	}
</style>
