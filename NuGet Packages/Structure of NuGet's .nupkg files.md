A NuGet's `.nupkg` files is essentialy a ZIP archive with a specific internal structure that NuGet recognizes.

#### Overall format

- File extension: `.nupkg`
- File type: ZIP archive
- Required metadata and optional content files
- Always includes a `.nuspec` manifest describing the package.

#### Core contents

| File/Folder                                     | Purpose                                                                                                                                            |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`[PackageId].nuspec`**                        | **Required**. XML manifest containing metadata like package ID, version, authors, description, dependencies, and target frameworks.                |
| **`lib/`**                                      | Assemblies (DLLs) intended for consumption by projects. Usually organized by target framework (e.g., `lib/net8.0/MyLibrary.dll`).                  |
| **`ref/`**                                      | Reference assemblies (API surface only) for compile-time use without runtime implementation.                                                       |
| **`tools/`**                                    | Scripts, executables, or other tools that run in the developer’s environment.                                                                      |
| **`build/`**                                    | MSBuild `.props` or `.targets` files automatically imported when the package is referenced. Organized by target framework if needed.               |
| **`buildMultiTargeting/`**                      | Like `build/`, but used when multiple target frameworks are involved.                                                                              |
| **`content/`** _(deprecated)_                   | Files to copy into the consuming project. Replaced by `contentFiles/`.                                                                             |
| **`contentFiles/`**                             | Structured content for inclusion in projects, with metadata in `.nuspec`.                                                                          |
| **`runtime/`**                                  | Runtime-specific assets (e.g., platform-specific DLLs, native libs).                                                                               |
| **`_rels/`**, `[Content_Types].xml`, `package/` | Internal Open Packaging Convention (OPC) files used by NuGet to handle relationships and content types. Usually, one does not edit these manually. |
#### Minimal example

A minimal `.nupkg` for a library targeting .NET 8 might look like:

```css
MyPackage.1.0.0.nupkg
├── MyPackage.nuspec 
├── lib/ 
│   └── net8.0/ 
│       └── MyPackage.dll 
├── [Content_Types].xml 
├── _rels/ 
│   └── .rels 
└── package/
	└── services/
		└── metadata/             
			└── core-properties/                 
				└── [hash].psmdcp
```

---

## **4. `.nuspec` manifest structure**

Example snippet:

```xml
<?xml version="1.0" encoding="utf-8"?>
<package>
  <metadata>
    <id>MyPackage</id>
    <version>1.0.0</version>
    <authors>Gideon A.</authors>
    <description>Example NuGet package</description>
    <dependencies>
      <dependency id="Newtonsoft.Json" version="13.0.3" />
    </dependencies>
  </metadata>
</package>
```

