<script>
	import SortIcon from '../../ui/SortIcon.svelte';
	import { safeExtractColumn } from './datatable.js';

	export let rowNumbers = undefined;
	export let headerColor = undefined;
	export let headerFontColor = undefined;
	export let orderedColumns = undefined;
	export let columnSummary = undefined;
	export let sortable = undefined;
	export let sort = undefined;
	export let formatColumnTitles = undefined;
	export let sortBy = undefined;
	export let wrapTitles = undefined;
	export let compact = undefined;
</script>

<thead>
	{#if orderedColumns.length > 0}
		{@const columnsWithGroupSpan = orderedColumns.map((column, index, array) => {
			// Determine if this column starts a new group or continues an existing one
			let isNewGroup = index === 0 || column.colGroup !== array[index - 1].colGroup;
			let span = 1;
			if (column.colGroup) {
				// Count how many contiguous columns have the same group
				for (let i = index + 1; i < array.length && array[i].colGroup === column.colGroup; i++) {
					span++;
				}
			}
			return { ...column, isNewGroup, span: isNewGroup ? span : 0 }; // Only assign span to the first column in a group
		})}
		{#if columnsWithGroupSpan.length > 0}
			<tr class="border-0" style:background-color={headerColor}>
				{#if rowNumbers}
					<th
						class="index w-[2%] {compact ? 'text-xs py-[1px] px-[4px]' : 'py-[2px] px-[8px]'}"
						style:background-color={headerColor}
					/>
				{/if}
				{#each columnsWithGroupSpan as column}
					{#if column.colGroup && column.isNewGroup}
						<th
							role="columnheader"
							colspan={column.span}
							class="pt-1 align-bottom text-gray-900 {compact ? 'px-[1px]' : 'px-[2px]'}"
						>
							<!-- Group header with dynamic colspan -->
							<div class=" border-b-[1px] border-b-gray-600 whitespace-normal pb-[2px]">
								{column.colGroup}
							</div>
						</th>
					{:else if column.colGroup}
						<!-- Not new group, th covered by previous column span-->
					{:else}
						<!-- Not part of a group - empty header cell -->
						<th></th>
					{/if}
				{/each}
			</tr>
		{/if}
	{/if}

	<tr class="border-b border-gray-600">
		{#if rowNumbers}
			<th
				role="columnheader"
				class="index w-[2%] {compact ? 'text-xs py-[1px] px-[4px]' : 'py-[2px] px-[8px]'}"
				style:background-color={headerColor}
			/>
		{/if}
		{#each orderedColumns as column}
			<th
				role="columnheader"
				class="{safeExtractColumn(column, columnSummary).type} {compact
					? 'text-xs py-[1px] px-[4px]'
					: 'py-[2px] px-[8px]'}"
				style:text-align={column.align}
				style:color={headerFontColor}
				style:background-color={headerColor}
				style:cursor={sortable ? 'pointer' : 'auto'}
				style:white-space={column.wrapTitle || wrapTitles ? 'normal' : 'nowrap'}
				on:click={sortable ? sort(column.id) : ''}
				style:vertical-align="bottom"
			>
				{column.title
					? column.title
					: formatColumnTitles
						? safeExtractColumn(column, columnSummary).title
						: safeExtractColumn(column, columnSummary).id}
				{#if sortBy.col === column.id}
					<SortIcon ascending={sortBy.ascending} />
				{/if}
			</th>
		{/each}
	</tr>
</thead>

<style>
	th {
		white-space: nowrap;
		overflow: hidden;
	}

	th:first-child {
		padding-left: 3px;
	}

	.index {
		color: var(--grey-300);
		text-align: left;
		max-width: -moz-min-content;
		max-width: min-content;
	}

	.string {
		text-align: left;
	}

	.date {
		text-align: left;
	}

	.number {
		text-align: right;
	}

	.boolean {
		text-align: left;
	}
</style>
