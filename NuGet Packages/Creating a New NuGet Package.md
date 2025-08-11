Assuming the folder structure looks like this:

```bash
MySolution/
│
├── SampleLibrary/           <-- The class library project (to package)
│   └── SampleLibrary.csproj
│
├── SampleLibrary.Tests/     <-- NUnit test project (NOT to package)
│   └── SampleLibrary.Tests.csproj
│
└── MySolution.sln       <-- Solution file
```

Ensure that:
- The **library builds successfully**.
- The test project **references the library**.
- The test project includes NUnit and runs correctly.

Edit `SampleLibrary/SampleLibrary.csproj` to include the necessary package metadata:

```xml
<PropertyGroup>
  <TargetFramework>net9.0</TargetFramework>
  <GeneratePackageOnBuild>true</GeneratePackageOnBuild>
  <PackageId>SampleLibrary</PackageId>
  <Version>1.0.0</Version>
  <Authors>YourName</Authors>
  <Description>My awesome library</Description>
  <PackageLicenseExpression>MIT</PackageLicenseExpression>
  <RepositoryUrl>https://github.com/your/repo</RepositoryUrl>
</PropertyGroup>
```

Restore the package dependencies

```bash
dotnet restore SampleLibrary/SampleLibrary.csproj
```

Build the package with `Release` configuration

```bash
dotnet build SampleLibrary/SampleLibrary.csproj --configuration Release
```

Create the Nuget package

```bash
dotnet pack SampleLibrary/SampleLibrary.csproj --configuration Release
```

This will output the `.nupkg` file to `SampleLibrary/bin/Release/SampleLibrary.1.0.0.nupkg`