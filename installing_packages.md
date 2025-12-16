# Installing Required Packages 

## Important Note

**Packages can only be installed on the master node.** You must be logged in as a specific user to install packages.

### Step 1: Connect to Master Node

```bash
ssh <username>@10.24.6.249
```

### Step 2: Install Python Packages

```bash
pip install --user <package_name>
```

### Step 3: Installing System Packages

```bash
sudo apt-get install <package_name>
```

If you do not have sudo access, contact the root user and have them install the required packages for you.

## Locating Installed Packages

### Find Python Package Installation Location

Show where a specific package is installed:

```bash
pip show <package_name>
```

List all installed packages and their locations:

```bash
pip list -v
```

Find User-Installed Python Packages

```bash
python3 -m site --user-site
```

Typically located at:
```
~/.local/lib/python<version>/site-packages/
```


## Verification

After installation, verify the package is accessible:

```bash
python3 -c "import <package_name>; print(<package_name>.__file__)"
```

This will show the exact file path of the imported package.

## Notes

- Packages installed on the master node should be accessible from slave nodes if they share the same file system. (if the slave nodes are unable to find installed packages, reach out to the root user)
- User-installed packages (with `--user` flag) are stored in your home directory
- System-wide packages require sudo access and are stored in `/usr/local/` or `/usr/`
- Always verify package installation before using it in your scripts
