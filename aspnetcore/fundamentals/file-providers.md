---
title: Zprostředkovatelé souborů v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak ASP.NET core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751100"
---
# <a name="file-providers-in-aspnet-core"></a>Zprostředkovatelé souborů v ASP.NET jádru

Podle [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů. Zprostředkovatelé souborů se používají v celém ASP.NET core framework. Příklad:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>zveřejňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a kořen `IFileProvider` [webu](xref:fundamentals/index#web-root) jako typy.
* [Middleware statického souboru](xref:fundamentals/static-files) používá k vyhledání statických souborů zprostředkovatele souborů.
* [Razor](xref:mvc/views/razor) používá zprostředkovatele souborů k vyhledání stránek a zobrazení.
* Nástroje .NET Core používají zprostředkovatele souborů a glob vzory určit, které soubory mají být publikovány.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="file-provider-interfaces"></a>Rozhraní zprostředkovatele souborů

Primární rozhraní <xref:Microsoft.Extensions.FileProviders.IFileProvider>je . `IFileProvider`vystavuje metody:

* Získat informace<xref:Microsoft.Extensions.FileProviders.IFileInfo>o souboru ( ).
* Získat informace<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o adresáři ( ).
* Nastavte oznámení o změnách <xref:Microsoft.Extensions.Primitives.IChangeToken>(pomocí ).

`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum

Můžete číst ze souboru <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> pomocí metody.

Ukázková aplikace *FileProviderSample* ukazuje, jak nakonfigurovat zprostředkovatele souborů v aplikaci pro použití v `Startup.ConfigureServices` celé aplikaci prostřednictvím vkládání [závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace zprostředkovatele souborů

V následující tabulce jsou `IFileProvider`uvedeny implementace .

| Implementace | Popis |
| -------------- | ----------- |
| [Složený souborZprostředkovatel](#compositefileprovider) | Slouží k poskytnutí kombinovaného přístupu k souborům a adresářům od jednoho nebo více jiných poskytovatelů. |
| [Objekt ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Slouží k přístupu k souborům vloženým do sestavení. |
| [PhysicalFileProvider](#physicalfileprovider) | Slouží k přístupu k fyzickým souborům systému. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Poskytuje <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> přístup k fyzickému systému souborů. `PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického zprostředkovatele) a obory všechny cesty do adresáře a jeho podřízené položky. Toto obory brání přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytváření a `PhysicalFileProvider` používání je `IFileProvider` požádat v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).

Při vytváření instancí tohoto zprostředkovatele přímo je vyžadována absolutní cesta k adresáři, která slouží jako základní cesta pro všechny požadavky provedené pomocí zprostředkovatele. Glob vzory nejsou podporovány v cestě adresáře.

Následující kód ukazuje, `PhysicalFileProvider` jak získat obsah adresáře a informace o souborech:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

Typy v předchozím příkladu:

* `provider`je `IFileProvider`.
* `contents`je `IDirectoryContents`.
* `fileInfo`je `IFileInfo`.

Zprostředkovatel souborů lze itetovat prostřednictvím adresáře určeného `applicationRoot` nebo volání `GetFileInfo` získat informace o souboru. Glob vzory nemohou být předány `GetFileInfo` k metodě. Zprostředkovatel souborů nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace *FileProviderSample* vytvoří `Startup.ConfigureServices` zprostředkovatele <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>v metodě pomocí :

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>Objekt ManifestEmbeddedFileProvider

Slouží <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> k přístupu k souborům vloženým do sestavení. Používá `ManifestEmbeddedFileProvider` manifest zkompilovaný do sestavení k rekonstrukci původní cesty vložené soubory.

Chcete-li generovat manifest vložených souborů:

1. Přidejte balíček [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet do projektu.
1. Nastavte `<GenerateEmbeddedFilesManifest>` vlastnost `true`na . Určete soubory, které chcete vložit [ \<s>EmbeddedResource ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které chcete vložit do sestavy.

Ukázková aplikace *FileProviderSample* vytvoří `ManifestEmbeddedFileProvider` a předá aktuálně spuštěné sestavení svému konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Soubory oboru k datu poslední změny.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Přetížení | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijme volitelný `root` relativní parametr cesty. `root` Zadejte volání <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> oboru pro tyto prostředky v rámci zadané cesty. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijme volitelný `root` parametr relativní cesty `lastModified` a<xref:System.DateTimeOffset>parametr data ( ). Datum `lastModified` obory poslední změny datum <xref:Microsoft.Extensions.FileProviders.IFileInfo> pro instance <xref:Microsoft.Extensions.FileProviders.IFileProvider>vrácené . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijme volitelnou `root` relativní `lastModified` cestu, `manifestName` datum a parametry. Představuje `manifestName` název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>Složený souborZprostředkovatel

Kombinuje <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` instance, vystavuje jediné rozhraní pro práci se soubory z více zprostředkovatelů. Při vytváření `CompositeFileProvider`, předat `IFileProvider` jednu nebo více instancí jeho konstruktoru.

V ukázkové aplikaci *FileProviderSample* `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a a `CompositeFileProvider` poskytují soubory registrovanému v kontejneru služeb aplikace. V `Startup.ConfigureServices` metodě projektu se nachází následující kód:

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledujte změny

Metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> poskytuje scénář sledovat jeden nebo více souborů nebo adresářů pro změny. Metoda: `Watch`

* Přijme řetězec cesty k souboru, který může použít [glob vzory](#glob-patterns) k určení více souborů.
* Vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>hodnotu .

Výsledný token změny zveřejňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Vlastnost, která může být zkontrolována k určení, pokud došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Nazývá se, když jsou zjištěny změny zadaného řetězce cesty. Každý token změny volá pouze jeho přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit <xref:System.Threading.Tasks.TaskCompletionSource`1> konstantní monitorování, použijte `IChangeToken` (viz níže) nebo znovu vytvořte instance v reakci na změny.

Ukázková aplikace *WatchConsole* zapíše zprávu při každé změně souboru *TXT* v adresáři *TextFiles:*

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Některé systémy souborů, jako jsou kontejnery Dockeru a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změně. Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou `1` prostředí `true` na systém souborů nebo pro dotazování na změny každé čtyři sekundy (nelze konfigurovat).

### <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají zástupné vzory nazývané *glob (nebo globbing) vzory*. Určete skupiny souborů s těmito vzorky. Dva zástupné znaky `*` `**`jsou a :

**`*`**  
Odpovídá všemu na aktuální úrovni složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou `/` ukončeny a `.` znaky v cestě k souboru.

**`**`**  
Odpovídá všemu na více úrovních adresáře. Lze použít k rekurzivně shodovat mnoho souborů v hierarchii adresáře.

Následující tabulka obsahuje běžné příklady glob patterns.

|Vzor  |Popis  |
|---------|---------|
|`directory/file.txt`|Odpovídá určitému souboru v určitém adresáři.|
|`directory/*.txt`|Porovná všechny soubory s příponou *TXT* v určitém adresáři.|
|`directory/*/appsettings.json`|Odpovídá všem *souborům appsettings.json* v adresářích přesně o *jednu* úroveň pod adresářovou složkou.|
|`directory/**/*.txt`|Porovná všechny soubory s příponou *TXT,* která se nachází kdekoli ve složce *adresáře.*|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core abstraktů přístup k systému souborů pomocí zprostředkovatelů souborů. Zprostředkovatelé souborů se používají v celém rámci ASP.NET Core:

* <xref:Microsoft.Extensions.Hosting.IHostingEnvironment>zveřejňuje [kořen obsahu](xref:fundamentals/index#content-root) aplikace a kořen `IFileProvider` [webu](xref:fundamentals/index#web-root) jako typy.
* [Middleware statického souboru](xref:fundamentals/static-files) používá k vyhledání statických souborů zprostředkovatele souborů.
* [Razor](xref:mvc/views/razor) používá zprostředkovatele souborů k vyhledání stránek a zobrazení.
* Nástroje .NET Core používají zprostředkovatele souborů a glob vzory určit, které soubory mají být publikovány.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="file-provider-interfaces"></a>Rozhraní zprostředkovatele souborů

Primární rozhraní <xref:Microsoft.Extensions.FileProviders.IFileProvider>je . `IFileProvider`vystavuje metody:

* Získat informace<xref:Microsoft.Extensions.FileProviders.IFileInfo>o souboru ( ).
* Získat informace<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o adresáři ( ).
* Nastavte oznámení o změnách <xref:Microsoft.Extensions.Primitives.IChangeToken>(pomocí ).

`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(v bajtech)
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Datum

Ze souboru můžete číst pomocí metody [IFileInfo.CreateReadStream.](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)

Ukázková aplikace ukazuje, jak nakonfigurovat `Startup.ConfigureServices` zprostředkovatele souborů v aplikaci pro použití v celé aplikaci prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace zprostředkovatele souborů

K dispozici `IFileProvider` jsou tři implementace.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický zprostředkovatel se používá pro přístup k fyzickým souborům systému. |
| [Objekt ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Vložený zprostředkovatel manifestu se používá pro přístup k souborům vloženým v sestaveních. |
| [Složený souborZprostředkovatel](#compositefileprovider) | Složený zprostředkovatel se používá k poskytování kombinovaného přístupu k souborům a adresářům od jednoho nebo více jiných poskytovatelů. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Poskytuje <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> přístup k fyzickému systému souborů. `PhysicalFileProvider`používá <xref:System.IO.File?displayProperty=fullName> typ (pro fyzického zprostředkovatele) a obory všechny cesty do adresáře a jeho podřízené položky. Toto obory brání přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Nejběžnější scénář pro vytváření a `PhysicalFileProvider` používání je `IFileProvider` požádat v konstruktoru prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection).

Při vytváření instancí tohoto zprostředkovatele přímo je vyžadována cesta k adresáři, která slouží jako základní cesta pro všechny požadavky provedené pomocí zprostředkovatele.

Následující kód ukazuje, jak `PhysicalFileProvider` vytvořit a použít k získání obsahu adresáře a informace o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider`je `IFileProvider`.
* `contents`je `IDirectoryContents`.
* `fileInfo`je `IFileInfo`.

Zprostředkovatel souborů lze itetovat prostřednictvím adresáře určeného `applicationRoot` nebo volání `GetFileInfo` získat informace o souboru. Zprostředkovatel souborů nemá přístup mimo `applicationRoot` adresář.

Ukázková aplikace vytvoří zprostředkovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>Objekt ManifestEmbeddedFileProvider

Slouží <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> k přístupu k souborům vloženým do sestavení. Používá `ManifestEmbeddedFileProvider` manifest zkompilovaný do sestavení k rekonstrukci původní cesty vložené soubory.

Chcete-li generovat manifest vložených souborů, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost na `true`. Určete soubory, které chcete vložit pomocí [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Pomocí [vzorů glob](#glob-patterns) určete jeden nebo více souborů, které chcete vložit do sestavy.

Ukázková aplikace `ManifestEmbeddedFileProvider` vytvoří a předá aktuálně spuštěné sestavení svému konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Další přetížení umožňují:

* Zadejte relativní cestu k souboru.
* Soubory oboru k datu poslední změny.
* Pojmenujte vložený prostředek obsahující manifest vloženého souboru.

| Přetížení | Popis |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Přijme volitelný `root` relativní parametr cesty. `root` Zadejte volání <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> oboru pro tyto prostředky v rámci zadané cesty. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Přijme volitelný `root` parametr relativní cesty `lastModified` a<xref:System.DateTimeOffset>parametr data ( ). Datum `lastModified` obory poslední změny datum <xref:Microsoft.Extensions.FileProviders.IFileInfo> pro instance <xref:Microsoft.Extensions.FileProviders.IFileProvider>vrácené . |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Přijme volitelnou `root` relativní `lastModified` cestu, `manifestName` datum a parametry. Představuje `manifestName` název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>Složený souborZprostředkovatel

Kombinuje <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` instance, vystavuje jediné rozhraní pro práci se soubory z více zprostředkovatelů. Při vytváření `CompositeFileProvider`, předat `IFileProvider` jednu nebo více instancí jeho konstruktoru.

V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a a `CompositeFileProvider` poskytují soubory registrovanému v kontejneru služeb aplikace:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledujte změny

[Metoda IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) poskytuje scénář pro sledování jednoho nebo více souborů nebo adresářů pro změny. `Watch`přijme řetězec cesty, který může použít [glob patterns](#glob-patterns) k určení více souborů. `Watch`vrátí <xref:Microsoft.Extensions.Primitives.IChangeToken>. Token změny zveřejňuje:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Vlastnost, která může být zkontrolována k určení, pokud došlo ke změně.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Nazývá se, když jsou zjištěny změny zadaného řetězce cesty. Každý token změny volá pouze jeho přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit <xref:System.Threading.Tasks.TaskCompletionSource`1> konstantní monitorování, použijte `IChangeToken` (viz níže) nebo znovu vytvořte instance v reakci na změny.

V ukázkové aplikaci je konzolová aplikace *WatchConsole* nakonfigurována tak, aby při každé změně textového souboru zobrazovala zprávu:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Dockeru a sdílené síťové složky, nemusí spolehlivě odesílat oznámení o změně. Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou `1` prostředí `true` na systém souborů nebo pro dotazování na změny každé čtyři sekundy (nelze konfigurovat).

## <a name="glob-patterns"></a>Glob vzory

Cesty systému souborů používají zástupné vzory nazývané *glob (nebo globbing) vzory*. Určete skupiny souborů s těmito vzorky. Dva zástupné znaky `*` `**`jsou a :

**`*`**  
Odpovídá všemu na aktuální úrovni složky, libovolnému názvu souboru nebo libovolné příponě souboru. Shody jsou `/` ukončeny a `.` znaky v cestě k souboru.

**`**`**  
Odpovídá všemu na více úrovních adresáře. Lze použít k rekurzivně shodovat mnoho souborů v hierarchii adresáře.

**Příklady globvzor**

**`directory/file.txt`**  
Odpovídá určitému souboru v určitém adresáři.

**`directory/*.txt`**  
Porovná všechny soubory s příponou *TXT* v určitém adresáři.

**`directory/*/appsettings.json`**  
Odpovídá `appsettings.json` všem souborům v adresářích přesně o *jednu* úroveň pod adresářovou složkou.

**`directory/**/*.txt`**  
Porovnává všechny soubory s příponou *TXT,* která se nachází kdekoli ve složce *adresáře.*

::: moniker-end
