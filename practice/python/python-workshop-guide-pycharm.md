# Python Development Environment Workshop Guide (Windows & PyCharm)

## Overview
This workshop guide covers setting up a professional Python development environment using PyCharm Community Edition and working with virtual environments on Windows.

Estimated time: 55 minutes

---

## Workshop Structure

### Part 1: Environment Setup (25 minutes)
### Part 2: Virtual Environment Practice (30 minutes)

---

## Prerequisites
- Windows 10/11
- Administrative access to your computer
- Internet connection
- At least 4GB of free disk space

---

## Part 1: Environment Setup (25 minutes)

### 1.1 Python Installation

Open Command Prompt and check if Python is installed:
```cmd
python --version
```

If Python is not installed:
1. Visit [python.org](https://www.python.org/downloads/)
2. Download the latest Python version (3.11+ recommended)
3. During installation (IMPORTANT):
   - ✅ Add Python to PATH
   - ✅ Install pip
   - ✅ Install for all users
   - ✅ Customize installation > Add Python to environment variables

### 1.2 PyCharm Installation

1. Download PyCharm Community Edition:
   - Visit [JetBrains PyCharm Download Page](https://www.jetbrains.com/pycharm/download/)
   - Select "Community Edition" (Free, Open Source)
   - Click "Download"

2. Install PyCharm:
   - Run the downloaded installer
   - Select following components:
     - ✅ 64-bit launcher
     - ✅ Add "Open Folder as Project"
     - ✅ Create Associations: .py
   - Select start menu folder
   - Click Install

3. First-time setup:
   - Start PyCharm
   - Accept privacy policy
   - Choose "Do not import settings" if this is your first installation
   - Select your preferred UI theme (Darcula/Light)

### 1.3 PyCharm Configuration

Important settings to adjust (File > Settings or Ctrl+Alt+S):

1. Python Interpreter:
   - Settings > Project > Python Interpreter
   - We'll set this up per project later

2. Editor Settings:
```
Editor > General:
- ✅ Show line numbers
- ✅ Show whitespaces

Editor > Code Style > Python:
- Tabs and Indents: 4 spaces
- ✅ Use tab character: Unchecked
```

---

## Part 2: Virtual Environment Practice (30 minutes)

### 2.1 Creating New Project

1. Launch PyCharm
2. Click "New Project" on the welcome screen
3. Configure new project:
   - Location: Choose your workspace folder/my_project
   - New environment using Virtualenv
   - Base interpreter: Choose your Python installation
   - ✅ Create a main.py file
   - Click Create

This will create a project with the following structure:
```
my_project/
  ├── venv/          # Virtual environment directory
  ├── .idea/         # PyCharm settings
  └── main.py        # Main application file
```

4. Add additional structure:
   - Right-click project root > New > Directory:
     - Create `src`
     - Create `tests`
   - Move `main.py` into `src` folder

### 2.2 Virtual Environment Management

The virtual environment is automatically created and activated by PyCharm. To verify:

1. Open PyCharm Terminal (Alt+F12)
2. Check Python interpreter location:
```cmd
where python
```
Should show path inside your project's venv directory.

### 2.3 Package Management

In PyCharm Terminal:
```cmd
# Upgrade pip
python -m pip install --upgrade pip

# Install common packages
pip install requests pytest black pylint

# Save dependencies
pip freeze > requirements.txt
```

To install packages using PyCharm GUI:
1. Settings > Project > Python Interpreter
2. Click + button
3. Search and install packages
4. Click Apply

### 2.4 Hello World Application

Open `src/main.py` and replace content with:

```python
def greet(name: str) -> str:
    """Return a personalized greeting."""
    return f"Hello, {name}!"

def main():
    """Main function."""
    name = input("Enter your name: ")
    print(greet(name))

if __name__ == "__main__":
    main()
```

### 2.5 Running the Application

Multiple ways to run in PyCharm:
1. Click green play button in the gutter (next to `if __name__ == "__main__":`)
2. Right-click in editor > Run 'main'
3. Keyboard shortcut: Shift+F10

### 2.6 Essential PyCharm Shortcuts

```
Shift+F10     - Run
Ctrl+Alt+L    - Format code
Ctrl+Alt+S    - Settings
Shift+Shift   - Search Everywhere
Alt+F12       - Toggle Terminal
Ctrl+Space    - Basic code completion
Alt+Enter     - Show intention actions
Ctrl+Q        - Quick documentation
```

### 2.7 Version Control Integration

1. VCS > Enable Version Control Integration
2. Select Git
3. Create `.gitignore`:
```
venv/
.idea/
__pycache__/
*.pyc
.pytest_cache/
```

---

## Troubleshooting Windows-Specific Issues

### Common Issues

1. Python not found after installation
   - Close and reopen PyCharm
   - Verify Python in System Environment Variables
   - Add Python paths manually if needed:
     ```
     C:\Users\YourUsername\AppData\Local\Programs\Python\Python3x\
     C:\Users\YourUsername\AppData\Local\Programs\Python\Python3x\Scripts\
     ```

2. PyCharm doesn't detect Python
   - File > Settings > Project > Python Interpreter
   - Click gear icon > Add
   - Select System Interpreter
   - Browse to Python installation

3. Package installation fails
   - Check internet connection
   - Verify pip is updated
   - Try installing via PyCharm GUI
   - Clear pip cache: `pip cache purge`

4. Virtual Environment Issues
   - Delete venv folder
   - Settings > Project > Python Interpreter
   - Click gear icon > Add
   - Create new environment

---

## PyCharm Pro Tips

1. Code Navigation:
   - Ctrl+Click on any symbol to jump to definition
   - Alt+F7 to find all usages
   - Ctrl+F12 to see file structure

2. Refactoring:
   - Shift+F6 to rename
   - Ctrl+Alt+M to extract method
   - Ctrl+Alt+V to extract variable

3. Debugging:
   - Click line number to set breakpoint
   - Shift+F9 to debug
   - F8 to step over
   - F7 to step into

4. Code Generation:
   - Alt+Insert for code generation menu
   - Ctrl+J for live templates

---

## Next Steps

1. Explore PyCharm's visual debugger
2. Learn about PyCharm's refactoring tools
3. Set up professional testing with pytest
4. Configure code quality tools
5. Learn advanced PyCharm features

---

## Resources

- [PyCharm Documentation](https://www.jetbrains.com/pycharm/documentation/)
- [Python Documentation](https://docs.python.org/)
- [PyCharm Quick Start Guide](https://www.jetbrains.com/help/pycharm/quick-start-guide.html)
- [JetBrains Python Tutorial](https://www.jetbrains.com/help/pycharm/creating-and-running-your-first-python-project.html)
