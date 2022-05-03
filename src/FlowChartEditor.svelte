<script>
    import MachineNode from "./MachineNode.svelte";
    import Connector from "./Connector.svelte";
    let m = { x: 0, y: 0 };
    let startingAnchorPoint = { x: 0, y: 0 };
    let startingAnchorPointSet = false;

    let connectorList = [];

    $: if (startingAnchorPoint.x > 0 && startingAnchorPoint.y > 0) {
        startingAnchorPointSet = true;
    } else {
        startingAnchorPointSet = false;
    }

    function handleMousemove(event) {
        m = { x: event.clientX, y: event.clientY };
    }

    function handleDown(e) {
        console.log("down", e);
        startingAnchorPoint = { x: e.clientX, y: e.clientY };
    }
    function handleUp(e) {
        console.log("up", e);
        if (startingAnchorPointSet) {
            console.log("line finished");
            connectorCreated(startingAnchorPoint, m);
        }
        startingAnchorPoint = { x: 0, y: 0 };
    }

    function connectorCreated(s, e) {
        connectorList = [...connectorList, { start: s, end: e, id: uuidv4() }];
        console.log("con ", connectorList);
    }

    // https://stackoverflow.com/questions/105034/how-to-create-a-guid-uuid
    function uuidv4() {
        return ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, (c) =>
            (
                c ^
                (crypto.getRandomValues(new Uint8Array(1))[0] & (15 >> (c / 4)))
            ).toString(16)
        );
    }
</script>

<div
    class="flow-chart-editor"
    on:mousemove={handleMousemove}
    on:mousedown={handleDown}
    on:mouseup={handleUp}
>
    <h1>Flow Chart Editor</h1>
    <div>
        The mouse position is {m.x} x {m.y}<br />
        The anchor point is {startingAnchorPoint.x} x {startingAnchorPoint.y}
        <br />
        Anchor is set {startingAnchorPointSet}
    </div>
    {#if startingAnchorPointSet}
        <Connector anchor={startingAnchorPoint} endPoint={m} />
    {/if}
    {#each connectorList as conn (conn.id)}
        <Connector anchor={conn.start} endPoint={conn.end} id={conn.id} />
    {/each}
    <MachineNode />
</div>

<style>
    .flow-chart-editor {
        border: 3px solid #2fc910;
        width: 97%;
        height: 600px;
        position: absolute;
        padding: 10px;
        margin: 10px;
    }

    h1 {
        font-size: xx-large;
        margin-bottom: 0.5em;
    }
</style>
