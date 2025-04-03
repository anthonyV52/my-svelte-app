<script>
    let { children } = $props();
</script>

<nav class="side-bar">
    <div class="side-bar-section top">
        <a class="side-bar-logo-link" href="/" aria-label="Home link">
            <img class="side-bar-logo" src="/favicon.png" alt="TRACE logo">
        </a>
    </div>
    <div class="side-bar-section mid">
    <button class="side-bar-button" aria-label="Tools button" type="button">
        <a class="side-bar-logo-link" href="/tools" aria-label="tools button">
            <img class="side-bar-button-logo" src="/fontawesome-free-6.7.2-desktop/svgs/solid/hammer.svg" alt="Tools logo">
        </a>
    </button>
    <button class="side-bar-button" aria-label="network tree button">
        <a class="side-bar-logo-link" href="/treeGraph">
           <img class="side-bar-button-logo" src="/fontawesome-free-6.7.2-desktop/svgs/solid/folder-tree.svg" alt="Network Tree logo">
        </a>
    </button>
    <button class="side-bar-button" aria-label="results button">
        <a class="side-bar-logo-link" href="/wordlist">
            <img class="side-bar-button-logo" src="/fontawesome-free-6.7.2-desktop/svgs/solid/list-check.svg" alt="results logo">
        </a>
    </button>
        <button class="side-bar-button" aria-label="AI Credential Generator button">
            <a class="side-bar-logo-link" href="/wordlist">
                <img class="side-bar-button-logo" src="/fontawesome-free-6.7.2-desktop/svgs/solid/brain.svg" alt="AI Credential Generator logo">
            </a>
        </button>
    </div>
    <div class="side-bar-section bottom">
    <button class="side-bar-button" aria-label="config-button">
        <a class="side-bar-logo-link" href="/configGraph">
            <img class="side-bar-button-logo" src="/fontawesome-free-6.7.2-desktop/svgs/solid/gear.svg" alt="Config logo">
        </a>
    </button>
    </div>
</nav>
<main class="container">
    {@render children?.()}
</main>

<style>
    :root  {
        font-family: 'Poppins', sans-serif;
    }
    .side-bar {
        position: fixed;
        top: 0;
        left: 0;
        height: 100%;
        width: 4%;
        background: #e1e1e1;
        display: flex;
        flex-direction: column;
        border-radius: 3%;
    }
    .side-bar-button{
        border-radius: 50%;
        width: 6vh;
        height: 6vh;
        background: #cfcfcf;
        border: none;
        padding: 0;
    }
    .side-bar-button-logo{
        width: 60%;
    }
    .side-bar-button:hover{
        border: #7e7d7d;
        background: #4aa6b0;
        transform: scale(1.2);
    }
    .side-bar-section{
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;
        align-items: center;
    }
    .side-bar-section.mid{
       justify-content: center;
    }
    .side-bar-section.mid button{
        margin: 1vh
    }
    .side-bar-section.bottom{
        justify-content: flex-end;
    }
    .side-bar-section.bottom button{
        margin: 3vh;
    }
    .side-bar-logo-link{
        border-radius: 50%;
        width: 8vh;
        height: 8vh;
    }
    .side-bar-logo{
        height: 8vh;
        width: auto;
    }
    .container{
         width: 96%;
         margin-left: 4%;
    }
</style>