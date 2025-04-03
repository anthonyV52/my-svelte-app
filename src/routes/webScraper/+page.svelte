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
            }
        } catch (err) {
            console.error("Failed to fetch web scraper data:", err);
        }
    });
</script>
<div>
    <ul>
    {#each credentials as cred}
         <li>
             {cred[0]}: {cred[1]}
         </li>
        {/each}
    </ul>
</div>