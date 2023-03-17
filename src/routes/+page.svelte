<script>
	import { base } from "$app/paths";
	import { sections, listOfCompanies } from "$lib/data.js";
	import { test_data } from "$lib/parse.js";

	import Company from "$lib/Company.svelte";

	let showCompaniesInClassroom = {
		// A211: false,
		// A214: false,
		// A215: false,
		// A218: false,
	};

	let viewCompany = "";
	let viewCompanyName = "";

	function showCompanies(key) {
		// showCompaniesInClassroom = Object.entries(showCompaniesInClassroom).map(
		// 	(object) => (object[1] = false)
		// );
		// console.log(showCompaniesInClassroom.key);
		showCompaniesInClassroom[key] = !showCompaniesInClassroom[key];
		return true;
	}

	function parseData(inputObject) {
		// console.log(inputObject);
		try {
			const lines = inputObject
				.split("\n")
				.map((line) => line.trim())
				.filter((line) => line.length > 0);

			const obj = {};
			let currentKey = "";
			let currentArray = [];

			lines.forEach((line) => {
				if (line.endsWith(":")) {
					// we have a new key
					currentKey = line.slice(0, -1);

					if (currentKey === "Om oss/att jobba hos oss") {
					} else if (currentKey === "Mer om oss") {
						// special case for 'Mer om oss' with three keys and empty values
						obj[currentKey] = {
							"Se företagsfilm": "",
							Webbsida: "",
							LinkedIn: "",
						};
					} else if (
						currentKey === "Vi är intresserade av dig som studerar" ||
						currentKey === "Vi är intresserade av att"
					) {
						// start a new array for these keys
						currentArray = [];
						obj[currentKey] = currentArray;
					} else {
						// create a new key with an empty string value
						obj[currentKey] = "";
					}
				} else {
					// we have a value for the current key

					if (currentKey === "Om oss/att jobba hos oss") {
						obj[currentKey] = line;
					} else if (currentKey === "Mer om oss") {
						if (line.includes("Se företagsfilm")) {
							obj[currentKey]["Se företagsfilm"] = line.slice(line.indexOf("(") + 1, -1);
						} else if (line.includes("Webbsida")) {
							if (line.includes("Webbsida1")) {
								obj[currentKey]["Webbsida1"] = line.slice(line.indexOf("(") + 1, -1);
							} else if (line.includes("Webbsida2")) {
								obj[currentKey]["Webbsida2"] = line.slice(line.indexOf("(") + 1, -1);
							} else {
								obj[currentKey]["Webbsida"] = line.slice(line.indexOf("(") + 1, -1);
							}
						} else if (line.includes("LinkedIn")) {
							obj[currentKey]["LinkedIn"] = line.slice(line.indexOf("(") + 1, -1);
						}
						// obj[currentKey][line] = "";
					} else if (
						currentKey === "Vi är intresserade av dig som studerar" ||
						currentKey === "Vi är intresserade av att"
					) {
						// add value to current array
						if (!obj[currentKey]) {
							obj[currentKey] = [];
						}
						obj[currentKey].push(line);
					} else if (currentKey.includes("Kompetenser vi värdesätter")) {
						obj[currentKey] += line;
					} else if (currentKey.includes("Du kan kontakta mig om du har några frågor")) {
						obj[currentKey] += line;
					}
				}
			});
			// console.log("added " + obj);
			return obj;
		} catch (error) {
			return error;
		}
	}
	let allCompanies = {};
	listOfCompanies.forEach((name) => {
		// console.log(name);
		// console.log(test_data[name]);
		allCompanies[name] = parseData(test_data[name]);
	});

	console.log(allCompanies);
</script>

<main class="flex flex-row [&>*]:m-4">
	<div class="flex w-40 flex-col [&>*]:m-2">
		{#each Object.entries(sections) as [sectionKey, sectionValue], i}
			<div>
				{#each sectionValue as classrooms}
					{#each Object.entries(classrooms) as [classroom, companies]}
						<button
							on:click={() => {
								showCompanies(classroom);
								console.log(classroom);
							}}
							class:a-button={i === 0}
							class:b-button={i === 1}
							class:c-button={i === 2}>{classroom}</button>
						<div class="[&>*]:m-1">
							{#if showCompaniesInClassroom[classroom]}
								{#each companies as company}
									<button
										on:click={() => {
											viewCompany = allCompanies[company];
											// TODO make better
											viewCompanyName = company;
										}}
										class="rounded-md bg-slate-300 p-2">
										{company}
									</button>
								{/each}
							{/if}
						</div>
					{/each}
				{/each}
			</div>
		{/each}
	</div>
	<article>
		<!-- {JSON.stringify(viewCompany)} -->
		<!-- {allCompanies["Accigo"]} -->
		<Company name={viewCompanyName} companyData={viewCompany} />
	</article>
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

<style>
	.a-button {
		@apply hover-bg-amber-300 m-2 self-start rounded-md bg-amber-400 p-2;
	}
	.b-button {
		@apply hover-bg-blue-300 m-2 self-start rounded-md bg-blue-400 p-2;
	}
	.c-button {
		@apply hover-bg-red-300 m-2 self-start rounded-md bg-red-400 p-2;
	}
</style>
