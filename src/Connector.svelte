<script>
    import { onMount } from "svelte";

    export let anchor = { x: 10, y: 10 };
    export let endPoint = { x: 20, y: 20 };
    export let id = "";

    let offsetLeft = 0;
    let offsetTop = 0;

    let svgDiv;

    onMount(() => {
        // console.log("bounding ", svgDiv.getBoundingClientRect());
        let rect = svgDiv.getBoundingClientRect();
        offsetTop = rect.y;
        offsetLeft = rect.x;
    });
    // $: console.log("anchor ", anchor, anchorSet);
    // $: console.log("offsets ", offsetTop, offsetLeft);
    $: console.log("Connector ID ", id);
    $: path =
        "M" +
        (anchor.x - offsetLeft) +
        " " +
        (anchor.y - offsetTop) +
        " L" +
        (endPoint.x - offsetLeft) +
        " " +
        (endPoint.y - offsetTop);
</script>

<div bind:this={svgDiv}>
    <svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
        <path d={path} stroke="cornflowerblue" stroke-width="3px" />
    </svg>
</div>

<style>
    svg {
        position: absolute;
        display: block;
        height: 97%;
        width: 97%;
        z-index: 100;
    }
    div {
        z-index: 100;
    }
</style>
