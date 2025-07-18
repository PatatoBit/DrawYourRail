<script lang="ts">
	import { onMount, onDestroy } from 'svelte';
	import mapboxgl from 'mapbox-gl';
	import 'mapbox-gl/dist/mapbox-gl.css';

	let map: mapboxgl.Map | null = null;
	let mapContainer: HTMLElement | null = null;
	let lng, lat, zoom;

	lng = 100.534942;
	lat = 13.739861;
	zoom = 9;

	let initialState = { lng, lat, zoom };

	const mapboxToken = import.meta.env.VITE_MAPBOX_TOKEN;

	onMount(() => {
		if (mapContainer) {
			mapboxgl.accessToken = mapboxToken;
			map = new mapboxgl.Map({
				container: mapContainer,
				center: [initialState.lng, initialState.lat],
				zoom: initialState.zoom
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

<div class="map" bind:this={mapContainer} />

<style>
	.map {
		position: absolute;
		width: 100%;
		height: 100%;
	}
</style>
