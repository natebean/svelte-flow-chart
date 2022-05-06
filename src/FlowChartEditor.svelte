<script>
    import MachineNode from "./MachineBlock.svelte";
    import Connector from "./Connector.svelte";
    let mousePosition = { x: 0, y: 0 };
    let mouseDownOnNode = false;
    let downNode = { position: { x: 0, y: 0 } };

    let connectorList = [];
    let blockList = [
        { id: uuidv4(), title: "Machine Node 1", left: 100, top: 100 },
        { id: uuidv4(), title: "Machine Node 2", left: 400, top: 100 },
        { id: uuidv4(), title: "Machine Node 3", left: 800, top: 100 },
    ];

    function handleMousemove(event) {
        mousePosition = { x: event.clientX, y: event.clientY };
    }

    function handleUp(e) {
        mouseDownOnNode = false;
    }

    function handleNodeMouseDown(e) {
        mouseDownOnNode = true;
        downNode = e.detail;
    }

    function handleNodeMouseUp(e) {
        if (mouseDownOnNode) {
            connectorCreated(downNode, e.detail);
        }
        mouseDownOnNode = false;
    }

    function connectorCreated(startNode, endNode) {
        if (startNode.type === "output" && endNode.type === "input") {
            connectorList = [
                ...connectorList,
                {
                    id: uuidv4(),
                    startNode,
                    endNode,
                },
            ];
        }
        // console.log("ConnectorList", connectorList);
    }

    function handleBlockDrag(e) {
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
</script>

<h1>Flow Chart Editor</h1>
<div>
    The mouse position is {mousePosition.x} x {mousePosition.y}<br />
    The anchor point is {downNode.position.x} x {downNode.position.y}
    <br />
    Anchor is set {mouseDownOnNode}
</div>
<div
    class="flow-chart-editor"
    on:mousemove={handleMousemove}
    on:mouseup={handleUp}
>
    {#if mouseDownOnNode}
        <Connector startPoint={downNode.position} endPoint={mousePosition} />
    {/if}
    {#each connectorList as conn (conn.id)}
        <Connector
            startPoint={conn.startNode.position}
            endPoint={conn.endNode.position}
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
