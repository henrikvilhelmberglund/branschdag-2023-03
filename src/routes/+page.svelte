<script>
	import { base } from "$app/paths";
	import { sections } from "$lib/data.js";

	let showCompaniesInClassroom = {
		A211: false,
		A214: false,
		A215: false,
		A218: false,
	};

	function showCompanies(key) {
		showCompaniesInClassroom = Object.entries(showCompaniesInClassroom).map(
			(object) => (object[1] = false)
		);
		console.log(showCompaniesInClassroom.key);
		showCompaniesInClassroom[key] = true;
		return true;
	}
</script>

<main class="flex flex-col [&>*]:m-4">
	<div class="w-64 items-center [&>*]:m-2">
		{#each Object.entries(sections) as [sectionKey, sectionValue]}
			<div>
				{#each sectionValue as classrooms}
					{#each Object.entries(classrooms) as [classroom, companies]}
						<button
							on:click={() => {
								showCompanies(classroom);
								console.log(classroom);
							}}
							class="self-center m-2 rounded-md bg-amber-400 p-2 hover:bg-amber-300"
							>{classroom}</button>
						<div class="divide-y-1">
							{#if showCompaniesInClassroom[classroom]}
								{#each companies as company}
									<p>
										{company}
									</p>
								{/each}
							{/if}
						</div>
					{/each}
				{/each}
			</div>
		{/each}
	</div>
</main>

<footer class="m-4 h-64">
	<p>
		by
		<a
			class="underline-blue-600 underline hover:text-blue-600"
			href="https://github.com/henrikvilhelmberglund"
			><img class="inline w-6" src="Henrik.png" alt="avatar" />henrikvilhelmberglund</a>
	</p>
</footer>
