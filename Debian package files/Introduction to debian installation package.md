One of the most reliable and native ways to distribute application targeting debian-based Linux distributions is through **`.deb` packages**.

### Why Use `.deb` Packages?

- **Native experience** for users on Debian, Ubuntu, and derivatives.
- Install via **APT** (`apt install sample-app`) or **double-click** in GUI software installers (e.g. `gdebi`).
- Automatically handles **dependencies**, **file locations**, and **uninstallation**.
- Can be used to register **services**, **desktop shortcuts**, **MIME types**, and more.
- Easily distributable via:
    - Personal website
    - APT repository (hosted or via Launchpad/PPA)
    - Package archives

## What Is Inside a `.deb` Package?

A `.deb` package is just an **ar archive** with the following contents:

```
data.tar.xz    # Your application files
control.tar.gz  # Package metadata & maintainer scripts
debian-binary   # Always contains "2.0"
```

## Tools Used

- `dpkg-deb`: Low-level tool to create or inspect .deb files.
- `dpkg`: Installs .deb files on local systems.
- `apt` / `apt-get`: Installs packages from repositories.
- `lintian`: Static analysis tool to check `.deb` policy compliance.