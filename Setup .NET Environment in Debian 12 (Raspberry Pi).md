
Reference: https://learn.microsoft.com/en-us/dotnet/iot/deployment

1. Grab the official .NET installation script and run it

```
curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin --channel STS
```

2. To simplify path resolution, add a `DOTNET_ROOT` environment variable and add the _.dotnet_ directory to `$PATH`:

```
echo 'export DOTNET_ROOT=$HOME/.dotnet' >> ~/.bashrc echo 'export PATH=$PATH:$HOME/.dotnet' >> ~/.bashrc source ~/.bashrc
```

3. Verify the .NET installation

```
dotnet --version
```

