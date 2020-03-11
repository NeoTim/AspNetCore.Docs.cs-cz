---
title: Poskytovatelé souborů v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 34a48bbcf9ffb20bb61f89c80adedc1cc4783988
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658786"
---
# <a name="file-providers-in-aspnet-core"></a>Poskytovatelé souborů v ASP.NET Core

[Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů. Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:

* `IWebHostEnvironment` zpřístupňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a [kořenový adresář webu](xref:fundamentals/index#web-root) jako typy `IFileProvider`.
* [Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.
* [Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.
* Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souborů

Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` zveřejňuje metody pro:

* Získat informace o souboru (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Získání informací o adresáři (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)
* Datum <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .

Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace poskytovatele souborů

K dispozici jsou tři implementace `IFileProvider`.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních. |
| [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů. |

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

Přidejte odkaz na balíček do projektu pro balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) .

Chcete-li vygenerovat manifest vložených souborů, nastavte vlastnost `<GenerateEmbeddedFilesManifest>` na hodnotu `true`. Zadejte soubory pro vložení [>\<EmbeddedResource](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

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

| Přetížení | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný parametr relativní cesty `root`. Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>). Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`. `manifestName` představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombinuje `IFileProvider` instancí a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`předat konstruktoru jednu nebo více instancí `IFileProvider`.

V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytují soubory `CompositeFileProvider` zaregistrovaným v kontejneru služby aplikace:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledovat změny

Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů. `Watch` přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů. `Watch` vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>. Token pro změnu zpřístupňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; volána, když jsou zjištěny změny do zadaného řetězce cesty. Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (zobrazeno níže) nebo znovu vytvořte `IChangeToken` instancí v reakci na změny.

V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách. Nastavte proměnnou prostředí `DOTNET_USE_POLLING_FILE_WATCHER` na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).

## <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* . Určete skupiny souborů s těmito vzory. Dva zástupné znaky jsou `*` a `**`:

**`*`**  
Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.

**`**`**  
Odpovídá cokoli v různých úrovních adresáře. Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.

**Příklady vzorů glob**

**`directory/file.txt`**  
Odpovídá konkrétnímu souboru v konkrétním adresáři.

**`directory/*.txt`**  
Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.

**`directory/*/appsettings.json`**  
Vyhledá všechny soubory `appsettings.json` v adresářích přesně jednu úroveň pod *adresářovou* složkou.

**`directory/**/*.txt`**  
Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů. Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> zpřístupňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a [kořenový adresář webu](xref:fundamentals/index#web-root) jako typy `IFileProvider`.
* [Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.
* [Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.
* Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souborů

Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider` zveřejňuje metody pro:

* Získat informace o souboru (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Získání informací o adresáři (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).
* Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (v bajtech)
* Datum <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>

Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .

Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace poskytovatele souborů

K dispozici jsou tři implementace `IFileProvider`.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních. |
| [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů. |

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

| Přetížení | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný parametr relativní cesty `root`. Zadejte `root` pro rozsah volání do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> na tyto prostředky v zadané cestě. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný parametr relativní cesty `root` a parametr data `lastModified` (<xref:System.DateTimeOffset>). Datum `lastModified` rozsahem data poslední změny pro instance <xref:Microsoft.Extensions.FileProviders.IFileInfo> vrácené <xref:Microsoft.Extensions.FileProviders.IFileProvider>. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a parametry `manifestName`. `manifestName` představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombinuje `IFileProvider` instancí a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`předat konstruktoru jednu nebo více instancí `IFileProvider`.

V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` poskytují soubory `CompositeFileProvider` zaregistrovaným v kontejneru služby aplikace:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledovat změny

Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů. `Watch` přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů. `Watch` vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>. Token pro změnu zpřístupňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; vlastnost, kterou lze zkontrolovat, aby bylo možné zjistit, zda došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; volána, když jsou zjištěny změny do zadaného řetězce cesty. Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit konstantní monitorování, použijte <xref:System.Threading.Tasks.TaskCompletionSource`1> (zobrazeno níže) nebo znovu vytvořte `IChangeToken` instancí v reakci na změny.

V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách. Nastavte proměnnou prostředí `DOTNET_USE_POLLING_FILE_WATCHER` na `1` nebo `true` k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat).

## <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* . Určete skupiny souborů s těmito vzory. Dva zástupné znaky jsou `*` a `**`:

**`*`**  
Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.

**`**`**  
Odpovídá cokoli v různých úrovních adresáře. Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.

**Příklady vzorů glob**

**`directory/file.txt`**  
Odpovídá konkrétnímu souboru v konkrétním adresáři.

**`directory/*.txt`**  
Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.

**`directory/*/appsettings.json`**  
Vyhledá všechny soubory `appsettings.json` v adresářích přesně jednu úroveň pod *adresářovou* složkou.

**`directory/**/*.txt`**  
Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .

::: moniker-end
