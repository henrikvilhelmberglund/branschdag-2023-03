<script>
	export let companyData;
	export let name;
	export let where;

	let showAbout = false;
	let showMore = false;
	let showStudentsWeAreInterestedIn = false;
	let showInterestedIn = false;
	let showLookingFor = false;
	let showContact = false;
	let enabled = {};
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
					? (showAbout = !showAbout)
					: key.includes("Mer om oss") || key.includes("mer om oss")
					? (showMore = !showMore)
					: key === "Vi är intresserade av dig som studerar"
					? (showStudentsWeAreInterestedIn = !showStudentsWeAreInterestedIn)
					: key === "Vi är intresserade av att"
					? (showInterestedIn = !showInterestedIn)
					: key.includes("Kompetenser vi värdesätter")
					? (showLookingFor = !showLookingFor)
					: key.includes("kontakta mig om du har") || key.includes("Kontakta mig om du har")
					? (showContact = !showContact)
					: "";
		}}
		class="key"
		class:toggled={enabled[key]}>{key}</button>
	{#if key === "Om oss/att jobba hos oss"}
		{#if showAbout}
			<p>
				{value}
			</p>
		{/if}
	{/if}
	{#if key.includes("Mer om oss") || key.includes("mer om oss")}
		{#if showMore}
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
		{#if showStudentsWeAreInterestedIn}
			{#each value as studentType}
				<p>
					{studentType}
				</p>
			{/each}
		{/if}
	{/if}
	{#if key === "Vi är intresserade av att"}
		{#if showInterestedIn}
			{#each value as ourInterest}
				<p>
					{ourInterest}
				</p>
			{/each}
		{/if}
	{/if}
	{#if key.includes("Kompetenser vi värdesätter")}
		{#if showLookingFor}
			<p>
				{value}
			</p>
		{/if}
	{/if}
	{#if key.includes("kontakta mig om du har") || key.includes("Kontakta mig om du har")}
		{#if showContact}
			{#if !value.includes("(")}
				<a
					class="underline-2 underline-solid underline-blue break-all underline"
					href="mailto:{value}">{value}</a>
			{/if}
		{:else}
			<a
				class="underline-2 underline-solid underline-blue break-all underline"
				href="mailto:{value.split('(')[0]}">{value.split("(")[0]}</a>
			<p>({value.split("(")[1]}</p>
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
