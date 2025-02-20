<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Road To 1M Racing</title>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; padding: 20px; }
        button { margin: 10px; padding: 10px; font-size: 16px; cursor: pointer; }
        button:disabled { background-color: gray; cursor: not-allowed; }
        ul { list-style-type: none; padding: 0; }
        li { margin: 5px 0; }
        input { margin: 5px; padding: 5px; }
        #status { margin-top: 20px; font-weight: bold; }
    </style>
</head>
<body>
    <h1>Road To 1M Racing</h1>
    <button id="startButton" onclick="startAutoDrive()">Start Auto Drive</button>
    <button id="stopButton" onclick="stopAutoDrive()" disabled>Stop Auto Drive</button>
    
    <h2>Add Checkpoint</h2>
    <input type="number" id="checkpointX" placeholder="X" step="0.1">
    <input type="number" id="checkpointY" placeholder="Y" step="0.1">
    <input type="number" id="checkpointZ" placeholder="Z" step="0.1">
    <button onclick="addCheckpoint()">Add Checkpoint</button>
    <ul id="checkpointList"></ul>
    
    <h2>Add Stop Point</h2>
    <input type="number" id="stopX" placeholder="X" step="0.1">
    <input type="number" id="stopY" placeholder="Y" step="0.1">
    <input type="number" id="stopZ" placeholder="Z" step="0.1">
    <button onclick="addStopPoint()">Add Stop Point</button>
    <ul id="stopList"></ul>

    <div id="status">Status: Ready</div>

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
                resetInputs("checkpoint");
                updateStatus("Checkpoint added!");
            } else {
                updateStatus("Please enter valid coordinates for the checkpoint.");
            }
        }

        function addStopPoint() {
            let x = parseFloat(document.getElementById("stopX").value);
            let y = parseFloat(document.getElementById("stopY").value);
            let z = parseFloat(document.getElementById("stopZ").value);
            if (!isNaN(x) && !isNaN(y) && !isNaN(z)) {
                stopPoints.push({ x, y, z });
                updateList("stopList", stopPoints);
                resetInputs("stop");
                updateStatus("Stop point added!");
            } else {
                updateStatus("Please enter valid coordinates for the stop point.");
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
            document.getElementById("startButton").disabled = true;
            document.getElementById("stopButton").disabled = false;
            updateStatus("Auto Drive Started!");
            driveThroughCheckpoints();
        }

        function stopAutoDrive() {
            running = false;
            document.getElementById("startButton").disabled = false;
            document.getElementById("stopButton").disabled = true;
            updateStatus("Auto Drive Stopped!");
        }

        async function driveThroughCheckpoints() {
            for (let checkpoint of checkpoints) {
                if (!running) return;
                updateStatus(`Driving to checkpoint (${checkpoint.x}, ${checkpoint.y}, ${checkpoint.z})`);
                await sleep(3000);  // Simulate driving to the checkpoint
                if (stopPoints.some(sp => getDistance(sp, checkpoint) < 5)) {
                    updateStatus("Pressing E at stop point...");
                    await sleep(2000); // Simulate pressing 'E' at stop point
                }
            }
            if (running) {
                updateStatus("All checkpoints visited! Restarting drive.");
                driveThroughCheckpoints();  // Restart the drive after visiting all checkpoints
            }
        }

        function getDistance(a, b) {
            return Math.sqrt((a.x - b.x) ** 2 + (a.y - b.y) ** 2 + (a.z - b.z) ** 2);
        }

        function sleep(ms) {
            return new Promise(resolve => setTimeout(resolve, ms));
        }

        function resetInputs(type) {
            if (type === "checkpoint") {
                document.getElementById("checkpointX").value = '';
                document.getElementById("checkpointY").value = '';
                document.getElementById("checkpointZ").value = '';
            }
            if (type === "stop") {
                document.getElementById("stopX").value = '';
                document.getElementById("stopY").value = '';
                document.getElementById("stopZ").value = '';
            }
        }

        function updateStatus(message) {
            document.getElementById("status").innerText = `Status: ${message}`;
        }
    </script>
</body>
</html>
