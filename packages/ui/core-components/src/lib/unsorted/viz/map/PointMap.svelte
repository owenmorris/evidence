<script context="module">
	export const evidenceInclude = true;
</script>

<script>
	import Points from './components/Points.svelte';
	import BaseMap from './BaseMap.svelte';
	import ErrorChart from '../core/ErrorChart.svelte';
	import EmptyChart from '../core/EmptyChart.svelte';
	import { QueryLoad } from '../../../atoms/query-load';
	import { Query } from '@evidence-dev/sdk/usql';

	/** @type {'pass' | 'warn' | 'error' | undefined} */
	export let emptySet = undefined;

	/** @type {string | undefined} */
	export let emptyMessage = undefined;

	let error;

	/** @type {import("@evidence-dev/sdk/usql").QueryValue} */
	export let data;
	if (!data) {
		error = 'data is required';
	}

	/** @type {string|undefined} */
	export let lat = undefined; // column containing lat values
	if (!lat) {
		error = 'lat is required (column containing latitude data)';
	}

	/** @type {string|undefined} */
	export let long = undefined; // column containing long values
	if (!long) {
		error = 'long is required (column containing longitude data)';
	}

	/** @type {number} */
	export let startingLat = undefined;
	/** @type {number} */
	export let startingLong = undefined;
	/** @type {number} */
	export let startingZoom = undefined;

	/** @type {number} */
	export let height = undefined; // height in pixels

	/** @type {string} */
	export let basemap = undefined;

	/** @type {string|undefined} */
	export let title = undefined;

	const chartType = 'Point Map';

	const initialHash = Query.isQuery(data) ? data.hash : undefined;
	$: isInitial = data?.hash === initialHash;
</script>

<QueryLoad {data} let:loaded>
	<EmptyChart slot="empty" {emptyMessage} {emptySet} {chartType} {isInitial} />
	<ErrorChart let:loaded slot="error" {chartType} error={error ?? loaded.error.message} />

	<BaseMap {startingLat} {startingLong} {startingZoom} {height} {basemap} {title}>
		<Points data={loaded} {lat} {long} {...$$restProps} />
	</BaseMap>
</QueryLoad>
