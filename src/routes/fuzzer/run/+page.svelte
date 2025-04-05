<script>
    import ToolStatusHeader from "$lib/components/ToolStatusHeader.svelte";
    import RunningResultsTable from "$lib/components/RunningResultsTableCrawler.svelte";
    import ToolButton from "$lib/components/ToolButton.svelte";
    import {page} from "$app/state";
    import {onDestroy, onMount} from "svelte";
    import {goto} from "$app/navigation";
    import ProgressBar from "$lib/components/ProgressBar.svelte";
    import RunningResultsTableFuzzer from "$lib/components/RunningResultsTableFuzzer.svelte";

    let networkLinks = $state([]);
    let networkLinksSize = $state(0)
    let intervalId
    let noContentCount = 0
    let delay = 1000
    let currTime = $state(0)
    let requestCount = $state(0)
    let reqSec = $state(0)
    if (page.url.searchParams.get('delay')){
        delay = page.url.searchParams.get('delay')
    }
    onMount(async () => {
        let startTime = performance.now()

        intervalId= setInterval(async () => {
            try {
                const response = await fetch("http://127.0.0.1:8000/fuzzer/data");
                const data = await response.json();
                currTime = performance.now() - startTime;
                if (data.error) {
                    console.error("Error:", data.error);
                } else if(response.status === 206) {
                    networkLinks = data.data;
                    networkLinksSize = networkLinks.length
                    if (page.url.searchParams.get('pageLimit')) {
                        if (networkLinksSize >= parseInt(page.url.searchParams.get('pageLimit').toString())) {
                            clearInterval(intervalId);
                            done = true
                            console.log("Fetching stopped, networkMap has reached the desired length.");
                        }
                    }
                }
                else if(response.status === 400) {
                    noContentCount ++
                }
                if(noContentCount >= 5){
                    clearInterval(intervalId)
                    done = true
                    console.log("Fetching stopped, no content was found.");
                }
                if(response.status === 200){
                    networkLinks = data;
                    done = true
                    clearInterval(intervalId)
                }
                requestCount = networkLinksSize
                reqSec = currTime/requestCount/1000
            } catch (err) {
                console.error("Failed to fetch fuzzer data:", err);
            }
        }, (delay*1.5))
    });

    $effect(()=>{
        networkLinksSize = Object.keys(networkLinks).length;
        console.log(networkLinksSize)
    })

    onDestroy(() => {
        clearInterval(intervalId);
    });
    let done = $state(false);
</script>



<h1 class="page-header">Fuzzer</h1>
<div class="page-wrapper">
    {#key networkLinksSize}
    <ProgressBar currentCount={networkLinksSize}></ProgressBar>
        {/key}
    {#key done}
    <ToolStatusHeader active={["Configuration", "Running",...(done ? ["Results"] : [])]} title="Running"></ToolStatusHeader>
        {/key}
    <div class="metrics">
        <div class="metric">
            <div class="metric-title">Running Time</div>
            <div class="metric-value">{currTime.toFixed(2)} ms</div>
        </div>
        <div class="metric">
            <div class="metric-title">Processed Requests</div>
            <div class="metric-value">{requestCount}</div>
        </div>
        <div class="metric">
            <div class="metric-title">Filtered Requests</div>
            <div class="metric-value">0</div>
        </div>
        <div class="metric">
            <div class="metric-title">Requests/sec</div>
            <div class="metric-value">{reqSec.toFixed(2)}</div>
        </div>
    </div>
    <div class="table-display-area">
        {#key networkLinks}
    <RunningResultsTableFuzzer networkLinks={networkLinks} currSize={networkLinksSize}></RunningResultsTableFuzzer>
            {/key}
    </div>
</div>
<div class="footer-buttons">
    <div class="left-buttons">
        <ToolButton callback={()=>{}} content="<b>Pause</b>"></ToolButton>
        <ToolButton callback={()=>{}} content="<b>Stop</b>"></ToolButton>
        <ToolButton callback={()=>{}} content="<b>Restart</b>"></ToolButton>
    </div>
    <div class="right-button">
        <ToolButton callback={()=>{}} content="<b>Show Terminal</b>"></ToolButton>
    </div>
</div>


<style>
    .page-header {
        margin-left: 2.5vw;
    }
    .table-display-area{
        width: 100%;
        display: flex;
        justify-content: center;
        overflow: scroll;
        scrollbar-width: none;  /* Firefox */
        -ms-overflow-style: none;
        max-height: 50vh;
    }
    .table-display-area::-webkit-scrollbar {
        display: none;  /* Chrome, Safari */
    }
    .page-wrapper {
        width: 90%;
        max-height: 100vh;
        margin-left: 2rem;
        display: flex;
        flex-direction: column;
        align-items: center;
        background: white;
    }
    .metrics{
        display: flex;
        width: 90%;
        justify-content: space-evenly;
        align-items: center;
    }
    .metric{
        display: flex;
        flex-direction: column;
        justify-content: space-evenly;
    }
    .metric-value{
        font-weight: bold;
    }
    .footer-buttons{
        position: absolute;
        display: flex;
        margin-bottom: 1vh;
        bottom: 1vh;
        width: 90%;
        justify-content: space-between;
    }

</style>