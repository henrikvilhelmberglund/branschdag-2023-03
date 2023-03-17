<script>
	export let companyData;
	export let name;

	let showAbout = false;
	let showMore = false;
	let showStudentsWeAreInterestedIn = false;
	let showInterestedIn = false;
	let showLookingFor = false;
	let showContact = false;
	let enabled = {};
</script>

<h1 class="text-xl">{name}</h1>
{#each Object.entries(companyData) as [key, value], i}
	<button
		on:click={() => {
			// reset();
			enabled[i] =
				i === 0
					? (showAbout = !showAbout)
					: i === 1
					? (showMore = !showMore)
					: i === 2
					? (showStudentsWeAreInterestedIn = !showStudentsWeAreInterestedIn)
					: i === 3
					? (showInterestedIn = !showInterestedIn)
					: i === 4
					? (showLookingFor = !showLookingFor)
					: i === 5
					? (showContact = !showContact)
					: "";
		}}
		class="key"
		class:toggled={enabled[i]}>{key}</button>
	{#if key === "Om oss/att jobba hos oss"}
		{#if showAbout}
			<p>
				{value}
			</p>
		{/if}
	{:else if key === "Mer om oss"}
		{#if showMore}
			<div class="flex-col">
				{#each Object.entries(value) as [linkType, link]}
					<a class="underline-2 underline-solid underline-blue underline" href={link}>{linkType}</a>
				{/each}
			</div>
		{/if}
	{:else if key === "Vi är intresserade av dig som studerar"}
		{#if showStudentsWeAreInterestedIn}
			{#each value as studentType}
				<p>
					{studentType}
				</p>
			{/each}
		{/if}
	{:else if key === "Vi är intresserade av att"}
		{#if showInterestedIn}
			{#each value as ourInterest}
				<p>
					{ourInterest}
				</p>
			{/each}
		{/if}
	{:else if key.includes("Kompetenser vi värdesätter")}
		{#if showLookingFor}
			<p>
				{value}
			</p>
		{/if}
	{:else if key === "Du kan kontakta mig om du har några frågor"}
		{#if showContact}
			<a href="mailto:{value}">{value}</a>
		{/if}
	{:else}
		<p class="text-lg">what{key}</p>
		<p>
			what is this
			{value}
		</p>
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
