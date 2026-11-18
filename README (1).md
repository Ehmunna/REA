# 🔍 Android এ Reverse Engineering Setup

**Termux + Ubuntu (proot-distro) এ Ghidra, Radare2, OpenCode ও REA ইনস্টল করার সম্পূর্ণ গাইড**

![Platform](https://img.shields.io/badge/Platform-Android-3DDC84?logo=android&logoColor=white)
![Termux](https://img.shields.io/badge/Termux-proot--distro-black)
![Ubuntu](https://img.shields.io/badge/Ubuntu-24.04+-E95420?logo=ubuntu&logoColor=white)
![Ghidra](https://img.shields.io/badge/Ghidra-12.2-red)
![JDK](https://img.shields.io/badge/JDK-25-orange)
![Node](https://img.shields.io/badge/Node.js-24-339933?logo=node.js&logoColor=white)

---

## 📦 এই গাইডে কী কী ইনস্টল হবে

| টুল | কাজ |
|------|------|
| **proot-distro + Ubuntu** | Termux এর ভেতরে পূর্ণ Linux পরিবেশ |
| **OpenJDK 25** | Ghidra চালানোর জন্য |
| **Ghidra 12.2** | NSA এর reverse engineering framework |
| **Radare2** | কমান্ড লাইন ভিত্তিক binary analysis টুল |
| **Node.js 24** | OpenCode ও REA চালানোর জন্য |
| **OpenCode** | টার্মিনাল ভিত্তিক AI coding agent |
| **REA (rea-agents)** | AI agent দিয়ে reverse engineering এর MCP server ও CLI |

---

## ✅ প্রয়োজনীয়তা

- Android ফোন (ARM64 হলে ভালো)
- [Termux](https://github.com/termux/termux-app) ইনস্টল করা
- কমপক্ষে ৪ GB ফাঁকা স্টোরেজ
- ভালো ইন্টারনেট সংযোগ
- Ubuntu **24.04 বা তার বেশি** (REA অফিসিয়ালি এটাই সাপোর্ট করে)

---

## 🚀 ইনস্টলেশন

### ধাপ ১: Termux এ Ubuntu সেটআপ

```bash
pkg update -y && pkg upgrade -y
pkg install proot-distro -y
proot-distro install ubuntu
proot-distro login ubuntu
```

> 💡 **টিপস:** বারবার লগইন কমান্ড না লিখতে Termux এ alias বানিয়ে নিন:
> ```bash
> echo "alias u='proot-distro login ubuntu'" >> ~/.bashrc
> source ~/.bashrc
> ```
> এরপর শুধু `u` লিখলেই Ubuntu তে ঢুকবেন।

---

### ধাপ ২: প্যাকেজ ইনস্টল (Ubuntu এর ভেতরে)

এখান থেকে সব কমান্ড **Ubuntu এর ভেতরে** চালাতে হবে।

```bash
cat /etc/os-release
apt update -y && apt upgrade -y
apt install -y wget unzip curl radare2 openjdk-25-jdk
java -version
```

`java -version` এ **25** দেখালে ঠিক আছে।

> ⚠️ `Unable to locate package openjdk-25-jdk` এরর এলে আপনার Ubuntu ভার্সনে এই প্যাকেজ নেই। `cat /etc/os-release` দিয়ে ভার্সন দেখে নিন।

---

### ধাপ ৩: JAVA_HOME সেট করা

```bash
echo 'export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))' >> ~/.bashrc
source ~/.bashrc
echo $JAVA_HOME
```

এতে ফোনের চিপ (arm64 / amd64) অনুযায়ী সঠিক path নিজে থেকেই বসে যায়।

---

### ধাপ ৪: Ghidra ইনস্টল

```bash
cd /opt
wget https://github.com/NationalSecurityAgency/ghidra/releases/download/Ghidra_12.2_build/ghidra_12.2_PUBLIC_20261016.zip
unzip ghidra_12.2_PUBLIC_20261016.zip
rm ghidra_12.2_PUBLIC_20261016.zip
mv ghidra_12.2_PUBLIC ghidra
ln -sf /opt/ghidra/ghidraRun /usr/local/bin/ghidra
```

> ⚠️ `wget` ফেল করলে [Ghidra Releases](https://github.com/NationalSecurityAgency/ghidra/releases) পেজ থেকে সঠিক লিংক কপি করে নিন।

---

### ধাপ ৫: Node.js (নতুন ভার্সন)

REA এর জন্য Node.js `^22.19.0`, `^24.11.0` বা `>=26.0.0` লাগে। Ubuntu এর ডিফল্ট `apt` ভার্সন সাধারণত পুরনো, তাই NodeSource ব্যবহার করুন।

```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | bash -
apt install -y nodejs
node -v
```

`node -v` এ `v24...` দেখাতে হবে।

---

### ধাপ ৬: OpenCode ও REA

```bash
npm install -g opencode-ai
npx rea-agents@latest setup --dry-run
npx rea-agents@latest setup
npx rea-agents@latest doctor
opencode
```

- `--dry-run` দিলে REA কী কী পরিবর্তন করবে তা না করেই দেখায়।
- setup এর সময় client হিসেবে **OpenCode** সিলেক্ট করুন।
- setup শেষে OpenCode বন্ধ করে আবার চালান, নইলে REA এর tools লোড হবে না।
- REA নিজে Ghidra ইনস্টল করে না, আপনার ইনস্টল করা Ghidra ব্যবহার করে।

---

## ▶️ ব্যবহার

**Ghidra GUI:**

```bash
ghidra
```

**Ghidra কমান্ড লাইনে (GUI ছাড়া):**

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

> 🖥️ Ghidra এর GUI চালাতে ডিসপ্লে সার্ভার লাগে। Termux-X11 অথবা VNC সেটআপ করতে হবে। শুধু কমান্ড লাইনে কাজ করলে `analyzeHeadless` ব্যবহার করুন।

---

## 🛠️ সমস্যা সমাধান

| সমস্যা | সমাধান |
|---------|---------|
| Ghidra চালালে JDK path চায় | ধাপ ৩ আবার করুন, অথবা `ls /usr/lib/jvm` দেখে `JAVA_HOME` হাতে সেট করুন |
| `java -version` এ ২৫ দেখায় না | `update-alternatives --config java` চালিয়ে ২৫ সিলেক্ট করুন |
| `wget` ফেল করছে | Ghidra Releases পেজ থেকে সঠিক ফাইলের নাম ও লিংক মিলিয়ে নিন |
| REA এর tools দেখা যাচ্ছে না | OpenCode বন্ধ করে আবার চালান |
| REA ঠিকমতো কাজ করছে কিনা সন্দেহ | `npx rea-agents@latest doctor` চালান |
| `node -v` এ পুরনো ভার্সন | ধাপ ৫ আবার করুন |

---

## ⚠️ গুরুত্বপূর্ণ নোট

- এই গাইড **শুধুমাত্র শিক্ষামূলক ও বৈধ কাজের জন্য**। শুধু নিজের তৈরি বা অনুমতি পাওয়া সফটওয়্যার analyze করুন।
- Termux এ proot ব্যবহার হয়, তাই সব টুল ডেস্কটপ Linux এর মতো পুরোপুরি নাও চলতে পারে।
- সময়ের সাথে Ghidra, JDK ও Node এর ভার্সন বদলাতে পারে। ইনস্টলের আগে অফিসিয়াল ডকুমেন্টেশন একবার দেখে নিন।

---

## 🔗 দরকারি লিংক

- [Ghidra](https://github.com/NationalSecurityAgency/ghidra)
- [Radare2](https://github.com/radareorg/radare2)
- [Termux](https://github.com/termux/termux-app)
- [proot-distro](https://github.com/termux/proot-distro)
- [OpenCode](https://opencode.ai)
- [REA (rea-agents)](https://www.npmjs.com/package/rea-agents)

---

## 🤝 কন্ট্রিবিউশন

কোনো ভুল পেলে বা উন্নতির আইডিয়া থাকলে **Issue** খুলুন অথবা **Pull Request** পাঠান।

⭐ গাইডটা কাজে লাগলে একটা **Star** দিতে ভুলবেন না!
