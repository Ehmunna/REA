#  Reverse Engineering Setup on Android

A complete guide to installing Ghidra, Radare2, OpenCode, and REA on Termux + Ubuntu

![Platform](https://img.shields.io/badge/Platform-Android-3DDC84?logo=android&logoColor=white)
![Termux](https://img.shields.io/badge/Termux-proot--distro-black)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04+-E95420?logo=ubuntu&logoColor=white)
![Ghidra](https://img.shields.io/badge/Ghidra-12.2-red)
![JDK](https://img.shields.io/badge/JDK-25-orange)
![Node](https://img.shields.io/badge/Node.js-24-339933?logo=node.js&logoColor=white)


##  What This Guide Installs

| Tool | Purpose |
|------|---------|
| **proot-distro + Ubuntu** | A full Linux environment inside Termux |
| **OpenJDK 25** | Required to run Ghidra |
| **Ghidra 12.2** | The NSA's reverse engineering framework |
| **Radare2** | Command-line binary analysis tool |
| **Node.js 24** | Required to run OpenCode and REA |
| **OpenCode** | Terminal-based AI coding agent |
| **REA (rea-agents)** | MCP server and CLI for AI-agent-driven reverse engineering |

---

##  Requirements

- An Android phone (ARM64 recommended)
- [Termux](https://github.com/termux/termux-app) installed
- At least 4 GB of free storage
- A stable internet connection
- Ubuntu **24.04 or newer** (this is what REA officially supports)

---

##  Installation

### Step 1: Set Up Ubuntu in Termux
```
pkg update -y && pkg upgrade -y
```
```
pkg install proot-distro -y
```
```
proot-distro install ubuntu
```
```
proot-distro login ubuntu
```

> 💡 **Tip:** To avoid typing the login command every time, create an alias in Termux:
> ```bash
> echo "alias u='proot-distro login ubuntu'" >> ~/.bashrc
> source ~/.bashrc
> ```
> After that, just type `u` to enter Ubuntu.

---

### Step 2: Install Packages (Inside Ubuntu)

From here on, run all commands **inside Ubuntu**.

```bash
cat /etc/os-release
apt update -y && apt upgrade -y
apt install -y wget unzip curl radare2 openjdk-25-jdk
java -version
```

If `java -version` shows **25**, you're good.

> ⚠️ If you get the error `Unable to locate package openjdk-25-jdk`, your Ubuntu version doesn't include this package. Check your version with `cat /etc/os-release`.

---

### Step 3: Set JAVA_HOME

```bash
echo 'export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))' >> ~/.bashrc
source ~/.bashrc
echo $JAVA_HOME
```

This automatically picks the correct path for your phone's chip (arm64 / amd64).

---

### Step 4: Install Ghidra

```
cd /opt
wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_12.2_build/ghidra_12.2_PUBLIC_20261016.zip
```
```
unzip ghidra_12.2_PUBLIC_20261016.zip
```
```
rm ghidra_12.2_PUBLIC_20261016.zip
```
```
mv ghidra_12.2_PUBLIC ghidra
```
```
ln -sf /opt/ghidra/ghidraRun /usr/local/bin/ghidra
```

> ⚠️ If `wget` fails, copy the correct link from the [Ghidra Releases](https://github.com/NationalSecurityAgency/ghidra/releases) page.

---

### Step 5: Install Node.js (Latest Version)

REA requires Node.js `^22.19.0`, `^24.11.0`, or `>=26.0.0`. The default `apt` version on Ubuntu is usually outdated, so use NodeSource.

```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | bash -
apt install -y nodejs
node -v
```

`node -v` should show `v24...`.

---

### Step 6: Install OpenCode and REA

```
npm install -g opencode-ai
```
```
npx rea-agents@latest setup --dry-run
npx rea-agents@latest setup
npx rea-agents@latest doctor
```
```
opencode
```


## check install Rea and gidra 

```
check rea and gidra install 
```
```
fix it
```

Done 👍 

- `--dry-run` shows what REA would change without actually changing anything.
- During setup, select **OpenCode** as the client.
- After setup finishes, close OpenCode and start it again, otherwise REA's tools won't load.
- REA does not install Ghidra itself; it uses the Ghidra you installed.

---

## ▶️ Usage

**Ghidra GUI:**

```bash
ghidra
```

**Ghidra from the command line (no GUI):**

```bash
/opt/ghidra/support/analyzeHeadless
```

**Radare2:**

```bash
r2 <binary-file>
```

**OpenCode:**

```bash
opencode
```

> 🖥️ Running the Ghidra GUI requires a display server. You'll need to set up Termux-X11 or VNC. If you only work from the command line, use `analyzeHeadless`.

---

## 🛠️ Troubleshooting

| Problem | Solution |
|---------|----------|
| Ghidra asks for the JDK path | Redo Step 3, or run `ls /usr/lib/jvm` and set `JAVA_HOME` manually |
| `java -version` doesn't show 25 | Run `update-alternatives --config java` and select 25 |
| `wget` fails | Verify the exact file name and link on the Ghidra Releases page |
| REA tools don't show up | Close OpenCode and start it again |
| Unsure whether REA is working | Run `npx rea-agents@latest doctor` |
| `node -v` shows an old version | Redo Step 5 |

---

## ⚠️ Important Notes

- This guide is **for educational and lawful use only**. Only analyze software you created or have permission to analyze.
- Termux uses proot, so not every tool may run exactly as it would on a desktop Linux system.
- Versions of Ghidra, JDK, and Node may change over time. Check the official documentation before installing.

---

## 🔗 Useful Links

- [Ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [Radare2](https://github.com/radareorg/radare2)
- [Termux](https://github.com/termux/termux-app)
- [proot-distro](https://github.com/termux/proot-distro)
- [OpenCode](https://opencode.ai)
- [REA (rea-agents)](https://www.npmjs.com/package/rea-agents)

---

## 🤝 Contributing

If you find a mistake or have an idea for improvement, open an **Issue** or send a **Pull Request**.

⭐ If this guide helped you, don't forget to give it a **Star**!
