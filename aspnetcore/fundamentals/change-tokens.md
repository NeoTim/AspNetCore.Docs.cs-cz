---
title: Zjišťovat změny pomocí tokenů změn v ASP.NET Core
author: rick-anderson
description: Naučte se používat změny tokenů ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656343"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Zjišťovat změny pomocí tokenů změn v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, ke kterým došlo ke změně. `IChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

`IChangeToken` má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> určuje, zda token proaktivně vyvolává zpětná volání. Pokud je `ActiveChangedCallbacks` nastavené na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.

Rozhraní `IChangeToken` zahrnuje metodu [RegisterChangeCallback (Action\<object >, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged` musí být nastaveno před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) se implicitně poskytuje pro aplikace ASP.NET Core.

Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` pro volání při změně tokenu:

* `Func<IChangeToken>` vytváří token.
* `Action` se volá při změně tokenu.

Přetížení [ChangeToken\<. > TState (Func\<IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) převezme další parametr `TState`, který se předává do uživatele tokenu `Action`.

`OnChange` vrátí <xref:System.IDisposable>. Volání <xref:System.IDisposable.Dispose*> zastaví naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> metoda vytvoří `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.
* tokeny `IChangeToken` lze přidat do záznamů mezipaměti pro aktivaci vyřazení mezipaměti při změně.
* U `TOptions` změn má výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> přetížení, které přijímá jednu nebo více instancí <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Každá instance vrací `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, která přijímá parametr `reloadOnChange`. `reloadOnChange` určuje, zda má být konfigurace znovu načtena při změně souboru. Toto nastavení se zobrazí v <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*><xref:Microsoft.Extensions.Hosting.Host> pohodlí:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` používá ke sledování souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider>.

Ve výchozím nastavení je `IFileMonitor` k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, která používá <xref:System.IO.FileSystemWatcher> ke sledování změn konfiguračních souborů.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;ukázková aplikace zapíše zprávu do konzoly.

`FileSystemWatcher` konfiguračního souboru může aktivovat více zpětných volání tokenů pro jednotlivé změny konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte příjemce tokenu `Action` zpětného volání pro oznámení o změně v tokenu opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje token. Zpětné volání je metoda `InvokeChanged`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

`state` zpětného volání se používá k předání do `IWebHostEnvironment`, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí. Hodnoty hash souborů se používají k tomu, aby se zabránilo spouštění příkazu `WriteConsole` v důsledku několika zpětných volání tokenu, když se konfigurační soubor změnil pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří rozhraní `IConfigurationMonitor`.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy, `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje token. `InvokeChanged` je metoda zpětného volání. `state` v této instanci je odkaz na instanci `IConfigurationMonitor`, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled` &ndash; určuje, zda by zpětné volání mělo spustit vlastní kód.
* `CurrentState` &ndash; popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, pokud `MonitoringEnabled` není `true`.
* Vytvoří výstup aktuální `state` v jeho `WriteConsole` výstupu.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je v `Startup.ConfigureServices`registrovaná jako služba:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena do `IndexModel`.

*Pages/index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Monitorování konfigurace (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když se aktivuje `OnPostStartMonitoring`, povolí se monitorování a aktuální stav se vymaže. Když se aktivuje `OnPostStopMonitoring`, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` k:

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti. Volání metody `GetFileContent` služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace. `IWebHostEnvironment.ContentRootFileProvider` slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> odkazujících na `IWebHostEnvironment.ContentRootPath`aplikace. `filePath` se získá s použitím [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` je zaregistrovaný v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V metodě `OnGet` stránky indexu (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo více instancí `IChangeToken` v jednom objektu použijte třídu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

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

`HasChanged` v sestavách složeného tokenu `true`, pokud je `true`některý z předreprezentovaných tokenů `HasChanged`. `ActiveChangeCallbacks` v sestavách složeného tokenu `true`, pokud je `true`některý z předreprezentovaných tokenů `ActiveChangeCallbacks`. Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Token změn* je stavební blok pro obecné účely, který se používá ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, ke kterým došlo ke změně. `IChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

`IChangeToken` má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> určuje, zda token proaktivně vyvolává zpětná volání. Pokud je `ActiveChangedCallbacks` nastavené na `false`, zpětné volání se nikdy nevolá a aplikace se musí dotazovat `HasChanged` na změny. Je také možné, že token nikdy nebude zrušen, pokud nedojde k žádným změnám nebo pokud se podkladová naslouchací proces změny odstraní nebo zakáže.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> přijímá hodnotu, která indikuje, jestli došlo ke změně.

Rozhraní `IChangeToken` zahrnuje metodu [RegisterChangeCallback (Action\<object >, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) , která zaregistruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged` musí být nastaveno před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>ChangeToken – třída

<xref:Microsoft.Extensions.Primitives.ChangeToken> je statická třída, která se používá k šíření oznámení, ke kterým došlo ke změně. `ChangeToken` se nachází v oboru názvů <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>. Pro aplikace, které nepoužívají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček pro balíček NuGet [Microsoft. Extensions. primitivs](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) .

Metoda [ChangeToken. (Func\<IChangeToken >, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje `Action` pro volání při změně tokenu:

* `Func<IChangeToken>` vytváří token.
* `Action` se volá při změně tokenu.

Přetížení [ChangeToken\<. > TState (Func\<IChangeToken >, Action\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) převezme další parametr `TState`, který se předává do uživatele tokenu `Action`.

`OnChange` vrátí <xref:System.IDisposable>. Volání <xref:System.IDisposable.Dispose*> zastaví naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změny v ASP.NET Core

Změna tokenů se používá ve výrazně ASP.NET Core ke sledování změn objektů:

* Pro sledování změn souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> metoda vytvoří `IChangeToken` pro zadané soubory nebo složky, které chcete sledovat.
* tokeny `IChangeToken` lze přidat do záznamů mezipaměti pro aktivaci vyřazení mezipaměti při změně.
* U `TOptions` změn má výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementace <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> přetížení, které přijímá jednu nebo více instancí <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>. Každá instance vrací `IChangeToken` k registraci zpětného volání upozornění na změnu pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Monitorování pro změny konfigurace

Ve výchozím nastavení používají šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appSettings. JSON*, *appSettings. Vývojové. JSON*a *appSettings. Produkční. JSON*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, která přijímá parametr `reloadOnChange`. `reloadOnChange` určuje, zda má být konfigurace znovu načtena při změně souboru. Toto nastavení se zobrazí v <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*><xref:Microsoft.AspNetCore.WebHost> pohodlí:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace na základě souborů je reprezentována <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` používá ke sledování souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider>.

Ve výchozím nastavení je `IFileMonitor` k dispozici <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, která používá <xref:System.IO.FileSystemWatcher> ke sledování změn konfiguračních souborů.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se některý ze souborů *appSettings* změní, obě implementace monitorování souborů spustí vlastní kód&mdash;ukázková aplikace zapíše zprávu do konzoly.

`FileSystemWatcher` konfiguračního souboru může aktivovat více zpětných volání tokenů pro jednotlivé změny konfiguračního souboru. Chcete-li zajistit, aby se vlastní kód spouštěl pouze jednou při spuštění více zpětných volání tokenu, implementace ukázky kontroluje hodnoty hash souborů. Ukázka používá algoritmus hash souborů SHA1. Opakování je implementováno pomocí exponenciálního zálohování. Opakování je k dispozici, protože může dojít k uzamčení souboru, které dočasně znemožňuje výpočet nového algoritmu hash v souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny spuštění

Zaregistrujte příjemce tokenu `Action` zpětného volání pro oznámení o změně v tokenu opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje token. Zpětné volání je metoda `InvokeChanged`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

`state` zpětného volání se používá k předání do `IHostingEnvironment`, což je užitečné při určení správného konfiguračního souboru *appSettings* , který se má monitorovat (například *appSettings. Vývoj. JSON* při ve vývojovém prostředí. Hodnoty hash souborů se používají k tomu, aby se zabránilo spouštění příkazu `WriteConsole` v důsledku několika zpětných volání tokenu, když se konfigurační soubor změnil pouze jednou.

Tento systém běží, dokud je aplikace spuštěná a uživatel ho nemůže zakázat.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Ukázka implementuje:

* Základní monitorování spouštěcího tokenu.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří rozhraní `IConfigurationMonitor`.

*Rozšíření/ConfigurationMonitor. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy, `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje token. `InvokeChanged` je metoda zpětného volání. `state` v této instanci je odkaz na instanci `IConfigurationMonitor`, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled` &ndash; určuje, zda by zpětné volání mělo spustit vlastní kód.
* `CurrentState` &ndash; popisuje aktuální stav monitorování pro použití v uživatelském rozhraní.

Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, pokud `MonitoringEnabled` není `true`.
* Vytvoří výstup aktuální `state` v jeho `WriteConsole` výstupu.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je v `Startup.ConfigureServices`registrovaná jako služba:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Stránka index nabízí uživatelský ovládací prvek pro monitorování konfigurace. Instance `IConfigurationMonitor` je vložena do `IndexModel`.

*Pages/index. cshtml. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Monitorování konfigurace (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavení aktuálního stavu pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když se aktivuje `OnPostStartMonitoring`, povolí se monitorování a aktuální stav se vymaže. Když se aktivuje `OnPostStopMonitoring`, monitorování se zakáže a stav se nastaví tak, aby odrážel, že monitorování se nevyskytuje.

Tlačítka v uživatelském rozhraní povolují a zakazují monitorování.

*Pages/index. cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Monitorovat změny souborů v mezipaměti

Obsah souboru může být uložen v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Mezipaměť v paměti je popsána v tématu [mezipaměť v paměti](xref:performance/caching/memory) . Bez provedení dalších kroků, jako je třeba implementace popsaná níže, se z mezipaměti vrátí *zastaralé* (zastaralé) údaje, pokud se změní zdrojová data.

Například neberoucí se v úvahu stav zdrojového souboru v mezipaměti při obnovení [klouzavého období vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) vede k zastaralým datům souborů v mezipaměti. Každý požadavek na data obnoví klouzavé období vypršení platnosti, ale soubor se nikdy znovu nenačte do mezipaměti. Všechny funkce aplikace, které používají obsah uložený v mezipaměti, podléhají možnému přijetí zastaralého obsahu.

Použití změnových tokenů ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souborů v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Ukázka používá `GetFileContent` k:

* Vrátit obsah souboru
* Implementujte algoritmus opakování s exponenciálním přechodem na případy, kde zámek souboru dočasně brání v čtení souboru.

*Nástroje/nástroje. cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Vytvoří se `FileService` pro zpracování vyhledávání souborů uložených v mezipaměti. Volání metody `GetFileContent` služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit je volajícímu (*Services/Souborová služba. cs*).

Pokud obsah uložený v mezipaměti nebyl nalezen pomocí klíče mezipaměti, jsou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Z poskytovatele souboru se získá token pro změnu pomocí [IFileProviders. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při úpravě souboru.
1. Obsah souboru je uložen v mezipaměti s [klouzavé periodou vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) . Token změny je připojen pomocí [MemoryCacheEntryExtensions. AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) k vyřazení položky mezipaměti, pokud se soubor změní v mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenu obsahu](xref:fundamentals/index#content-root)aplikace. [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider>, který odkazuje na <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>aplikace. `filePath` se získá s použitím [IFileInfo. PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` je zaregistrovaný v kontejneru služby spolu se službou mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V metodě `OnGet` stránky indexu (*pages/index. cshtml. cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken – třída

Pro reprezentaci jedné nebo více instancí `IChangeToken` v jednom objektu použijte třídu <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

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

`HasChanged` v sestavách složeného tokenu `true`, pokud je `true`některý z předreprezentovaných tokenů `HasChanged`. `ActiveChangeCallbacks` v sestavách složeného tokenu `true`, pokud je `true`některý z předreprezentovaných tokenů `ActiveChangeCallbacks`. Pokud dojde k několika souběžným událostem změny, je zpětné volání kompozitní změny vyvoláno jednou.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
