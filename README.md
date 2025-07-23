<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Financial Assistant of a Bank</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #e6f2f1;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .chat-container {
      width: 95%;
      max-width: 400px;
      height: 90vh;
      background: #ffffff;
      box-shadow: 0 0 10px rgba(0,0,0,0.2);
      border-radius: 10px;
      display: flex;
      flex-direction: column;
      overflow: hidden;
    }

    .chat-header {
      background: #075e54;
      color: white;
      padding: 15px;
      text-align: center;
      font-size: 18px;
    }

    .chat-box {
      flex: 1;
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

    @media (max-width: 600px) {
      .chat-container {
        height: 95vh;
      }
    }
  </style>
</head>
<body>
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

  <script>
    const faqResponses = {
      "balance": "Check your balance via app, SMS, or ATM. | आप बैलेंस SMS या एटीएम से चेक कर सकते हैं | शिल्लक ATM किंवा SMS ने तपासा",
      "loan": "We offer various loan options. Visit the branch for more. | हम विभिन्न प्रकार के लोन देते हैं | कर्जासाठी शाखेत भेट द्या",
      "credit card": "Apply online or visit branch. | क्रेडिट कार्ड के लिए ऑनलाइन या शाखा में आवेदन करें | क्रेडिट कार्डसाठी ऑनलाईन अर्ज करा",
      "emi": "Use our EMI calculator online. | EMI की गणना ऑनलाइन करें | EMI कॅल्क्युलेटर वापरा",
      "fd": "Start FD with ₹1000. | ₹1000 से FD शुरू करें | ₹1000 पासून FD सुरू करा",
      "upi": "UPI से तुरंत पैसे भेजें | Instantly transfer using UPI | UPI ने त्वरित पैसे पाठवा",
      "kyc": "Update KYC online or in branch. | KYC ऑनलाइन या शाखा में अपडेट करें | KYC ऑनलाईन किंवा शाखेत अपडेट करा",
      "net banking": "Register online or in branch. | नेट बैंकिंग के लिए रजिस्टर करें | नेट बँकिंगसाठी नोंदणी करा",
      "open account": "Open account with PAN and Aadhaar. | पैन और आधार से खाता खोलें | पॅन व आधारने खाते उघडा",
      "debit card": "Apply for debit card online. | डेबिट कार्ड के लिए ऑनलाइन आवेदन करें | डेबिट कार्डसाठी ऑनलाईन अर्ज करा",
      "statement": "Download e-statement from net banking. | नेट बैंकिंग से स्टेटमेंट डाउनलोड करें | नेट बँकिंगमधून स्टेटमेंट डाउनलोड करा",
      "ifsc": "Find IFSC code on our website. | IFSC कोड हमारी वेबसाइट पर देखें | आमच्या वेबसाइटवर IFSC कोड पाहा",
      "aadhar link": "Link Aadhaar to your bank online. | बैंक में आधार लिंक करें | बँकेत आधार लिंक करा",
      "pan update": "Submit PAN card copy at branch. | पैन कार्ड शाखा में जमा करें | शाखेत पॅन सादर करा",
      "mobile update": "Update mobile via net banking. | नेट बैंकिंग से मोबाइल नंबर अपडेट करें | नेट बँकिंगने मोबाईल अपडेट करा",
      "rd": "Recurring Deposit starts from ₹500. | ₹500 से RD शुरू करें | ₹500 पासून RD सुरू करा",
      "overdraft": "Overdraft is available with current account. | चालू खाते में ओवरड्राफ्ट सुविधा उपलब्ध है | चालू खात्याला ओवरड्राफ्ट उपलब्ध आहे",
      "cheque": "Request cheque book online. | चेक बुक ऑनलाइन मांगे | ऑनलाईन चेकबुक मागवा",
      "mini statement": "Send ‘MINI’ to 9223XXXXXX. | 9223XXXXXX पर 'MINI' भेजें | 'MINI' 9223XXXXXX वर पाठवा",
      "block card": "Block card via app or call 1800-XXX. | कार्ड को ऐप से ब्लॉक करें | अॅपद्वारे कार्ड ब्लॉक करा",
      "interest rate": "Interest rates vary. Check latest online. | ब्याज दरें बदलती रहती हैं | व्याजदर ऑनलाईन तपासा",
      "account close": "Visit branch to close your account. | खाता बंद करने शाखा जाएं | खाते बंद करण्यासाठी शाखा भेट द्या"
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
      recognition.lang = 'hi-IN'; // Supports Hindi & Marathi
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
      return "Sorry, I didn’t get that. कृपया बँक संबंधित प्रश्न विचारा | Please ask a banking-related question.";
    }
  </script>
</body>
</html>
