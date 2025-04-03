<script>
// @ts-nocheck

	import { goto } from "$app/navigation";
	import { fade, slide } from "svelte/transition";
	import ToolStatusHeader from "$lib/components/ToolStatusHeader.svelte";
	let crawling = false;

	let res = null;
	let advancedOptions = false;

	async function startCrawl(event) {
		event.preventDefault();
		crawling = true;

		const data = new FormData(event.currentTarget);

		try {
			let depth = data.get("CrawlDepth") ? data.get("CrawlDepth") : 2
			let pageNumberLimit = data.get("PageNumberLimit") ? data.get("PageNumberLimit") : 50
			sessionStorage.setItem("crawlerPageLimit", pageNumberLimit);
			let userAgent = data.get("UserAgent") ? data.get('UserAgent') : "Mozilla/3.0"
			let delay = data.get("RequestDelay") ? data.get("RequestDelay") : 1000
			sessionStorage.setItem("crawlerDelay", delay);
			const response = await fetch("http://127.0.0.1:8000/crawler", {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify({
					TargetURL: data.get("TargetURL"),
					CrawlDepth:  depth,
					PageNumberLimit: pageNumberLimit,
					UserAgent: userAgent,
					RequestDelay: delay,
				}),
			});

			if (!response.ok) throw new Error(`Server error: ${response.status}`);

			res = await response.json();
			await goto(`/crawler/run?pageLimit=${pageNumberLimit}&delay=${delay}`);
		} catch (error) {
			console.error("Error starting crawler:", error);
			crawling = false;
			sessionStorage.removeItem("crawlerDelay");
			sessionStorage.removeItem("crawlerPageLimit");
		}
	}
</script>

{#if !crawling}
	<div class="page-wrapper">
		<ToolStatusHeader active={["Configuration"]}></ToolStatusHeader>

		<div class="crawler-container">
			<form class="crawler-form" on:submit={startCrawl}>
				<div class="form-group">
					<label for="TargetURL">Target URL *</label>
					<input type="url" id="TargetURL" name="TargetURL" required />
				</div>
				{#if advancedOptions}
					<div class="advanced-options" transition:slide >
					<div class="form-group">
						<label for="CrawlDepth">Crawl Depth</label>
						<input type="number" id="CrawlDepth" name="CrawlDepth" value="2" />
					</div>

					<div class="form-group">
						<label for="PageNumberLimit">Limit on Number of Pages</label>
						<input type="number" id="PageNumberLimit" name="PageNumberLimit" value="50" />
					</div>

					<div class="form-group">
						<label for="UserAgent">User-Agent String</label>
						<select id="UserAgent" name="UserAgent">
							<option value="Mozilla/3.0">Mozilla/3.0</option>
							<option value="AppleWebKit/537.36">AppleWebKit/537.36</option>
							<option value="Chrome/112.0.0.0">Chrome/112.0.0.0</option>
							<option value="Mobile Safari/537.36">Mobile Safari/537.36</option>
						</select>
					</div>

					<div class="form-group">
						<label for="RequestDelay">Request Delay (ms)</label>
						<input type="number" id="RequestDelay" name="RequestDelay" value="1000" />
					</div>
					</div>
					{/if}

				<button type="submit" class="start-btn">Start</button>
			</form>

			<button class="toggle-btn" on:click={() => (advancedOptions = !advancedOptions)}>
				{advancedOptions ? "Hide Advanced Options ⌃" : "Show Advanced Options ⌵"}
			</button>
		</div>
	</div>
{:else}
	{#if res}
		<h1>Crawling complete</h1>
		<button on:click={() => goto(`/treeGraph`)}> Show Tree Graph</button>
	{:else}
		<h1>Crawling...</h1>
	{/if}
{/if}

<style>
	:global(body) {
		background: white;
	}

	.page-wrapper {
		width: 90%;
		padding: 2rem;
		display: flex;
		flex-direction: column;
		align-items: center;
		background: white;
	}


	.crawler-container {
		width: 100%;
		max-width: 700px;
		padding: 2rem;
		border-radius: 12px;
		box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
		background: #f9f9f9;
	}

	form {
		display: flex;
		flex-direction: column;
		gap: 1rem;
	}

	.form-group {
		display: flex;
		flex-direction: column;
	}

	label {
		font-size: 0.9rem;
		margin-bottom: 0.25rem;
		color: #333;
	}

	input,
	select {
		padding: 0.6rem;
		font-size: 1rem;
		border-radius: 6px;
		border: 1px solid #ccc;
	}

	.start-btn {
		background-color: rgba(74, 166, 176, 0.6);
		color: #000;
		border: none;
		border-radius: 6px;
		padding: 0.75rem;
		font-size: 1rem;
		cursor: pointer;
		width: 100%;
		transition: ease-in-out .25s;
	}

	.start-btn:hover{
		background: #4aa6b0;
		transform: scale(1.05);
	}

	.toggle-btn {
		margin-top: 1rem;
		background: none;
		border: none;
		color: #4aa6b0;
		cursor: pointer;
		font-weight: bold;
	}
</style>

