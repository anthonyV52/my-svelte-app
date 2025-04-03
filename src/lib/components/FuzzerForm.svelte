<script>
// @ts-nocheck

	import { goto } from "$app/navigation";
	import ToolStatusHeader from "$lib/components/ToolStatusHeader.svelte";
    
	let config = {
		TargetURL: "",
		WordList: [],
		HTTPMethod: "GET",
		Cookies: "",
		HideStatusCode: "",
		ShowOnlyStatusCode: "",
		FilterContentLength: "",
		PageLimit: 100
	};

	let fileName = "";
	let startEnabled = false;
	let errors = {};

	function validate() {
		errors = {};
		if (!config.TargetURL) errors.TargetURL = "Target URL is required.";
		if (config.WordList.length === 0) errors.WordList = "Please upload a wordlist.";
		startEnabled = Object.keys(errors).length === 0;
	}

	function handleFileUpload(event) {
		const file = event.target.files[0];
		fileName = file.name;
		const reader = new FileReader();
		reader.onload = () => {
			config.WordList = reader.result.split(/\r?\n/).filter(Boolean);
			validate();
		};
		reader.readAsText(file);
	}

	async function startFuzzing() {
		validate();
		if (!startEnabled) return;

		const payload = {
			TargetURL: config.TargetURL,
			HTTPMethod: config.HTTPMethod,
			Cookies: config.Cookies ? config.Cookies.split(";") : [],
			HideStatusCode: config.HideStatusCode.split(",").map(x => x.trim()),
			ShowOnlyStatusCode: config.ShowOnlyStatusCode.split(",").map(x => x.trim()),
			FilterContentLength: parseInt(config.FilterContentLength || "0"),
			PageLimit: config.PageLimit,
			WordList: config.WordList
		};
		try {
			const res = await fetch("http://127.0.0.1:8000/fuzzer", {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(payload)
			});

			if (!res.ok) throw new Error("Fuzzing failed");
			goto("/fuzzer/run");
		} catch (err) {
			console.error(err);
		}
	}
</script>

<ToolStatusHeader active={["Configuration"]}/>

<form class="fuzzing-form" on:submit|preventDefault={startFuzzing}>
	<input
		type="url"
		placeholder="Enter target URL"
		title="Enter the target URL for fuzzing"
		bind:value={config.TargetURL}
		class:error={errors.TargetURL}
	/>
	{#if errors.TargetURL}<span class="error-msg">{errors.TargetURL}</span>{/if}

	<input
		type="text"
		placeholder="Upload Wordlist"
		readonly
		value={fileName}
	/>
	<input type="file" accept=".txt" on:change={handleFileUpload} title="Upload wordlist" />

	<div class="radio-group">
		<label><input type="radio" bind:group={config.HTTPMethod} value="GET" /> GET</label>
		<label><input type="radio" bind:group={config.HTTPMethod} value="PUT" /> PUT</label>
		<label><input type="radio" bind:group={config.HTTPMethod} value="POST" /> POST</label>
	</div>

	<input
		type="text"
		placeholder="Cookies"
		title="Provide cookies if using PUT/POST"
		bind:value={config.Cookies}
		disabled={config.HTTPMethod === 'GET'}
	/>
	<input
		type="text"
		placeholder="Hide Status Codes"
		title="Enter comma-separated status codes to hide"
		bind:value={config.HideStatusCode}
	/>
	<input
		type="text"
		placeholder="Show Only Status Codes"
		title="Enter comma-separated status codes to show"
		bind:value={config.ShowOnlyStatusCode}
	/>
	<input
		type="text"
		placeholder="Content Length"
		title="Specify content length filters"
		bind:value={config.FilterContentLength}
	/>

	<button type="submit" disabled={!startEnabled}>Start</button>
</form>

<style>
	.fuzzing-form {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		width: 60%;
		max-width: 700px;
		margin: auto;
	}
	input[type="text"],
	input[type="url"] {
		padding: 0.5rem;
		border: 1px solid #ccc;
		border-radius: 6px;
		font-size: 1rem;
	}
	input.error {
		border-color: red;
	}
	.error-msg {
		color: red;
		font-size: 0.9rem;
	}
	.radio-group {
		display: flex;
		gap: 1.5rem;
	}
	button[type="submit"] {
		background: #90cdd2;
		border: none;
		padding: 0.75rem;
		border-radius: 6px;
		cursor: pointer;
		font-weight: bold;
	}
</style>