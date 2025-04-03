<script>

    import { goto } from "$app/navigation";
    import ToolStatusHeader from "$lib/components/ToolStatusHeader.svelte";
    import { onMount } from "svelte";

    // Define form data object with default values
    let wordlistConfig = { file: null };
    let usernameOptions = {
        characters: false,
        numbers: true,
        symbols: true,
        length: 12,
    };
    let passwordOptions = {
        characters: true,
        numbers: false,
        symbols: false,
        length: 12,
    };

    // Variable to store file name for display
    let fileName = "";

    // Variable to track validation errors
    let errors = {};

    // Function to validate form fields
    function validate() {
        errors = {}; // Reset errors

        if (!wordlistConfig.file)
            errors.file = "Please upload a wordlist file.";

        if (passwordOptions.length < 10)
            errors.plength = "Invalid password length";
        if (usernameOptions.length < 5)
            errors.ulength = "Invalid username length";

    }

    // Handle file upload and process contents
    function handleFileUpload(event) {
        const file = event.target.files[0];
        if (!file) return;
        fileName = file.name; // Store file name for UI
        const reader = new FileReader();
        name = fileName;
        wordlistConfig.file = file;
    }

    // Function to submit form
    function submitWordlist() {
        validate(); // Validate before submission
        if (Object.keys(errors).length > 0) return;

        console.log("Wordlist submitted:", wordlistConfig);
        goto("/webScraper")
    }
</script>

<div class="container">
    <form class="wordlist-form" on:submit|preventDefault={submitWordlist}>
        <!-- File upload field -->

        <input type="file" accept=".txt" on:change={handleFileUpload}/>

        {#if errors.file}<span class="error-msg">{errors.file}</span>{/if}

        <!-- Username & Password-->
        <div style="display: flex; gap: 20px;">
            <!-- Username -->
            <div class="box" style="flex: 1;">
                <h4>Username</h4>
                {#each Object.keys(usernameOptions) as key (key)}
                    {#if key !== "length"}
                        <div
                                style="display: flex; justify-content: space-between; align-items: center; margin: 10px 0;"
                        >
                            <span
                            >{key.charAt(0).toUpperCase() +
                            key.slice(1)}</span
                            >
                            <button
                                    class="toggle-container {usernameOptions[key]
                                    ? 'active'
                                    : ''}"
                                    on:click={() =>
                                    (usernameOptions[key] =
                                        !usernameOptions[key])}
                                    aria-pressed={usernameOptions[key]}
                                    aria-label="Toggle {key}"
                            >
                                <div class="toggle"></div>
                            </button>
                        </div>
                    {/if}
                {/each}
                <label for="username-length">Length</label>
                <input
                        type="number"
                        bind:value={usernameOptions.length}
                        class="input-field"
                        min="1"
                />
                {#if errors.ulength}<span class="error-msg">{errors.ulength}</span>{/if}
            </div>

            <!-- password -->
            <div class="box" style="flex: 1;">
                <h4>Password</h4>
                {#each Object.keys(passwordOptions) as key (key)}
                    {#if key !== "length"}
                        <div
                                style="display: flex; justify-content: space-between; align-items: center; margin: 10px 0;"
                        >
                            <span
                            >{key.charAt(0).toUpperCase() +
                            key.slice(1)}</span
                            >
                            <button
                                    class="toggle-container {passwordOptions[key]
                                    ? 'active'
                                    : ''}"
                                    on:click={() =>
                                    (passwordOptions[key] =
                                        !passwordOptions[key])}
                                    aria-pressed={passwordOptions[key]}
                                    aria-label="Toggle {key}"
                            >
                                <div class="toggle"></div>
                            </button>
                        </div>
                    {/if}
                {/each}
                <label for="password-length">Length</label>
                <input
                        type="number"
                        bind:value={passwordOptions.length}
                        class="input-field"
                        min="1"
                />
                {#if errors.plength}<span class="error-msg">{errors.plength}</span>{/if}
            </div>

        </div>
    </form>
    <!-- Submit button -->
    <!-- Generate Button -->
    <div class="bottom-left">
        <button on:click={submitWordlist} class="submit">Generate</button>
    </div>
</div>

<style>
    .bottom-left {
        position: fixed;
        bottom: 20px;
        left: 50px;
    }

    .wordlist-form {
        display: flex;
        flex-direction: column;
        gap: 1rem;
        width: 60%;
        max-width: 700px;
        margin: auto;
    }

    input,
    textarea {
        padding: 0.5rem;
        border: 1px solid #ccc;
        border-radius: 6px;
        font-size: 1rem;
    }

    .error-msg {
        color: red;
        font-size: 0.9rem;
    }

    button {
        background: #90cdd2;
        border: none;
        padding: 0.75rem;
        border-radius: 6px;
        cursor: pointer;
        font-weight: bold;
    }

    .body {
        font-family: Arial, sans-serif;
        background-color: #f4f4f4;
        margin: 0;
        padding: 0;
    }

    .container {
        display: flex;
        height: 100vh;
        flex: 1;
        padding: 20px;
    }

    .box {
        background: white;
        padding: 15px;
        border-radius: 5px;
        margin-bottom: 15px;
        box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
    }

    .toggle-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        cursor: pointer;
        background: #ddd;
        width: 40px;
        height: 20px;
        border-radius: 20px;
        padding: 3px;
        position: relative;
    }

    .toggle {
        width: 16px;
        height: 16px;
        background: white;
        border-radius: 50%;
        transition: 0.3s;
    }

    .toggle-container.active {
        background: #64b5f6;
    }

    .toggle-container.active .toggle {
        transform: translateX(20px);
    }

    .input-field {
        width: 100%;
        padding: 5px;
        border: 1px solid #ccc;
        border-radius: 4px;
        margin-top: 5px;
    }

    .btn {
        background: #64b5f6;
        color: white;
        border: none;
        padding: 10px 15px;
        border-radius: 4px;
        cursor: pointer;
    }
</style>
