<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Decentralized Resume Verification</title>
  <script src="https://cdn.jsdelivr.net/npm/ethers/dist/ethers.min.js"></script>
  <style>
    body {
      font-family: "Poppins", sans-serif;
      background: #e5f0ff;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
    }
    .container {
      background: white;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      padding: 30px;
      width: 380px;
      text-align: center;
    }
    h2 {
      color: #1e3a8a;
      margin-bottom: 20px;
    }
    input[type="file"] {
      margin: 10px 0;
    }
    button {
      width: 100%;
      padding: 10px;
      margin-top: 10px;
      border: none;
      border-radius: 6px;
      color: white;
      font-weight: 600;
      cursor: pointer;
    }
    #connectWalletBtn {
      background-color: #2563eb;
    }
    #verifyBtn {
      background-color: #16a34a;
    }
    .status {
      margin-top: 15px;
      padding: 10px;
      border-radius: 6px;
      font-weight: 500;
    }
    .connected {
      background-color: #d1fae5;
      color: #065f46;
    }
    .not-verified {
      background-color: #fee2e2;
      color: #991b1b;
    }
    .verified {
      background-color: #dcfce7;
      color: #166534;
    }
    .error {
      background-color: #fee2e2;
      color: #991b1b;
    }
    .small-text {
      color: #6b7280;
      font-size: 13px;
      margin-top: 5px;
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Decentralized Resume Verification</h2>

    <button id="connectWalletBtn">Connect Wallet</button>
    <div id="walletStatus" class="status small-text">Not connected</div>

    <form id="uploadForm">
      <label for="resumeFile">Upload Resume PDF</label><br>
      <input type="file" id="resumeFile" accept=".pdf" />
    </form>

    <div id="verificationStatus" class="status not-verified">
      ❌ Not Verified
    </div>

    <button id="verifyBtn">✅ Verify Resume (Admin Only)</button>
  </div>

  <script>
    const CONTRACT_ADDRESS = "YOUR_DEPLOYED_CONTRACT_ADDRESS"; // <-- replace this
    const ABI = [
      "function admin() view returns (address)",
      "function resumeCount() view returns (uint256)",
      "function verifyResume(uint256 _resumeId) public",
      "function getResume(uint256) view returns (string,string,string,string[],string,bool,address)"
    ];

    let provider, signer, contract, userAddress, adminAddress;

    async function connectWallet() {
      if (!window.ethereum) {
        alert("MetaMask not detected!");
        return;
      }
      provider = new ethers.providers.Web3Provider(window.ethereum);
      await provider.send("eth_requestAccounts", []);
      signer = provider.getSigner();
      userAddress = await signer.getAddress();
      contract = new ethers.Contract(CONTRACT_ADDRESS, ABI, signer);
      adminAddress = await contract.admin();

      document.getElementById("walletStatus").innerText = "✅ Wallet Connected: " + userAddress.slice(0, 6) + "..." + userAddress.slice(-4);
      document.getElementById("walletStatus").className = "status connected";

      if (userAddress.toLowerCase() === adminAddress.toLowerCase()) {
        document.getElementById("verifyBtn").style.display = "block";
      } else {
        document.getElementById("verifyBtn").style.display = "none";
      }
    }

    async function verifyResume() {
      try {
        const resumeId = prompt("Enter Resume ID to verify:");
        if (!resumeId) return;

        const tx = await contract.verifyResume(resumeId);
        await tx.wait();

        document.getElementById("verificationStatus").innerText = "✅ Verified Successfully";
        document.getElementById("verificationStatus").className = "status verified";
      } catch (err) {
        console.error(err);
        document.getElementById("verificationStatus").innerText = "❌ Error verifying resume";
        document.getElementById("verificationStatus").className = "status error";
      }
    }

    document.getElementById("connectWalletBtn").addEventListener("click", connectWallet);
    document.getElementById("verifyBtn").addEventListener("click", verifyResume);
  </script>
</body>
</html>
