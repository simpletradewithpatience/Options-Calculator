<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Options Calculator – Long Call, Put, Bull & Bear Spreads</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #121212;
      color: #e0e0e0;
      margin: 0;
      padding: 20px;
    }

    h2, h3 {
      color: #f0f0f0;
      text-align: center;
    }

    .container {
      max-width: 950px;
      margin: auto;
      background-color: #1e1e1e;
      border-radius: 10px;
      padding: 25px;
      box-shadow: 0 0 15px rgba(0, 0, 0, 0.5);
    }

    .grid {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 20px;
    }

    label {
      margin-top: 10px;
      display: block;
      font-weight: bold;
    }

    input, select {
      width: 100%;
      padding: 8px;
      margin-top: 5px;
      border: none;
      border-radius: 6px;
      background-color: #2e2e2e;
      color: #f0f0f0;
    }

    button {
      margin-top: 20px;
      padding: 12px;
      background-color: #007bff;
      color: white;
      border: none;
      width: 100%;
      font-size: 16px;
      border-radius: 6px;
      cursor: pointer;
    }

    button:hover {
      background-color: #0056b3;
    }

    .output {
      margin-top: 30px;
      background-color: #1b1b1b;
      padding: 20px;
      border-radius: 10px;
      border-left: 6px solid #007bff;
    }

    .output p {
      margin: 10px 0;
    }

    pre {
      background-color: #2a2a2a;
      padding: 12px;
      border-radius: 6px;
      white-space: pre-wrap;
      margin-top: 10px;
      color: #e0e0e0;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>📈 Options Strategy Calculator – Long, Spread, Smart Exit 💡</h2>

    <div class="grid">
      <div>
        <label>Stock Name</label>
        <input type="text" id="stockName">

        <label>Spot Price</label>
        <input type="number" id="spotPrice">

        <label>Lot Size (per lot)</label>
        <input type="number" id="lotSize">

        <label>Number of Lots</label>
        <input type="number" id="noOfLots">

        <label>Expiry Date</label>
        <input type="date" id="expiryDate" onchange="calculateDTE()">
        <p>DTE: <span id="dte">0</span> days</p>

        <label>Max Risk Allowed (₹)</label>
        <input type="number" id="maxRisk">

        <label>Strategy Type</label>
        <select id="strategyType" onchange="toggleStrategyFields()">
          <option value="">-- Select Strategy --</option>
          <option value="longCall">Long Call</option>
          <option value="longPut">Long Put</option>
          <option value="bullCallSpread">Bull Call Spread</option>
          <option value="bearPutSpread">Bear Put Spread</option>
        </select>
      </div>

      <div id="longCallFields" style="display:none">
        <h3>🔧 Long Call Inputs</h3>
        <label>Strike Price</label>
        <input type="number" id="longCallStrike">
        <label>Premium Paid</label>
        <input type="number" id="longCallPremium">
        <label>Current Option LTP</label>
        <input type="number" id="longCallLTP">
        <button onclick="calculateLongCall()">Calculate</button>
      </div>

      <div id="longPutFields" style="display:none">
        <h3>🔧 Long Put Inputs</h3>
        <label>Strike Price</label>
        <input type="number" id="longPutStrike">
        <label>Premium Paid</label>
        <input type="number" id="longPutPremium">
        <label>Current Option LTP</label>
        <input type="number" id="longPutLTP">
        <button onclick="calculateLongPut()">Calculate</button>
      </div>

      <div id="bullCallSpreadFields" style="display:none">
        <h3>🧮 Bull Call Spread Inputs</h3>
        <label>Buy Call Strike Price</label>
        <input type="number" id="bcsBuyStrike">
        <label>Buy Premium Paid</label>
        <input type="number" id="bcsBuyPremium">
        <label>Buy Option LTP</label>
        <input type="number" id="bcsBuyLTP">
        <label>Sell Call Strike Price</label>
        <input type="number" id="bcsSellStrike">
        <label>Sell Premium Received</label>
        <input type="number" id="bcsSellPremium">
        <label>Sell Option LTP</label>
        <input type="number" id="bcsSellLTP">
        <button onclick="calculateBullCallSpread()">Calculate</button>
      </div>

      <div id="bearPutSpreadFields" style="display:none">
        <h3>🧮 Bear Put Spread Inputs</h3>
        <label>Buy Put Strike Price</label>
        <input type="number" id="bpsBuyStrike">
        <label>Buy Premium Paid</label>
        <input type="number" id="bpsBuyPremium">
        <label>Buy Option LTP</label>
        <input type="number" id="bpsBuyLTP">
        <label>Sell Put Strike Price</label>
        <input type="number" id="bpsSellStrike">
        <label>Sell Premium Received</label>
        <input type="number" id="bpsSellPremium">
        <label>Sell Option LTP</label>
        <input type="number" id="bpsSellLTP">
        <button onclick="calculateBearPutSpread()">Calculate</button>
      </div>
    </div>

    <div class="output">
      <h3>📊 Output</h3>
      <p><strong>Breakeven Point:</strong> <span id="breakeven">--</span></p>
      <p><strong>Max Profit:</strong> <span id="maxProfit">--</span></p>
      <p><strong>Max Loss (Capital Used):</strong> ₹<span id="maxLoss">--</span></p>
      <p><strong>Theoretical ROI:</strong> <span id="roi">--</span></p>
      <p><strong>Real-Time PnL:</strong> ₹<span id="pnl">--</span></p>
      <p><strong>Suggested Target Profit:</strong> ₹<span id="targetAuto">--</span></p>
      <p><strong>🧠 Smart Exit:</strong></p>
      <pre id="exitSuggestion">--</pre>
    </div>

    <button onclick="resetCalculator()" style="background-color: #6c757d;">🔄 Reset Calculator</button>
  </div>

<script>
function toggleStrategyFields() {
  const strategy = document.getElementById("strategyType").value;
  document.getElementById("longCallFields").style.display = strategy === "longCall" ? "block" : "none";
  document.getElementById("longPutFields").style.display = strategy === "longPut" ? "block" : "none";
  document.getElementById("bullCallSpreadFields").style.display = strategy === "bullCallSpread" ? "block" : "none";
  document.getElementById("bearPutSpreadFields").style.display = strategy === "bearPutSpread" ? "block" : "none";
}

function calculateDTE() {
  const expiry = new Date(document.getElementById("expiryDate").value);
  const today = new Date();
  const diff = Math.ceil((expiry - today) / (1000 * 60 * 60 * 24));
  document.getElementById("dte").innerText = diff > 0 ? diff : 0;
}

function calculateLongCall() {
  const strike = parseFloat(document.getElementById("longCallStrike").value);
  const premium = parseFloat(document.getElementById("longCallPremium").value);
  const ltp = parseFloat(document.getElementById("longCallLTP").value);
  const spot = parseFloat(document.getElementById("spotPrice").value);
  const lotSize = parseInt(document.getElementById("lotSize").value);
  const noOfLots = parseInt(document.getElementById("noOfLots").value);
  const risk = parseFloat(document.getElementById("maxRisk").value);
  const dte = parseInt(document.getElementById("dte").innerText);
  const totalQty = lotSize * noOfLots;

  if ([strike, premium, ltp, spot, lotSize, noOfLots, risk].some(isNaN)) {
    alert("⚠️ Please fill in all required fields.");
    return;
  }

  const cost = premium * totalQty;
  const pnl = (ltp - premium) * totalQty;
  const roi = ((pnl / cost) * 100).toFixed(2) + "%";
  const breakeven = strike + premium;
  const targetProfit = cost;

  updateOutput(breakeven, "Unlimited", cost, roi, pnl, targetProfit, getSmartExitMessage(pnl, risk, targetProfit, dte, spot, strike, "call"));
}

function calculateLongPut() {
  const strike = parseFloat(document.getElementById("longPutStrike").value);
  const premium = parseFloat(document.getElementById("longPutPremium").value);
  const ltp = parseFloat(document.getElementById("longPutLTP").value);
  const spot = parseFloat(document.getElementById("spotPrice").value);
  const lotSize = parseInt(document.getElementById("lotSize").value);
  const noOfLots = parseInt(document.getElementById("noOfLots").value);
  const risk = parseFloat(document.getElementById("maxRisk").value);
  const dte = parseInt(document.getElementById("dte").innerText);
  const totalQty = lotSize * noOfLots;

  if ([strike, premium, ltp, spot, lotSize, noOfLots, risk].some(isNaN)) {
    alert("⚠️ Please fill in all required fields.");
    return;
  }

  const cost = premium * totalQty;
  const pnl = (ltp - premium) * totalQty;
  const roi = ((pnl / cost) * 100).toFixed(2) + "%";
  const breakeven = strike - premium;
  const targetProfit = cost;

  updateOutput(breakeven, "Unlimited", cost, roi, pnl, targetProfit, getSmartExitMessage(pnl, risk, targetProfit, dte, spot, strike, "put"));
}

function calculateBullCallSpread() {
  const buyStrike = parseFloat(document.getElementById("bcsBuyStrike").value);
  const buyPremium = parseFloat(document.getElementById("bcsBuyPremium").value);
  const buyLTP = parseFloat(document.getElementById("bcsBuyLTP").value);
  const sellStrike = parseFloat(document.getElementById("bcsSellStrike").value);
  const sellPremium = parseFloat(document.getElementById("bcsSellPremium").value);
  const sellLTP = parseFloat(document.getElementById("bcsSellLTP").value);
  const spot = parseFloat(document.getElementById("spotPrice").value);
  const lotSize = parseInt(document.getElementById("lotSize").value);
  const noOfLots = parseInt(document.getElementById("noOfLots").value);
  const risk = parseFloat(document.getElementById("maxRisk").value);
  const dte = parseInt(document.getElementById("dte").innerText);
  const totalQty = lotSize * noOfLots;

  if ([buyStrike, buyPremium, buyLTP, sellStrike, sellPremium, sellLTP, spot, lotSize, noOfLots, risk].some(isNaN)) {
    alert("⚠️ Please fill in all required fields for Bull Call Spread.");
    return;
  }

  const netPremium = buyPremium - sellPremium;
  const cost = netPremium * totalQty;
  const breakeven = buyStrike + netPremium;
  const maxProfit = (sellStrike - buyStrike - netPremium) * totalQty;
  const pnl = ((buyLTP - buyPremium) - (sellLTP - sellPremium)) * totalQty;
  const roi = ((pnl / cost) * 100).toFixed(2) + "%";
  const targetProfit = maxProfit;

  updateOutput(breakeven, maxProfit.toFixed(2), cost, roi, pnl, targetProfit, getSmartExitMessage(pnl, risk, targetProfit, dte, spot, buyStrike, "callSpread"));
}

function calculateBearPutSpread() {
  const buyStrike = parseFloat(document.getElementById("bpsBuyStrike").value);
  const buyPremium = parseFloat(document.getElementById("bpsBuyPremium").value);
  const buyLTP = parseFloat(document.getElementById("bpsBuyLTP").value);
  const sellStrike = parseFloat(document.getElementById("bpsSellStrike").value);
  const sellPremium = parseFloat(document.getElementById("bpsSellPremium").value);
  const sellLTP = parseFloat(document.getElementById("bpsSellLTP").value);
  const spot = parseFloat(document.getElementById("spotPrice").value);
  const lotSize = parseInt(document.getElementById("lotSize").value);
  const noOfLots = parseInt(document.getElementById("noOfLots").value);
  const risk = parseFloat(document.getElementById("maxRisk").value);
  const dte = parseInt(document.getElementById("dte").innerText);
  const totalQty = lotSize * noOfLots;

  if ([buyStrike, buyPremium, buyLTP, sellStrike, sellPremium, sellLTP, spot, lotSize, noOfLots, risk].some(isNaN)) {
    alert("⚠️ Please fill in all required fields for Bear Put Spread.");
    return;
  }

  const netPremium = buyPremium - sellPremium;
  const cost = netPremium * totalQty;
  const breakeven = buyStrike - netPremium;
  const maxProfit = (buyStrike - sellStrike - netPremium) * totalQty;
  const pnl = ((buyLTP - buyPremium) - (sellLTP - sellPremium)) * totalQty;
  const roi = ((pnl / cost) * 100).toFixed(2) + "%";
  const targetProfit = maxProfit;

  updateOutput(breakeven, maxProfit.toFixed(2), cost, roi, pnl, targetProfit, getSmartExitMessage(pnl, risk, targetProfit, dte, spot, buyStrike, "putSpread"));
}

function updateOutput(breakeven, maxProfit, maxLoss, roi, pnl, targetProfit, exitMessage) {
  document.getElementById("breakeven").innerText = breakeven.toFixed(2);
  document.getElementById("maxProfit").innerText = maxProfit;
  document.getElementById("maxLoss").innerText = maxLoss.toFixed(2);
  document.getElementById("roi").innerText = roi;
  document.getElementById("pnl").innerText = pnl.toFixed(2);
  document.getElementById("targetAuto").innerText = targetProfit.toFixed(2);
  document.getElementById("exitSuggestion").innerText = exitMessage;
}

function getSmartExitMessage(pnl, risk, targetProfit, dte, spot, strike, type) {
  const pnlPctOfTarget = (pnl / targetProfit) * 100;
  let suggestion = "";

  if (pnl >= targetProfit) {
    suggestion = "✅ Book Profit 🎯\nTarget achieved. Consider trailing SL or partial booking.";
  } else if (pnl <= -risk) {
    suggestion = "❌ Book Loss 🛑\nMax loss breached. Respect discipline and exit.";
  } else if (dte < 2 && ((type === "call" && spot < strike) || (type === "put" && spot > strike))) {
    suggestion = "⏱️ Exit or Roll\nTheta risk high near expiry. Consider rolling or closing.";
  } else if ((type === "call" && spot > strike * 1.05) || (type === "put" && spot < strike * 0.95)) {
    suggestion = "⚠️ Hold with Caution\nBreakout in progress. Trail SL or review Greeks.";
  } else if (pnl > 0 && pnl >= 0.5 * risk && pnl < targetProfit * 0.5) {
    suggestion = "📈 Gaining Momentum\nProfit crossed 50% of your risk threshold. Consider partial exit or trailing stop.";
  } else if (pnlPctOfTarget >= 50 && pnlPctOfTarget < 100) {
    suggestion = "🟡 Partial Booking Opportunity\nStrong momentum. Consider partial exit.";
  } else {
    suggestion = "🟢 Hold\nWithin range. No action needed yet.";
  }

  return suggestion;
}

function resetCalculator() {
  document.querySelectorAll("input").forEach(input => {
    input.value = "";
    input.style.border = "none";
  });
  document.getElementById("strategyType").value = "";
  toggleStrategyFields();
  updateOutput(0, "--", 0, "--", 0, 0, "--");
  document.getElementById("dte").innerText = "0";
}

const updateOutputBackup = updateOutput;
updateOutput = function (...args) {
  document.getElementById("roi").style.color = args[4] >= 0 ? "#4caf50" : "#f44336";
  document.getElementById("pnl").style.color = args[4] >= 0 ? "#4caf50" : "#f44336";
  updateOutputBackup(...args);
};
</script>
</body>
</html>
