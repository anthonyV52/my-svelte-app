<script>
    let props = $props();
    let ip = props.ip;
    let path = props.path;
</script>

<div class="tree-node {props.root ? 'root' : ''}">
    <div class="tree-node-title">
        {ip}
    </div>
    <div class="dividing-line">

    </div>
    <div class="tree-node-content">
        {path}
    </div>
</div>

<style>
    .tree-node {
        display: flex;
        flex-direction: column;
        font-size: 1.5vh;
        background: white;
        border: 2px solid #006699;
        border-radius: 10px;
        padding: 5px;
        margin: 10px auto;
        text-align: center;
        height: 10vh;
        width: fit-content;
        box-shadow: 2px 2px 5px rgba(0, 0, 0, 0.2);
        position: relative;
        justify-content: space-around;
    }
    .dividing-line{
        width: 100%;
        height: 2%;
        background: darkgrey;
    }


</style>
