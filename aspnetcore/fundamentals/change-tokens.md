---
title: Zjištění změn s tokeny změn v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat tokeny změn ke sledování změn.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/03/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 8b73b72d093b33edeb91bc78080e05aa312579ec
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561664"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Zjištění změn s tokeny změn v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

A *změnit token* se používají ke sledování změny stavu stavební blok pro obecné účely, nízké úrovně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken rozhraní

<xref:Microsoft.Extensions.Primitives.IChangeToken> šíří oznámení, že došlo ke změně. `IChangeToken` v se nachází <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček pro [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet.

`IChangeToken` má dvě vlastnosti:

* <xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> Označuje, pokud token proaktivně vyvolá zpětná volání. Pokud `ActiveChangedCallbacks` je nastavena na `false`zpětné volání se nikdy nevolá, a aplikace se musí dotazovat `HasChanged` změny. Je také možné pro token nikdy zrušit, pokud žádné změny nebo je uvolněn nebo zakázané základní naslouchací proces změn.
* <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> Získá hodnotu označující, pokud došlo ke změně.

`IChangeToken` Rozhraní obsahuje [RegisterChangeCallback (akce\<objektu >, objekt)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) metody, které je zaregistruje zpětné volání, která je vyvolána při změně tokenu. `HasChanged` musí být nastavena před vyvoláním zpětného volání.

## <a name="changetoken-class"></a>Třída ChangeToken

<xref:Microsoft.Extensions.Primitives.ChangeToken> slouží k šíření oznámení, že došlo ke změně statické třídě. `ChangeToken` v se nachází <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> oboru názvů. Pro aplikace, které nepoužívají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app), vytvořit odkaz na balíček pro [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) balíček NuGet.

[ChangeToken.OnChange (Func\<IChangeToken >, akce)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) metoda registrů `Action` volat při každé změně tokenu:

* `Func<IChangeToken>` vytvoří token.
* `Action` je volána při změně tokenu.

[ChangeToken.OnChange\<TState > (Func\<IChangeToken >, akce\<TState >, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) přijímá další přetížení `TState` parametr, který je předán do tokenu příjemce `Action`.

`OnChange` Vrátí <xref:System.IDisposable>. Volání <xref:System.IDisposable.Dispose*> token z naslouchání pro další změny se zastaví a uvolní prostředky tokenu.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Příklad používá změna tokenů v ASP.NET Core

Změna tokenů se viditelného oblasti ASP.NET Core používají k monitorování změn objektů:

* Pro sledování změn souborů, <xref:Microsoft.Extensions.FileProviders.IFileProvider>společnosti <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> metoda vytvoří `IChangeToken` určité soubory nebo složky, které chcete sledovat.
* `IChangeToken` tokeny lze přidat na položky mezipaměti k aktivaci odsunuté mezipaměť při změně.
* Pro `TOptions` změní výchozí <xref:Microsoft.Extensions.Options.OptionsMonitor`1> provádění <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> má přetížení, která přijímá jeden nebo více <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instancí. Vrátí každá instance `IChangeToken` pro registraci zpětné volání oznámení změn pro možnosti sledování změn.

## <a name="monitor-for-configuration-changes"></a>Monitorujte změny konfigurace

Ve výchozím nastavení, použijte šablony ASP.NET Core [konfigurační soubory JSON](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings. Development.JSON*, a *appsettings. Production.JSON*) k načtení nastavení konfigurace aplikace.

Tyto soubory jsou nakonfigurováni pomocí [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) rozšiřující metody na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> , který přijme `reloadOnChange` parametru. `reloadOnChange` Označuje, pokud byste znovu načíst konfiguraci na změny v souboru. Toto nastavení objeví ve <xref:Microsoft.AspNetCore.WebHost> Komfortní metoda <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

Konfigurace založená na souboru je reprezentován <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>. `FileConfigurationSource` používá <xref:Microsoft.Extensions.FileProviders.IFileProvider> sledování souborů.

Ve výchozím nastavení `IFileMonitor` je poskytován <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, který používá <xref:System.IO.FileSystemWatcher> monitorovat změny v konfiguraci souboru.

Ukázková aplikace předvádí dvě implementace pro sledování změn konfigurace. Pokud je libovolná z *appsettings* změnit soubory, i sledování implementace souboru provést vlastní kód&mdash;ukázkové aplikace zapíše zprávu do konzoly.

Konfigurační soubor `FileSystemWatcher` může aktivovat více tokenů zpětných volání pro změnu souboru v jediné konfiguraci. Pokud chcete mít jistotu, že vlastní kód běží pouze po při aktivaci více zpětných volání tokenu, ukázková implementace zkontroluje hodnoty hash souborů. Ukázka používá algoritmus hash SHA1 souboru. Opakování je implementováno s exponenciální regresní. Opakování je k dispozici, protože zamykání souborů může dojít, který brání dočasně computingu nová hodnota hash souboru.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Jednoduché spouštění změnit token

Zaregistrovat token příjemce `Action` zpětné volání pro upozornění na změnu na konfiguraci znovu načíst token.

V `Startup.Configure`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje tento token. Zpětné volání je `InvokeChanged` metody:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

`state` Zpětného volání, které se používá a zajistěte tak předání `IHostingEnvironment`, což je vhodné při zadání správné *appsettings* konfiguračního souboru k monitorování (třeba *appsettings. Development.JSON* při ve vývojovém prostředí). Hodnoty hash souboru se používají k zabránění `WriteConsole` příkaz spouštění více než jednou z důvodu více tokenů zpětná volání, pokud konfigurační soubor byl změněn pouze jednou.

Tento systém běží, dokud aplikace běží a nedá se zakázat uživatelem.

### <a name="monitor-configuration-changes-as-a-service"></a>Sledujte změny konfigurace, jako služba

Implementuje vzorku:

* Základní spuštění token monitorování.
* Monitorování jako služba.
* Mechanismus pro povolení a zákaz monitorování.

Ukázka vytvoří `IConfigurationMonitor` rozhraní.

*Extensions/ConfigurationMonitor.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Konstruktor třídy implementované, `ConfigurationMonitor`, zaregistruje zpětné volání oznámení změn:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` poskytuje tento token. `InvokeChanged` je metoda zpětného volání. `state` v tomto případě se odkaz na `IConfigurationMonitor` instanci, která se používá pro přístup ke sledování stavu. Se používají dvě vlastnosti:

* `MonitoringEnabled` &ndash; Určuje zpětné volání vhodné spustit své vlastní kód.
* `CurrentState` &ndash; Popisuje aktuální monitorování stavu pro použití v uživatelském rozhraní.

`InvokeChanged` Metoda je podobná dřívější přístup, s výjimkou, že:

* Spuštěním jeho kódu, není-li `MonitoringEnabled` je `true`.
* Vypíše aktuální `state` v jeho `WriteConsole` výstup.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Instance `ConfigurationMonitor` je zaregistrováno jako služba v `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Indexovou stránku nabízí uživatelský ovládací prvek v konfiguraci monitorování. Instance `IConfigurationMonitor` se vloží do `IndexModel`.

*Pages/Index.cshtml.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Konfigurace monitorování (`_monitor`) slouží k povolení nebo zakázání monitorování a nastavte aktuální stav pro zpětnou vazbu uživatelského rozhraní:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Když `OnPostStartMonitoring` je spuštěn, je zapnuté monitorování, a aktuální stav je vymazán. Když `OnPostStopMonitoring` se aktivuje, monitorování je zakázáno a stav je nastaven tak, aby odrážely, že monitorování neprobíhá.

Tlačítka v uživatelském rozhraní povolit a zakázat monitorování.

*Pages/Index.cshtml*:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Sledování změn v mezipaměti souborů

Obsah souboru může být uložený v mezipaměti v paměti pomocí <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>. Ukládání do mezipaměti v paměti je popsána v [ukládat do mezipaměti v paměti](xref:performance/caching/memory) tématu. Bez nutnosti převádět další kroky, jako je například implementace je popsáno níže, *zastaralé* (zastaralé) vrátí data z mezipaměti zdrojová data mění.

Například nebere v úvahu stavu uložené v mezipaměti zdrojového souboru při obnovování [klouzavé vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) období vede k datům zastaralých souborů v mezipaměti. Každý požadavek pro data se tato možnost obnoví klouzavou dobu vypršení platnosti, ale soubor nikdy znovu načten do mezipaměti. Žádné aplikace funkce, které používají obsah uložený v mezipaměti v souboru se mohou pravděpodobně příjem starý obsah.

Použití tokenů změn v souboru ukládání do mezipaměti scénář brání přítomnost zastaralých souborů obsahu v mezipaměti. Ukázková aplikace předvádí implementace přístupu.

Ukázka používá `GetFileContent` na:

* Vrátí obsah souboru.
* Implementace algoritmu opakování s exponenciální regresní pro případy, soubor zámku kde dočasně brání, čtení souboru.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

A `FileService` je vytvořená za účelem zpracování vyhledávání v mezipaměti souborů. `GetFileContent` Volání metody služby se pokusí získat obsah souboru z mezipaměti v paměti a vrátí řízení volajícímu (*Services/FileService.cs*).

Pokud obsah uložený v mezipaměti není nalezena pomocí klíče mezipaměti, budou provedeny následující akce:

1. Obsah souboru se získá pomocí `GetFileContent`.
1. Změnit token pochází od zprostředkovatele souborů s [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*). Zpětné volání token, který se aktivuje, když se upraví soubor.
1. Obsah souboru je uložené v mezipaměti s [klouzavé vypršení platnosti](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) období. Změnit token je připojen pomocí [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) vyřazení položka mezipaměti, pokud se soubor změní, zatímco je tam uložena.

V následujícím příkladu se soubory ukládají v kořenovém adresáři obsahu aplikace. [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) slouží k získání <xref:Microsoft.Extensions.FileProviders.IFileProvider> odkazující na aplikace <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>. `filePath` Se získá pomocí [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

`FileService` Je registrován v kontejneru služby spolu s paměti služby ukládání do mezipaměti.

V `Startup.ConfigureServices`:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Model stránky načte obsah souboru pomocí služby.

Na stránce Index `OnGet` – metoda (*Pages/Index.cshtml.cs*):

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Třída CompositeChangeToken

Představující jeden nebo více `IChangeToken` instance do jednoho objektu, používají <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> třídy.

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

`HasChanged` na složený token sestavy `true` Pokud žádný token `HasChanged` je `true`. `ActiveChangeCallbacks` na složený token sestavy `true` Pokud žádný token `ActiveChangeCallbacks` je `true`. Pokud dojde k události více souběžných změny, je složený změnu zpětné volání vyvolat jednou.

## <a name="additional-resources"></a>Další zdroje

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
