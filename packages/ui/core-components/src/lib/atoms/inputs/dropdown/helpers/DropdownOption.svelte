<script context="module">
	export const evidenceInclude = true;

	let manualIdx = 0;
</script>

<script>
	import { getContext, onMount } from 'svelte';
	import { DropdownContext } from '../constants.js';
	export let value;
	export let valueLabel = value;
	/** @type {number} */
	export let idx = -1;

	/**
	 * For internal use only
	 */
	export let __auto = false;

	if (!__auto) {
		idx = manualIdx++;
	}

	/** @type {import("../constants.js").EvidenceDropdownContext} */
	const dropdownContext = getContext(DropdownContext);
	onMount(() => dropdownContext.registerOption({ value, label: valueLabel, idx, __auto }));
</script>
