<script>
    import SiteMapList from "$lib/components/SiteMapList.svelte";
    let props = $props()
    let networkLinks = $state([])
    networkLinks = props.networkLinks
    import { fade, slide } from "svelte/transition";
</script>

<table class="running-results-table">
    <thead>
    <tr>
        <th class="running-results-table-header">ID</th>
        <th class="running-results-table-header">Response</th>
        <th class="running-results-table-header">Lines</th>
        <th class="running-results-table-header">Words</th>
        <th class="running-results-table-header">Characters</th>
        <th class="running-results-table-header">Payload</th>
        <th class="running-results-table-header">Error</th>
    </tr>
    </thead>
    <tbody>
    {#each Object.entries(networkLinks) as [link, values], i}
        <tr class="running-result" transition:slide>
            <td class="running-result-ID">{i}</td>
            <td class="running-result-response">{values[1]}</td>
            <td class="running-result-lines">120</td>
            <td class="running-result-word-count">120</td>
            <td class="running-result-character-count">120234</td>
            <td class="running-result-payload">{link}</td>
            <td class="running-result-character-error">{values[1].toString().startsWith("4")}</td>
        </tr>
    {/each}
    </tbody>
</table>

<style>
    thead{
        background: rgba(74, 166, 176, 0.6);
    }
    table {
        margin-top: 1.5vh;
        border-collapse: collapse;
        font-size: 0.6rem;
        letter-spacing: 1px;
        width: 85%;
    }
    th:first-child, td:first-child {
        border-top-left-radius: 7px;
        border-bottom-left-radius: 7px;
    }

    th:last-child, td:last-child {
        border-top-right-radius: 7px;
        border-bottom-right-radius: 7px;
    }
    td{
        text-align: justify;
    }
    th{
        text-align: justify;
    }
    tbody tr:nth-child(even) {
        background-color: #f2f2f2;
    }

    tbody tr:nth-child(odd) {
        background-color: #ffffff;
    }
</style>