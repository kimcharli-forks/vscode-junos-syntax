# Testing the Extension Locally (Before Publishing)

## Option 1: Press F5 (Extension Development Host)

Open the project in VS Code, then press **F5** (or **Run > Start Debugging**). This launches a new VS Code window with the extension loaded. Open any `.junos` file there to see the syntax highlighting live.

## Option 2: Install as a VSIX Package

**1. Install `vsce`** (if not already installed):
```bash
npm install -g @vscode/vsce
```

**2. Package it:**
```bash
cd /path/to/vscode-junos-syntax
vsce package --no-dependencies
```
This produces a file like `junos-1.3.0.vsix`.

**3. Install the VSIX in VS Code:**
```bash
code --install-extension junos-1.3.0.vsix
```
Or via the UI: **Extensions** sidebar → `...` menu → **Install from VSIX…**

---

## Sample Test File

Create a file with a `.junos` extension and paste in some Junos config:

```
system {
    host-name router1;
}
interfaces {
    ge-0/0/0 {
        unit 0 {
            family inet {
                address 192.168.1.1/24;
            }
        }
    }
}
policy-options {
    policy-statement ACCEPT-ALL {
        term 1 {
            then accept;
        }
    }
}
```

For rollback config files (`.conf.N`), rename accordingly: `cp test.junos test.conf.1`

---

## Note on `.conf` Files

Plain `.conf` is no longer auto-associated (removed to avoid conflicts with nginx, Apache, etc.).
To associate `.conf` with Junos in a specific workspace, add to `.vscode/settings.json`:

```json
{
    "files.associations": {
        "*.conf": "junos"
    }
}
```
