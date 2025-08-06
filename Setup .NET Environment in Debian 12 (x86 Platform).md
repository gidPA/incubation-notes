Taken from the official .NET installation guide: https://learn.microsoft.com/en-us/dotnet/core/install/linux-debian?tabs=dotnet9

###### Add the Microsoft package signing key to the list of trusted keys and add the package repository

```bash
wget https://packages.microsoft.com/config/debian/12/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
```

###### Install the SDK

```bash
sudo apt update
sudo apt install -y dotnet-sdk-9.0
```

###### Install the runtime

```bash
sudo apt-get install -y aspnetcore-runtime-9.0
```