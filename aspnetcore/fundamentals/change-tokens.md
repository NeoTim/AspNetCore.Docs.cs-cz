---
title: Zjištění změn s tokeny změn v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat tokeny změn ke sledování změn.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64900597"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Zjištění změn s tokeny změn v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

A *změnit token* je použít ke sledování změn stavební blok pro obecné účely, nízké úrovně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken rozhraní

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) šíří oznámení, že došlo ke změně. `IChangeToken` v se nachází [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) oboru názvů. Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), odkaz [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet v souboru projektu.

`IChangeToken` má dvě vlastnosti:

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) označují, pokud token proaktivně vyvolá zpětná volání. Pokud `ActiveChangedCallbacks` je nastavena na `false`zpětné volání se nikdy nevolá, a aplikace se musí dotazovat `HasChanged` změny. Je také možné pro token nikdy zrušit, pokud žádné změny nebo je uvolněn nebo zakázané základní naslouchací proces změn.
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) získá hodnotu označující, pokud došlo ke změně.

Rozhraní obsahuje jednu metodu [RegisterChangeCallback (akce&lt;objekt&gt;, objekt)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), který zaregistruje zpětné volání, která je vyvolána při změně tokenu. `HasChanged` musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>Třída ChangeToken

`ChangeToken` slouží k šíření oznámení, že došlo ke změně statické třídě. `ChangeToken` v se nachází [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) oboru názvů. Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), odkaz [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet v souboru projektu.

`ChangeToken` [OnChange (Func&lt;IChangeToken&gt;, akce)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) metoda registrů `Action` volat při každé změně tokenu:

* `Func<IChangeToken>` vytvoří token.
* `Action` je volána při změně tokenu.

`ChangeToken` má [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, akce&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) přetížení, které přijímá Další `TState` parametr, který je předán do tokenu příjemce `Action`.

`OnChange` Vrátí [IDisposable](/dotnet/api/system.idisposable). Volání [Dispose](/dotnet/api/system.idisposable.dispose) token z naslouchání pro další změny se zastaví a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad používá změna tokenů v ASP.NET Core

Změna tokenů se používají v viditelného oblastech sledování změny objektů ASP.NET Core:

* Pro sledování změn souborů, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)společnosti [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) metoda vytvoří `IChangeToken` určité soubory nebo složky, které chcete sledovat.
* `IChangeToken` tokeny lze přidat na položky mezipaměti k aktivaci odsunuté mezipaměť při změně.
* Pro `TOptions` změní výchozí [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) provádění [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) má přetížení, která přijímá jeden nebo více [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)instancí. Vrátí každá instance `IChangeToken` pro registraci zpětné volání oznámení změn pro možnosti sledování změn.

## <a name="monitoring-for-configuration-changes"></a>Sledování změn konfigurace

Ve výchozím nastavení, použijte šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.JSON*, a *appsettings. Production.JSON*) k načtení nastavení konfigurace aplikace.

Tyto soubory jsou nakonfigurováni pomocí [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) rozšiřující metody na [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) , který přijme `reloadOnChange` parametr (ASP.NET Core 1.1 a novější). `reloadOnChange` Označuje, pokud byste znovu načíst konfiguraci na změny v souboru. Toto nastavení najdete v článku [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) Komfortní metoda [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

Konfigurace založená na souboru je reprezentován [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource). `FileConfigurationSource` používá [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) sledování souborů.

Ve výchozím nastavení `IFileMonitor` je poskytován [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), který používá [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) monitorovat změny v konfiguraci souboru.

Ukázková aplikace předvádí dvě implementace pro sledování změn konfigurace. Pokud *appsettings.json* změny souborů nebo prostředí verze souboru se změní, každá implementace spustí vlastní kód. Ukázková aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více tokenů zpětných volání pro změnu souboru v jediné konfiguraci. Ukázková implementace chrání před tímto problémem kontrolou hodnoty hash souborů v konfiguračních souborech. Kontrolu hodnoty hash souborů zajišťuje, že aspoň jeden konfigurační soubory se změnila před spuštěním vlastního kódu. Ukázka používá algoritmus hash SHA1 souboru (*Utilities/Utilities.cs*):

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   Opakování je implementováno s exponenciální regresní. Zkuste znovu je k dispozici, protože zamykání souborů může dojít, který brání dočasně computingu nová hodnota hash na jeden ze souborů.

### <a name="simple-startup-change-token"></a>Jednoduché spouštění změnit token

Zaregistrovat token příjemce `Action` zpětné volání pro upozornění na změnu na token znovu načíst konfiguraci (*Startup.cs*):

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje tento token. Zpětné volání je `InvokeChanged` metody:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

`state` Zpětného volání, které se používá a zajistěte tak předání `IHostingEnvironment`. To je užitečné k určení správné *appsettings* konfigurační soubor JSON pro monitorování, *appsettings.&lt; Prostředí&gt;.json*. Hodnoty hash souboru se používají k zabránění `WriteConsole` příkaz spouštění více než jednou z důvodu více tokenů zpětná volání, pokud konfigurační soubor byl změněn pouze jednou.

Tento systém běží, dokud aplikace běží a nedá se zakázat uživatelem.

### <a name="monitoring-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služba

Implementuje vzorku:

* Základní spuštění token monitorování.
* Monitorování jako služba.
* Mechanismus pro povolení a zákaz monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní (*Extensions/ConfigurationMonitor.cs*):

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor třídy implementované, `ConfigurationMonitor`, zaregistruje zpětné volání oznámení změn:

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje tento token. `InvokeChanged` je metoda zpětného volání. `state` v tomto případě se odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke sledování stavu. Se používají dvě vlastnosti:

* `MonitoringEnabled` Určuje zpětné volání vhodné spustit své vlastní kód.
* `CurrentState` Popisuje aktuální monitorování stavu pro použití v uživatelském rozhraní.

`InvokeChanged` Metoda je podobná dřívější přístup, s výjimkou, že:

* Spuštěním jeho kódu, není-li `MonitoringEnabled` je `true`.
* Poznámky k aktuální `state` v jeho `WriteConsole` výstup.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je zaregistrováno jako služba v `ConfigureServices` z *Startup.cs*:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

Indexovou stránku nabízí uživatelský ovládací prvek v konfiguraci monitorování. Instance `IConfigurationMonitor` se vloží do `IndexModel`:

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

Tlačítko povolí nebo zakáže monitorování:

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` je spuštěn, je zapnuté monitorování, a aktuální stav je vymazán. Když `OnPostStopMonitoring` se aktivuje, monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování neprobíhá.

## <a name="monitoring-cached-file-changes"></a>Sledování změn souborů v mezipaměti

Obsah souboru může být uložený v mezipaměti v paměti pomocí [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). Ukládání do mezipaměti v paměti je popsána v [ukládat do mezipaměti v paměti](xref:performance/caching/memory) tématu. Bez nutnosti převádět další kroky, jako je například implementace je popsáno níže, *zastaralé* (zastaralé) vrátí data z mezipaměti zdrojová data mění.

Bez zohlednění stav uložený v mezipaměti zdrojového souboru při obnovování [klouzavé vypršení platnosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) období vede k zastaralá data z mezipaměti. Každý požadavek pro data se tato možnost obnoví klouzavou dobu vypršení platnosti, ale soubor nikdy znovu načten do mezipaměti. Žádné aplikace funkce, které používají obsah uložený v mezipaměti v souboru se mohou pravděpodobně příjem starý obsah.

Použití tokenů změn v souboru ukládání do mezipaměti scénář brání zastaralých souborů obsahu v mezipaměti. Ukázková aplikace předvádí implementace přístupu.

Ukázka používá `GetFileContent` na:

* Vrátí obsah souboru.
* Implementace algoritmu opakování s exponenciální regresní pro případy, kde soubor zámku se dočasně nedaří soubor čtená.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

A `FileService` je vytvořená za účelem zpracování vyhledávání v mezipaměti souborů. `GetFileContent` Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátí řízení volajícímu (*Services/FileService.cs*).

Pokud obsah uložený v mezipaměti není nalezena pomocí klíče mezipaměti, budou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Změnit token pochází od zprostředkovatele souborů s [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch). Zpětné volání token, který se aktivuje, když se upraví soubor.
1. Obsah souboru je uložené v mezipaměti s [klouzavé vypršení platnosti](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) období. Změnit token je připojen pomocí [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) vyřazení položka mezipaměti, pokud se soubor změní, zatímco je tam uložena.

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

`FileService` Je registrován v kontejneru služby spolu s paměti služby ukládání do mezipaměti (*Startup.cs*):

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Třída CompositeChangeToken

Představující jeden nebo více `IChangeToken` instance do jednoho objektu, používají [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) třídy.

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        { 
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

`HasChanged` na složený token sestavy `true` Pokud žádný token `HasChanged` je `true`. `ActiveChangeCallbacks` na složený token sestavy `true` Pokud žádný token `ActiveChangeCallbacks` je `true`. Pokud dojde k události více souběžných změny, zpětné volání složené změn je volána přesně jednou.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
