<script>
    import { draggable } from "@neodrag/svelte";
    import { createEventDispatcher, onDestroy, onMount } from "svelte";
    import { blank_object, noop } from "svelte/internal";
    const dispatch = createEventDispatcher();

    export let id = "";
    export let top = 0;
    export let left = 0;
    export let title = "Machine Node";

    let blockRef;

    $: blockId = `block-${id}`;
    $: locationInfo = `${left}px ${top}px`;

    let locationStyles = `top: ${top}px; left: ${left}px;`;

    let options = {
        bounds: "parent",
        handle: ".handle-drag",
    };
    onMount(() => {
        const divs = document.querySelectorAll(`#${blockId} .node`);
        divs.forEach((el) => {
            el.addEventListener("mousedown", nodeMouseDownHandler);
            el.addEventListener("mouseup", nodeMouseUpHandler);
        });
    });

    function mouseHandler(e, type) {
        const el = e.target;
        const anchorNodeType = e.target.getAttribute("data-node-type");
        const anchorNodeId = e.target.getAttribute("data-node-id");
        var rect = blockRef.getBoundingClientRect();
        const blockLeft = rect.left;
        const blockTop = rect.top;
        dispatch(type, {
            type: anchorNodeType,
            id: anchorNodeId,
            blockId: id,
            position: {
                x: blockLeft + el.offsetLeft + el.offsetWidth / 2,
                y: blockTop + el.offsetTop + el.offsetHeight / 2,
            },
        });
    }

    function nodeMouseDownHandler(e) {
        mouseHandler(e, "nodeMouseDown");
    }

    function nodeMouseUpHandler(e) {
        mouseHandler(e, "nodeMouseUp");
    }
    function dragHandler(e) {
        const blockLeft = e.detail.domRect.left;
        const blockTop = e.detail.domRect.top;
        const divs = document.querySelectorAll(`#${blockId} .node`);
        const nodePositions = Array.from(divs).map((el) => {
            const anchorNodeType = el.getAttribute("data-node-type");
            const anchorNodeId = el.getAttribute("data-node-id");
            return {
                blockId: id,
                nodeId: anchorNodeId,
                nodeType: anchorNodeType,
                position: {
                    x: blockLeft + el.offsetLeft + el.offsetWidth / 2,
                    y: blockTop + el.offsetTop + el.offsetHeight / 2,
                },
            };
        });
        dispatch("blockDrag", {
            blockId: id,
            nodePositions: nodePositions,
        });
    }

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

<!--
    on:neodrag:start={(e) => console.log("Dragging started", e)}
    on:neodrag={(e) => console.log(e.detail)}
    on:neodrag:end={(e) => console.log("Dragging stopped", e)}
    -->

<div
    id={blockId}
    class="machine-node"
    use:draggable={options}
    on:neodrag={dragHandler}
    style={locationStyles}
    bind:this={blockRef}
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
                <p>{title}</p>
            </div>
            <div class="main-body">{locationInfo}</div>
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
