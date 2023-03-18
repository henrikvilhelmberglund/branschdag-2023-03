<script>
	import { afterUpdate } from "svelte";
	export let companyData;
	export let name;
	export let where;

	let enabled = {};
	export let enabledSnapshot = {};

	function applySnapshot() {
		enabled = enabledSnapshot;
	}

	afterUpdate(() => {
		applySnapshot();
	});
</script>

<h1 class="text-xl">{name}</h1>
{#if where}
	<h2 class="text-sm">{where}</h2>
{/if}
{#each Object.entries(companyData) as [key, value]}
	<button
		on:click={() => {
			// reset();
			enabled[key] =
				key === "Om oss/att jobba hos oss"
					? (enabled[key] = !enabled[key])
					: key.includes("Mer om oss") || key.includes("mer om oss")
					? (enabled[key] = !enabled[key])
					: key === "Vi är intresserade av dig som studerar"
					? (enabled[key] = !enabled[key])
					: key === "Vi är intresserade av att"
					? (enabled[key] = !enabled[key])
					: key.includes("Kompetenser vi värdesätter")
					? (enabled[key] = !enabled[key])
					: key.includes("kontakta mig om du har") || key.includes("Kontakta mig om du har")
					? (enabled[key] = !enabled[key])
					: key.includes("Extra")
					? (enabled[key] = !enabled[key])
					: "";
		}}
		class="key"
		class:toggled={enabled[key]}>{key}</button>
	{#if key === "Om oss/att jobba hos oss"}
		{#if enabled[key]}
			{#each value as line}
				<p>
					{line}
				</p>
			{/each}
		{/if}
	{/if}
	{#if key.includes("Mer om oss") || key.includes("mer om oss")}
		{#if enabled[key]}
			<div class="flex-col">
				{#each Object.entries(value) as [linkType, link]}
					<!-- check if link actually is something  -->
					{#if link}
						<a class="underline-2 underline-solid underline-blue underline" href={link}
							>{linkType}</a>
					{/if}
				{/each}
			</div>
		{/if}
	{/if}
	{#if key === "Vi är intresserade av dig som studerar"}
		{#if enabled[key]}
			{#each value as studentType}
				<p>
					{studentType}
				</p>
			{/each}
		{/if}
	{/if}
	{#if key === "Vi är intresserade av att"}
		{#if enabled[key]}
			{#each value as ourInterest}
				<p>
					{ourInterest}
				</p>
			{/each}
		{/if}
	{/if}
	{#if key.includes("Kompetenser vi värdesätter")}
		{#if enabled[key]}
			<p>
				{value}
			</p>
		{/if}
	{/if}
	{#if key.includes("kontakta mig om du har") || key.includes("Kontakta mig om du har")}
		{#if enabled[key]}
			{#each value as address}
				{#if !value.includes("(")}
					<a
						class="underline-2 underline-solid underline-blue block break-all underline"
						href="mailto:{address}">{address}</a>
				{/if}
			{:else}
				<a
					class="underline-2 underline-solid underline-blue break-all underline"
					href="mailto:{address.split('(')[0]}">{address.split("(")[0]}</a>
				<p>({value.split("(")[1]}</p>
			{/each}
		{/if}
	{/if}
	{#if key.includes("Extra")}
		{#if enabled[key]}
			<p>
				{value}
			</p>
		{/if}
	{/if}
{/each}

<style>
	.key {
		@apply rounded-md bg-slate-300 p-2;
	}
	.toggled {
		@apply rounded-md bg-blue-300 p-2;
	}
</style>
