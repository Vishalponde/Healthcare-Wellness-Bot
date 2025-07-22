<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Healthcare & Wellness Chatbot</title>
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
      background: #00796b;
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
    }

    .message {
      margin: 10px 0;
      padding: 8px 12px;
      border-radius: 15px;
      display: inline-block;
      max-width: 80%;
      word-wrap: break-word;
    }

    .user {
      background: #00796b;
      color: white;
      align-self: flex-end;
      float: right;
      clear: both;
    }

    .bot {
      background: #f1f1f1;
      color: #333;
      align-self: flex-start;
      float: left;
      clear: both;
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
      background: #00796b;
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
    <div class="chat-header">Healthcare & Wellness Bot</div>
    <div class="chat-box" id="chatBox">
      <div class="message bot">Hello! I’m your health assistant. How can I help you today?</div>
    </div>

    <!-- Quick Buttons -->
    <div class="quick-buttons">
      <button onclick="quickQuestion('timing')">Timings</button>
      <button onclick="quickQuestion('appointment')">Appointment</button>
      <button onclick="quickQuestion('location')">Location</button>
      <button onclick="quickQuestion('diet')">Diet Tips</button>
      <button onclick="quickQuestion('stress')">Stress Help</button>
      <button onclick="quickQuestion('vaccination')">Vaccines</button>
    </div>

    <!-- Input with Voice -->
    <div class="chat-input">
      <input type="text" id="userInput" placeholder="Type or use voice..."/>
      <button onclick="sendMessage()">Send</button>
      <button onclick="startVoice()">🎤</button>
    </div>
  </div>

  <script>
    function sendMessage() {
      const input = document.getElementById("userInput");
      const chatBox = document.getElementById("chatBox");
      const userText = input.value.trim();
      if (userText === "") return;

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
        alert("Voice input not supported in this browser.");
        return;
      }
      const recognition = new webkitSpeechRecognition();
      recognition.lang = 'en-IN';
      recognition.start();
      recognition.onresult = function(event) {
        document.getElementById("userInput").value = event.results[0][0].transcript;
        sendMessage();
      };
    }

    function getBotResponse(msg) {
      msg = msg.toLowerCase();

      if (msg.includes("hello") || msg.includes("hi") || msg.includes("नमस्कार")) {
        return "Hello! / नमस्कार! How can I assist you today? / मी तुम्हाला कशी मदत करू शकतो?";
      } 
      if (msg.includes("timing") || msg.includes("hours") || msg.includes("वेळ")) {
        return "We are open from 9 AM to 7 PM, Mon-Sat. / आम्ही सकाळी ९ ते संध्याकाळी ७ पर्यंत खुले असतो, सोमवार ते शनिवार.";
      }
      if (msg.includes("location") || msg.includes("कुठे")) {
        return "We’re located at 123 Health Street, Wellness City. / आम्ही 123 हेल्थ स्ट्रीट, वेलनेस सिटी येथे आहोत.";
      }
      if (msg.includes("appointment") || msg.includes("अपॉइंटमेंट")) {
        return "You can book an appointment by phone or website. / तुम्ही फोन करून किंवा वेबसाइटवरून अपॉइंटमेंट बुक करू शकता.";
      }
      if (msg.includes("diet") || msg.includes("आहार")) {
        return "A balanced diet is key to health. / संतुलित आहार हा आरोग्याचा पाया आहे.";
      }
      if (msg.includes("stress") || msg.includes("तणाव")) {
        return "Practice deep breathing & relaxation. / खोल श्वास घ्या आणि विश्रांती घ्या.";
      }
      if (msg.includes("vaccination") || msg.includes("लस")) {
        return "Yes, vaccines are available. / होय, लसी उपलब्ध आहेत.";
      }
      return "I'm here for health, appointments, and wellness tips. Please ask again. / मी आरोग्य आणि सल्ल्यासाठी येथे आहे. पुन्हा विचारा.";
    }
  </script>
</body>
</html>
