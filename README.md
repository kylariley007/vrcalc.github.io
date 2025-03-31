<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Valentine's Rentals Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f7e6ee;
            text-align: center;
            padding: 20px;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 15px;
            box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
            max-width: 500px;
            margin: auto;
        }
        h1 {
            color: #d63384;
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            margin: 5px 0;
            border-radius: 8px;
            border: 1px solid #ddd;
        }
        button {
            background-color: #d63384;
            color: white;
            border: none;
            font-size: 18px;
            cursor: pointer;
        }
        button:hover {
            background-color: #b52b6f;
        }
        .result {
            margin-top: 10px;
            font-size: 18px;
            font-weight: bold;
        }
    </style>
    <script>
        function calculatePrice() {
            let customOptions = document.getElementById('customOptions').checked;
            let guestCount = parseInt(document.getElementById('guests').value) || 0;
            let miles = parseInt(document.getElementById('miles').value) || 0;
            let deliveryOptIn = document.getElementById('deliveryOptIn').checked;
            
            let chairPrice = 2;
            let table6ftPrice = 6;
            let table8ftPrice = 8.50;
            let tentPrices = { 'None': 0, '10x10': 25, '10x20': 80, '20x20': 150, '20x30': 200, '20x40': 250 };
            let sidewallPrices = { 'None': 0, '10x10': 0, '10x20': 40, '20x20': 60, '20x30': 70, '20x40': 80 };
            let freeMiles = 5;
            let deliveryRate = 2.00;

            let tables6ft = customOptions ? parseInt(document.getElementById('tables6ft').value) || 0 : Math.ceil(guestCount / 6);
            let tables8ft = customOptions ? parseInt(document.getElementById('tables8ft').value) || 0 : Math.ceil(guestCount / 8);
            let chairs = customOptions ? parseInt(document.getElementById('chairs').value) || 0 : guestCount;
            let tentSize = customOptions ? document.getElementById('tentSize').value : (guestCount > 0 ? (guestCount <= 10 ? '10x10' : guestCount <= 20 ? '10x20' : guestCount <= 40 ? '20x20' : guestCount <= 60 ? '20x30' : '20x40') : 'None');
            
            let tentCost = tentPrices[tentSize];
            let sidewallCost = sidewallPrices[tentSize];

            let tableCost6ft = tables6ft * table6ftPrice;
            let tableCost8ft = tables8ft * table8ftPrice;
            let chairCost = chairs * chairPrice;
            
            let totalItemCost = tableCost6ft + tableCost8ft + chairCost + tentCost + sidewallCost;
            let extraMiles = deliveryOptIn ? Math.max(0, (miles * 2) - (freeMiles * 2)) : 0;
            let deliveryCost = extraMiles * deliveryRate;
            let totalCost = totalItemCost + deliveryCost;
            
            document.getElementById('result').innerHTML = `
                <strong>Setup Details:</strong><br>
                Tables (6ft): ${tables6ft} ($${tableCost6ft.toFixed(2)})<br>
                Tables (8ft): ${tables8ft} ($${tableCost8ft.toFixed(2)})<br>
                Chairs: ${chairs} ($${chairCost.toFixed(2)})<br>
                ${tentSize !== 'None' ? `Tent Size: ${tentSize} ($${tentCost.toFixed(2)})<br>` : ''}
                ${sidewallCost > 0 ? `Sidewalls: $${sidewallCost.toFixed(2)}<br>` : ''}
                Delivery: $${deliveryCost.toFixed(2)}<br>
                <strong>Total Estimate: $${totalCost.toFixed(2)}</strong>
                <br><br>
                <button onclick="editEstimate()">Edit Estimate</button>
                <button onclick="downloadPDF()">Download Estimate as PDF</button>
            `;
        }

        function editEstimate() {
            document.getElementById('result').contentEditable = true;
        }

        function downloadPDF() {
            let content = document.getElementById('result').innerText;
            let blob = new Blob([content], { type: 'application/pdf' });
            let link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = 'Estimate.pdf';
            link.click();
        }
    </script>
</head>
<body>
    <div class="container">
        <h1>❤️ Valentine's Rentals Calculator ❤️</h1>
        <label>
            <input type="checkbox" id="customOptions" onchange="document.getElementById('guests').disabled = this.checked"> Customize options manually (Skip recommendations)
        </label>
        <input type="number" id="guests" placeholder="Number of Guests">
        <input type="number" id="tables6ft" placeholder="Number of 6ft Tables">
        <input type="number" id="tables8ft" placeholder="Number of 8ft Tables">
        <input type="number" id="chairs" placeholder="Number of Chairs">
        <select id="tentSize">
            <option value="None">No Tent</option>
            <option value="10x10">10x10</option>
            <option value="10x20">10x20</option>
            <option value="20x20">20x20</option>
            <option value="20x30">20x30</option>
            <option value="20x40">20x40</option>
        </select>
        <input type="number" id="miles" placeholder="Miles One Way">
        <label>
            <input type="checkbox" id="deliveryOptIn"> Opt-in for delivery (First 5 miles free, $2 per mile after)
        </label>
        <button onclick="calculatePrice()">Calculate Price 💰</button>
        <div class="result" id="result"></div>
    </div>
</body>
</html>
