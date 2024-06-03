<script>
	import { onMount } from "svelte";
	import mapboxgl from "mapbox-gl";
	import "../routes/styles.css";

	mapboxgl.accessToken =
		"pk.eyJ1IjoiY2FuYWRpYW51cmJhbmluc3RpdHV0ZSIsImEiOiJjbG95bzJiMG4wNW5mMmlzMjkxOW5lM241In0.o8ZurilZ00tGHXFV-gLSag";

	let map;

	const layers = [
		{
			id: "1-10mil",
			label: "1 to 10 million",
			checked: true,
			color: "#001d29",
		},
		{
			id: "500k-1mil",
			label: "500k to 1 million",
			checked: true,
			color: "#002332",
		},
		{
			id: "100-500k",
			label: "100k to 500k",
			checked: true,
			color: "#004663",
		},
		{
			id: "75-100k",
			label: "75k to 100k",
			checked: true,
			color: "#016895",
		},
		{ id: "50-75k", label: "50k to 75k", checked: true, color: "#018bc6" },
		{ id: "40-50k", label: "40k to 50k", checked: true, color: "#01aef8" },
		{ id: "30-40k", label: "30k to 40k", checked: true, color: "#34bef9" },
		{ id: "20-30k", label: "20k to 30k", checked: true, color: "#67cefb" },
		{ id: "10-20k", label: "10k to 20k", checked: true, color: "#99dffc" },
		{ id: "1-10k", label: "1k to 10k", checked: true, color: "#cceffe" },
		{ id: "0-1k", label: "0 to 1k", checked: true, color: "#ebf9ff" },
	];

	onMount(() => {
		map = new mapboxgl.Map({
			container: "map",
			style: "mapbox://styles/canadianurbaninstitute/clwv2xjui00hg01qp2w4t8slf?fresh=true",
			center: [-89, 58],
			zoom: 3.3,
			maxZoom: 18,
			minZoom: 2,
			// maxBounds: bounds,
			scrollZoom: true,
			attributionControl: false,
		});

		map.addControl(new mapboxgl.NavigationControl(), "top-right");

		map.addControl(
			new mapboxgl.AttributionControl({
				customAttribution: "Canadian Urban Institute",
			}),
		);

		map.on("load", () => {
			layers.forEach((layer) => {
				const visibility = layer.checked ? "visible" : "none";
				map.setLayoutProperty(layer.id, "visibility", visibility);
			});

			var popup = new mapboxgl.Popup({
				closeButton: true,
				closeOnClick: true,
			});

			map.on(
				"click",
				[
					"1-10mil",
					"500k-1mil",
					"100-500k",
					"75-100k",
					"50-75k",
					"40-50k",
					"30-40k",
					"20-30k",
					"10-20k",
					"1-10k",
					"0-1k",
				],
				function (e) {
					// Change the cursor to a pointer when the mouse is over the business-mms layer
					map.getCanvas().style.cursor = "pointer";

					// Get the company name from the feature's properties
					let name = e.features[0].properties.CSDNAME;
					let pop = e.features[0].properties.pop;

					// Set the popup content and location
					popup
						.setLngLat(e.lngLat)
						.setHTML("<h4>" + name + "</h4>" + "Population: " + pop)
						.addTo(map);
				},
			);

			map.on(
				"mouseleave",
				[
					"1-10mil",
					"500k-1mil",
					"100-500k",
					"75-100k",
					"50-75k",
					"40-50k",
					"30-40k",
					"20-30k",
					"10-20k",
					"1-10k",
					"0-1k",
				],
				function () {
					// Reset the cursor when it leaves the business-mms layer
					map.getCanvas().style.cursor = "";
				},
			);

			map.on(
				"mouseenter",
				[
					"1-10mil",
					"500k-1mil",
					"100-500k",
					"75-100k",
					"50-75k",
					"40-50k",
					"30-40k",
					"20-30k",
					"10-20k",
					"1-10k",
					"0-1k",
				],
				function () {
					// Reset the cursor when it leaves the business-mms layer
					map.getCanvas().style.cursor = "pointer";
				},
			);
		});
	});

	function toggleLayer(layerId) {
		const visibility = map.getLayoutProperty(layerId, "visibility");
		if (visibility === "visible") {
			map.setLayoutProperty(layerId, "visibility", "none");
		} else {
			map.setLayoutProperty(layerId, "visibility", "visible");
		}
	}
</script>

<svelte:head>
	<link
		href="https://api.mapbox.com/mapbox-gl-js/v2.14.1/mapbox-gl.css"
		rel="stylesheet"
	/>
	<script
		src="https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v5.0.0/mapbox-gl-geocoder.min.js"
	></script>
</svelte:head>
<div id="content">
	<div id="controls">
		<h4>Census Subdivision Map</h4>
		<h5>Click on a CSD to view information about it.</h5>
		<h5>Population:</h5>
		<div class="controls">
			{#each layers as layer}
				<div class="label">
					<input
						type="checkbox"
						id={layer.id}
						on:change={() => toggleLayer(layer.id)}
						checked
					/>
					<div
						class="box"
						style="display: block;width:20px; height: 20px; background-color: {layer.color}; border: 1px solid #00243350; border-radius: 0.2em"
					></div>
					<label for={layer.id}>{layer.label}</label>
				</div>
			{/each}
		</div>
	</div>
	<div id="map" />
</div>

<style>
	:global(body) {
		padding: 0px;
		margin: 0px;
		background-color: white;
		height: 100%;
	}

	#content {
		display: flex;
		flex-direction: row;
		width: 100%;
	}

	#controls {
		width: 30vw;
		padding: 1em;
		display: flex;
		flex-direction: column;
		gap: 2em;
	}

	#map {
		height: 100vh;
		width: 100%;
		position: relative;
		border-left: 1px solid #eee;
	}

	.label {
		display: flex;
		flex-direction: row;
		padding: 0.5em;
		gap: 0.5em;
	}
</style>
