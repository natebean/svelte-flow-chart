<script>
    import { draggable } from "@neodrag/svelte";
    import { createEventDispatcher, onDestroy, onMount } from "svelte";
    const dispatch = createEventDispatcher();

    export let id = "";
    export let top = 0;
    export let left = 0;

    let locationStyles = `top: ${top}px; left: ${left}px;`;

    let options = {
        bounds: "parent",
        handle: ".handle-drag",
    };

    function nodeMouseDownHandler(e) {
        const anchorNodeType = e.target.getAttribute("data-node-type");
        const anchorNodeId = e.target.getAttribute("data-node-id");
        dispatch("nodeMouseDown", {
            type: anchorNodeType,
            id: anchorNodeId,
            blockId: id,
        });
    }

    function nodeMouseUpHandler(e) {
        const anchorNodeType = e.target.getAttribute("data-node-type");
        const anchorNodeId = e.target.getAttribute("data-node-id");
        dispatch("nodeMouseUp", {
            type: anchorNodeType,
            id: anchorNodeId,
            blockId: id,
        });
    }
    onMount(() => {
        const divs = document.querySelectorAll(`#block-${id} .node`);
        divs.forEach((el) => {
            el.addEventListener("mousedown", nodeMouseDownHandler);
            el.addEventListener("mouseup", nodeMouseUpHandler);
        });
    });

    onDestroy(() => {
        // TODO Clean up destroy
        const divs = document.querySelectorAll(`.block-${id} .node`);
        console.log("remove listeners");
        divs.forEach((el) =>
            el.removeEventListener("click", (event) => {
                console.log(event.target);
            })
        );
    });
</script>

<div
    id="block-{id}"
    class="machine-node"
    use:draggable={options}
    style={locationStyles}
>
    <div class="wrapper">
        <div class="node-inputs node-wrapper">
            <div
                class="node node-input"
                data-node-type="input"
                data-node-id="1"
            />
        </div>
        <div class="main-inner">
            <div class="header handle-drag">
                <p>Machine Node</p>
            </div>
            <div class="main-body">Other stuff</div>
        </div>
        <div class="node-outputs node-wrapper">
            <div
                class="node node-output"
                data-node-type="output"
                data-node-id="1"
            />
        </div>
    </div>
</div>

<style>
    .machine-node {
        width: fit-content;
        max-width: 350px;
        z-index: 1;
        /* position: relative; */
        position: absolute;
    }
    .wrapper {
        align-items: center;
        display: flex;
    }
    .main-inner {
        background-color: #1c6ea4;
        padding: 3px 12px;
    }
    .header {
        padding: 0.5em;
        user-select: none;
        cursor: move;
        z-index: 1;
        border: 1px solid #000203;
        margin-bottom: 3px;
    }
    .main-body {
        min-height: 100px;
    }
    .node-input {
        border-radius: 50%;
        margin-right: -8px;
    }
    .node-output {
        margin-left: -8px;
    }
    .node {
        border: 1px solid #929fa5;
        width: 16px;
        height: 16px;
        cursor: crosshair;
        background-color: rgb(201, 201, 191);
        margin-bottom: 5px;
    }
    .node:hover {
        background-color: rgb(255, 255, 0);
    }
    .node-wrapper {
        user-select: none;
        z-index: 10;
    }
</style>
