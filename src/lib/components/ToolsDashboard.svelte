<script>
	// Default tool list for inactive project
	import {goto} from "$app/navigation";

	let tools = [
		{
			name: "HTTP Tester",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "Tests HTTP methods and headers for unexpected behavior",
			action: "Set Up",
			enabled: false
		},
		{
			name: "SQL Injection",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "Fill with info .....",
			action: "Set Up",
			enabled: false
		},
		{
			name: "Parameter Fuzzing",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "Tries random values for fuzzing parameters",
			action: "Set Up",
			enabled: true,
			callback: ()=>{goto("/fuzzer")}
		},
		{
			name: "Brute Force Tester",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "Attempts brute force attacks",
			action: "Set Up",
			enabled: false
		},
		{
			name: "Intruder",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "ill with info .....",
			action: "Set Up",
			enabled: false
		},
		{
			name: "Crawler",
			progress: 0,
			status: "Ready to Go!",
			color: "#ccc",
			tooltip: "This tool scans a website to gather links and map its structure. It helps you understand how pages connect, find broken links, and improve navigation.",
			action: "Set Up",
			enabled: true,
			callback: ()=>{goto("/crawler")}
		}
	];
</script>

<h2 class="project-name">Base Perimeter Network</h2>

<div class="tools-list">
	{#each tools as tool}
		<div class="tool-row">
			<div class="tool-name">{tool.name}</div>

			<div class="tool-progress">
				<div class="progress-bar">
					<div
						class="progress-fill"
						style="width: {tool.progress}%; background-color: {tool.color};"
					></div>
				</div>
				<div class="progress-label">
					{tool.progress}% <span>{tool.status}</span>
				</div>
			</div>

			<div class="tool-actions">
				<div class="tooltip-wrapper">
					<button class="info-btn" data-tooltip={tool.tooltip}>
						<img class="info-logo" src="./fontawesome-free-6.7.2-desktop/svgs/regular/circle-question.svg" alt="Info">
					</button>
				</div>
				<button class="action-btn" disabled={!tool.enabled} onclick={tool.callback}>{tool.action}</button>
			</div>
		</div>
	{/each}
</div>

<button class="stop-btn">Stop Project</button>

<style>
	.project-name {
		margin-left: 2.5vw;
		color: #666;
		font-size: 1.2rem;
		margin-bottom: 2rem;
	}

	.tools-list {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		align-items: center;
		width: 60%;
	}

	.tool-row {
		display: flex;
		align-items: center;
		justify-content: space-between;
		width: 85%;
		padding: 0.7rem;
		border-radius: 12px;
		background: #f2f2f2;
	}

	.tool-name {
		width: 20%;
		font-weight: 500;
	}

	.tool-progress {
		width: 40%;
		display: flex;
		flex-direction: column;
	}

	.progress-bar {
		background: #ddd;
		height: 8px;
		border-radius: 4px;
		overflow: hidden;
	}

	.progress-fill {
		height: 8px;
		transition: width 0.3s ease-in-out;
	}

	.progress-label {
		font-size: 0.85rem;
		margin-top: 4px;
		color: #333;
	}

	.tool-actions {
		display: flex;
		align-items: center;
		gap: 1rem;
	}

	.info-btn {
		border: none;
		border-radius: 50%;
		cursor: pointer;
		font-weight: bold;
		height: 4vh;
	}

	.info-logo{
		height: 4vh

	}

	.action-btn {
		background: #bde0fe;
		border: none;
		padding: 0.5rem 1rem;
		border-radius: 6px;
		cursor: pointer;
		font-weight: bold;
	}

	.action-btn:disabled {
		background: #ccc;
		cursor: not-allowed;
	}

	.stop-btn {
		margin: 2rem auto;
		display: block;
		background: #d8f3dc;
		border: none;
		padding: 0.75rem 2rem;
		border-radius: 6px;
		cursor: pointer;
		font-weight: bold;
		color: #000;
	}

	.info-btn::after {
		content: attr(data-tooltip);
		position: absolute;
		background: #333;
		color: white;
		padding: 5px;
		border-radius: 4px;
		font-size: 12px;
		white-space: nowrap;
		opacity: 0;
		visibility: hidden;
		transition: opacity 0.2s;
		bottom: 100%;
		left: 50%;
		transform: translateX(-50%);
	}

	.info-btn:hover::after {
		opacity: 1;
		visibility: visible;
	}
</style>

