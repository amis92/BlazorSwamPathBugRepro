# Blazor Service Worker Asset Manifest path construction bug repro

Issue for which this repro exists: https://github.com/dotnet/aspnetcore/issues/25959

Issue text for reference follows.


### Describe the bug
When `BaseIntermediateOutputPath` is non-default, `GenerateServiceWorkerAssetsManifest` task fails.

### To Reproduce

```
> dotnet new blazorwasm --pwa -o BlazorSwamPathBugRepro
> cd BlazorSwamPathBugRepro
> echo "<Project>
    <PropertyGroup>
        <BaseOutputPath>$(MSBuildThisFileDirectory)build/bin/$(MSBuildProjectName)/</BaseOutputPath>
        <BaseIntermediateOutputPath>$(MSBuildThisFileDirectory)build/obj/$(MSBuildProjectName)/</BaseIntermediateOutputPath>
    </PropertyGroup>
</Project>" > Directory.Build.props
```

Repro repo: https://github.com/amis92/BlazorSwamPathBugRepro

### Exceptions (if any)

<details>
<summary>Exception stack trace</summary>
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018: The "GenerateServiceWorkerAssetsManifest" task failed unexpectedly. [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018: System.IO.DirectoryNotFoundException: Could not find a part of the path 'C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\obj\Debug\net5.0\service-worker-assets.js'. [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at System.IO.FileStream.ValidateFileHandle(SafeFileHandle fileHandle) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at System.IO.FileStream.CreateFileOpenHandle(FileMode mode, FileShare share, FileOptions options) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize, FileOptions options) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at System.IO.FileStream..ctor(String path, FileMode mode, FileAccess access, FileShare share, Int32 bufferSize) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at System.IO.File.Create(String path) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at Microsoft.NET.Sdk.BlazorWebAssembly.GenerateServiceWorkerAssetsManifest.Execute() [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at Microsoft.Build.BackEnd.TaskExecutionHost.Microsoft.Build.BackEnd.ITaskExecutionHost.Execute() [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\Sdks\Microsoft.NET.Sdk.BlazorWebAssembly\targets\Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets(68,5): error MSB4018:    at Microsoft.Build.BackEnd.TaskBuilder.ExecuteInstantiatedTask(ITaskExecutionHost taskExecutionHost, TaskLoggingContext taskLoggingContext, 
TaskHost taskHost, ItemBucket bucket, TaskExecutionMode howToExecuteTask) [C:\Users\Amadeusz\Source\Repos\BlazorSwamPathBugRepro\BlazorSwamPathBugRepro.csproj]
</details>

### Further technical details
- ASP.NET Core version: 5.0.100-rc.1.20452.10
- Include the output of `dotnet --info`:
<details>
<summary>dotnet --info output</summary>

```
.NET SDK (reflecting any global.json):
 Version:   5.0.100-rc.1.20452.10
 Commit:    473d1b592e

Runtime Environment:
 OS Name:     Windows
 OS Version:  10.0.18363
 OS Platform: Windows
 RID:         win10-x64
 Base Path:   C:\Program Files\dotnet\sdk\5.0.100-rc.1.20452.10\

Host (useful for support):
  Version: 5.0.0-rc.1.20451.14
  Commit:  38017c3935

.NET SDKs installed:
  2.1.809 [C:\Program Files\dotnet\sdk]
  3.1.402 [C:\Program Files\dotnet\sdk]
  5.0.100-rc.1.20452.10 [C:\Program Files\dotnet\sdk]

.NET runtimes installed:
  Microsoft.AspNetCore.All 2.1.0 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.2 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.13 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.17 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.18 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.21 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.All 2.1.22 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.All]
  Microsoft.AspNetCore.App 2.1.0 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.2 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.13 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.17 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.18 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.21 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 2.1.22 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 3.1.2 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 3.1.7 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 3.1.8 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.AspNetCore.App 5.0.0-rc.1.20451.17 [C:\Program Files\dotnet\shared\Microsoft.AspNetCore.App]
  Microsoft.NETCore.App 2.1.13 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 2.1.17 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 2.1.18 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 2.1.21 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 2.1.22 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 3.1.2 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 3.1.7 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 3.1.8 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.NETCore.App 5.0.0-rc.1.20451.14 [C:\Program Files\dotnet\shared\Microsoft.NETCore.App]
  Microsoft.WindowsDesktop.App 3.1.2 [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]
  Microsoft.WindowsDesktop.App 3.1.7 [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]
  Microsoft.WindowsDesktop.App 3.1.8 [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]
  Microsoft.WindowsDesktop.App 5.0.0-rc.1.20452.2 [C:\Program Files\dotnet\shared\Microsoft.WindowsDesktop.App]

To install additional .NET runtimes or SDKs:
  https://aka.ms/dotnet-download
```

</details>

- The IDE (~~VS~~ / VS Code/ ~~VS4Mac~~) you're running on, and it's version
Version: 1.49.0 (system setup)
Commit: e790b931385d72cf5669fcefc51cdf65990efa5d
Date: 2020-09-10T13:22:08.892Z
Electron: 9.2.1
Chrome: 83.0.4103.122
Node.js: 12.14.1
V8: 8.3.110.13-electron.0
OS: Windows_NT x64 10.0.18363


### Personal investigation

The issue seems to crop up in Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets which is part of `Microsoft.NET.Sdk.BlazorWebAssembly` in this repository:

https://github.com/dotnet/aspnetcore/blob/50eb578c738bf007f0a37efcdc99ee72157b9dd1/src/Components/WebAssembly/Sdk/src/targets/Microsoft.NET.Sdk.BlazorWebAssembly.ServiceWorkerAssetsManifest.targets#L19-L21

First of all, when the `BaseIntermediateOutputPath` is rooted, it doesn't actually use any user-set path and instead just forces the path to be relative and arbitrary, which doesn't seem to be correct (but maybe I'm missing something):
> `obj\$(Configuration)\$(TargetFramework)\$(ServiceWorkerAssetsManifest)`

However, the second case looks like it should work, except it doesn't seem possible for the `BaseIntermediateOutputPath` to *not* be rooted at that point, because of the following:

https://github.com/dotnet/sdk/blob/d7ee9e27927f11c7e1b8ad1f48bcbffd2ac7a000/src/WebSdk/Publish/Targets/Microsoft.NET.Sdk.Publish.targets#L74-L75

Additionally I'm concerned that the `_ServiceWorkerAssetsManifestIntermediateOutputPath` itself shouldn't be used at all in other targets in that file and it is (the failing task itself takes it as an input); instead the `_ServiceWorkerAssetsManifestFullPath` probably should be.