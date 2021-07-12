# Bottles dependencies repository
Repository for software dependencies on Bottles

There is also an [online database](https://usebottles.com/database/dependencies) for this repository.


## How to contribute
This is a fully community driven repository. This mean that other users can open Pull Requests to provide new dependency to increase software support in Bottles!

To submit a new dependency, just commit a new manifest file. This must follow the structure:
```yaml
Name: dependency_name
Description: Dependency complete name
Dependencies: []
Steps: []
```

**Steps:**
**install_exe** can be used when you want to install a dependency (*.exe)
```yaml
action: install_exe
file_name: vcredist_x64.exe
url: https://download.visualstudio.microsoft.com/download/pr/10912041/cee5d6bca2ddbcd039da727bf4acb48a/vcredist_x64.exe
rename: vcredist2013_x64.exe
file_checksum: 49b1164f8e95ec6409ea83cdb352d8da
```
  
**install_msi** can be used when you want to install a dependency (*.msi)
```yaml
action: install_msi
file_name: msxml6.msi
url: https://download.microsoft.com/download/2/e/0/2e01308a-e17f-4bf9-bf48-161356cf9c81/msxml6.msi
rename: vcredist2013_x64.exe
file_checksum: a13bff305ec8ab7ab89c2a1758c55cb9
```

**delete_sys32_dlls** can be used when you need to remove system .dlls
```yaml
- action: delete_sys32_dlls
  dlls:
  - comcat.dll
  - msvcrt.dll
  - oleaut32.dll
  - olepro32.dll
```

**cab_extract** can be used to exctract a Windows cabinet
```yaml
- action: cab_extract
  file_name: msxml6-KB973686-enu-amd64.exe
  url: https://web.archive.org/web/20190122095451/https://download.microsoft.com/download/1/5/8/158F681A-E595-472B-B15E-62B649B1B6FF/msxml6-KB973686-enu-amd64.exe
  file_checksum: 13a292beb9ebe46ac97b8a4352fe2cd5

# or from the temp dir

- action: cab_extract
  file_name: msxml6.msi
  url: temp/msxml6
```

**copy_cab_dll** can be used to copy dll from an extracted Windows cabinet to the prefix
```yaml
- action: copy_cab_dll
  file_name: msxml6.dll.86F857F6_A743_463D_B2FE_98CB5F727E09
  url: temp/msxml6
  dest: windows/system32/msxml6.dll
```

**override_dll** can be used to add a new DLL override
```yaml
- action: override_dll
  dll: msxml6
  type: native,builtin
```

The manifest name should be the same declared inside in the `Name` parameter, followed by the `.json` extension.

**Parameters:**

- **Name** should be a concise name for the dependency, no spaces, use underscore instead (e.g. vcredist2019)
- **Description** this is not a full description, this is just the complete name (e.g. Microsoft Visual C++ Redistributable (2015-2019) 14.28.29325)
- **Dependencies** at this time is not used, in the future it will be used to indicate other dependencies needed to install this one
- **Steps** this contains all steps that Bootles should perform to install this dependency, e.g.:
```yaml
action: install_exe
file_name: vcredist_x64.exe
url: https://download.visualstudio.microsoft.com/download/pr/10912041/cee5d6bca2ddbcd039da727bf4acb48a/vcredist_x64.exe
rename: vcredist2013_x64.exe
file_checksum: 49b1164f8e95ec6409ea83cdb352d8da
```
  the method `install_exe` can be used multiple time to install more than one executable (use `install_msi` with the same syntax for `.msi` files). The `rename` parameter is optional and must be used rename a downloaded file to a more specific one. Another method can be `delete_sys32_dlls`, this must be used to delete one or more `dll` from the `C:\Windows\System32` path, to avoid conflicts, e.g.:
```yaml
Steps:
- action: delete_sys32_dlls
  dlls:
  - comcat.dll
  - msvcrt.dll
  - oleaut32.dll
  - olepro32.dll
  - stdole2.dll
```

> Note that every file should declare its checksum (MD5), this should used by Bottles to validate every file, checking for damaged or partials downloads.

When a pull requests is verified and merged, a moderator will add your new dependency to the `index.json` file, this will make your work discoverable by Bottles.

### Best practices/Rules
- Provide links to official, tangible, reliable and possibly immutable resources
- Do not self-host resources
- Don't post any copyright infringing resources
- Do not delete manifests, when a dependency should be removed from Bottles, just remove it from `index.json`, unless it violates the rules, in that case it will be eliminated.
