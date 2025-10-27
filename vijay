<!DOCTYPE html>
<html>
<head>
    <title>Decentralized Resume Verification</title>
    <script src="https://cdn.jsdelivr.net/npm/ethers@5.7.2/dist/ethers.min.js"></script>
</head>
<body>
    <h1>Decentralized Resume Verification</h1>
    
    <h2>Submit Resume</h2>
    <form id="resumeForm">
        <label>Name: <input type="text" id="name" required /></label><br>
        <label>Email: <input type="email" id="email" required /></label><br>
        <label>Qualification: <input type="text" id="qualification" required /></label><br>
        <label>Skills (comma separated): <input type="text" id="skills" required /></label><br>
        <label>Experience: <input type="text" id="experience" required /></label><br>
        <button type="submit">Submit Resume</button>
    </form>
    <hr>
    <h2>Verify Resume (Admin Only)</h2>
    <form id="verifyForm">
        <label>Resume ID: <input type="number" id="resumeId" required /></label>
        <button type="submit">Verify Resume</button>
    </form>
    <hr>
    <h2>Get Resume By ID</h2>
    <form id="getForm">
        <label>Resume ID: <input type="number" id="getId" required /></label>
        <button type="submit">Get Resume</button>
    </form>
    <pre id="result"></pre>

    <script>
        // Replace with your deployed contract address and ABI
        const contractAddress = 'YOUR_CONTRACT_ADDRESS';
        const contractABI = [
            // ABI snippets for used functions only
            "function submitResume(string,string,string,string[],string) public",
            "function verifyResume(uint) public",
            "function getResume(uint) public view returns (string,string,string,string[],string,bool,address)"
        ];

        let provider, signer, contract;

        async function init() {
            provider = new ethers.providers.Web3Provider(window.ethereum);
            await provider.send("eth_requestAccounts", []);
            signer = provider.getSigner();
            contract = new ethers.Contract(contractAddress, contractABI, signer);
        }

        document.getElementById('resumeForm').onsubmit = async function(e) {
            e.preventDefault();
            await init();
            const name = document.getElementById('name').value;
            const email = document.getElementById('email').value;
            const qualification = document.getElementById('qualification').value;
            const skills = document.getElementById('skills').value.split(",");
            const experience = document.getElementById('experience').value;
            try {
                const tx = await contract.submitResume(name, email, qualification, skills, experience);
                await tx.wait();
                document.getElementById('result').innerText = "Resume submitted successfully!";
            } catch (err) {
                document.getElementById('result').innerText = err.message;
            }
        };

        document.getElementById('verifyForm').onsubmit = async function(e) {
            e.preventDefault();
            await init();
            const resumeId = document.getElementById('resumeId').value;
            try {
                const tx = await contract.verifyResume(resumeId);
                await tx.wait();
                document.getElementById('result').innerText = "Resume verified!";
            } catch (err) {
                document.getElementById('result').innerText = err.message;
            }
        };

        document.getElementById('getForm').onsubmit = async function(e) {
            e.preventDefault();
            await init();
            const getId = document.getElementById('getId').value;
            try {
                const resume = await contract.getResume(getId);
                document.getElementById('result').innerText =
                    "Name: " + resume[0] + "\nEmail: " + resume[1] +
                    "\nQualification: " + resume[2] + "\nSkills: " + resume[3].join(", ") +
                    "\nExperience: " + resume[4] + "\nVerified: " + resume[5] +
                    "\nSubmitted By: " + resume[6];
            } catch (err) {
                document.getElementById('result').innerText = err.message;
            }
        };
    </script>
</body>
</html>
