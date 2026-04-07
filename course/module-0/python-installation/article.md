# Installing Python

For this course, installing Python locally is optional: all suggested tasks run on our servers, and any custom Python code can be executed in the **[Sandbox](/sandbox)**.

If you still want to run code locally, here is a short installation guide.

In this quick guide you will install the latest Python 3 for your system and verify everything works. Let’s go! 🚀

## Step 1. Check if Python is already installed

Open your terminal/console and check the version commands.

-   **Windows (PowerShell or CMD):**

    ```bash
    python --version
    py --version
    pip --version
    ```

-   **macOS / Linux (Terminal):**

    ```bash
    python3 --version
    pip3 --version
    ```

If you see a version (e.g., `Python 3.12.x`), Python is already installed. If commands are not found — proceed to installation.

## Windows installation

1. Download the official installer: **[python.org → Downloads → Windows](https://www.python.org/downloads/windows/)**.
2. Run the installer.
3. On the first screen, tick **“Add python.exe to PATH”** (important!) and click **Install Now**.
4. Wait for completion and close the installer.
5. Verify in PowerShell/CMD:

```bash
py --version
python --version
pip --version
```

If `python` is not found, try `py`, or sign out/in. Reboot if necessary.

## macOS installation

Two convenient options — pick one.

### Option A: Official installer

1. Download from **[python.org → Downloads → macOS](https://www.python.org/downloads/macos/)**.
2. Open the `.pkg` and follow the installer.
3. Verify versions:

```bash
python3 --version
pip3 --version
```

### Option B: Homebrew

1. Make sure Homebrew is installed (`brew -v`). If not, follow **[brew.sh](https://brew.sh/)**.
2. Install Python:

```bash
brew install python
```

3. Verify versions:

```bash
python3 --version
pip3 --version
```

Note: on macOS, the `python3` command (not `python`) is standard — this is expected.

## Linux installation

Open Terminal and run commands for your distribution.

-   **Ubuntu / Debian:**

    ```bash
    sudo apt update
    sudo apt install -y python3 python3-pip
    ```

-   **Fedora:**

    ```bash
    sudo dnf install -y python3 python3-pip
    ```

-   **Arch Linux:**

    ```bash
    sudo pacman -S python python-pip
    ```

Verify versions:

```bash
python3 --version
pip3 --version
```

## FAQ

-   **`python` command not found.** On Windows try `py`. On macOS/Linux use `python3` and `pip3` — that’s normal.
-   **Upgrade `pip`:**

    ```bash
    python -m pip install --upgrade pip
    # or
    python3 -m pip install --upgrade pip
    ```

Good luck with learning Python! ✨
