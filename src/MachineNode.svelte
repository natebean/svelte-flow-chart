<script>
    import { draggable } from "@neodrag/svelte";
</script>

<div class="machine-node" use:draggable={{}}>
    <h3>Machine Node</h3>
</div>

<style>
    .machine-node {
        background-color: #1c6ea4;
        /* top: 15px;
        left: 15px; */
        width: 100px;
        height: 100px;
        /* position: relative; */
    }
    h3 {
        padding: 0.5em;
        font-size: medium;
        /* user-select: none; */
        cursor: move;
    }
    * {
        border: 2px solid #000203;
    }
</style>
