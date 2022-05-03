<script>
    import { draggable } from "@neodrag/svelte";
    let options = {
        bounds: "parent",
        handle: ".handle-drag",
    };
</script>

<div class="machine-node" use:draggable={options}>
    <div class="wrapper">
        <div class="node-inputs node-wrapper">
            <div class="node node-input" />
            <div class="node node-input" />
        </div>
        <div class="main-inner handle-drag">
            <div class="header">
                <p>Machine Node</p>
            </div>
            <div class="main-body">Other stuff</div>
        </div>
        <div class="node-outputs node-wrapper">
            <div class="node node-output" />
            <div class="node node-output" />
        </div>
    </div>
</div>

<style>
    .machine-node {
        position: relative;
        width: 150px;
        z-index: 1;
    }
    .main-body {
        height: 100%;
    }
    .wrapper {
        align-items: center;
        display: flex;
    }
    .main-inner {
        background-color: #1c6ea4;
        padding: 12px;
    }
    p {
        padding: 0.5em;
        user-select: none;
        cursor: move;
        z-index: 1;
        border: 1px solid #000203;
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
