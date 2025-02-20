<!DOCTYPE html>



<html lang="en">
 <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Road To 1M Racing</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 20px; }
        button { margin: 10px; padding: 10px; font-size: 16px; }
        ul { list-style-type: none; padding: 0; }
        li { margin: 5px 0; }
    </style>
    <script>
        let checkpoints = [];
        let stopPoints = [];
        let running = false;

        function addCheckpoint() {
            let x = parseFloat(document.getElementById("checkpointX").value);
            let y = parseFloat(document.getElementById("checkpointY").value);
            let z = parseFloat(document.getElementById("checkpointZ").value);
            if (!isNaN(x) && !isNaN(y) && !isNaN(z)) {
                checkpoints.push({ x, y, z });
                updateList("checkpointList", checkpoints);
            }
        }

        function addStopPoint() {
            let x = parseFloat(document.getElementById("stopX").value);
            let y = parseFloat(document.getElementById("stopY").value);
            let z = parseFloat(document.getElementById("stopZ").value);
            if (!isNaN(x) && !isNaN(y) && !isNaN(z)) {
                stopPoints.push({ x, y, z });
                updateList("stopList", stopPoints);
            }
        }

        function updateList(elementId, list) {
            let listElement = document.getElementById(elementId);
            listElement.innerHTML = "";
            list.forEach((point, index) => {
                listElement.innerHTML += `<li>(${point.x}, ${point.y}, ${point.z}) <button onclick='removePoint(${index}, "${elementId}")'>Remove</button></li>`;
            });
        }

        function removePoint(index, listType) {
            if (listType === "checkpointList") checkpoints.splice(index, 1);
            if (listType === "stopList") stopPoints.splice(index, 1);
            updateList(listType, listType === "checkpointList" ? checkpoints : stopPoints);
        }

        function startAutoDrive() {
            running = true;
            alert("Auto Drive Started!");
            driveThroughCheckpoints();
        }

        function stopAutoDrive() {
            running = false;
            alert("Auto Drive Stopped!");
        }

        async function driveThroughCheckpoints() {
            for (let checkpoint of checkpoints) {
                if (!running) return;
                console.log("Setting Waypoint: ", checkpoint);
                await sleep(5000);
                if (stopPoints.some(sp => getDistance(sp, checkpoint) < 5)) {
                    console.log("Pressing E at stop point");
                    await sleep(2000);
                }
            }
            if (running) driveThroughCheckpoints();
        }

        function getDistance(a, b) {
            return Math.sqrt((a.x - b.x) ** 2 + (a.y - b.y) ** 2 + (a.z - b.z) ** 2);
        }

        function sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
        }
    </script>
</head>
<body>
    <h1>Road To 1M Racing</h1>
    <button onclick="startAutoDrive()">Start</button>
    <button onclick="stopAutoDrive()">Stop</button>
    
    <h2>Add Checkpoint</h2>
    <input type="number" id="checkpointX" placeholder="X" step="0.1">
    <input type="number" id="checkpointY" placeholder="Y" step="0.1">
    <input type="number" id="checkpointZ" placeholder="Z" step="0.1">
    <button onclick="addCheckpoint()">Add</button>
    <ul id="checkpointList"></ul>
    
    <h2>Add Stop Point</h2>
    <input type="number" id="stopX" placeholder="X" step="0.1">
    <input type="number" id="stopY" placeholder="Y" step="0.1">
    <input type="number" id="stopZ" placeholder="Z" step="0.1">
    <button onclick="addStopPoint()">Add</button>
    <ul id="stopList"></ul>
</body>
</html>
