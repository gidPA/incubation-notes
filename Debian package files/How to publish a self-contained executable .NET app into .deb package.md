## Prerequisites

1. **.NET SDK** (e.g., 9.0)
2. **Debian-based Linux system** (or WSL/VM)
3. `dotnet` CLI
4. `dpkg-deb` utility (comes with most Debian-based distros)
5. Basic project structure is already working with `dotnet publish`

### Step 1: Publish the Project as Self-Contained

```bash
dotnet publish -c Release -r linux-x64 --self-contained true -o ./publish
```

### Step 2: Prepare the Debian Package Directory Structure

I will use "MyApp" as the example project name.

Inside the root directory of the project

```
release/
└─── MyApp_1.0.0/
	├── DEBIAN/
	│   └── control  # file for specifying package metadata
	│	└── postinst  # bash script to execute after installation
	│	└── postinst  # bash script to execute after installation
	│	└── prerm  # bash script to execute before package uninstallation
	│	└── postrm  # bash script to execute before package uninstallation
	└── opt/
	    └── MyApp/
	        ├── MyApp         # main executable
	        ├── other files
	└── etc/
	    └── systemd/
		    └── system/
			    └── myapp.service # systemd service file (if needed)
	└── usr/
	    └── bin/
		    └── myapp # symlink to allow the app to be callable anywhere (optional)
```

### Step 3: Create the Control File

inside `release/MyApp_1.0.0/DEBIAN/control`

```
Package: myapp
Version: 1.0.0
Section: base
Priority: optional
Architecture: arm64
Maintainer: Maintainer Name <maintainer@example.com>
Description: My Sample .net app.
```

>**Note:** The `Depends:` parameter can be used if the app requires certain system packages (e.g., `libssl-dev`), but self-contained .NET apps usually don’t need that.

### Step 4: Create the systemd unit file

Inside `release/MyApp_1.0.0/etc/systemd/system/myapp.service`

```ini
[Unit]
Description=My Sample .net app.

[Service]
WorkingDirectory=/opt/MyApp
ExecStart=/opt/MyApp/MyApp
Restart=always
RestartSec=10
KillSignal=SIGINT
SysLogIdentifier=dotnet-my-app
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
```

### Step 5: Write the `postinst` script to start and enable the app automatically 

Inside `release/MyApp_1.0.0/DEBIAN/postinst`

```bash
#!/bin/bash
set -e

systemctl daemon-reexec || true
systemctl daemon-reload
systemctl enable myapp.service
systemctl start cpumonitorapp.service
```

### Step 6: Write the `prerm` script to stop and disable the service upon uninstallation

Inside `release/MyApp_1.0.0/DEBIAN/prerm`

```bash
#!/bin/bash
set -e

# Stop and disable the systemd service
systemctl stop myapp.service || true
systemctl disable myapp.service || true
```

Make it executable

```bash
chmod 755 yourapp_1.0.0/DEBIAN/prerm
```

### Step 7: Write the `postrm` script to clean up the service file

Inside `release/MyApp_1.0.0/DEBIAN/postrm`

```bash
#!/bin/bash
set -e

# Remove systemd service unit file if it exists
if [ -f /etc/systemd/system/myapp.service ]; then
    rm -f /etc/systemd/system/myapp.service
    systemctl daemon-reload
fi
```

Make it executable

```bash
chmod 755 yourapp_1.0.0/DEBIAN/postrm
```
### (Optional) Step 8: Create a Symlink for Easy Access

Inside `release/MyApp_1.0.0/usr/bin/myapp`

```bash
#!/bin/sh
/opt/MyApp/MyApp
```

Make the symlink executable

```bash
chmod +x release/MyApp_1.0.0/usr/bin/myapp
```

### Step 9: Build the .deb package

```bash
cd release
dpkg-deb --build --root-owner-group MyApp_1.0.0
```

This produces

```
MyApp_1.0.0.deb
```

### Step 10: Install and test

Copy the file to the raspberry pi:

```bash
scp MyApp_1.0.0.deb user@hostname.local:home/user/Downloads
```

Log in to the raspberry pi via SSH, and install the application. Inside the raspberry pi's shell:

```bash
sudo dpkg -i Downloads/MyApp_1.0.0.deb
```

Test the application by:

- Accessing the API endpoint via curl, or
- Opening the webpage in a web browser

Test if the uninstallation has been correctly implemented by 

```bash
sudo apt remove myapp
```

### Important things to know

- In the configuration files above, it is important to leave an empty line at the end of each file.
- When you build a `.deb` package, **any directory structure under the root of your package**, such as `etc/systemd/system`, **mimics the real Linux filesystem**, and `dpkg` will copy it **verbatim to the corresponding locations on the system** during installation.

### Template script to automate above steps

[[Script to automate .deb package creation]]