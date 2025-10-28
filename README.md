<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Applyze Recruiting Bot</title>
<style>
  body {
    margin: 0;
    height: 100vh;
    background: url('1.jpeg') no-repeat center center/cover;
    background-attachment: fixed;
    font-family: 'Poppins', sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    overflow-x: hidden;
  }

  /* Mission and Vision Section */
  .info-section {
    background: rgba(255, 255, 255, 0.95);
    width: 85%;
    max-width: 850px;
    padding: 40px;
    border-radius: 20px;
    box-shadow: 0 4px 25px rgba(0, 0, 0, 0.15);
    text-align: center;
    margin-bottom: 100px;
    transform: translateX(-120%);
    opacity: 0;
    animation: slideIn 1s ease forwards;
  }

  @keyframes slideIn {
    to {
      transform: translateX(0);
      opacity: 1;
    }
  }

  .info-section h2 {
    color: #4B6CFB;
    margin-bottom: 12px;
    font-size: 24px;
  }

  .info-section p {
    color: #333;
    line-height: 1.7;
    font-size: 16px;
    margin-bottom: 24px;
  }

  /* Chatbot Floating Button */
  .chat-toggle {
    position: fixed;
    bottom: 25px;
    right: 25px;
    background: #4B6CFB;
    color: white;
    width: 60px;
    height: 60px;
    border-radius: 50%;
    border: none;
    cursor: pointer;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
    font-size: 26px;
    display: flex;
    justify-content: center;
    align-items: center;
    transition: 0.3s;
    z-index: 999;
  }

  .chat-toggle:hover {
    transform: scale(1.1);
  }

  /* Chatbot Window */
  .chatbot {
    position: fixed;
    bottom: 100px;
    right: 25px;
    width: 340px;
    height: 520px;
    background: #ffffff;
    border-radius: 12px;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.25);
    display: flex;
    flex-direction: column;
    overflow: hidden;
    border: 1px solid #ddd;
    transition: all 0.3s ease;
    opacity: 0;
    transform: scale(0.8);
    pointer-events: none;
  }

  .chatbot.active {
    opacity: 1;
    transform: scale(1);
    pointer-events: all;
  }

  .chat-header {
    background: #4B6CFB;
    color: white;
    padding: 14px;
    text-align: center;
    font-weight: bold;
  }

  .chat-messages {
    flex: 1;
    padding: 12px;
    overflow-y: auto;
    background: #f9f9f9;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  .chat-input {
    display: flex;
    border-top: 1px solid #ddd;
  }

  .chat-input input {
    flex: 1;
    padding: 10px;
    border: none;
    outline: none;
  }

  .chat-input button {
    background: #4B6CFB;
    color: white;
    border: none;
    padding: 10px 16px;
    cursor: pointer;
  }

  .message {
    line-height: 1.4;
    animation: fadeIn 0.3s ease;
    max-width: 85%;
    word-wrap: break-word;
    clear: both;
  }

  .bot {
    background: #e9e9e9;
    color: #333;
    padding: 8px 10px;
    border-radius: 10px 10px 10px 0;
    display: inline-block;
    align-self: flex-start;
  }

  .user {
    background: #4B6CFB;
    color: white;
    border-radius: 10px 10px 0 10px;
    padding: 8px 10px;
    display: inline-block;
    align-self: flex-end;
  }

  .summary-box {
    background: #e0e0e0;
    padding: 10px;
    border-radius: 10px;
    line-height: 1.6;
    font-size: 14px;
    color: #222;
    margin-top: 5px;
  }

  .summary-box strong {
    color: #4B6CFB;
  }

  .options {
    margin-top: 5px;
  }

  .options button {
    display: inline-block;
    margin: 5px 5px 0 0;
    padding: 8px 12px;
    border: 1px solid #4B6CFB;
    background: white;
    color: #4B6CFB;
    border-radius: 6px;
    cursor: pointer;
    transition: 0.3s;
  }

  .options button:hover {
    background: #4B6CFB;
    color: white;
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(5px); }
    to { opacity: 1; transform: translateY(0); }
  }
</style>
</head>
<body>

<!-- Mission and Vision Section -->
<div class="info-section">
  <h2>MISSION</h2>
  <p>To enhance the recruitment process using AI-driven applicant screening that ensures fairness and efficient hiring, while redirecting applicants to better opportunities that match their skills and potential.</p>
  <h2>VISION</h2>
  <p>To become the leading recruitment partner that empowers organizations to hire smarter and faster, while helping every applicant find their best-fit career path with fairness and transparency.</p>
</div>

<!-- Chatbot Window -->
<div class="chatbot" id="chatbot">
  <div class="chat-header">Applyze Recruiting Bot</div>
  <div class="chat-messages" id="messages">
    <div class="message bot">👋 Hi! What is your name?</div>
  </div>
  <div class="chat-input">
    <input type="text" id="userInput" placeholder="Type your answer..." />
    <button onclick="sendMessage()">Send</button>
  </div>
</div>

<!-- Floating Toggle Button -->
<button class="chat-toggle" id="chatToggle">💬</button>

<script>
  // Toggle Chatbot Visibility
  const chatbot = document.getElementById('chatbot');
  const chatToggle = document.getElementById('chatToggle');
  chatToggle.addEventListener('click', () => {
    chatbot.classList.toggle('active');
  });

  let step = 0;
  let userData = { name: '', phone: '', email: '', address: '', education: '', experience: '' };
  const input = document.getElementById('userInput');

  input.addEventListener('keypress', function(e) {
    if (e.key === 'Enter') sendMessage();
  });

  function sendMessage() {
    const messages = document.getElementById('messages');
    const userText = input.value.trim();
    if (!userText) return;

    const userMessage = document.createElement('div');
    userMessage.classList.add('message', 'user');
    userMessage.textContent = userText;
    messages.appendChild(userMessage);
    input.value = '';
    messages.scrollTop = messages.scrollHeight;

    if (step === 0) {
      userData.name = userText;
      botReply(`Hi ${userData.name}! May I have your phone number?`);
      step = 1;
    } 
    else if (step === 1) {
      userData.phone = userText;
      botReply("Got it! What’s your email address?");
      step = 2;
    } 
    else if (step === 2) {
      userData.email = userText;
      botReply("Thank you! May I have your current address?");
      step = 3;
    } 
    else if (step === 3) {
      userData.address = userText;
      botReply("Are you interested to apply?");
      showOptions(["Yes", "No"]);
      step = 4;
    } 
    else if (step === 4) {
      if (userText.toLowerCase() === 'yes') {
        botReply("Great! What is your highest level of educational attainment?");
        step = 5;
      } else {
        botReply("Thank you for participating; we appreciate your interest in Applyze.");
        step = 99;
      }
    } 
    else if (step === 5) {
      userData.education = userText;
      botReply("How many years of experience do you have specifically as a Customer Service Representative?");
      step = 6;
    } 
    else if (step === 6) {
      userData.experience = userText;

      const summaryMessage = document.createElement('div');
      summaryMessage.classList.add('message', 'bot');

      const summaryBox = document.createElement('div');
      summaryBox.classList.add('summary-box');
      summaryBox.innerHTML = `
        📋 <strong>Application Summary</strong><br><br>
        👤 <strong>Name:</strong> ${userData.name}<br>
        📞 <strong>Phone:</strong> ${userData.phone}<br>
        📧 <strong>Email:</strong> ${userData.email}<br>
        🏠 <strong>Address:</strong> ${userData.address}<br>
        🎓 <strong>Educational Attainment:</strong> ${userData.education}<br>
        💼 <strong>Experience:</strong> ${userData.experience} years
      `;
      summaryMessage.appendChild(summaryBox);
      messages.appendChild(summaryMessage);

      setTimeout(() => {
        botReply("Thank you for sharing your experience! Our team will review your application and contact you soon. Have a great day!");
      }, 1000);

      step = 99;
    }

    messages.scrollTop = messages.scrollHeight;
  }

  function botReply(text) {
    const messages = document.getElementById('messages');
    const botMessage = document.createElement('div');
    botMessage.classList.add('message', 'bot');
    botMessage.textContent = text;
    messages.appendChild(botMessage);
    messages.scrollTop = messages.scrollHeight;
  }

  function showOptions(options) {
    const messages = document.getElementById('messages');
    const container = document.createElement('div');
    container.classList.add('options');
    options.forEach(opt => {
      const btn = document.createElement('button');
      btn.textContent = opt;
      btn.onclick = () => {
        document.getElementById('userInput').value = opt;
        sendMessage();
        container.remove();
      };
      container.appendChild(btn);
    });
    messages.appendChild(container);
    messages.scrollTop = messages.scrollHeight;
  }
</script>
</body>
</html>
