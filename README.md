<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Bank Assistant with EMI & Interest Calculator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #e6f2f1;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .chat-container {
      width: 95%;
      max-width: 400px;
      background: #ffffff;
      box-shadow: 0 0 10px rgba(0,0,0,0.2);
      border-radius: 10px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
      margin-top: 10px;
    }

    .chat-header {
      background: #075e54;
      color: white;
      padding: 15px;
      text-align: center;
      font-size: 18px;
    }

    .chat-box {
      height: 50vh;
      padding: 15px;
      overflow-y: auto;
      font-size: 15px;
      background-color: #dcf8c6;
    }

    .message {
      margin: 10px 0;
      padding: 8px 12px;
      border-radius: 20px;
      display: inline-block;
      max-width: 80%;
      word-wrap: break-word;
      clear: both;
    }

    .user {
      background: #dcf8c6;
      color: black;
      float: right;
    }

    .bot {
      background: #ffffff;
      color: black;
      float: left;
    }

    .chat-input {
      display: flex;
      padding: 10px;
      background: #f1f1f1;
    }

    .chat-input input {
      flex: 1;
      padding: 10px;
      border: none;
      border-radius: 20px;
      outline: none;
    }

    .chat-input button {
      background: #25d366;
      border: none;
      color: white;
      padding: 10px 15px;
      margin-left: 10px;
      border-radius: 20px;
      cursor: pointer;
    }

    .quick-buttons {
      display: flex;
      flex-wrap: nowrap;
      overflow-x: auto;
      padding: 10px;
      background: #fafafa;
      gap: 5px;
    }

    .quick-buttons button {
      flex: 0 0 auto;
      background: #009688;
      color: white;
      border: none;
      padding: 6px 10px;
      border-radius: 15px;
      cursor: pointer;
      font-size: 13px;
      white-space: nowrap;
    }

    .calculator {
      width: 95%;
      max-width: 400px;
      background: #ffffff;
      box-shadow: 0 0 10px rgba(0,0,0,0.2);
      border-radius: 10px;
      padding: 15px;
      margin: 15px 0;
    }

    .calculator input, .calculator select, .calculator button {
      width: 100%;
      margin: 8px 0;
      padding: 10px;
      font-size: 15px;
      border-radius: 8px;
      border: 1px solid #ccc;
    }

    .calculator button {
      background-color: #00796b;
      color: white;
      cursor: pointer;
      border: none;
    }

    @media (max-width: 600px) {
      .chat-box {
        height: 45vh;
      }
    }
  </style>
</head>
<body>

  <!-- Chatbot -->
  <div class="chat-container">
    <div class="chat-header">💰 Financial Assistant of a Bank</div>
    <div class="chat-box" id="chatBox">
      <div class="message bot">Hello! नमस्कार! I am your financial assistant. Ask me anything related to banking!</div>
    </div>

    <div class="quick-buttons">
      <button onclick="quickQuestion('Balance')">Balance</button>
      <button onclick="quickQuestion('Loan')">Loan</button>
      <button onclick="quickQuestion('Credit Card')">Credit Card</button>
      <button onclick="quickQuestion('EMI')">EMI</button>
      <button onclick="quickQuestion('FD')">FD</button>
      <button onclick="quickQuestion('UPI')">UPI</button>
      <button onclick="quickQuestion('KYC')">KYC</button>
    </div>

    <div class="chat-input">
      <input type="text" id="userInput" placeholder="Type or speak..."/>
      <button onclick="sendMessage()">Send</button>
      <button onclick="startVoice()">🎤</button>
    </div>
  </div>

  <!-- EMI / Interest Calculator -->
  <div class="calculator">
    <h3>📊 EMI / Interest Calculator</h3>
    <select id="calcType" onchange="toggleCalc()">
      <option value="emi">💳 EMI Calculator</option>
      <option value="simple">📈 Simple Interest</option>
    </select>

    <input type="number" id="principal" placeholder="Principal Amount (₹)">
    <input type="number" id="rate" placeholder="Rate of Interest (%)">
    <input type="number" id="time" placeholder="Time Period (months for EMI, years for Interest)">

    <button onclick="calculate()">Calculate</button>
    <p id="calcResult"></p>
  </div>

  <script>
    const faqResponses = {
      "balance": "Check your balance via app, SMS, or ATM. | आप बैलेंस SMS या एटीएम से चेक कर सकते हैं | शिल्लक ATM किंवा SMS ने तपासा",
      "loan": "We offer various loan options. Visit the branch for more. | हम विभिन्न प्रकार के लोन देते हैं | कर्जासाठी शाखेत भेट द्या",
      "credit card": "Apply online or visit branch. | क्रेडिट कार्ड के लिए ऑनलाइन या शाखा में आवेदन करें | क्रेडिट कार्डसाठी ऑनलाईन अर्ज करा",
      "emi": "Use the EMI calculator below to get your monthly installment. | नीचे EMI कैलकुलेटर का उपयोग करें | खाली EMI कॅल्क्युलेटर वापरा",
      "fd": "Start FD with ₹1000. | ₹1000 से FD शुरू करें | ₹1000 पासून FD सुरू करा",
      "upi": "UPI से तुरंत पैसे भेजें | Instantly transfer using UPI | UPI ने त्वरित पैसे पाठवा",
      "kyc": "Update KYC online or in branch. | KYC ऑनलाइन या शाखा में अपडेट करें | KYC ऑनलाईन किंवा शाखेत अपडेट करा"
    };

    function sendMessage() {
      const input = document.getElementById("userInput");
      const chatBox = document.getElementById("chatBox");
      const userText = input.value.trim();
      if (!userText) return;

      const userMsg = document.createElement("div");
      userMsg.className = "message user";
      userMsg.textContent = userText;
      chatBox.appendChild(userMsg);

      const botMsg = document.createElement("div");
      botMsg.className = "message bot";
      botMsg.textContent = getBotResponse(userText);
      chatBox.appendChild(botMsg);

      chatBox.scrollTop = chatBox.scrollHeight;
      input.value = "";
    }

    function quickQuestion(text) {
      document.getElementById("userInput").value = text;
      sendMessage();
    }

    function startVoice() {
      if (!('webkitSpeechRecognition' in window)) {
        alert("Voice input not supported.");
        return;
      }
      const recognition = new webkitSpeechRecognition();
      recognition.lang = 'hi-IN';
      recognition.start();
      recognition.onresult = function(event) {
        document.getElementById("userInput").value = event.results[0][0].transcript;
        sendMessage();
      };
    }

    function getBotResponse(msg) {
      msg = msg.toLowerCase();
      for (const key in faqResponses) {
        if (msg.includes(key)) return faqResponses[key];
      }
      return "Sorry, I didn’t get that. कृपया बँक संबंधित प्रश्न विचारा | Please ask a banking-related question contact 8055750397 Email vishuponde@gmail.com.";
    }

    function toggleCalc() {
      const type = document.getElementById("calcType").value;
      const timeInput = document.getElementById("time");
      timeInput.placeholder = type === "emi" ? "Time Period (in months)" : "Time Period (in years)";
    }

    function calculate() {
      const type = document.getElementById("calcType").value;
      const p = parseFloat(document.getElementById("principal").value);
      const r = parseFloat(document.getElementById("rate").value);
      const t = parseFloat(document.getElementById("time").value);
      const result = document.getElementById("calcResult");

      if (isNaN(p) || isNaN(r) || isNaN(t)) {
        result.textContent = "❌ Please fill all fields correctly.";
        return;
      }

      if (type === "emi") {
        const monthlyRate = r / 12 / 100;
        const emi = (p * monthlyRate * Math.pow(1 + monthlyRate, t)) / (Math.pow(1 + monthlyRate, t) - 1);
        result.textContent = `💳 EMI: ₹${emi.toFixed(2)} per month for ${t} months`;
      } else {
        const si = (p * r * t) / 100;
        result.textContent = `📈 Simple Interest: ₹${si.toFixed(2)} over ${t} years`;
      }
    }
  </script>
</body>
</html>
