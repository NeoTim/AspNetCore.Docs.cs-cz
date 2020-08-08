---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: rick-anderson
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: f52b210b33cd319e36570a1f731bb02a86189a96
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017477"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Zjišťovat změny pomocí tokenů změn v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.

`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged`musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`se volá, když se změní token.

Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .

`OnChange`Vrátí <xref:System.IDisposable> . Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.
* Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí. Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*a *appsettings.Production.json*) k načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr. `reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace. Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host> metodě usnadnění <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> . `FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.

Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

`state`Zpětné volání se používá k předání do `IWebHostEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appsettings.Development.jsv* případě, kdy ve vývojovém prostředí). Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. `state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.
* `CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .
* Vypíše aktuální `state` výstup do `WriteConsole` výstupu.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena do `IndexModel` .

*Pages/index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže. Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` :

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti. `GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent` .
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace. `IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci `IWebHostEnvironment.ContentRootPath` . `filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.

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

`HasChanged`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` . `ActiveChangeCallbacks`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` . Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je nastaveno na `false` , zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>přijímá hodnotu, která indikuje, jestli došlo ke změně.

`IChangeToken`Rozhraní zahrnuje metodu [RegisterChangeCallback (Action \<Object> , Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged`musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken`nachází se v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

Metoda [ChangeToken. při změně (Func \<IChangeToken> , Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje, `Action` aby se volala při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`se volá, když se změní token.

Přetížení [ChangeToken.-Change \<TState> (Func \<IChangeToken> , Action \<TState> , TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přebírá další `TState` parametr, který se předává do příjemce tokenu `Action` .

`OnChange`Vrátí <xref:System.IDisposable> . Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do záznamů mezipaměti a aktivovat tak vyřazení mezipaměti při změně.
* Pro `TOptions` změny, výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace pro <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, které přijímá jednu nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí. Každá instance vrátí `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*a *appsettings.Production.json*) k načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , která přijímá `reloadOnChange` parametr. `reloadOnChange`Určuje, zda má být při změnách souborů znovu načtena konfigurace. Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost> metodě usnadnění <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována pomocí <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> . `FileConfigurationSource`<xref:Microsoft.Extensions.FileProviders.IFileProvider>Nástroj používá k monitorování souborů.

Ve výchozím nastavení `IFileMonitor` je k dispozici pomocí <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> , který <xref:System.IO.FileSystemWatcher> Nástroj používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód &mdash; . Ukázková aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více zpětných volání tokenu pro jednu změnu konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte zpětné volání příjemce tokenu `Action` pro oznámení změn do tokenu opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

`state`Zpětné volání se používá k předání do `IHostingEnvironment` , což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appsettings.Development.jsv* případě, kdy ve vývojovém prostředí). Hodnoty hash souborů se používají k zabránění `WriteConsole` spuštění příkazu vícekrát v důsledku zpětného volání s více tokeny, když je konfigurační soubor změněn pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy, `ConfigurationMonitor` , zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. `state`V této instanci je odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled`: Určuje, zda zpětné volání má spustit vlastní kód.
* `CurrentState`: Popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

`InvokeChanged`Metoda je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód `MonitoringEnabled` , pokud není `true` .
* Vypíše aktuální `state` výstup do `WriteConsole` výstupu.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrovaná jako služba v `Startup.ConfigureServices` :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena do `IndexModel` .

*Pages/index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Nástroj Configuration monitor ( `_monitor` ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` se aktivuje, monitorování je povolené a aktuální stav se vymaže. Když `OnPostStopMonitoring` se aktivuje, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` :

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti. `GetFileContent`Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent` .
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace. [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazatele myši v aplikaci <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> . `filePath`Je získaný pomocí [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService`Je zaregistrován v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V metodě stránky indexu `OnGet` (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo více `IChangeToken` instancí v jednom objektu použijte <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídu.

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

`HasChanged`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `HasChanged` `true` . `ActiveChangeCallbacks`v sestavách složeného tokenu, `true` Pokud je nějaký reprezentovaný token `ActiveChangeCallbacks` `true` . Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
