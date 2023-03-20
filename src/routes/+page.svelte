<script>
	import { base } from "$app/paths";
	import { sections, listOfCompanies } from "$lib/data.js";
	import { test_data } from "$lib/parse.js";

	import Company from "$lib/Company.svelte";
	import AutoComplete from "simple-svelte-autocomplete";

	$: whereIsTheCompany = findCompany(sections, viewCompanyName);

	function findCompany(sections, companyName) {
		for (const section in sections) {
			for (const subSection of sections[section]) {
				for (const key in subSection) {
					if (subSection[key].includes(companyName)) {
						return key;
					}
				}
			}
		}
		return null;
	}

	let showCompaniesInClassroom = {
		// A211: false,
		// A214: false,
		// A215: false,
		// A218: false,
	};

	let toggledClassrooms = {};

	// let viewCompany = "";
	let viewCompanyName = "";

	$: viewCompany = allCompanies[viewCompanyName] ?? "";

	let filteredListOfCompanies = listOfCompanies;

	$: if (selectedDropdown !== "Any") {
		filteredListOfCompanies = listOfCompanies.filter((company) =>
			allCompanies[company]["Vi är intresserade av dig som studerar"].includes(selectedDropdown)
		);
	}

	$: if (selectedDropdown === "Any") {
		filteredListOfCompanies = listOfCompanies;
	}

	let selectedDropdown;
	let dropdownOptions = [
		"Any",
		"Business Intelligence – analytiker",
		"Data Engineer",
		//
		// "Data Engineers",
		"DevOps Engineer",
		"Distans – IT-säkerhetstekniker",
		"Frontend-utvecklare",
		"IT-infrastrukturspecialist",
		"IT-säkerhetstekniker",
		"Javautvecklare",
		//
		// "Javautveckling",
		//
		// "Kvalitetssäkare och testare",
		"Kvalitetssäkrare och testare inom IT",
		"Mjukvaruutvecklare, Inbyggda system och IoT",
		//
		// "Mjukvaruutvecklare, Inbyggda system och IoT (Internet of Things-utvecklare)",
		"Programutvecklare .NET",
		"Programutvecklare Java",
		//
		// "Pythonutvecklare",
		// "Pythonutvecklare för AI",
		"Pythonutvecklare inom AI",
		"UX-designer",
		"Virtual Reality-utvecklare",
		//
		// "VR",
		"Webbutvecklare .NET",
		"Webbutvecklare fullstack open source",
	];

	let enabledThingies;

	// Sveltekit Snapshot
	/** @type {import('./$types').Snapshot<string>} */
	export const snapshot = {
		capture: () => {
			return {
				showCompaniesInClassroom: showCompaniesInClassroom,
				viewCompany: viewCompany,
				viewCompanyName: viewCompanyName,
				selectedDropdown: selectedDropdown,
				enabledThingies: enabledThingies,
				toggledClassrooms: toggledClassrooms,
			};
		},
		restore: (obj) => {
			showCompaniesInClassroom = obj.showCompaniesInClassroom;
			viewCompany = obj.viewCompany;
			viewCompanyName = obj.viewCompanyName;
			selectedDropdown = obj.selectedDropdown;
			enabledThingies = obj.enabledThingies;
			toggledClassrooms = obj.toggledClassrooms;
		},
	};

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

					if (
						currentKey.includes("Mer om oss") ||
						currentKey.includes("mer om oss") ||
						currentKey.includes("läs mer om oss")
					) {
						// special case for 'Mer om oss' with three keys and empty values
						obj[currentKey] = {
							"Se företagsfilm": "",
							Webbsida: "",
							LinkedIn: "",
						};
					} else if (
						currentKey === "Vi är intresserade av dig som studerar" ||
						currentKey === "Vi är intresserade av att" ||
						currentKey.includes("kontakta mig om du har några frågor") ||
						currentKey.includes("Kontakta mig om du har några frågor") ||
						currentKey.includes("Kontakta mig om du har frågor") ||
						currentKey === "Om oss/att jobba hos oss"
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
						if (!obj[currentKey]) {
							obj[currentKey] = [];
						}
						obj[currentKey].push(line);
					} else if (
						currentKey.includes("Mer om oss") ||
						currentKey.includes("mer om oss") ||
						currentKey.includes("läs mer om oss")
					) {
						if (line.includes("företagsfilm") || line.includes("Företagsfilm")) {
							obj[currentKey]["Se företagsfilm"] = line.slice(line.indexOf("(") + 1, -1);
						}
						if (line.includes("Webbsida")) {
							if (line.includes("Webbsida1")) {
								obj[currentKey]["Webbsida1"] = line.slice(line.indexOf("(") + 1, -1);
							} else if (line.includes("Webbsida2")) {
								obj[currentKey]["Webbsida2"] = line.slice(line.indexOf("(") + 1, -1);
							} else {
								obj[currentKey]["Webbsida"] = line.slice(line.indexOf("(") + 1, -1);
							}
						}
						if (line.includes("Hemsida")) {
							obj[currentKey]["Hemsida"] = line.slice(line.indexOf("(") + 1, -1);
						}
						if (line.includes("LinkedIn")) {
							obj[currentKey]["LinkedIn"] = line.slice(line.indexOf("(") + 1, -1);
						}
						if (line.includes("Rörlig företagspresentation")) {
							obj[currentKey]["Rörlig företagspresentation"] = line.slice(
								line.indexOf("(") + 1,
								-1
							);
						}
						// obj[currentKey][line] = "";
					} else if (currentKey === "Vi är intresserade av att") {
						// add value to current array
						if (!obj[currentKey]) {
							obj[currentKey] = [];
						}
						obj[currentKey].push(line);
					} else if (currentKey === "Vi är intresserade av dig som studerar") {
						// add value to current array
						// check for typos in data
						if (!obj[currentKey]) {
							obj[currentKey] = [];
						}
						if (!dropdownOptions.includes(line)) {
							throw new Error(`invalid subject string: ${line}`);
						}
						obj[currentKey].push(line);
					} else if (
						currentKey.includes("Kompetenser vi värdesätter") ||
						currentKey.includes("Kompetenser som vi värdesätter")
					) {
						obj[currentKey] += line;
					} else if (currentKey.includes("Extra")) {
						obj[currentKey] += line;
					} else if (
						currentKey.includes("kontakta mig om du har några frågor") ||
						currentKey.includes("Kontakta mig om du har några frågor") ||
						currentKey.includes("Kontakta mig om du har frågor") ||
						currentKey.includes("Kontakta mig vid frågor")

					) {
						if (!obj[currentKey]) {
							obj[currentKey] = [];
						}
						obj[currentKey].push(line);
						// obj[currentKey] += line;
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

	// console.log(allCompanies);
</script>

<label for="filter-dropdown">Filter by subject</label>
<select class="border" bind:value={selectedDropdown} name="filter-dropdown" id="">
	{#each dropdownOptions as dropdownOption}
		<option value={dropdownOption}>{dropdownOption}</option>
	{/each}
</select>
<p>Search</p>
<AutoComplete
	class="border border-solid"
	items={filteredListOfCompanies}
	bind:selectedItem={viewCompanyName} />
<main class="flex w-40  flex-row">
	{#each Object.entries(sections) as [sectionKey, sectionValue], i}
		<div>
			{#each sectionValue as classrooms}
				{#each Object.entries(classrooms) as [classroom, companies]}
					<button
						on:click={() => {
							showCompanies(classroom);
							console.log(classroom);
							toggledClassrooms[classroom] = !toggledClassrooms[classroom];
						}}
						class:a-button={i === 0}
						class:b-button={i === 1}
						class:c-button={i === 2}
						class:toggled-classroom-a={toggledClassrooms[classroom] && i === 0}
						class:toggled-classroom-b={toggledClassrooms[classroom] && i === 1}
						class:toggled-classroom-c={toggledClassrooms[classroom] && i === 2}>{classroom}</button>
					<div class="[&>*]:m-1">
						{#if showCompaniesInClassroom[classroom]}
							{#each companies as company}
								{#if selectedDropdown === "Any" || allCompanies[company]["Vi är intresserade av dig som studerar"].includes(selectedDropdown)}
									<button
										on:click={() => {
											if (viewCompanyName === company) {
												viewCompany = "";
												viewCompanyName = "";
											} else {
												viewCompany = allCompanies[company];
												viewCompanyName = company;
												// console.log(viewCompany);
											}
										}}
										class:viewed-company={company === viewCompanyName}
										class="rounded-md border border-solid border-transparent bg-slate-300 p-2">
										{company}
									</button>
								{/if}
							{/each}
						{/if}
					</div>
				{/each}
			{/each}
		</div>
	{/each}
</main>
<article class="flex flex-col [&>*]:m-2">
	<Company
		bind:enabledSnapshot={enabledThingies}
		where={whereIsTheCompany}
		name={viewCompanyName}
		companyData={viewCompany} />
</article>

<footer class="m-4 h-64">
	<p>
		by
		<a
			class="underline-blue-600 underline hover:text-blue-600"
			href="https://github.com/henrikvilhelmberglund"
			><img class="inline w-6" src="{base}/Henrik.png" alt="avatar" />henrikvilhelmberglund</a>
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
	.viewed-company {
		@apply rounded-md border border-solid border-black bg-green-400 p-2;
	}
	.toggled-classroom-a {
		@apply outline-solid outline-2 outline-amber-700;
	}
	.toggled-classroom-b {
		@apply outline-solid outline-2 outline-blue-700;
	}
	.toggled-classroom-c {
		@apply outline-solid outline-2 outline-red-700;
	}
</style>
