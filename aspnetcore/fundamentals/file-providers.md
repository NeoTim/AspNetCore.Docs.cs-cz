---
title: Zprostředkovatelé souborů v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 2ce40ea0d576d08a6b42c3eb6693754f2a0bddce
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809218"
---
# <a name="file-providers-in-aspnet-core"></a>Zprostředkovatelé souborů v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/) a [Luke Latham](https://github.com/guardrex)

ASP.NET Core abstrahuje přístupu k systému souborů prostřednictvím zprostředkovatelé souborů. Zprostředkovatelé souborů se používají v rozhraní ASP.NET Core:

* [IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) zpřístupní obsah kořenové a kořenový adresář webové jako aplikace `IFileProvider` typy.
* [Middleware statické soubory](xref:fundamentals/static-files) zprostředkovatelé souborů používá k vyhledání statické soubory.
* [Razor](xref:mvc/views/razor) zprostředkovatelé souborů používá k nalezení stránek a zobrazení.
* Nástroje pro .NET core používá k určení, které soubory by se měly zveřejňovat zprostředkovatelé souborů a vzory glob.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Rozhraní poskytovatele souboru

Primární rozhraní se ale [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). `IFileProvider` poskytuje metody pro:

* Získání informací o souboru ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).
* Získat informace o adresáři ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).
* Nastavení oznámení o změnách (pomocí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).

`IFileInfo` poskytuje metody a vlastnosti pro práci se soubory:

* [Existuje](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [IsDirectory](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [Název](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* [Délka](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (v bajtech)
* [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) datum

Můžete číst ze souboru pomocí [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) metody.

Ukázková aplikace předvádí, jak nakonfigurovat poskytovatele souborů v `Startup.ConfigureServices` pro používání v rámci aplikace prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Implementace zprostředkovatele souborů

Tři implementace `IFileProvider` jsou k dispozici.

| Implementace | Popis |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Fyzické zprostředkovatele se používá pro přístup k fyzické soubory v systému. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Poskytovateli vloženého manifestu se používá pro přístup k souborům, které jsou součástí sestavení. |
| [CompositeFileProvider](#compositefileprovider) | Složený zprostředkovatele slouží k poskytování kombinovaný přístup k souborů a adresářů od jiných zprostředkovatelů. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) poskytuje přístup k fyzické systému souborů. `PhysicalFileProvider` používá [System.IO.File](/dotnet/api/system.io.file) typu (u fyzického provider) a všechny cesty k adresáři a jeho podřízené obory. Tento rozsah brání v přístupu k systému souborů mimo zadaný adresář a jejích potomků. Při vytváření instance tohoto zprostředkovatele, cestu k adresáři je povinný a slouží jako základní cesta pro všechny požadavky vytvořené pomocí zprostředkovatele. Můžete vytvořit instanci `PhysicalFileProvider` poskytovatele přímo, nebo můžete požádat o `IFileProvider` v konstruktoru prostřednictvím [injektáž závislostí](xref:fundamentals/dependency-injection).

**Statické typy**

Následující kód ukazuje, jak vytvořit `PhysicalFileProvider` a použít ho k získání obsah adresáře a informace o souboru:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typy v předchozím příkladu:

* `provider` je `IFileProvider`.
* `contents` je `IDirectoryContents`.
* `fileInfo` je `IFileInfo`.

Poskytovatel souboru lze použít k iteraci v rámci adresáře zadaného parametrem `applicationRoot` nebo volání `GetFileInfo` k získání informací souboru. Poskytovatel soubor nemá přístup mimo `applicationRoot` adresáře.

Ukázková aplikace vytvoří poskytovatel aplikace `Startup.ConfigureServices` pomocí [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

**Získat typy zprostředkovatele souborů pomocí vkládání závislostí**

Vložit do jakéhokoli konstruktoru třídy zprostředkovatele a přiřaďte ho do místního pole. Použijte pole v rámci metod tříd pro přístup k souborům.

V ukázkové aplikaci `IndexModel` přijímá třídy `IFileProvider` instance získat obsah adresáře pro základní cesty aplikace.

*Pages/Index.cshtml.cs*:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

`IDirectoryContents` Jsou vstupní stránky.

*Pages/Index.cshtml*:

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) se používá pro přístup k souborům, které jsou vložené v rámci sestavení. `ManifestEmbeddedFileProvider` Manifestu kompilovány do sestavení používá k rekonstrukci původní cesty vložených souborů.

Chcete-li vygenerovat manifest vložené soubory, nastavte `<GenerateEmbeddedFilesManifest>` vlastnost `true`. Zadejte soubory, které chcete vložit s [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Použití [glob vzory](#glob-patterns) určit jeden nebo více souborů určených pro vložení do sestavení.

Vytvoří ukázkovou aplikaci `ManifestEmbeddedFileProvider` a předává právě spuštěné sestavení do konstruktoru.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

Další přetížení umožňují:

* Zadejte relativní cestou k souboru.
* Obor soubory na datum poslední změny.
* Název vloženého zdroje obsahujícího vložený soubor manifestu.

| přetížení | Popis |
| -------- | ----------- |
| [ManifestEmbeddedFileProvider(Assembly, String)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | Přijímá volitelný `root` parametr relativní cesty. Zadejte `root` do oboru volání [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) k těmto prostředkům na zadané cestě. |
| [ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | Přijímá volitelný `root` parametr relativní cesty a `lastModified` datum ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parametru. `lastModified` Obory datum poslední změny pro datum [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instancí vrácených [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). |
| [ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | Přijímá volitelný `root` relativní cesta `lastModified` data, a `manifestName` parametry. `manifestName` Představuje název vloženého zdroje obsahujícího manifest. |

### <a name="compositefileprovider"></a>CompositeFileProvider

[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombinuje `IFileProvider` instance vystavení jednotné rozhraní pro práci se soubory od více poskytovatelů. Při vytváření `CompositeFileProvider`, předejte jeden nebo více `IFileProvider` instance konstruktoru.

V ukázkové aplikaci `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` zadejte soubory `CompositeFileProvider` zaregistrovaný v kontejneru aplikace služby:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Sledování změn

[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) metoda poskytuje scénář podívejte se na jeden nebo více souborů nebo adresářů pro změny. `Watch` přijímá řetězec cesty, které můžete použít [glob vzory](#glob-patterns) zadat více souborů. `Watch` Vrátí [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken). Token zpřístupňuje změny:

* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): Vlastnost, která může být kontrolována k určení, pokud došlo ke změně.
* [RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Volá se, když se zjistí změny na zadané cestě řetězec. Každý token změnit jen volá jeho přidružené zpětné volání v reakci na jediné změny. Chcete-li povolit konstantní monitorování, použijte [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (viz dole) nebo znovu vytvořit `IChangeToken` instance v reakci na změny.

V ukázkové aplikaci *WatchConsole* konzoly aplikace je nakonfigurovaná pro zobrazení zprávy, když se změní textového souboru:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Některé systémy souborů, jako jsou kontejnery Docker a sdílené síťové složky a nemusí spolehlivě posílat oznámení o změnách. Nastavte `DOTNET_USE_POLLING_FILE_WATCHER` proměnnou prostředí, aby `1` nebo `true` k dotazování systému souborů pro změny každé čtyři sekundy (nejde konfigurovat).

## <a name="glob-patterns"></a>Vzory glob

Volat jeden vzor zástupných znaků použít systémové cesty k souborům *glob (nebo podpory zástupných znaků) vzory*. Tyto vzory zadejte skupiny souborů. Dva zástupné znaky jsou `*` a `**`:

**`*`**  
Na aktuální úrovni složky, některý název souboru nebo libovolnou příponou odpovídá úplně všechno. Shody jsou ukončeny `/` a `.` znaky v cestě k souboru.

**`**`**  
Napříč několika úrovněmi adresáře odpovídá úplně všechno. Můžete použít k rekurzivnímu shodovat s mnoha soubory v hierarchii adresářů.

**Příklady glob vzor**

**`directory/file.txt`**  
Odpovídá konkrétní soubor v konkrétní adresář.

**`directory/*.txt`**  
Vyhledá všechny soubory s *.txt* rozšíření v konkrétní adresář.

**`directory/*/appsettings.json`**  
Odpovídá všem `appsettings.json` soubory v adresářích přesně jednu úroveň níže *directory* složky.

**`directory/**/*.txt`**  
Vyhledá všechny soubory s *.txt* rozšíření najít kdekoli v rámci *directory* složky.
