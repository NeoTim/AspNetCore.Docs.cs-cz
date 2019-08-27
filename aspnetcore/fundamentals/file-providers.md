---
title: Poskytovatelé souborů v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 44c439dce893d486668bf8ac3f20cdf7952c5186
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059096"
---
# <a name="file-providers-in-aspnet-core"></a>Poskytovatelé souborů v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů. Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:

* `IWebHostEnvironment`zpřístupňuje kořenový adresář obsahu aplikace a webové kořenové adresáře `IFileProvider` jako typy.
* [Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.
* [Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.
* Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souborů

Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider`Zpřístupňuje metody pro:

* Získat informace o souboru<xref:Microsoft.Extensions.FileProviders.IFileInfo>().
* Získat informace o adresáři<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().
* Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum

Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .

Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace poskytovatele souborů

K dispozici `IFileProvider` jsou tři implementace systému.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních. |
| [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Poskytuje přístup k fyzickému systému souborů. `PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> používá typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro `IFileProvider` vyžádání v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.

Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider``IFileProvider`je.
* `contents``IDirectoryContents`je.
* `fileInfo``IFileInfo`je.

Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru. Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Slouží k přístupu k souborům integrovaným v rámci sestavení. `ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.

Přidejte odkaz na balíček do projektu pro balíček [Microsoft. Extensions. Providers. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) .

Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`hodnotu. Zadejte soubory, které se mají vložit [ \<do EmbeddedResource >](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.

Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Umožňuje nastavit rozsah souborů na datum poslední úpravy.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Metody | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný `root` parametr relativní cesty. Zadejte obor <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> pro volání do těchto prostředků v zadané cestě. `root` |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný `root` parametr relativní cesty `lastModified` a parametr data (<xref:System.DateTimeOffset>). Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `lastModified` |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry. `manifestName` Představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Kombinuje`IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.

V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledovat změny

Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů. `Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů. `Watch`<xref:Microsoft.Extensions.Primitives.IChangeToken>vrátí. Token pro změnu zpřístupňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Volá se, když se v zadaném řetězci cesty zjistí změny. Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit konstantní monitorování, <xref:System.Threading.Tasks.TaskCompletionSource`1> použijte (viz níže) nebo znovu `IChangeToken` vytvořte instance v reakci na změny.

V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách. Nastavte proměnnou `1` `true` prostředí na nebo k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat). `DOTNET_USE_POLLING_FILE_WATCHER`

## <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* . Určete skupiny souborů s těmito vzory. Dva zástupné znaky `*` jsou `**`a:

**`*`**  
Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou zakončeny `/` znaky `.` a v cestě k souboru.

**`**`**  
Odpovídá cokoli v různých úrovních adresáře. Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.

**Příklady vzorů glob**

**`directory/file.txt`**  
Odpovídá konkrétnímu souboru v konkrétním adresáři.

**`directory/*.txt`**  
Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.

**`directory/*/appsettings.json`**  
Vyhledá `appsettings.json` všechny soubory v adresářích přesně jednu úroveň pod adresářovou složkou.

**`directory/**/*.txt`**  
Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů. Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zpřístupňuje kořenový adresář obsahu aplikace a webové kořenové adresáře `IFileProvider` jako typy.
* [Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.
* [Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.
* Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souborů

Primární rozhraní je <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `IFileProvider`Zpřístupňuje metody pro:

* Získat informace o souboru<xref:Microsoft.Extensions.FileProviders.IFileInfo>().
* Získat informace o adresáři<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().
* Nastavte oznámení o změně (pomocí <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum

Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .

Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace poskytovatele souborů

K dispozici `IFileProvider` jsou tři implementace systému.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních. |
| [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

<xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Poskytuje přístup k fyzickému systému souborů. `PhysicalFileProvider`<xref:System.IO.File?displayProperty=fullName> používá typ (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytvoření a použití `PhysicalFileProvider` je pro `IFileProvider` vyžádání v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

Při vytvoření instance tohoto zprostředkovatele přímo se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele.

Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsahu adresáře a informací o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider``IFileProvider`je.
* `contents``IDirectoryContents`je.
* `fileInfo``IFileInfo`je.

Zprostředkovatele souboru lze použít k iterování adresáře určeného `applicationRoot` nebo voláním `GetFileInfo` k získání informací o souboru. Zprostředkovatel souboru nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

<xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Slouží k přístupu k souborům integrovaným v rámci sestavení. `ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.

Chcete-li vygenerovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`hodnotu. Zadejte soubory, které se mají [ &lt;vložit&gt;do EmbeddedResource](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které mají být vloženy do sestavení.

Ukázková aplikace vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení ke svému konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Umožňuje nastavit rozsah souborů na datum poslední úpravy.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Metody | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijímá volitelný `root` parametr relativní cesty. Zadejte obor <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> pro volání do těchto prostředků v zadané cestě. `root` |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijímá volitelný `root` parametr relativní cesty `lastModified` a parametr data (<xref:System.DateTimeOffset>). Datum v oboru datum poslední změny <xref:Microsoft.Extensions.FileProviders.IFileInfo> instancí vrácených <xref:Microsoft.Extensions.FileProviders.IFileProvider>. `lastModified` |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry. `manifestName` Představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

<xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Kombinuje`IFileProvider` instance a zpřístupňuje jedno rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.

V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledovat změny

Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů. `Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů. `Watch`<xref:Microsoft.Extensions.Primitives.IChangeToken>vrátí. Token pro změnu zpřístupňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Volá se, když se v zadaném řetězci cesty zjistí změny. Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit konstantní monitorování, <xref:System.Threading.Tasks.TaskCompletionSource`1> použijte (viz níže) nebo znovu `IChangeToken` vytvořte instance v reakci na změny.

V ukázkové aplikaci je aplikace konzoly *WatchConsole* nakonfigurovaná tak, aby zobrazila zprávu pokaždé, když se upraví textový soubor:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změnách. Nastavte proměnnou `1` `true` prostředí na nebo k dotazování systému souborů na změny každé čtyři sekundy (nedají se konfigurovat). `DOTNET_USE_POLLING_FILE_WATCHER`

## <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají vzory zástupných znaků označované jako *vzory glob (nebo expanze)* . Určete skupiny souborů s těmito vzory. Dva zástupné znaky `*` jsou `**`a:

**`*`**  
Odpovídá cokoli na úrovni aktuální složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou zakončeny `/` znaky `.` a v cestě k souboru.

**`**`**  
Odpovídá cokoli v různých úrovních adresáře. Dá se použít k rekurzivnímu spárování mnoha souborů v rámci hierarchie adresářů.

**Příklady vzorů glob**

**`directory/file.txt`**  
Odpovídá konkrétnímu souboru v konkrétním adresáři.

**`directory/*.txt`**  
Vyhledá všechny soubory s příponou *. txt* v konkrétním adresáři.

**`directory/*/appsettings.json`**  
Vyhledá `appsettings.json` všechny soubory v adresářích přesně jednu úroveň pod adresářovou složkou.

**`directory/**/*.txt`**  
Porovná všechny soubory s příponou *. txt* nalezené kdekoli ve složce *adresáře* .

::: moniker-end
