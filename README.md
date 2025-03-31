# ❤️ Valentine's Rentals Calculator ❤️

A simple rental pricing calculator for estimating event costs.

## User Inputs

- **Customize manually?** (Check to enter values manually)
- **Number of Guests:** `input`
- **6ft Tables:** `input`
- **8ft Tables:** `input`
- **Chairs:** `input`
- **Tent Size:** `dropdown`
  - No Tent
  - 10x10
  - 10x20
  - 20x20
  - 20x30
  - 20x40
- **Miles (One Way):** `input`
- **Opt-in for Delivery?** (Check if delivery is needed)

---

## Pricing Breakdown

- **Chair:** $2 each
- **6ft Table:** $6 each
- **8ft Table:** $8.50 each
- **Tent Pricing:**
  - 10x10: $25
  - 10x20: $80
  - 20x20: $150
  - 20x30: $200
  - 20x40: $250
- **Sidewall Pricing (per tent size):**
  - 10x10: Not an option
  - 10x20: $40
  - 20x20: $60
  - 20x30: $70
  - 20x40: $80
- **Delivery:** Free up to 5 miles, then $2 per extra mile

---

## JavaScript Code

```javascript
function calculatePrice() {
    let customOptions = document.getElementById('customOptions').checked;
    let guestCount = parseInt(document.getElementById('guests').value) || 0;
    let miles = parseInt(document.getElementById('miles').value) || 0;
    let deliveryOptIn = document.getElementById('deliveryOptIn').checked;

    let chairPrice = 2, table6ftPrice = 6, table8ftPrice = 8.50;
    let tentPrices = { 'None': 0, '10x10': 25, '10x20': 80, '20x20': 150, '20x30': 200, '20x40': 250 };
    let sidewallPrices = { 'None': 0, '10x10': 0, '10x20': 40, '20x20': 60, '20x30': 70, '20x40': 80 };
    let freeMiles = 5, deliveryRate = 2.00;

    let tables6ft = customOptions ? parseInt(document.getElementById('tables6ft').value) || 0 : Math.ceil(guestCount / 6);
    let tables8ft = customOptions ? parseInt(document.getElementById('tables8ft').value) || 0 : Math.ceil(guestCount / 8);
    let chairs = customOptions ? parseInt(document.getElementById('chairs').value) || 0 : guestCount;
    let tentSize = customOptions ? document.getElementById('tentSize').value : (guestCount > 0 ? (guestCount <= 10 ? '10x10' : guestCount <= 20 ? '10x20' : guestCount <= 40 ? '20x20' : guestCount <= 60 ? '20x30' : '20x40') : 'None');

    let tentCost = tentPrices[tentSize];
    let sidewallCost = sidewallPrices[tentSize];

    let totalCost = (tables6ft * table6ftPrice) + (tables8ft * table8ftPrice) + (chairs * chairPrice) + tentCost + sidewallCost;
    let extraMiles = deliveryOptIn ? Math.max(0, (miles * 2) - (freeMiles * 2)) : 0;
    let deliveryCost = extraMiles * deliveryRate;
    totalCost += deliveryCost;

    document.getElementById('result').innerHTML = `
        **Setup Details:**  
        - 6ft Tables: ${tables6ft} ($${(tables6ft * table6ftPrice).toFixed(2)})  
        - 8ft Tables: ${tables8ft} ($${(tables8ft * table8ftPrice).toFixed(2)})  
        - Chairs: ${chairs} ($${(chairs * chairPrice).toFixed(2)})  
        ${tentSize !== 'None' ? `- Tent: ${tentSize} ($${tentCost.toFixed(2)})  ` : ''}
        ${sidewallCost > 0 ? `- Sidewalls: $${sidewallCost.toFixed(2)}  ` : ''}
        - Delivery: $${deliveryCost.toFixed(2)}  
        **Total Estimate: $${totalCost.toFixed(2)}**  

        <button onclick="editEstimate()">Edit Estimate</button>
        <button onclick="downloadPDF()">Download PDF</button>
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
