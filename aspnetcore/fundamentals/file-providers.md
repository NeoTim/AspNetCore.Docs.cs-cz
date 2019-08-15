---
title: Poskytovatelé souborů v ASP.NET Core
author: guardrex
description: Přečtěte si, jak ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: b93b2df7fad7c173f43ad69aec865f09de6c9c34
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487580"
---
# <a name="file-providers-in-aspnet-core"></a>Poskytovatelé souborů v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)

ASP.NET Core k abstrakci přístupu k systému souborů prostřednictvím použití poskytovatelů souborů. Poskytovatelé souborů se používají v rámci ASP.NET Coreho rozhraní:

* [IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) vystaví kořenový adresář obsahu aplikace a webový kořenový `IFileProvider` adresář jako typy.
* [Middleware statických souborů](xref:fundamentals/static-files) používá k vyhledání statických souborů poskytovatele souborů.
* [Razor](xref:mvc/views/razor) používá k vyhledání stránek a zobrazení poskytovatele souborů.
* Nástroje .NET Core využívají poskytovatele souborů a vzory glob k určení, které soubory se mají publikovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souborů

Primární rozhraní je [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). `IFileProvider`Zpřístupňuje metody pro:

* Získat informace o souboru ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).
* Získat informace o adresáři ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).
* Nastavte oznámení o změně (pomocí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).

`IFileInfo`poskytuje metody a vlastnosti pro práci se soubory:

* [Neexistuje](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [Adresář](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [Název](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* [Délka](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (v bajtech)
* Datum [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)

Můžete číst ze souboru pomocí metody [IFileInfo. CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) .

Ukázková aplikace ukazuje, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro použití v rámci aplikace přes [vkládání závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace poskytovatele souborů

K dispozici `IFileProvider` jsou tři implementace systému.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzický poskytovatel se používá pro přístup k fyzickým souborům systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Zprostředkovatel manifestu Embedded se používá pro přístup k souborům integrovaným v sestaveních. |
| [CompositeFileProvider](#compositefileprovider) | Složený poskytovatel se používá k poskytnutí kombinovaného přístupu k souborům a adresářům z jednoho nebo více poskytovatelů. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) poskytuje přístup k fyzickému systému souborů. `PhysicalFileProvider`používá typ [System. IO. File](/dotnet/api/system.io.file) (pro fyzického poskytovatele) a obory všech cest k adresáři a jeho podřízeným položkám. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jeho podřízené položky. Při vytváření instance tohoto poskytovatele se vyžaduje cesta k adresáři a slouží jako základní cesta pro všechny požadavky vytvořené pomocí poskytovatele. Můžete vytvořit instanci `PhysicalFileProvider` poskytovatele přímo nebo si můžete `IFileProvider` vyžádat v konstruktoru prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection).

**Statické typy**

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

Ukázková aplikace vytvoří poskytovatele ve `Startup.ConfigureServices` třídě aplikace pomocí [IHostingEnvironment. ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

**Získání typů poskytovatele souborů pomocí injektáže závislosti**

Vložit poskytovatele do libovolného konstruktoru třídy a přiřadit ho k místnímu poli. Pro přístup k souborům použijte pole v rámci metod třídy.

V ukázkové aplikaci `IndexModel` třída `IFileProvider` obdrží instanci, která získá obsah adresáře pro základní cestu aplikace.

*Pages/Index.cshtml.cs*:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

Na `IDirectoryContents` stránce jsou iterace.

*Pages/index. cshtml*:

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) se používá pro přístup k souborům integrovaným v rámci sestavení. `ManifestEmbeddedFileProvider` Používá manifest kompilovaný do sestavení k rekonstrukci původních cest vložených souborů.

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
| [ManifestEmbeddedFileProvider (sestavení, řetězec)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | Přijímá volitelný `root` parametr relativní cesty. Určete rozsah volání, která se GetDirectoryContents na tyto prostředky v zadané cestě. [](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) `root` |
| [ManifestEmbeddedFileProvider (sestavení, řetězec, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | Přijímá parametr volitelné `root` relativní cesty `lastModified` a parametr Date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)). Datum v oboru datum poslední změny pro instance [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) , které vrátí IFileProvider. [](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) `lastModified` |
| [ManifestEmbeddedFileProvider (sestavení, řetězec, řetězec, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | Přijímá volitelnou `root` relativní cestu, `lastModified` datum a `manifestName` parametry. `manifestName` Představuje název vloženého prostředku obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombinuje `IFileProvider` instance a zveřejňuje jedno rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`, předejte do konstruktoru jednu `IFileProvider` nebo více instancí.

V ukázkové aplikaci `PhysicalFileProvider` `ManifestEmbeddedFileProvider` a poskytuje soubory pro `CompositeFileProvider` registraci v kontejneru služby aplikace:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledovat změny

Metoda [IFileProvider. Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) nabízí scénář, jak sledovat změny jednoho nebo více souborů nebo adresářů. `Watch`přijímá řetězec cesty, který může používat [vzory glob](#glob-patterns) k určení více souborů. `Watch`Vrátí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken). Token pro změnu zpřístupňuje:

* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Vlastnost, která se dá zkontrolovat, aby se zjistilo, jestli došlo ke změně.
* [RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Volá se, když se v zadaném řetězci cesty zjistí změny. Každý token změny volá pouze své přidružené zpětné volání v reakci na jednu změnu. Chcete-li povolit stálé monitorování, použijte [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (zobrazený níže) nebo `IChangeToken` znovu vytvořte instance v reakci na změny.

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
