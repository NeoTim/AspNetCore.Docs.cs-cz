---
title: Vzor možnosti v ASP.NET Core
author: guardrex
description: Objevte, jak použít model možnosti k reprezentování skupiny související nastavení v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2018
uid: fundamentals/configuration/options
ms.openlocfilehash: 99aa5028a8704c7e9e3010415137e2560213a2ad
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505789"
---
# <a name="options-pattern-in-aspnet-core"></a>Vzor možnosti v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Možnosti vzor používá k reprezentování skupiny související nastavení třídy. Když [nastavení konfigurace](xref:fundamentals/configuration/index) jsou izolované aplikace dodržuje podle scénáře do samostatné třídy, dvě důležité softwarového inženýrství zásady:

* [Princip oddělení rozhraní (ISP)](http://deviq.com/interface-segregation-principle/): scénáře (třídy), které závisí na nastavení konfigurace záviset pouze na nastavení konfigurace, které používají.
* [Oddělení oblastí zájmu](http://deviq.com/separation-of-concerns/): nastavení pro různé části aplikace nejsou závislé nebo propojených mezi sebou.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample) ([stažení](xref:index#how-to-download-a-sample)) Tento článek je usnadňuje její sledování s ukázkovou aplikací.

## <a name="prerequisites"></a>Požadavky

::: moniker range=">= aspnetcore-2.1"

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Odkaz [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidejte odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.

::: moniker-end

## <a name="basic-options-configuration"></a>Základní možnosti konfigurace

Základní možnosti konfigurace je znázorněn příklad &num;1 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Třída možností musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třídy `MyOptions`, má dvě vlastnosti `Option1` a `Option2`. Nastavení výchozích hodnot je nepovinný, ale konstruktoru třídy v následujícím příkladu nastaví na výchozí hodnotu `Option1`. `Option2` má výchozí hodnotu nastavenou vlastnost inicializace přímo (*Models/MyOptions.cs*):

[!code-csharp[](options/sample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Třídy se přidá do kontejneru služby s [konfigurovat&lt;TOptions&gt;(IServiceCollection, parametry IConfiguration)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsconfigurationservicecollectionextensions.configure#Microsoft_Extensions_DependencyInjection_OptionsConfigurationServiceCollectionExtensions_Configure__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_Microsoft_Extensions_Configuration_IConfiguration_) a vázán ke konfiguraci:

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example1)]

Na následující stránce používá model [injektáž závislostí konstruktor](xref:mvc/controllers/dependency-injection) s [IOptions&lt;TOptions&gt; ](/dotnet/api/Microsoft.Extensions.Options.IOptions-1) pro přístup k nastavení (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Ukázkové *appsettings.json* souboru určuje hodnoty pro `option1` a `option2`:

[!code-json[](options/sample/appsettings.json?highlight=2-3)]

Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Při použití vlastního [ConfigurationBuilder](/dotnet/api/system.configuration.configurationbuilder) Pokud chcete načíst ze souboru nastavení konfigurace možností, potvrďte, že je správně nastavena základní cesta:
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
> Explicitním nastavením základní cesta není nutné při načítání konfigurace možností z nastavení souboru prostřednictvím [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností s delegáta

Konfigurace jednoduchých možností s delegáta je znázorněn příklad &num;v 2 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Použití delegáta k nastavení hodnot možností. Tato ukázková aplikace používá `MyOptionsWithDelegateConfig` třídy (*Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[](options/sample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu druhý `IConfigureOptions<TOptions>` služby se přidá do kontejneru služby. Delegát používá ke konfiguraci vazby s `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat několik poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici v balíčcích NuGet. Se použijí v pořadí, které jsou registrovány.

Každé volání [konfigurovat&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1.configure) přidá `IConfigureOptions<TOptions>` služby service container. V předchozím příkladu hodnoty `Option1` a `Option2` jsou určené v *appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurované delegáta.

Pokud je povoleno více než jedna služba konfigurace, poslední zdroj konfigurace zadaná *wins* a nastaví hodnotu konfigurace. Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace suboptions

Konfigurace suboptions je znázorněn příklad &num;3 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Aplikace by měl vytvořit třídy možnosti, které se vztahují na konkrétní scénář skupiny (třídy) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty by měl mít přístup pouze na hodnoty konfigurace, které používají.

Při vytváření vazby možnosti konfigurace, každou vlastnost v typu možnosti je vázán na konfigurační klíč ve formátu `property[:sub-property:]`. Například `MyOptions.Option1` vlastnost je vázána na klíč `Option1`, který je pro čtení z `option1` vlastnost v *appsettings.json*.

V následujícím kódu, třetí `IConfigureOptions<TOptions>` služby se přidá do kontejneru služby. Vytvoří vazbu mezi `MySubOptions` do části `subsection` z *appsettings.json* souboru:

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example3)]

`GetSection` – Metoda rozšíření vyžaduje [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíček NuGet. Pokud aplikace využívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), balíček je automaticky přidána.

Ukázkové *appsettings.json* soubor definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:

[!code-json[](options/sample/appsettings.json?highlight=4-7)]

`MySubOptions` Třídy definuje vlastnosti, `SubOption1` a `SubOption2`, pro uchování hodnoty možnosti (*Models/MySubOptions.cs*):

[!code-csharp[](options/sample/Models/MySubOptions.cs?name=snippet1)]

Model stránky `OnGet` metoda vrátí řetězec s hodnotami možnosti (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující možnost dílčí třída hodnoty:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti zobrazení modelu nebo pomocí vkládání přímé zobrazení

Možnosti zobrazení modelu nebo s přístupem zobrazení vkládání je znázorněn příklad &num;4 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

Možnosti může být zadán v zobrazení modelu nebo vložením `IOptions<TOptions>` přímo do zobrazení (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Vkládání s přímým přístupem, Vložit `IOptions<MyOptions>` s `@inject` – direktiva:

[!code-cshtml[](options/sample/Pages/Index.cshtml?range=1-10&highlight=5)]

Při spuštění aplikace se hodnoty možnosti jsou zobrazeny na vykreslené stránce:

![Možnosti hodnot možnost1: value1_from_json a možnost2: -1 se načítají z modelu a vkládání do zobrazení.](options/_static/view.png)

::: moniker range=">= aspnetcore-1.1"

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data s IOptionsSnapshot

Probíhá opětovné načtení dat konfigurace pomocí `IOptionsSnapshot` je ukázáno v příkladu &num;5 [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

[IOptionsSnapshot](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1) podporuje možnosti s minimální režie zpracování znovu načíst.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Možnosti jsou vypočten jednou každý požadavek při přistupovat a uložili do mezipaměti po dobu platnosti požadavku.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

`IOptionsSnapshot` je snímek [IOptionsMonitor&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) a aktualizace automaticky pokaždé, když monitorování aktivuje upravovat podle aktuálních zdroj dat změny.

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

Následující příklad ukazuje, jak nové `IOptionsSnapshot` vytvořené po *appsettings.json* změny (*Pages/Index.cshtml.cs*). Více požadavků na server vrátit konstantní hodnoty podle *appsettings.json* souborů, dokud je soubor změněn a konfigurace znovu načte.

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje úvodní `option1` a `option2` hodnoty načtené z *appsettings.json* souboru:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty *appsettings.json* do souboru `value1_from_json UPDATED` a `200`. Uložit *appsettings.json* souboru. Aktualizujte prohlížeč, pokud chcete zobrazit, že jsou aktualizované hodnoty možnosti:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Možnosti podpory s IConfigureNamedOptions s názvem

Možnosti podpory s názvem [IConfigureNamedOptions](/dotnet/api/microsoft.extensions.options.iconfigurenamedoptions-1) je znázorněn příklad &num;6 v [ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/sample).

*S názvem možnosti* podpora umožňuje, aby aplikace k rozlišení mezi pojmenované možnosti konfigurace. V ukázkové aplikaci s názvem možnosti jsou deklarovány pomocí [OptionsServiceCollectionExtensions.Configure&lt;TOptions&gt;(IServiceCollection, řetězec, akce&lt;TOptions&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configure)která pak volá metodu rozšíření [ConfigureNamedOptions&lt;TOptions&gt;. Konfigurace](/dotnet/api/microsoft.extensions.options.configurenamedoptions-1.configure) metody:

[!code-csharp[](options/sample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenované možnosti s [IOptionsSnapshot&lt;TOptions&gt;. Získat](/dotnet/api/microsoft.extensions.options.ioptionssnapshot-1.get) (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/sample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Spuštěním ukázkové aplikace, jsou vráceny pojmenované možnosti:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` hodnoty jsou k dispozici z konfigurace, které jsou načteny z *appsettings.json* souboru. `named_options_2` hodnoty jsou k dispozici podle:

* `named_options_2` Delegování v `ConfigureServices` pro `Option1`.
* Výchozí hodnota pro `Option2` poskytované `MyOptions` třídy.

## <a name="configure-all-options-with-the-configureall-method"></a>Všechny možnosti nakonfigurovat ConfigureAll – metoda

Nakonfigurujte všechny možnosti instance s [OptionsServiceCollectionExtensions.ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) metody. Následující kód konfiguruje `Option1` pro všechny konfigurace instance s hodnotou běžné. Přidejte následující kód do ručně `ConfigureServices` metody:

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

Spuštěním ukázkové aplikace po přidání kódu vytvoří následující výsledek:

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> Všechny možnosti jsou pojmenované instance. Existující `IConfigureOption` instancí jsou považovány za cílení `Options.DefaultName` instanci, která je `string.Empty`. `IConfigureNamedOptions` také implementuje `IConfigureOptions`. Výchozí implementace [IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) ([zdroj odkazu](https://github.com/aspnet/Options/blob/release/2.0/src/Microsoft.Extensions.Options/IOptionsFactory.cs) obsahuje logiku pro každý odpovídajícím způsobem používat. `null` Pojmenované možnost se používá cílit na všechny pojmenované instance místo konkrétní pojmenovanou instanci ([ConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.configureall) a [PostConfigureAll](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) Tato konvence).

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="optionsbuilder-api"></a>OptionsBuilder rozhraní API

<xref:Microsoft.Extensions.Options.OptionsBuilder`1> slouží ke konfiguraci `TOptions` instancí. `OptionsBuilder` zjednodušuje vytváření, s názvem možnosti, jako je pouze jeden parametr do původní `AddOptions<TOptions>(string optionsName)` volat bez povolí, všechny následné volání. Možnosti ověřování a `ConfigureOptions` přetížení, které přijímají závislostí služby jsou dostupné jen přes `OptionsBuilder`.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");
    
services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="configurelttoptions-tdep1--tdep4gt-method"></a>Konfigurace&lt;TOptions, TDep1,... TDep4&gt; – metoda

Konfigurovat možnosti implementací pomocí služby od DI `IConfigure[Named]Options` standardní způsobem je úplné. Přetížení pro `ConfigureOptions` na `OptionsBuilder<TOptions>` umožňují používat až pět služeb můžete nakonfigurovat možnosti:

```csharp
services.AddOptions<MyOptions>("optionalName")
    .Configure<Service1, Service2, Service3, Service4, Service5>(
        (o, s, s2, s3, s4, s5) => 
            o.Property = DoSomethingWith(s, s2, s3, s4, s5));
```

Přetížení zaregistruje přechodné obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, které má konstruktor, který přijímá typy obecné služby určené. 

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a>Možnosti ověřování

Možnosti ověřování umožňuje ověřit možnosti při nakonfigurování možností. Volání `Validate` s metodu ověřování, která vrátí `true` Pokud možnosti jsou platné a `false` Pokud nejsou platné:

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

V předchozím příkladu nastaví možnosti pojmenovanou instanci na `optionalOptionsName`. Výchozí možnosti instance je `Options.DefaultName`.

Ověření se spustí, jakmile se vytvoří instance možností. Je zaručeno, že vaše instance možnosti předat čas ověření první, který je přístupný.

> [!IMPORTANT]
> Možnosti ověřování nepodporuje ochranu proti možnosti úprav po možnosti počáteční konfiguraci a ověřit.

`Validate` Metoda přijímá `Func<TOptions, bool>`. Chcete-li plně přizpůsobit ověřování, implementovat `IValidateOptions<TOptions>`, která umožňuje:

* Ověření více typů možnosti: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, který závisí na jiný typ možnosti: `public DependsOnAnotherOptionValidator(IOptions<AnotherOption> options)`

`IValidateOptions` ověří:

* Konkrétní pojmenované instance možností.
* Všechny možnosti `name` je `null`.

Vrátit `ValidateOptionsResult` od implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data ověřování na základě poznámek je k dispozici [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) balíčku voláním `ValidateDataAnnotations` metodu na `OptionsBuilder<TOptions>`:

```csharp
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
        sp.GetRequiredService<IOptions<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}    
```

V úvahu pro budoucí verzi se nemůžou dočkat, až ověřování (selhání rychle při spuštění).

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

## <a name="ipostconfigureoptions"></a>IPostConfigureOptions

Nastavte postconfiguration s [IPostConfigureOptions&lt;TOptions&gt;](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1). Postconfiguration běží po všech [IConfigureOptions&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) vyvolá konfigurace:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

[PostConfigure&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ipostconfigureoptions-1.postconfigure) je dostupné pro následující po konfiguraci s názvem možnosti:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Použití [PostConfigureAll&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.dependencyinjection.optionsservicecollectionextensions.postconfigureall) po konfiguraci všechny instance konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

::: moniker-end

## <a name="options-factory-monitoring-and-cache"></a>Možnosti objekt pro vytváření, monitorování a mezipaměť

[IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) se používá pro oznámení při `TOptions` instance změnit. `IOptionsMonitor` podporuje možnosti opětovně načítá, oznámení, změn a `IPostConfigureOptions`.

::: moniker range=">= aspnetcore-2.0"

[IOptionsFactory&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1) zodpovídá za vytvoření nové možnosti instancí. Má jediný [vytvořit](/dotnet/api/microsoft.extensions.options.ioptionsfactory-1.create) metody. Výchozí implementace používá všech registrovaných `IConfigureOptions` a `IPostConfigureOptions` a spustí všechny nakonfiguruje nejprve, za nímž následuje po konfiguruje. Rozlišuje mezi `IConfigureNamedOptions` a `IConfigureOptions` a jen volá odpovídající rozhraní.

[IOptionsMonitorCache&lt;TOptions&gt; ](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1) používá `IOptionsMonitor` do mezipaměti `TOptions` instancí. `IOptionsMonitorCache` Zruší platnost instancí možnosti v monitorování, tak, aby přepočítány hodnoty ([TryRemove](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryremove)). Hodnoty mohou být ručně nepředchází funkce také [TryAdd](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.tryadd). [Vymazat](/dotnet/api/microsoft.extensions.options.ioptionsmonitorcache-1.clear) metoda se používá, když všechny pojmenované instance by měl být znovu vytvořit na vyžádání.

::: moniker-end

## <a name="accessing-options-during-startup"></a>Přístup k možnosti při spuštění

`IOptions` je možné v `Startup.Configure`, protože služby jsou sestaveny dříve, než `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.Value.Option1;
}
```

`IOptions` neměli byste používat v `Startup.ConfigureServices`. Příčinou je pořadí registrace služby můžou existovat nekonzistentní možnosti dostane do stavu.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
