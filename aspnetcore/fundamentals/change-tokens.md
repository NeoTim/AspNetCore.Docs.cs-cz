---
title: Detekce změn pomocí tokenů změn v ASP.NET core
author: rick-anderson
description: Přečtěte si, jak pomocí tokenů změn sledovat změny.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 70451e219f1295b854e2f84aac55f0cfd1786b19
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656343"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Detekce změn pomocí tokenů změn v ASP.NET core

::: moniker range=">= aspnetcore-3.0"

*Token změny* je univerzální, nízkoúrovňový stavební blok používaný ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Balíček [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet je implicitně k dispozici ASP.NET aplikacím Core.

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je `false`nastavena na , zpětné volání je `HasChanged` nikdy volána a aplikace musí dotazování na změny. Je také možné token nikdy zrušit, pokud dojde k žádné změny nebo základní naslouchací proces změny je uvolněna nebo zakázána.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>obdrží hodnotu, která označuje, zda došlo ke změně.

Rozhraní `IChangeToken` obsahuje [Metodu RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) která registruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged`musí být nastavena před vyvolání zpětného volání.

## <a name="changetoken-class"></a>Třída ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída používaná k šíření oznámení, že došlo ke změně. `ChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Balíček [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet je implicitně k dispozici ASP.NET aplikacím Core.

Metoda [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje volání, `Action` které se stane při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`při změně tokenu.

[\<ChangeToken.OnChange TState\<>(Func IChangeToken\<>, Akce TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přetížení trvá další `Action` `TState` parametr, který je předán do tokenu spotřebitele .

`OnChange`vrátí <xref:System.IDisposable>. Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změn v ASP.NET Core

Tokeny změn se používají v prominentních oblastech ASP.NET core ke sledování změn objektů:

* Pro sledování změn <xref:Microsoft.Extensions.FileProviders.IFileProvider>souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` 's pro zadané soubory nebo složku, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do položek mezipaměti pro aktivaci vyřazovacích položek mezipaměti při změně.
* Pro `TOptions` změny má <xref:Microsoft.Extensions.Options.OptionsMonitor`1> výchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> implementace přetížení, který přijímá <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> jednu nebo více instancí. Každá instance `IChangeToken` vrátí zaregistrovat zpětné volání oznámení o změně pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Sledování změn konfigurace

Ve výchozím nastavení používají ASP.NET šablony Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*a *nastavení aplikací. Production.json*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> která přijímá `reloadOnChange` parametr. `reloadOnChange`označuje, zda by měla být konfigurace znovu načtena při změnách souborů. Toto nastavení <xref:Microsoft.Extensions.Hosting.Host> se <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>zobrazí metodou pohodlí :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace založená na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>souborech je reprezentována . `FileConfigurationSource`používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> ke sledování souborů.

Ve výchozím `IFileMonitor` nastavení je k <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>dispozici <xref:System.IO.FileSystemWatcher> aplikace , která používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se změní některý ze souborů *nastavení aplikace,* obě&mdash;implementace monitorování souborů spustí vlastní kód, ukázková aplikace zapíše zprávu do konzoly.

Konfigurační `FileSystemWatcher` soubor může aktivovat více tokenů zpětná volání pro jednu změnu konfiguračního souboru. Chcete-li zajistit, že vlastní kód je spuštěn pouze jednou při aktivaci více token zpětné vzpěr, ukázkové implementace kontroluje hodnotu hashe souboru. Ukázka používá hašování souboru SHA1. Opakování je implementováno s exponenciální back-off. Opakování je k dispozici, protože může dojít k uzamčení souboru, který dočasně zabraňuje výpočtu nové hash v souboru.

*Utility / Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny při spuštění

Zaregistrujte token `Action` spotřebitele zpětné volání pro oznámení o změně na token opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Zpětné `state` volání slouží k předání v `IWebHostEnvironment`, což je užitečné pro zadání správného konfiguračního souboru *nastavení aplikace* ke sledování (například *nastavení aplikací. Development.json,* když v vývojovém prostředí). Stavy hashe souborů se `WriteConsole` používají k zabránění spuštění příkazu vícekrát z důvodu více zpětných volání tokenů, pokud se konfigurační soubor změnil pouze jednou.

Tento systém běží tak dlouho, dokud je aplikace spuštěná a nemůže být zakázána uživatelem.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Vzorek implementuje:

* Základní monitorování spouštěcích tokenů.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. V `state` tomto případě je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled`&ndash; Označuje, zda by zpětné volání mělo spustit vlastní kód.
* `CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v ui.

Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, `MonitoringEnabled` `true`pokud není .
* Výstupy proud `state` v `WriteConsole` jeho výstupu.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrována jako `Startup.ConfigureServices`služba v :

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Stránka Index nabízí uživateli kontrolu nad monitorováním konfigurace. Instance `IConfigurationMonitor` je injekčně `IndexModel`do .

*Stránky/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Konfigurační`_monitor`monitor ( ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu zpětné vazby uživatelského uživatelského nastavení:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Při `OnPostStartMonitoring` aktivaci je povoleno monitorování a aktuální stav je vymazán. Při `OnPostStopMonitoring` aktivaci monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování nedochází.

Tlačítka v ui povolit a zakázat monitorování.

*Stránky/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Sledování změn souborů uložených v mezipaměti

Obsah souboru lze uložit do <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>mezipaměti v paměti pomocí . Ukládání do mezipaměti je popsáno v tématu [Cache in-memory.](xref:performance/caching/memory) Bez dalších kroků, jako je například implementace popsané níže, *jsou zastaralá* (zastaralá) data vrácena z mezipaměti, pokud se změní zdrojová data.

Například nezohlednění stavu zdrojového souboru uloženého v mezipaměti při obnovení [klouzavé](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) doby vypršení platnosti vede k zastaralým datům souboru uloženým v mezipaměti. Každý požadavek na data obnovuje posuvné období vypršení platnosti, ale soubor je nikdy znovu načíst do mezipaměti. Všechny funkce aplikace, které používají obsah souboru uložený v mezipaměti, podléhají možnému příjmu zastaralého obsahu.

Použití tokenů změny ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souboru v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Vzorek používá `GetFileContent` k:

* Vrátit obsah souboru.
* Implementujte algoritmus opakování s exponenciálním back-off, který se vztahuje na případy, kdy zámek souboru dočasně zabraňuje čtení souboru.

*Utility / Utilities.cs*:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

A `FileService` je vytvořen pro zpracování vyhledávání souborů uložených v mezipaměti. Volání `GetFileContent` metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit jej volajícímu (*Services/FileService.cs*).

Pokud obsah uložený v mezipaměti není pomocí klíče mezipaměti nalezen, provedou se následující akce:

1. Obsah souboru se `GetFileContent`získává pomocí aplikace .
1. Token změny je získán od poskytovatele souboru s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při změně souboru.
1. Obsah souboru je uložen do mezipaměti s [klouzavou dobou vypršení platnosti.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) Token změny je připojen s [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřadit položku mezipaměti, pokud se soubor změní, zatímco je uložen do mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root)aplikace . `IWebHostEnvironment.ContentRootFileProvider`slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> ukazování na `IWebHostEnvironment.ContentRootPath`aplikaci . Získá `filePath` se pomocí [aplikace IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

Je `FileService` registrován v kontejneru služby spolu se službou ukládání do mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V `OnGet` metodě stránky Rejstřík *(Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Třída CompositeChangeToken

Pro reprezentaci `IChangeToken` jedné nebo více instancí <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> v jednom objektu použijte třídu.

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

`HasChanged`na sestavy složené `true` tokenu, `HasChanged` `true`pokud je nějaký reprezentované token . `ActiveChangeCallbacks`na sestavy složené `true` tokenu, `ActiveChangeCallbacks` `true`pokud je nějaký reprezentované token . Pokud dojde k více souběžných událostí změny, je vyvoláno zpětné volání složené změny jednou.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*Token změny* je univerzální, nízkoúrovňový stavební blok používaný ke sledování změn stavu.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="ichangetoken-interface"></a>Rozhraní IChangeToken

<xref:Microsoft.Extensions.Primitives.IChangeToken>šíří oznámení, že došlo ke změně. `IChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček [balíčku Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.

`IChangeToken`má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks>označuje, zda token proaktivně vyvolává zpětná volání. Pokud `ActiveChangedCallbacks` je `false`nastavena na , zpětné volání je `HasChanged` nikdy volána a aplikace musí dotazování na změny. Je také možné token nikdy zrušit, pokud dojde k žádné změny nebo základní naslouchací proces změny je uvolněna nebo zakázána.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>obdrží hodnotu, která označuje, zda došlo ke změně.

Rozhraní `IChangeToken` obsahuje [Metodu RegisterChangeCallback(Action\<Object>, Object),](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) která registruje zpětné volání, které je vyvoláno při změně tokenu. `HasChanged`musí být nastavena před vyvolání zpětného volání.

## <a name="changetoken-class"></a>Třída ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken>je statická třída používaná k šíření oznámení, že došlo ke změně. `ChangeToken`sídlí v <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), vytvořte odkaz na balíček [balíčku Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet.

Metoda [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) zaregistruje volání, `Action` které se stane při každé změně tokenu:

* `Func<IChangeToken>`vytvoří token.
* `Action`při změně tokenu.

[\<ChangeToken.OnChange TState\<>(Func IChangeToken\<>, Akce TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přetížení trvá další `Action` `TState` parametr, který je předán do tokenu spotřebitele .

`OnChange`vrátí <xref:System.IDisposable>. Volání <xref:System.IDisposable.Dispose*> zastaví token z naslouchání pro další změny a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad použití tokenů změn v ASP.NET Core

Tokeny změn se používají v prominentních oblastech ASP.NET core ke sledování změn objektů:

* Pro sledování změn <xref:Microsoft.Extensions.FileProviders.IFileProvider>souborů <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> vytvoří metoda `IChangeToken` 's pro zadané soubory nebo složku, které chcete sledovat.
* `IChangeToken`tokeny lze přidat do položek mezipaměti pro aktivaci vyřazovacích položek mezipaměti při změně.
* Pro `TOptions` změny má <xref:Microsoft.Extensions.Options.OptionsMonitor`1> výchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> implementace přetížení, který přijímá <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> jednu nebo více instancí. Každá instance `IChangeToken` vrátí zaregistrovat zpětné volání oznámení o změně pro změny možností sledování.

## <a name="monitor-for-configuration-changes"></a>Sledování změn konfigurace

Ve výchozím nastavení používají ASP.NET šablony Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.json*a *nastavení aplikací. Production.json*) pro načtení nastavení konfigurace aplikace.

Tyto soubory jsou konfigurovány pomocí metody rozšíření [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean),](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> která přijímá `reloadOnChange` parametr. `reloadOnChange`označuje, zda by měla být konfigurace znovu načtena při změnách souborů. Toto nastavení <xref:Microsoft.AspNetCore.WebHost> se <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>zobrazí metodou pohodlí :

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace založená na <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>souborech je reprezentována . `FileConfigurationSource`používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> ke sledování souborů.

Ve výchozím `IFileMonitor` nastavení je k <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>dispozici <xref:System.IO.FileSystemWatcher> aplikace , která používá ke sledování změn konfiguračního souboru.

Ukázková aplikace ukazuje dvě implementace pro sledování změn konfigurace. Pokud se změní některý ze souborů *nastavení aplikace,* obě&mdash;implementace monitorování souborů spustí vlastní kód, ukázková aplikace zapíše zprávu do konzoly.

Konfigurační `FileSystemWatcher` soubor může aktivovat více tokenů zpětná volání pro jednu změnu konfiguračního souboru. Chcete-li zajistit, že vlastní kód je spuštěn pouze jednou při aktivaci více token zpětné vzpěr, ukázkové implementace kontroluje hodnotu hashe souboru. Ukázka používá hašování souboru SHA1. Opakování je implementováno s exponenciální back-off. Opakování je k dispozici, protože může dojít k uzamčení souboru, který dočasně zabraňuje výpočtu nové hash v souboru.

*Utility / Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduchý token změny při spuštění

Zaregistrujte token `Action` spotřebitele zpětné volání pro oznámení o změně na token opětovného načtení konfigurace.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()`poskytuje token. Zpětné volání je `InvokeChanged` metoda:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Zpětné `state` volání slouží k předání v `IHostingEnvironment`, což je užitečné pro zadání správného konfiguračního souboru *nastavení aplikace* ke sledování (například *nastavení aplikací. Development.json,* když v vývojovém prostředí). Stavy hashe souborů se `WriteConsole` používají k zabránění spuštění příkazu vícekrát z důvodu více zpětných volání tokenů, pokud se konfigurační soubor změnil pouze jednou.

Tento systém běží tak dlouho, dokud je aplikace spuštěná a nemůže být zakázána uživatelem.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledování změn konfigurace jako služby

Vzorek implementuje:

* Základní monitorování spouštěcích tokenů.
* Monitorování jako služba.
* Mechanismus pro povolení a zakázání monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Rozšíření/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor implementované třídy `ConfigurationMonitor`, zaregistruje zpětné volání pro oznámení o změně:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()`dodává token. `InvokeChanged`je metoda zpětného volání. V `state` tomto případě je odkaz `IConfigurationMonitor` na instanci, která se používá pro přístup ke stavu monitorování. Používají se dvě vlastnosti:

* `MonitoringEnabled`&ndash; Označuje, zda by zpětné volání mělo spustit vlastní kód.
* `CurrentState`&ndash; Popisuje aktuální stav monitorování pro použití v ui.

Metoda `InvokeChanged` je podobná dřívějšímu přístupu, s tím rozdílem, že:

* Nespustí svůj kód, `MonitoringEnabled` `true`pokud není .
* Výstupy proud `state` v `WriteConsole` jeho výstupu.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je registrována jako `Startup.ConfigureServices`služba v :

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Stránka Index nabízí uživateli kontrolu nad monitorováním konfigurace. Instance `IConfigurationMonitor` je injekčně `IndexModel`do .

*Stránky/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Konfigurační`_monitor`monitor ( ) se používá k povolení nebo zakázání monitorování a nastavení aktuálního stavu zpětné vazby uživatelského uživatelského nastavení:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Při `OnPostStartMonitoring` aktivaci je povoleno monitorování a aktuální stav je vymazán. Při `OnPostStopMonitoring` aktivaci monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování nedochází.

Tlačítka v ui povolit a zakázat monitorování.

*Stránky/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Sledování změn souborů uložených v mezipaměti

Obsah souboru lze uložit do <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>mezipaměti v paměti pomocí . Ukládání do mezipaměti je popsáno v tématu [Cache in-memory.](xref:performance/caching/memory) Bez dalších kroků, jako je například implementace popsané níže, *jsou zastaralá* (zastaralá) data vrácena z mezipaměti, pokud se změní zdrojová data.

Například nezohlednění stavu zdrojového souboru uloženého v mezipaměti při obnovení [klouzavé](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) doby vypršení platnosti vede k zastaralým datům souboru uloženým v mezipaměti. Každý požadavek na data obnovuje posuvné období vypršení platnosti, ale soubor je nikdy znovu načíst do mezipaměti. Všechny funkce aplikace, které používají obsah souboru uložený v mezipaměti, podléhají možnému příjmu zastaralého obsahu.

Použití tokenů změny ve scénáři ukládání souborů do mezipaměti zabraňuje přítomnosti zastaralého obsahu souboru v mezipaměti. Ukázková aplikace ukazuje implementaci přístupu.

Vzorek používá `GetFileContent` k:

* Vrátit obsah souboru.
* Implementujte algoritmus opakování s exponenciálním back-off, který se vztahuje na případy, kdy zámek souboru dočasně zabraňuje čtení souboru.

*Utility / Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

A `FileService` je vytvořen pro zpracování vyhledávání souborů uložených v mezipaměti. Volání `GetFileContent` metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátit jej volajícímu (*Services/FileService.cs*).

Pokud obsah uložený v mezipaměti není pomocí klíče mezipaměti nalezen, provedou se následující akce:

1. Obsah souboru se `GetFileContent`získává pomocí aplikace .
1. Token změny je získán od poskytovatele souboru s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání tokenu se aktivuje při změně souboru.
1. Obsah souboru je uložen do mezipaměti s [klouzavou dobou vypršení platnosti.](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) Token změny je připojen s [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřadit položku mezipaměti, pokud se soubor změní, zatímco je uložen do mezipaměti.

V následujícím příkladu jsou soubory uloženy v [kořenovém adresáři obsahu](xref:fundamentals/index#content-root)aplikace . [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) se používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> k získání ukazující <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>na aplikaci . Získá `filePath` se pomocí [aplikace IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

Je `FileService` registrován v kontejneru služby spolu se službou ukládání do mezipaměti paměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

V `OnGet` metodě stránky Rejstřík *(Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Třída CompositeChangeToken

Pro reprezentaci `IChangeToken` jedné nebo více instancí <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> v jednom objektu použijte třídu.

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

`HasChanged`na sestavy složené `true` tokenu, `HasChanged` `true`pokud je nějaký reprezentované token . `ActiveChangeCallbacks`na sestavy složené `true` tokenu, `ActiveChangeCallbacks` `true`pokud je nějaký reprezentované token . Pokud dojde k více souběžných událostí změny, je vyvoláno zpětné volání složené změny jednou.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
