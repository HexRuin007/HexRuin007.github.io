<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Road To 1M Racing</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f4f7f6;
            color: #333;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }
        h1 {
            color: #3e8e41;
            margin-bottom: 20px;
        }
        h2 {
            color: #2d4b28;
            margin-bottom: 10px;
        }
        .card {
            background-color: white;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
            width: 90%;
            max-width: 400px;
            text-align: center;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 12px 24px;
            font-size: 16px;
            margin: 10px;
            cursor: pointer;
            border-radius: 8px;
            transition: background-color 0.3s ease;
        }
        button:hover:enabled {
            background-color: #45a049;
        }
        button:disabled {
            background-color: #ccc;
            cursor: not-allowed;
        }
        input {
            padding: 8px;
            margin: 8px;
            font-size: 16px;
            border: 2px solid #ccc;
            border-radius: 8px;
            width: 80%;
        }
        input:focus {
            border-color: #4CAF50;
            outline: none;
        }
        ul {
            list-style-type: none;
            padding: 0;
            text-align: left;
        }
        li {
            margin: 8px 0;
            font-size: 16px;
        }
        .status {
            font-weight: bold;
            font-size: 18px;
            margin-top: 20px;
        }
        .status.ready {
            color: #4CAF50;
        }
        .status.running {
            color: #f39c12;
        }
        .status.stopped {
            color: #e74c3c;
        }
        .list-container {
            max-height: 200px;
            overflow-y: auto;
        }
        .list-container li {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
    </style>
</head>
<body>
    <h1>Road To 1M Racing</h1>

    <div class="card">
        <button id="startButton" onclick="startGO()">Start GO</button>
        <button id="stopButton" onclick="stopGO()" disabled>Stop GO</button>
    </div>

    <div class="card">
        <h2>Add Checkpoint</h2>
        <button onclick="addCheckpoint()">Add Checkpoint (Player Position)</button>
        <div class="list-container">
            <ul id="checkpointList"></ul>
        </div>
    </div>

    <div class="card">
        <h2>Add Stop Point</h2>
        <input type="number" id="stopX" placeholder="X" step="0.1">
        <input type="number" id="stopY" placeholder="Y" step="0.1">
        <input type="number" id="stopZ" placeholder="Z" step="0.1">
        <button onclick="addStopPoint()">Add Stop Point</button>
        <div class="list-container">
            <ul id="stopList"></ul>
        </div>
    </div>

    <div class="status ready" id="status">Status: Ready</div>

    <script>
        let checkpoints = [];
        let stopPoints = [];
        let running = false;

        // Simulate getting player's current position (for example purposes)
        function getPlayerPosition() {
            // In real-world applications, this data would come from the game engine
            // Here we use random coordinates for demonstration
            return {
                x: (Math.random() * 100).toFixed(2),
                y: (Math.random() * 100).toFixed(2),
                z: (Math.random() * 100).toFixed(2)
            };
        }

        function addCheckpoint() {
            let playerPos = getPlayerPosition(); // Get current player position
            checkpoints.push(playerPos);
            updateList("checkpointList", checkpoints);
            updateStatus(`Checkpoint added at (${playerPos.x}, ${playerPos.y}, ${playerPos.z})`);
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

        function startGO() {
            running = true;
            document.getElementById("startButton").disabled = true;
            document.getElementById("stopButton").disabled = false;
            updateStatus("GO Started!", "running");
            driveThroughCheckpoints();
        }

        function stopGO() {
            running = false;
            document.getElementById("startButton").disabled = false;
            document.getElementById("stopButton").disabled = true;
            updateStatus("GO Stopped!", "stopped");
        }

        async function driveThroughCheckpoints() {
            for (let checkpoint of checkpoints) {
                if (!running) return;
                updateStatus(`Driving to checkpoint (${checkpoint.x}, ${checkpoint.y}, ${checkpoint.z})`, "running");
                await sleep(3000);  // Simulate driving to the checkpoint
                if (stopPoints.some(sp => getDistance(sp, checkpoint) < 5)) {
                    updateStatus("Pressing E at stop point...", "running");
                    await sleep(2000); // Simulate pressing 'E' at stop point
                }
            }
            if (running) {
                updateStatus("All checkpoints visited! Restarting GO.", "running");
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

        function updateStatus(message, statusClass = "ready") {
            const statusElement = document.getElementById("status");
            statusElement.innerText = `Status: ${message}`;
            statusElement.className = `status ${statusClass}`;
        }
    </script>
</body>
</html>
