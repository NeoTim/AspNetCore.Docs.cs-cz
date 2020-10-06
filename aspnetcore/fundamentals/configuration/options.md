---
title: Vzor možností v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762331"
---
# <a name="options-pattern-in-aspnet-core"></a>Vzor možností v ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Od [Kirka Larkin](https://twitter.com/serpent5) a [Rick Anderson](https://twitter.com/RickAndMSFT).

Vzor možností používá třídy k poskytnutí přístupu silného typu ke skupinám souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

Toto téma poskytuje informace o vzoru možností v ASP.NET Core. Informace o použití vzoru možností v konzolových aplikacích najdete v tématu [model možností v .NET](/dotnet/core/extensions/options).

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a>Vytvoření vazby hierarchické konfigurace

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptions%601>:

* Nepodporuje ***:***
  * Čtení konfiguračních dat po spuštění aplikace.
  * [Pojmenované možnosti](#named)
* Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:

* Je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v tématu [použití IOptionsSnapshot ke čtení aktualizovaných dat](#ios).
* Je zaregistrován jako [obor](xref:fundamentals/dependency-injection#scoped) , a proto nemůže být vložen do služby typu singleton.
* Podporuje [pojmenované možnosti](#named)

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:

* Slouží k načtení možností a správě oznámení možností pro `TOptions` instance.
* Je zaregistrován jako typ [singleton](xref:fundamentals/dependency-injection#singleton) a může být vložen do jakékoli [životnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).
* Podporovaných
  * Oznámení změny
  * [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
  * [Znovu Načítatelné konfigurace](#ios)
  * Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )
  
Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavení nebo změnu možností po všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfiguracích.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a>Použití IOptionsSnapshot ke čtení aktualizovaných dat

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti. Změny konfigurace se čtou po spuštění aplikace při použití zprostředkovatelů konfigurace, které podporují čtení aktualizovaných hodnot konfigurace.

Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:

* `IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.
* `IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen. Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.

Následující kód používá <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> .

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

V předchozím kódu se po čtení aplikace změny konfiguračního souboru JSON po jeho spuštění.

## <a name="ioptionsmonitor"></a>IOptionsMonitor

Následující kód zaregistruje instanci konfigurace, která se `MyOptions` váže k.

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

Následující příklad používá <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> :

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

V předchozím kódu se ve výchozím nastavení změny konfiguračního souboru JSON po spuštění aplikace čtou.

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a>Podpora pojmenovaných možností pomocí IConfigureNamedOptions

Pojmenované možnosti:

* Jsou užitečné v případě, že je více konfiguračních oddílů svázán se stejnými vlastnostmi.
* Rozlišují velká a malá písmena.

V souboru zvažte následující *appsettings.js* :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

Namísto vytváření dvou tříd pro vytvoření vazby `TopItem:Month` a `TopItem:Year` pro každou část se používá následující třída:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

Následující kód konfiguruje pojmenované možnosti:

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

Následující kód zobrazí pojmenované možnosti:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

Všechny možnosti jsou pojmenované instance. <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít. `null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance. <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použijte tuto konvenci.

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .

`OptionsBuilder` se používá v sekci [ověřování možností](#val) .

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

Služby jsou dostupné ze injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>` poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použití až pěti služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření typu je ekvivalentní k čemu rozhraní při volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb. 

<a name="val"></a>

## <a name="options-validation"></a>Ověřování možností

Ověřování možností povoluje ověření hodnot možností.

V souboru zvažte následující *appsettings.js* :

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

Následující třída se váže ke `"MyConfig"` konfiguračnímu oddílu a použije několik `DataAnnotations` pravidel:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

Následující kód:

* Volá <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> , aby se získal objekt [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) , který se váže ke `MyConfigOptions` třídě.
* Volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> pro povolení ověřování pomocí `DataAnnotations` .

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

`ValidateDataAnnotations`Metoda rozšíření je definována v balíčku NuGet [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) . U webových aplikací, které používají `Microsoft.NET.Sdk.Web` sadu SDK, se na tento balíček odkazuje implicitně ze sdíleného rozhraní.

Následující kód zobrazí konfigurační hodnoty nebo chyby ověřování:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

Následující kód aplikuje složitější ověřovací pravidlo pomocí delegáta:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a>IValidateOptions pro komplexní ověřování

Následující třída implementuje <xref:Microsoft.Extensions.Options.IValidateOptions`1> :

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

`IValidateOptions` povoluje přesunutí ověřovacího kódu z `StartUp` a do třídy.

Pomocí předchozího kódu je ověřování povoleno v `Startup.ConfigureServices` s následujícím kódem:

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` . Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

## <a name="optionsconfigurationextensions-nuget-package"></a>Balíček NuGet Options.ConfigurationExtensions

Na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) se implicitně odkazuje v ASP.NET Corech aplikacích.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:

* Oznámení změny
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu Načítatelné konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601> dá se použít k podpoře možností. Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.

Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` . Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` . `Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Soubor *appsettings.jsv* ukázce určuje hodnoty pro `option1` a `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností pomocí delegáta

Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.

K nastavení hodnot možností použijte delegáta. Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba. Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby. V předchozím příkladu jsou hodnoty `Option1` a uvedeny `Option2` v *appsettings.jsna*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.

Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace. Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace podNastavení

Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.

Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.

Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` . Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v *appsettings.jsna*.

V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba. Váže `MySubOptions` se k části `subsection` *appsettings.jsv* souboru:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.

*appsettings.jsukázek v* souboru definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Vkládání možností

Vkládání možností je znázorněno jako příklad 4 v ukázkové aplikaci.

Vložit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* RazorZobrazení stránky nebo MVC s [`@inject`](xref:mvc/views/razor#inject) Razor direktivou.
* Model stránky nebo zobrazení.

Následující příklad z ukázkové aplikace se vloží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do modelu stránky (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data pomocí IOptionsSnapshot

Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>Při použití se možnosti vypočítávají jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.

Rozdíl mezi `IOptionsMonitor` a `IOptionsSnapshot` je to, že:

* `IOptionsMonitor` je [Služba typu Singleton](xref:fundamentals/dependency-injection#singleton) , která načte aktuální hodnoty možností kdykoli, což je zvláště užitečné v závislostech singleton.
* `IOptionsSnapshot` je [Oborová služba](xref:fundamentals/dependency-injection#scoped) a poskytuje snímek možností v době, kdy `IOptionsSnapshot<T>` je objekt vytvořen. Snímky možností jsou navržené pro použití s přechodnými a vymezenými závislostmi.

Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po *appsettings.js* změny (*pages/index. cshtml. cs*). Více požadavků na server vrací konstantní hodnoty, které poskytuje *appsettings.jsv* souboru, dokud nedojde ke změně souboru a obnovení konfigurace.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené z *appsettings.js* souboru:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v *appsettings.jsv* souboru na `value1_from_json UPDATED` a `200` . Uložte *appsettings.jsdo* souboru. Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností s IConfigureNamedOptions

Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.

Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány s [OptionsServiceCollectionExtensions.Configurovat](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), která volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření. U pojmenovaných možností se rozlišují velká a malá písmena.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` hodnoty se poskytují z konfigurace, které se načítají z *appsettings.jsv* souboru. `named_options_2` hodnoty poskytuje:

* `named_options_2`Delegát v `ConfigureServices` pro `Option1` .
* Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Do metody přidejte následující kód ručně `Startup.ConfigureServices` :

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít. `null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb. 

## <a name="options-validation"></a>Ověřování možností

Ověřování možností umožňuje ověřit možnosti při konfiguraci možností. Zavolejte `Validate` na metodu ověřování, která vrátí, `true` Pokud jsou možnosti platné, a `false` Pokud nejsou platné:

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName` . Instance výchozích možností je `Options.DefaultName` .

Ověřování se spustí při vytvoření instance Options. Je zaručeno, že instance možností před prvním tím, než se přistupuje k ověření.

> [!IMPORTANT]
> Ověřování možností nechrání před úpravami možností po vytvoření instance Options. Například `IOptionsSnapshot` Možnosti se vytváří a ověřují jednou za požadavek při prvním použití možností. Tyto `IOptionsSnapshot` Možnosti se znovu neověřují u dalších pokusů o přístup *pro stejný požadavek*.

`Validate`Metoda přijímá `Func<TOptions, bool>` . Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>` , což umožňuje:

* Ověřování více typů možností: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, které závisí na jiném typu možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` ověří

* Konkrétní instance pojmenované možnosti.
* Všechny možnosti `name` , pokud je `null` .

Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options. DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) pomocí volání <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody on `OptionsBuilder<TOptions>` . `Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

Ověření Eager (při spuštění selže) je v budoucí verzi zváženo.

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` . Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip odděleníní rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): scénáře (třídy) závislé na nastavení konfigurace závisejí pouze na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): nastavení různých částí aplikace nejsou závislá ani vzájemně závislá na sobě.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Předpoklady

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601> slouží k načtení možností a správě oznámení možností pro `TOptions` instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> podporuje následující scénáře:

* Oznámení změny
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu Načítatelné konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Neplatných selektivních možností ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> )

Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po <xref:Microsoft.Extensions.Options.IConfigureOptions%601> výskytu všech konfigurací.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601> zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> Konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí do mezipaměti `TOptions` . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala ( <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*> ). Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> . <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>Metoda se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601> dá se použít k podpoře možností. Nicméně <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> . Můžete pokračovat <xref:Microsoft.Extensions.Options.IOptions%601> v používání existujících architektur a knihoven, které již rozhraní používají, <xref:Microsoft.Extensions.Options.IOptions%601> a nevyžadují scénáře, které poskytuje <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> .

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností se v ukázkové aplikaci ukazuje jako příklad 1.

Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions` má dvě vlastnosti, `Option1` a `Option2` . Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1` . `Option2` má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions`Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Soubor *appsettings.jsv* ukázce určuje hodnoty pro `option1` a `option2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Když použijete vlastní <xref:System.Configuration.ConfigurationBuilder> nastavení pro načtení konfigurace možností z konfiguračního souboru, zkontrolujte, jestli je správně nastavená základní cesta:
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> Při načítání konfigurace možností ze souboru s nastavením prostřednictvím je explicitně nutné nastavit základní cestu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností pomocí delegáta

Konfigurace jednoduchých možností s delegátem je znázorněna jako příklad 2 v ukázkové aplikaci.

K nastavení hodnot možností použijte delegáta. Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána druhá služba. Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig` :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby. V předchozím příkladu jsou hodnoty `Option1` a uvedeny `Option2` v *appsettings.jsna*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.

Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace. Při spuštění aplikace `OnGet` vrátí metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace podNastavení

Konfigurace dílčích možností je v ukázkové aplikaci znázorněna jako příklad 3.

Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.

Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]` . Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1` , který je načten z `option1` vlastnosti v *appsettings.jsna*.

V následujícím kódu <xref:Microsoft.Extensions.Options.IConfigureOptions%601> je do kontejneru služby přidána třetí služba. Váže `MySubOptions` se k části `subsection` *appsettings.jsv* souboru:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection`Metoda vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.

*appsettings.jsukázek v* souboru definuje `subsection` člena s klíči pro `suboption1` a `suboption2` :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions`Třída definuje vlastnosti `SubOption1` a `SubOption2` , aby obsahovala hodnoty možností (*modely/MySubOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Metoda modelu stránky `OnGet` vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení

Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad 4.

Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` pomocí `@inject` direktivy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a zavstřikem do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data pomocí IOptionsSnapshot

Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu 5.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> podporuje opětovné načítání možností s minimální režií zpracování.

Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.

Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> se vytvoří nový po *appsettings.js* změny (*pages/index. cshtml. cs*). Více požadavků na server vrací konstantní hodnoty, které poskytuje *appsettings.jsv* souboru, dokud nedojde ke změně souboru a obnovení konfigurace.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje počáteční `option1` `option2` hodnoty a hodnoty načtené z *appsettings.js* souboru:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v *appsettings.jsv* souboru na `value1_from_json UPDATED` a `200` . Uložte *appsettings.jsdo* souboru. Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností s IConfigureNamedOptions

Pojmenovaná podpora s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> je znázorněna jako příklad 6 v ukázkové aplikaci.

Podpora pojmenovaných možností umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány s [OptionsServiceCollectionExtensions.Configurovat](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), která volá [ConfigureNamedOptions \<TOptions> . Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření. U pojmenovaných možností se rozlišují velká a malá písmena.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` hodnoty se poskytují z konfigurace, které se načítají z *appsettings.jsv* souboru. `named_options_2` hodnoty poskytuje:

* `named_options_2`Delegát v `ConfigureServices` pro `Option1` .
* Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Do metody přidejte následující kód ručně `Startup.ConfigureServices` :

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Spuštění ukázkové aplikace po přidání kódu vytvoří následující výsledek:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za cílení `Options.DefaultName` instance, což je `string.Empty` . <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> . Výchozí implementace pro <xref:Microsoft.Extensions.Options.IOptionsFactory%601> má logiku odpovídajícím způsobem použít. `null`Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance ( <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence).

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601> se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder` zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici pouze prostřednictvím `OptionsBuilder` .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder \<TOptions> ](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta [konfigurace, protože](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání metody [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) provede přechodný obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> , který má konstruktor, který přijímá obecné zadané typy služeb. 

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavení po konfiguraci nastavte na <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> . Po dokončení všech konfigurací se spustí po konfiguraci <xref:Microsoft.Extensions.Options.IConfigureOptions%601> :

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici pro nastavení po konfiguraci s názvem:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Použijte <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601> a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure` , protože služby jsou vytvořeny před spuštěním `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices` . Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

::: moniker-end

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/configuration/index>
