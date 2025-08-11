
To test the NuGet package before pushing it to nuget.org:

1. Create a local NuGet source folder:
   
```bash
mkdir ~/LocalNuget
```

2. Copy the `.nupkg` into it:
   
```bash
cp MyLibrary/bin/Release/MyLibrary.1.0.0.nupkg ~/LocalNuget/
```

3. Add the source to the local NuGet config:
   
```bash
dotnet nuget add source ~/LocalNuget --name Local
```

4. In another project, try:
   
```bash
dotnet add package MyLibrary --source Local
```
