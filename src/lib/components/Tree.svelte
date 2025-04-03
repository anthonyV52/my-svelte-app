<script>
    import { onMount } from "svelte";
    import TreeNode from './TreeNode.svelte';
    import Tree from './Tree.svelte';

    let props = $props()
    let networkMap = $state()
    networkMap = props.networkMap;
    let scale = props.scale;
</script>

<div class="network-tree" style:transform="scale({scale});">
        {#each networkMap as node}
            <div class="tree-branch">
                <TreeNode ip={node.ip} path={node.path}/>
                {#if node.children && node.children.length > 0}
                    <div class="children">
                        <Tree networkMap={node.children} />
                    </div>
                {/if}
            </div>
        {/each}
</div>

<style>
    .network-tree {
        display: flex;
        position: relative;
        min-width: 25%;
        min-height: 100%;
    }

    .children {
        display: flex;
        justify-content: center;
        align-items: center;
        position: relative;
        margin-top: 2px;
    }
    .tree-branch{
        margin-top: 2px;
        margin-left: 2px;
        margin-right: 2px;
    }


</style>
