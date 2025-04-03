<script>
    import Tree from "$lib/components/Tree.svelte";
    import {onDestroy, onMount} from "svelte";
    import {goto} from "$app/navigation";
    import SiteMapList from "$lib/components/SiteMapList.svelte";
    import {fade} from 'svelte/transition';
    import {page} from "$app/state";
    import LoadingSpinner from "$lib/components/LoadingSpinner.svelte";
    import ToolButton from "$lib/components/ToolButton.svelte";

    // let networkMap = [
    //     {
    //         ip: "192.168.1.34:8080",
    //         path: "www.google.com",
    //         children: [
    //             {
    //                 ip: "192.168.1.34:8080",
    //                 path: "www.google.com/search",
    //                 children: [
    //                     {
    //                         ip: "192.168.1.34:8080",
    //                         path: "www.google.com/search/search",
    //                         children: [
    //                             {
    //                                 ip: "192.168.1.34:8080",
    //                                 path: "www.google.com/search/search/s",
    //                                 children:[]
    //                             },
    //                             {
    //                                 ip: "192.168.1.34:8080",
    //                                 path: "www.google.com/search/search/search/s",
    //                                 children: []
    //                             }
    //                         ]
    //                     }
    //                 ]
    //             },
    //             {
    //                 ip: "192.168.1.34:8080",
    //                 path: "www.google.com/gmail",
    //                 children: []
    //             }
    //         ]
    //     }
    // ];

    let networkMap = $state([]);
    let networkMapSize = $state(0)
    let intervalId
    let noContentCount = 0
    let delay = 1000
    if (page.url.searchParams.get('delay')){
        delay = page.url.searchParams.get('delay')
    }
    onMount(async () => {
        intervalId= setInterval(async () => {
                try {
                    const response = await fetch("http://127.0.0.1:8000/crawler/data");
                    const data = await response.json();
                    if (data.error) {
                        console.error("Error:", data.error);
                    } else if(response.status === 206) {
                            networkMap = data.data;
                            networkMapSize = countNodes(networkMap);
                            if (page.url.searchParams.get('pageLimit')) {
                                if (networkMapSize >= parseInt(page.url.searchParams.get('pageLimit').toString())) {
                                    clearInterval(intervalId);
                                    console.log("Fetching stopped, networkMap has reached the desired length.");
                                }
                            }
                        }
                        else if(response.status === 400) {
                            noContentCount ++
                        }
                        if(noContentCount >= 5){
                            clearInterval(intervalId)
                            console.log("Fetching stopped, no content was found.");
                        }
                        if(response.status === 200){
                            networkMap = data;
                            networkMapSize = countNodes(networkMap);
                            clearInterval(intervalId)
                        }
                } catch (err) {
                    console.error("Failed to fetch crawler data:", err);
                }
            }, (delay*1.5))
    });

    async function generateWordlist() {
        await goto(`/webScraper`);
    }
    let treeMode = $state(true)


    let scale = $state(1);
    let isZoomedOut = $state(false);
    function zoomOut() {
        scale  -= 0.05;
        console.log(scale)
        if(scale<=0.5) isZoomedOut = true;
    }

    function resetZoom() {
        if(isZoomedOut) {
            scale = 1;
            isZoomedOut = false;
        }
    }

    function countNodes(networkMap) {
        let count = 0;

        function traverse(nodes) {
            for (const node of nodes) {
                count++;
                if (node.children && node.children.length > 0) {
                    traverse(node.children);
                }
            }
        }

        traverse(networkMap);
        return count;
    }


    onDestroy(() => {
        clearInterval(intervalId);
    });
</script>

<div class="tree-graph">
    {#if treeMode}

        <h1 class="page-header">Tree graph</h1>
        <div class="display-zone">
            {#if !networkMap || networkMap.length === 0}
                <LoadingSpinner></LoadingSpinner>
                {:else}
                {#key networkMapSize}
        <Tree networkMap={networkMap} scale={scale}></Tree>
                    {/key}
                {/if}
        </div>
    {:else}
        <h1 class="page-header"> List View</h1>
        <div class="display-zone">
            {#if !networkMap || networkMap.length === 0}
                <LoadingSpinner></LoadingSpinner>
            {:else}
        <SiteMapList networkMap ={networkMap}></SiteMapList>
                {/if}
        </div>
    {/if}
    <ToolButton callback={()=>{isZoomedOut ? resetZoom : zoomOut}} content={isZoomedOut ? "Reset Zoom" : "Zoom Out"}>
    </ToolButton>
    <ToolButton callback={generateWordlist} content="<b> Wordlist</b>"></ToolButton>
    <ToolButton callback={()=>{treeMode = !treeMode}} content="<b>Switch View</b>"></ToolButton>
</div>
<style>
    .page-header {
        margin-left: 2vw;
        margin-top: 0;
    }
    .display-zone{
        display: flex;
        justify-content: center;
        overflow: scroll;
        width: 100%;
        height: 80vh;
        scrollbar-width: none;  /* Firefox */
        -ms-overflow-style: none;
    }
    .display-zone::-webkit-scrollbar {
        display: none;  /* Chrome, Safari */
    }
</style>