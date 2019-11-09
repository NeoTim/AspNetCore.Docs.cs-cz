---
title: File Providers in ASP.NET Core
author: guardrex
description: Learn how ASP.NET Core abstracts file system access through the use of File Providers.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 531f7acd7a704a74e6142d201f613f05288deecb
ms.sourcegitcommit: 4818385c3cfe0805e15138a2c1785b62deeaab90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896845"
---
# <a name="file-providers-in-aspnet-core"></a>File Providers in ASP.NET Core

By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstracts file system access through the use of File Providers. File Providers are used throughout the ASP.NET Core framework:

* `IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.
* [Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.
* [Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.
* .NET Core tooling uses File Providers and glob patterns to specify which files should be published.

[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>File Provider interfaces

The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` exposes methods to:

* Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` provides methods and properties for working with files:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date

You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.

The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>File Provider implementations

Three implementations of `IFileProvider` are available.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies. |
| [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů. `PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).

Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.

Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider` je `IFileProvider`.
* `contents` je `IDirectoryContents`.
* `fileInfo` je `IFileInfo`.

Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru. Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení. `ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.

Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.

Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`. Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.

Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Umožňuje nastavit rozsah souborů na datum poslední úpravy.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Metody | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný parametr relativní cesty `root`. Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>). Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`. `manifestName` představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers. When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.

In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Watch for changes

The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes. `Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files. `Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>. The change token exposes:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string. Each change token only calls its associated callback in response to a single change. To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.

In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Some file systems, such as Docker containers and network shares, may not reliably send change notifications. Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).

## <a name="glob-patterns"></a>Glob patterns

File system paths use wildcard patterns called *glob (or globbing) patterns*. Specify groups of files with these patterns. The two wildcard characters are `*` and `**`:

**`*`**  
Matches anything at the current folder level, any filename, or any file extension. Matches are terminated by `/` and `.` characters in the file path.

**`**`**  
Matches anything across multiple directory levels. Can be used to recursively match many files within a directory hierarchy.

**Glob pattern examples**

**`directory/file.txt`**  
Matches a specific file in a specific directory.

**`directory/*.txt`**  
Matches all files with *.txt* extension in a specific directory.

**`directory/*/appsettings.json`**  
Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.

**`directory/**/*.txt`**  
Matches all files with *.txt* extension found anywhere under the *directory* folder.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstracts file system access through the use of File Providers. File Providers are used throughout the ASP.NET Core framework:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.
* [Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.
* [Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.
* .NET Core tooling uses File Providers and glob patterns to specify which files should be published.

[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>File Provider interfaces

The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` exposes methods to:

* Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` provides methods and properties for working with files:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date

You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.

The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>File Provider implementations

Three implementations of `IFileProvider` are available.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies. |
| [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> poskytuje přístup k fyzickému systému souborů. `PhysicalFileProvider` používá typ <xref:System.IO.File?displayProperty=fullName> (pro fyzického poskytovatele) a rozsahy všech cest k adresáři a jeho podřízeným položkám. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytváření a používání `PhysicalFileProvider` je vyžádání `IFileProvider` v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).

Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.

Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider` je `IFileProvider`.
* `contents` je `IDirectoryContents`.
* `fileInfo` je `IFileInfo`.

Zprostředkovatele souboru lze použít k iterování adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací o souboru. Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace vytvoří zprostředkovatele v `Startup.ConfigureServices` třídy aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> se používá pro přístup k souborům integrovaným v rámci sestavení. `ManifestEmbeddedFileProvider` používá manifest kompilovaný do sestavení pro rekonstrukci původních cest vložených souborů.

Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`. Zadejte soubory pro vložení [&gt;&lt;EmbeddedResource ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.

Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Umožňuje nastavit rozsah souborů na datum poslední úpravy.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Metody | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný parametr relativní cesty `root`. Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>). Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`. `manifestName` představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers. When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.

In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Watch for changes

The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes. `Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files. `Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>. The change token exposes:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string. Each change token only calls its associated callback in response to a single change. To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.

In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Some file systems, such as Docker containers and network shares, may not reliably send change notifications. Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).

## <a name="glob-patterns"></a>Glob patterns

File system paths use wildcard patterns called *glob (or globbing) patterns*. Specify groups of files with these patterns. The two wildcard characters are `*` and `**`:

**`*`**  
Matches anything at the current folder level, any filename, or any file extension. Matches are terminated by `/` and `.` characters in the file path.

**`**`**  
Matches anything across multiple directory levels. Can be used to recursively match many files within a directory hierarchy.

**Glob pattern examples**

**`directory/file.txt`**  
Matches a specific file in a specific directory.

**`directory/*.txt`**  
Matches all files with *.txt* extension in a specific directory.

**`directory/*/appsettings.json`**  
Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.

**`directory/**/*.txt`**  
Matches all files with *.txt* extension found anywhere under the *directory* folder.

::: moniker-end
