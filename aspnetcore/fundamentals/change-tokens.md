---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: guardrex
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/27/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 86cde7b60f5c398fc6bb215b593643c05565cf3c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384712"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Zjišťovat změny pomocí tokenů změn v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je nastaveno na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.

Rozhraní zahrnuje metodu [RegisterChangeCallback (objekt akce\<>, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `IChangeToken` `HasChanged`musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` , aby se volala při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`se volá, když se změní token.

Přetížení [ChangeToken\<. TState >\<(Func IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce `Action` tokenu. .

`OnChange`<xref:System.IDisposable>vrátí. Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů vytvoří <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` metoda pro zadané soubory nebo složky, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.
* Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> pro má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí. Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , která přijímá `reloadOnChange` parametr. `reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace. Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host> metodě <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>usnadnění:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource`Nástroj <xref:Microsoft.Extensions.FileProviders.IFileProvider> používá k monitorování souborů.

Ve výchozím nastavení `IFileMonitor` je k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pomocí, který nástroj <xref:System.IO.FileSystemWatcher> používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;. Ukázková aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte zpětné `Action` volání příjemce tokenu pro oznámení změn do tokenu opětovného načtení konfigurace.

V `Startup.Configure`nástroji:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Zpětné volání se používá k předání `IWebHostEnvironment`do, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například appSettings. `state`  *Vývoj. JSON* při ve vývojovém prostředí. Hodnoty hash souborů se používají k zabránění spuštění `WriteConsole` příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy `ConfigurationMonitor`,, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. V této instanci je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. `state` Používají se dvě vlastnosti:

* `MonitoringEnabled`&ndash; Určuje, zda zpětné volání má spustit vlastní kód.
* `CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

`InvokeChanged` Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, `MonitoringEnabled` Pokud `true`není.
* Vypíše aktuální `state` `WriteConsole` výstup do výstupu.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena `IndexModel`do.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Nástroj Configuration monitor (`_monitor`) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže. Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` :

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří `FileService` se pro zpracování vyhledávání souborů uložených v mezipaměti. Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*). `GetFileContent`

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v kořenu obsahu aplikace. `IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v `IWebHostEnvironment.ContentRootPath`aplikaci. Je získaný pomocí [IFileInfo. PhysicalPath.](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) `filePath`

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`nástroji:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V `OnGet` metodě stránky indexu (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo `IChangeToken` více instancí v jednom objektu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> použijte třídu.

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

`HasChanged`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `HasChanged` . `ActiveChangeCallbacks`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `ActiveChangeCallbacks` . Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je nastaveno na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.

Rozhraní zahrnuje metodu [RegisterChangeCallback (objekt akce\<>, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `IChangeToken` `HasChanged`musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken`nachází se <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> v oboru názvů. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` , aby se volala při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`se volá, když se změní token.

Přetížení [ChangeToken\<. TState >\<(Func IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce `Action` tokenu. .

`OnChange`<xref:System.IDisposable>vrátí. Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů vytvoří <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> `IChangeToken` metoda pro zadané soubory nebo složky, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.
* Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> pro má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí. Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) , která přijímá `reloadOnChange` parametr. `reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace. Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost> metodě <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>usnadnění:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource`Nástroj <xref:Microsoft.Extensions.FileProviders.IFileProvider> používá k monitorování souborů.

Ve výchozím nastavení `IFileMonitor` je k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>pomocí, který nástroj <xref:System.IO.FileSystemWatcher> používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;. Ukázková aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte zpětné `Action` volání příjemce tokenu pro oznámení změn do tokenu opětovného načtení konfigurace.

V `Startup.Configure`nástroji:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Zpětné volání se používá k předání `IHostingEnvironment`do, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například appSettings. `state`  *Vývoj. JSON* při ve vývojovém prostředí. Hodnoty hash souborů se používají k zabránění spuštění `WriteConsole` příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy `ConfigurationMonitor`,, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. V této instanci je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. `state` Používají se dvě vlastnosti:

* `MonitoringEnabled`&ndash; Určuje, zda zpětné volání má spustit vlastní kód.
* `CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

`InvokeChanged` Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, `MonitoringEnabled` Pokud `true`není.
* Vypíše aktuální `state` `WriteConsole` výstup do výstupu.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena `IndexModel`do.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Nástroj Configuration monitor (`_monitor`) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže. Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` :

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří `FileService` se pro zpracování vyhledávání souborů uložených v mezipaměti. Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*). `GetFileContent`

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v kořenu obsahu aplikace. [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>v aplikaci. Je získaný pomocí [IFileInfo. PhysicalPath.](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) `filePath`

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`nástroji:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V `OnGet` metodě stránky indexu (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo `IChangeToken` více instancí v jednom objektu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> použijte třídu.

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

`HasChanged`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `HasChanged` . `ActiveChangeCallbacks`v sestavách `true` složeného tokenu, pokud `true`je nějaký reprezentovaný token `ActiveChangeCallbacks` . Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
