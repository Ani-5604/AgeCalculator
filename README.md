<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Age Calculator</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-image: linear-gradient(to bottom, rgba(91, 68, 58, 0.8), rgba(66, 66, 80, 0.8)), url('agecal.jpg');
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }
        .container, .welcome {
            align-self: flex-start;
            background-color: #fff;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 500px;
            padding: 20px;
            text-align: center;
            margin: 10px;
        }
        .welcome {
            max-width: 450px;
            align-self: start;
            align-items: center;
            animation-duration: 11ms;
            text-decoration: wavy;
            font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
            font-style: italic;
            flex-grow: initial;
            font-weight: 100;
            background-color: #f0e008;
        }
        h1, h2 {
            margin-top: 0;
            color: #070606;
        }
        input[type="date"], input[type="text"], button {
            width: calc(100% - 30px);
            padding: 10px;
            margin-bottom: 10px;
            background-color: rgb(255, 187, 240);
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            border: 1px solid #cccccc;
            box-sizing: border-box;
        }
        .question-mark-container {
            position: relative;
            display: inline-block;
        }
        .question-mark {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            cursor: pointer;
            font-size: 16px;
            color: #888;
            border-radius: 50%;
            background-color: #fff;
            width: 25px;
            height: 25px;
            line-height: 25px;
            text-align: center;
            box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }
        .tooltip {
            position: absolute;
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            border-radius: 4px;
            padding: 10px;
            display: none;
            width: 200px;
            z-index: 1;
        }
        .tooltip::after {
            content: '';
            position: absolute;
            border-style: solid;
            border-width: 5px 5px 0;
            border-color: rgba(0, 0, 0, 0.8) transparent transparent;
            top: 100%;
            left: 50%;
            transform: translateX(-50%);
        }
        input:hover + .question-mark-container .tooltip, .tooltip:hover {
            display: block;
        }
        button[type="button"] {
            background-color: #4CAF50;
            color: rgb(0, 0, 0);
            padding: 12px 20px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button[type="button"]:hover {
            background-color: #45a049;
        }
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            align-self: start;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
        }
        .modal-content {
            background-color: #fefefe;
            margin: 15% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            border-radius: 8px;
            box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2), 0 6px 20px 0 rgba(0,0,0,0.19);
        }
        .close {
            color: #0a0404;
            float: right;
            font-weight: bold;
        }
        #greeting {
            font-size: 20px;
            color: #007bff;
        }
        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Age Calculator</h1>
        <form id="ageCalculatorForm">
            <div>
                <label for="name">Enter Your Name please:</label>
                <input type="text" id="name" required>
                <span class="question-mark-container">
                    <span class="question-mark">?</span>
                    <div class="tooltip">This is your Name field</div>
                </span>
                <div id="greeting"></div>
                <button type="button" onclick="displayGreeting()">Submit Name</button>
            </div>
            <label for="DateofBirth">Enter Your Date Of Birth (dd-mm-yyyy):</label>
            <input type="date" id="DateofBirth" required>
            <span class="question-mark-container">
                <span class="question-mark">?</span>
                <div class="tooltip">This is your Date Of Birth field</div>
            </span>
            <button type="button" onclick="calculateAge()">Submit</button>
        </form>
    </div>
    <div id="resultModal" class="modal">
        <div class="modal-content">
            <span class="close" onclick="closeModal()">&times;</span>
            <p><span id="ageResult"></span></p>
        </div>
    </div>
    <script>
        function displayGreeting() {
            const name = document.getElementById("name").value;
            document.getElementById("greeting").textContent = `Welcome, ${name}!`;
        }

        function calculateAge() {
            const name = document.getElementById('name').value.trim();
            const dobInput = document.getElementById('DateofBirth').value;
            if (!name || !dobInput) {
                alert('Please enter your name and date of birth.');
                return;
            }

            const dob = new Date(dobInput);
            const today = new Date();

            let age = today.getFullYear() - dob.getFullYear();
            let monthDifference = today.getMonth() - dob.getMonth();
            let dayDifference = today.getDate() - dob.getDate();

            if (monthDifference < 0 || (monthDifference === 0 && dayDifference < 0)) {
                age--;
                monthDifference += 12;
            }

            if (dayDifference < 0) {
                const lastMonth = new Date(today.getFullYear(), today.getMonth(), 0).getDate();
                dayDifference += lastMonth;
                monthDifference--;
            }

            displayModal(name, age, monthDifference, dayDifference);
        }

        function displayModal(name, age, months, days) {
            document.getElementById('ageResult').textContent = `${name}, you are ${age} years ${months} months and ${days} days old.`;
            document.getElementById('resultModal').style.display = 'block';
        }

        function closeModal() {
            document.getElementById('resultModal').style.display = 'none';
        }
    </script>
</body>
</html>
