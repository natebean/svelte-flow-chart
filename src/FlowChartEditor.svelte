<script>
    import MachineNode from "./MachineBlock.svelte";
    import Connector from "./Connector.svelte";
    let mousePosition = { x: 0, y: 0 };
    let startingAnchorPoint = { x: 0, y: 0 };
    let startingAnchorPointSet = false;
    let mouseDownOnNode = false;
    let downNode = {};

    let connectorList = [];
    let blockList = [
        { id: uuidv4(), title: "Machine Node", left: 100, top: 100 },
        { id: uuidv4(), title: "Machine Node", left: 400, top: 100 },
    ];

    $: if (
        startingAnchorPoint.x > 0 &&
        startingAnchorPoint.y > 0 &&
        mouseDownOnNode
    ) {
        startingAnchorPointSet = true;
    } else {
        startingAnchorPointSet = false;
    }

    function handleMousemove(event) {
        mousePosition = { x: event.clientX, y: event.clientY };
    }

    function handleDown(e) {
        startingAnchorPoint = { x: e.clientX, y: e.clientY };
    }
    function handleUp(e) {
        mouseDownOnNode = false;
    }

    function connectorCreated(startNode, endNode) {
        connectorList = [
            ...connectorList,
            {
                id: uuidv4(),
                startNode,
                endNode,
            },
        ];
        console.log("connectorList", connectorList);
    }

    function handleBlockDrag(e) {
        // console.log("drag", e.detail);
        const updatedConnectorList = connectorList.map((connector) => {
            if (connector.startNode.blockId === e.detail.blockId) {
                const outputs = e.detail.nodePositions.filter(
                    (node) =>
                        node.nodeType === "output" &&
                        node.id === connector.startNode.nodeId
                );
                connector.startNode.position = outputs[0].position;
                return connector;
            } else if (connector.endNode.blockId === e.detail.blockId) {
                const inputs = e.detail.nodePositions.filter(
                    (node) =>
                        node.nodeType === "input" &&
                        node.id === connector.endNode.nodeId
                );
                connector.endNode.position = inputs[0].position;
                return connector;
            } else {
                return connector;
            }
        });
        connectorList = updatedConnectorList;
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

    function handleNodeMouseDown(e) {
        mouseDownOnNode = true;
        downNode = e.detail;
        // console.log("Down ", e.detail);
    }

    function handleNodeMouseUp(e) {
        // console.log("Up ", e.detail);
        if (startingAnchorPointSet) {
            connectorCreated(downNode, e.detail);
        }
        mouseDownOnNode = false;
        startingAnchorPoint = { x: 0, y: 0 };
    }
</script>

<h1>Flow Chart Editor</h1>
<div>
    The mouse position is {mousePosition.x} x {mousePosition.y}<br />
    The anchor point is {startingAnchorPoint.x} x {startingAnchorPoint.y}
    <br />
    Anchor is set {startingAnchorPointSet}
</div>
<div
    class="flow-chart-editor"
    on:mousemove={handleMousemove}
    on:mousedown={handleDown}
    on:mouseup={handleUp}
>
    {#if startingAnchorPointSet}
        <Connector anchor={startingAnchorPoint} endPoint={mousePosition} />
    {/if}
    {#each connectorList as conn (conn.id)}
        <Connector
            anchor={conn.startNode.position}
            endPoint={conn.endNode.position}
            id={conn.id}
        />
    {/each}
    {#each blockList as block (block.id)}
        <MachineNode
            id={block.id}
            top={block.top}
            left={block.left}
            title={block.title}
            on:nodeMouseDown={handleNodeMouseDown}
            on:nodeMouseUp={handleNodeMouseUp}
            on:blockDrag={handleBlockDrag}
        />
    {/each}
</div>

<style>
    .flow-chart-editor {
        border: 1px solid #2fc910;
        width: 97%;
        height: 600px;
        position: absolute;
        padding: 2px 10px;
        margin: 10px;
    }

    h1 {
        font-size: xx-large;
        margin-bottom: 0.5em;
    }
</style>
