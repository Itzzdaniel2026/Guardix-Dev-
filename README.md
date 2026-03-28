



<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Community Dashboard</title>
  <style>
    body {
      margin: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #0b0c10;
      color: #f5f5f5;
    }
    .center {
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
    }
    .card {
      background: #1f2833;
      padding: 24px 28px;
      border-radius: 10px;
      box-shadow: 0 0 25px rgba(0,0,0,0.5);
      width: 100%;
      max-width: 520px;
    }
    h1, h2 {
      margin-top: 0;
      color: #66fcf1;
    }
    label {
      display: block;
      margin: 8px 0 4px;
      font-size: 14px;
    }
    input, textarea {
      width: 100%;
      padding: 8px 10px;
      border-radius: 6px;
      border: 1px solid #45a29e;
      background: #0b0c10;
      color: #f5f5f5;
      box-sizing: border-box;
      font-size: 14px;
    }
    textarea {
      resize: vertical;
      min-height: 70px;
    }
    button {
      margin-top: 12px;
      padding: 8px 14px;
      border-radius: 6px;
      border: none;
      background: #45a29e;
      color: #0b0c10;
      font-weight: 600;
      cursor: pointer;
    }
    button:disabled {
      opacity: 0.6;
      cursor: default;
    }
    .small {
      font-size: 12px;
      opacity: 0.8;
    }
    .command-bar {
      display: flex;
      gap: 8px;
      margin-bottom: 16px;
      align-items: center;
    }
    .command-bar input {
      flex: 1;
    }
    .tabs {
      display: flex;
      gap: 8px;
      margin-bottom: 12px;
    }
    .tab-btn {
      flex: 1;
      padding: 6px 8px;
      border-radius: 6px;
      border: 1px solid #45a29e;
      background: #0b0c10;
      color: #f5f5f5;
      font-size: 13px;
      cursor: pointer;
    }
    .tab-btn.active {
      background: #45a29e;
      color: #0b0c10;
    }
    .section {
      display: none;
      animation: slideIn 0.25s ease-out;
    }
    .section.active {
      display: block;
    }
    @keyframes slideIn {
      from { transform: translateX(20px); opacity: 0; }
      to   { transform: translateX(0); opacity: 1; }
    }
    .status {
      margin-top: 8px;
      font-size: 13px;
    }
    .status.ok {
      color: #66fcf1;
    }
    .status.err {
      color: #ff6b6b;
    }
  </style>
</head>
<body>

<div class="center" id="login-screen">
  <div class="card">
    <h1>Dashboard Login</h1>
    <p class="small">Enter the dashboard password to continue.</p>
    <label for="password">Password</label>
    <input type="password" id="password" placeholder="Enter password">
    <button id="login-btn">Unlock</button>
    <div id="login-status" class="status"></div>
  </div>
</div>

<div class="center" id="dashboard-screen" style="display:none;">
  <div class="card">
    <h1>Community Dashboard</h1>
    <p class="small">
      Use the command bar (<code>.part</code>, <code>.black</code>, <code>.sbl</code>) or click the tabs.
    </p>

    <div class="command-bar">
      <input id="command-input" placeholder="Type .part, .black, or .sbl and press Enter">
      <button id="command-go">Go</button>
    </div>

    <div class="tabs">
      <button class="tab-btn active" data-target="partnership-section">.part – Partnership</button>
      <button class="tab-btn" data-target="blacklist-user-section">.black – User Blacklist</button>
      <button class="tab-btn" data-target="blacklist-server-section">.sbl – Server Blacklist</button>
    </div>

    <!-- Partnership (.part) -->
    <div class="section active" id="partnership-section">
      <h2>Partnership Request</h2>
      <form id="partnership-form">
        <label>Server name</label>
        <input type="text" id="part-server-name" required>

        <label>Server owner</label>
        <input type="text" id="part-server-owner" required>

        <label>Server logo URL</label>
        <input type="url" id="part-server-logo" placeholder="https://...">

        <button type="submit">Send Partnership Notice</button>
        <div id="part-status" class="status"></div>
      </form>
    </div>

    <!-- User blacklist (.black) -->
    <div class="section" id="blacklist-user-section">
      <h2>User Blacklist</h2>
      <form id="blacklist-user-form">
        <label>Discord user</label>
        <input type="text" id="black-user" required>

        <label>Discord ID</label>
        <input type="text" id="black-id" required>

        <label>Reason</label>
        <textarea id="black-reason" required></textarea>

        <button type="submit">Send User Blacklist Notice</button>
        <div id="black-status" class="status"></div>
      </form>
    </div>

    <!-- Server blacklist (.sbl) -->
    <div class="section" id="blacklist-server-section">
      <h2>Server Blacklist</h2>
      <form id="blacklist-server-form">
        <label>Server name</label>
        <input type="text" id="sbl-server-name" required>

        <label>Server ID</label>
        <input type="text" id="sbl-server-id" required>

        <label>Reason</label>
        <textarea id="sbl-reason" required></textarea>

        <button type="submit">Send Server Blacklist Notice</button>
        <div id="sbl-status" class="status"></div>
      </form>
    </div>

  </div>
</div>

<script>
  // --- CONFIG ---
  const DASHBOARD_PASSWORD = "!dev/ "; // note the space at the end, as you specified

  const WEBHOOK_PARTNERSHIP = "https://discord.com/api/webhooks/1487256678507479112/fxsKoLZKQKndL9t5NLoNLQarkZvA6iaGhxBsI7_SBw8tdQAOq5Piw79xMUsExay9uqLK";
  const WEBHOOK_USER_BLACKLIST = "https://discord.com/api/webhooks/1487256600312942792/-F7e1ugBmFZWTJ6k6CFpX-Xx0lDHJbpS4qxk1B6RpXVyBLQFFRHfE2lcCT-KA5koD3LJ";
  const WEBHOOK_SERVER_BLACKLIST = "https://discord.com/api/webhooks/1487256600312942792/-F7e1ugBmFZWTJ6k6CFpX-Xx0lDHJbpS4qxk1B6RpXVyBLQFFRHfE2lcCT-KA5koD3LJ";

  // --- LOGIN ---
  const loginScreen = document.getElementById("login-screen");
  const dashboardScreen = document.getElementById("dashboard-screen");
  const loginBtn = document.getElementById("login-btn");
  const passwordInput = document.getElementById("password");
  const loginStatus = document.getElementById("login-status");

  function tryLogin() {
    const value = passwordInput.value;
    if (value === DASHBOARD_PASSWORD) {
      loginStatus.textContent = "";
      loginScreen.style.display = "none";
      dashboardScreen.style.display = "flex";
    } else {
      loginStatus.textContent = "Incorrect password.";
      loginStatus.className = "status err";
    }
  }

  loginBtn.addEventListener("click", tryLogin);
  passwordInput.addEventListener("keydown", e => {
    if (e.key === "Enter") tryLogin();
  });

  // --- TABS / COMMANDS ---
  const tabButtons = document.querySelectorAll(".tab-btn");
  const sections = document.querySelectorAll(".section");
  const commandInput = document.getElementById("command-input");
  const commandGo = document.getElementById("command-go");

  function showSection(id) {
    sections.forEach(sec => {
      sec.classList.toggle("active", sec.id === id);
    });
    tabButtons.forEach(btn => {
      btn.classList.toggle("active", btn.dataset.target === id);
    });
  }

  tabButtons.forEach(btn => {
    btn.addEventListener("click", () => showSection(btn.dataset.target));
  });

  function handleCommand(cmdRaw) {
    const cmd = cmdRaw.trim().toLowerCase();
    if (cmd === ".part") {
      showSection("partnership-section");
    } else if (cmd === ".black") {
      showSection("blacklist-user-section");
    } else if (cmd === ".sbl") {
      showSection("blacklist-server-section");
    }
  }

  commandGo.addEventListener("click", () => handleCommand(commandInput.value));
  commandInput.addEventListener("keydown", e => {
    if (e.key === "Enter") {
      e.preventDefault();
      handleCommand(commandInput.value);
    }
  });

  // --- WEBHOOK HELPER ---
  async function sendWebhook(url, payload, statusEl, buttonEl) {
    statusEl.textContent = "";
    statusEl.className = "status";
    buttonEl.disabled = true;

    try {
      const res = await fetch(url, {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(payload)
      });

      if (!res.ok) {
        throw new Error("HTTP " + res.status);
      }

      statusEl.textContent = "Sent successfully.";
      statusEl.className = "status ok";
    } catch (err) {
      statusEl.textContent = "Failed to send: " + err.message;
      statusEl.className = "status err";
    } finally {
      buttonEl.disabled = false;
    }
  }

  // --- PARTNERSHIP FORM (.part) ---
  const partForm = document.getElementById("partnership-form");
  const partStatus = document.getElementById("part-status");

  partForm.addEventListener("submit", e => {
    e.preventDefault();
    const serverName = document.getElementById("part-server-name").value.trim();
    const serverOwner = document.getElementById("part-server-owner").value.trim();
    const serverLogo = document.getElementById("part-server-logo").value.trim();
    const btn = partForm.querySelector("button[type='submit']");

    const content = "@everyone New partnership request received.";
    const embeds = [
      {
        title: "New Partnership Request",
        color: 0x45a29e,
        fields: [
          { name: "Server name", value: serverName || "N/A", inline: false },
          { name: "Server owner", value: serverOwner || "N/A", inline: false }
        ],
        footer: {
          text: "Partnership system"
        }
      }
    ];

    if (serverLogo) {
      embeds[0].thumbnail = { url: serverLogo };
    }

    const payload = { content, embeds };
    sendWebhook(WEBHOOK_PARTNERSHIP, payload, partStatus, btn);
  });

  // --- USER BLACKLIST FORM (.black) ---
  const blackForm = document.getElementById("blacklist-user-form");
  const blackStatus = document.getElementById("black-status");

  blackForm.addEventListener("submit", e => {
    e.preventDefault();
    const user = document.getElementById("black-user").value.trim();
    const id = document.getElementById("black-id").value.trim();
    const reason = document.getElementById("black-reason").value.trim();
    const btn = blackForm.querySelector("button[type='submit']");

    const content = "@everyone Please do not affiliate with the following user.";
    const embeds = [
      {
        title: "User Blacklist Notice",
        color: 0xff0000,
        fields: [
          { name: "Discord user", value: user || "N/A", inline: false },
          { name: "Discord ID", value: id || "N/A", inline: false },
          { name: "Reason", value: reason || "N/A", inline: false }
        ],
        footer: {
          text: "Blacklist system"
        }
      }
    ];

    const payload = { content, embeds };
    sendWebhook(WEBHOOK_USER_BLACKLIST, payload, blackStatus, btn);
  });

  // --- SERVER BLACKLIST FORM (.sbl) ---
  const sblForm = document.getElementById("blacklist-server-form");
  const sblStatus = document.getElementById("sbl-status");

  sblForm.addEventListener("submit", e => {
    e.preventDefault();
    const serverName = document.getElementById("sbl-server-name").value.trim();
    const serverId = document.getElementById("sbl-server-id").value.trim();
    const reason = document.getElementById("sbl-reason").value.trim();
    const btn = sblForm.querySelector("button[type='submit']");

    const content = "@everyone The following server has been blacklisted. Please leave this server; failure to do so may result in removal from this community.";
    const embeds = [
      {
        title: "Server Blacklist Notice",
        color: 0xff0000,
        fields: [
          { name: "Server name", value: serverName || "N/A", inline: false },
          { name: "Server ID", value: serverId || "N/A", inline: false },
          { name: "Reason", value: reason || "N/A", inline: false }
        ],
        footer: {
          text: "Server blacklist system"
        }
      }
    ];

    const payload = { content, embeds };
    sendWebhook(WEBHOOK_SERVER_BLACKLIST, payload, sblStatus, btn);
  });
</script>

</body>
</html>
