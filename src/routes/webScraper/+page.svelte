<script>
    import {onMount} from "svelte";
    let credentials = $state()
    onMount(async () => {
        try {
            const response = await fetch("http://127.0.0.1:8000/webscraper");
            const data = await response.json();
            console.log(data)
            if (data.error) {
                console.error("Error:", data.error);
            } else {
                credentials = data;
                console.log(credentials)
            }
        } catch (err) {
            console.error("Failed to fetch web scraper data:", err);
        }
    });
</script>
<div>
    {#key credentials}
    <ul>
    {#each credentials as cred}
         <li>
             {cred}
         </li>
        {/each}
    </ul>
        {/key}
</div>