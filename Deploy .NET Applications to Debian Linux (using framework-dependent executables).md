
### Setup .NET environment

x86 platform: [[Setup .NET Environment in Debian 12 (x86 Platform)]]
arm64 platform: [[Setup .NET Environment in Debian 12 (Raspberry Pi)]]

### Publish the .NET project in the host

Publish the project

```bash
dotnet publish
```

Navigate to the publish directory

```bash
cd <AppName>/bin/Release/netx.x/publish
```

Copy to the deployment target

```bash
scp -r . <username>@<ip or hostname>:<directory>
```

### Create a Systemd file for .NET Application

```bash
sudo nano /etc/systemd/system/<app-name>.service
```

Add the following configuration

```ini
[Unit]
Description=My .NET Core Application

[Service]
WorkingDirectory=/var/www/
# In raspberry pi, the dotnet executable is placed inside $HOME/.dotnet
ExecStart=/usr/bin/dotnet <app directory>
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=root
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
```

Save and close the file, then reload the systemd daemon to apply the changes.

```bash
sudo systemctl daemon-reload
```

### Starting, stopping, and setting up the app to start on boot

Start the .NET application service using the following command:

```bash
sudo systemctl start <app-name>
```

Check the status of the app:

```bash
sudo systemctl status <app-name>
```

Start the app on boot

```bash
sudo systemctl enable <app-name>
```