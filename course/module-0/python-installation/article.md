---
meta:
    title: "Installing Python"
    description: "How to install Python 3 on Windows, macOS or Linux: a step-by-step guide, the Add to PATH checkbox, and checking that it all works."
---

# Installing Python

You don't have to install Python for this course: the tasks run on our servers, and any code can be executed in the **<a href="https://python-academy.org/en/sandbox" target="_blank">Sandbox</a>**. So if you're itching to start — skip ahead, this chapter isn't going anywhere.

But sooner or later you'll want to write code on your own machine: your files, your projects, no browser tab involved. Come back here then — below is the short path from "nothing installed" to "it works" 🚀.

## Step 1. Check whether Python is already there

Python may have shipped with your system or been installed earlier — on macOS and Linux it's there almost always. We'll check in the terminal: the window where you type commands as text instead of clicking.

- **Windows** — press `Win`, type "PowerShell" and open it.
- **macOS** — press `Cmd + Space`, type "Terminal" and open it.
- **Linux** — usually `Ctrl + Alt + T`.

Type the command and press `Enter`:

**macOS / Linux**

```sh
python3 --version
```

**Windows**

```powershell
python --version
```

If a version number comes back — say, `Python 3.12.4` — it's already in place and you can skip step 2. Any version starting with a 3 will do.

A "command not found" answer is fine too: it just means Python isn't installed yet. Let's move on.

## Step 2. Install Python

### Windows

1. Open **<a href="https://www.python.org/downloads/windows/" target="_blank">python.org → Downloads → Windows</a>** and download the installer.
2. Run it.
3. On the very first screen tick **"Add python.exe to PATH"** — and only then click **Install Now**.
4. Wait for it to finish and close the installer.

That checkbox deserves a word of its own, because it's easy to miss and the consequences are annoying. PATH is the list of folders your system searches for programs when you type a command. Without the tick, Python installs but the system never learns about it: you type `python` and get "command not found" back. Ticking it takes a second; hunting down the reason afterwards can take half an hour.

If you did miss it, the easiest fix is to run the installer again and choose **Modify**.

### macOS

The first way, with no extra tooling: download the installer from **<a href="https://www.python.org/downloads/macos/" target="_blank">python.org → Downloads → macOS</a>**, open the `.pkg` and follow the steps.

The second way is **<a href="https://brew.sh/" target="_blank">Homebrew</a>**, if you already have it (check with `brew -v`):

```bash
brew install python
```

### Linux

The command depends on your distribution:

- **Ubuntu / Debian:**

    ```bash
    sudo apt update
    sudo apt install -y python3 python3-pip
    ```

- **Fedora:**

    ```bash
    sudo dnf install -y python3 python3-pip
    ```

- **Arch Linux:**

    ```bash
    sudo pacman -S python python-pip
    ```

## Step 3. Check that it works

Close the terminal and open it again — it needs to learn about the new program. Now the same command as in step 1:

**macOS / Linux**

```sh
python3 --version
```

**Windows**

```powershell
python --version
```

A version number in reply means you're done, Python is yours.

If Windows still says "command not found", try `py --version`. If that doesn't help, it's almost certainly the PATH checkbox: run the installer again and choose **Modify**. Sometimes simply logging out and back in is enough.

On macOS and Linux the command is `python3`, not `python` — that's not a fault but the norm: the short name `python` is historically taken there.

Good luck! ✨
