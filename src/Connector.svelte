<script>
    import { onMount } from "svelte";

    export let startPoint = { x: 10, y: 10 };
    export let endPoint = { x: 20, y: 20 };

    let offsetLeft = 0;
    let offsetTop = 0;

    let svgDiv;

    onMount(() => {
        let rect = svgDiv.getBoundingClientRect();
        offsetTop = rect.y;
        offsetLeft = rect.x;
    });
    $: path =
        "M" +
        (startPoint.x - offsetLeft) +
        " " +
        (startPoint.y - offsetTop) +
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
        z-index: -100;
    }
    path {
        z-index: 300;
    }
    div {
        z-index: -100;
    }
</style>
