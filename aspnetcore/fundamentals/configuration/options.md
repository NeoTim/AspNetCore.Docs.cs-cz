---
title: Vzor možností v ASP.NET jádru
author: rick-anderson
description: Zjistěte, jak pomocí vzoru možností reprezentovat skupiny souvisejících nastavení v ASP.NET základních aplikacích.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
uid: fundamentals/configuration/options
ms.openlocfilehash: 756d3d57122642ab10ab671c9accb75975c3799d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665457"
---
# <a name="options-pattern-in-aspnet-core"></a>Vzor možností v ASP.NET jádru

::: moniker range=">= aspnetcore-3.0"

Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:

* [Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.
* [Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace naleznete v části [Možnosti ověření.](#options-validation)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="package"></a>Balíček

Balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) je implicitně odkazován v aplikacích ASP.NET Core.

## <a name="options-interfaces"></a>Rozhraní možností

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Změnit oznámení
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu načítatelná konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )

[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti. Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ). Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku. Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)

<xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností. Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.

Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a . Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu . `Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:
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
> Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností s delegátem

Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.

Pomocí delegáta nastavte hodnoty možností. Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba. Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.

Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace. Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace dílčích možností

Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.

Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám). Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.

Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře . Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba. Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.

Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Možnosti vstřikování

Možnosti vkládání je demonstrována jako příklad 4 v ukázkové aplikaci.

Vstříkněte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Razor stránky nebo MVC [`@inject`](xref:mvc/views/razor#inject) zobrazení se směrnicí Razor.
* Model stránky nebo zobrazení.

Následující příklad z ukázkové <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikace vloží do modelu stránky *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot

Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.

Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu platnosti požadavku.

Rozdíl mezi `IOptionsMonitor` `IOptionsSnapshot` a je, že:

* `IOptionsMonitor`je [služba singleton,](xref:fundamentals/dependency-injection#singleton) která kdykoli načte aktuální hodnoty možností, což je užitečné zejména v závislostech singleton.
* `IOptionsSnapshot`je [služba s vymezeným oborem](xref:fundamentals/dependency-injection#scoped) a poskytuje `IOptionsSnapshot<T>` snímek možností v době, kdy je objekt vytvořen. Možnosti snímky jsou určeny pro použití s přechodnými závislostmi a závislostmi s vymezeným oborem.

Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*). Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`. Uložte soubor *appsettings.json.* Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions

Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.

Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření. Pojmenované možnosti rozlišují malá a velká písmena.

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.* `named_options_2`hodnoty jsou poskytovány:

* Delegát `named_options_2` v `ConfigureServices` `Option1`pro .
* Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Přidejte do `Startup.ConfigureServices` metody ručně následující kód:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně. Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).

## <a name="optionsbuilder-api"></a>Rozhraní API pro stavitele možností

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních. Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurace možností pomocí služeb DI

Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). `OptionsBuilder<TOptions>`poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují použití až pět služeb pro konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.

Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb. 

## <a name="options-validation"></a>Ověření možností

Ověření možností umožňuje ověřit možnosti při konfiguraci možností. Volání `Validate` s metodou ověření, která vrátí, `true` pokud jsou možnosti platné a `false` pokud nejsou platné:

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

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Předchozí příklad nastaví instanci `optionalOptionsName`pojmenovaných možností na . Výchozí instance možností `Options.DefaultName`je .

Ověření se spustí při vytvoření instance možností. Instance možností je zaručeno, že projít ověření při prvním přístupu.

> [!IMPORTANT]
> Ověření možností nechrání před úpravami možností po vytvoření instance možností. Například `IOptionsSnapshot` možnosti jsou vytvořeny a ověřeny jednou na požadavek při prvním přístupu k možnostem. Možnosti `IOptionsSnapshot` nejsou znovu ověřeny při následných pokusech o přístup *pro stejný požadavek*.

Metoda `Validate` přijímá `Func<TOptions, bool>`. Chcete-li plně `IValidateOptions<TOptions>`přizpůsobit ověření, implementujte , který umožňuje:

* Ověření více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Ověřuje:

* Konkrétní instance pojmenovaných možností.
* Všechny možnosti, kdy `name` je `null`.

Vraťte `ValidateOptionsResult` a z implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Ověření založené na datových poznámkách je k dispozici v balíčku [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody na . `OptionsBuilder<TOptions>` `Microsoft.Extensions.Options.DataAnnotations`je implicitně odkazováno v aplikacích ASP.NET Core.

```csharp
using System.ComponentModel.DataAnnotations;
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

Eager ověření (selhání rychle při spuštění) je v úvahu pro budoucí verzi.

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:

* [Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.
* [Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace naleznete v části [Možnosti ověření.](#options-validation)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)

## <a name="options-interfaces"></a>Rozhraní možností

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Změnit oznámení
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu načítatelná konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )

[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti. Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ). Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku. Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)

<xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností. Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.

Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a . Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu . `Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:
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
> Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností s delegátem

Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.

Pomocí delegáta nastavte hodnoty možností. Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba. Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.

Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace. Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace dílčích možností

Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.

Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám). Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.

Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře . Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba. Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.

Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a>Možnosti vstřikování

Možnosti vkládání je demonstrována jako příklad 4 v ukázkové aplikaci.

Vstříkněte <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> do:

* Razor stránky nebo MVC [`@inject`](xref:mvc/views/razor#inject) zobrazení se směrnicí Razor.
* Model stránky nebo zobrazení.

Následující příklad z ukázkové <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> aplikace vloží do modelu stránky *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot

Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.

Pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu platnosti požadavku.

Rozdíl mezi `IOptionsMonitor` `IOptionsSnapshot` a je, že:

* `IOptionsMonitor`je [služba singleton,](xref:fundamentals/dependency-injection#singleton) která kdykoli načte aktuální hodnoty možností, což je užitečné zejména v závislostech singleton.
* `IOptionsSnapshot`je [služba s vymezeným oborem](xref:fundamentals/dependency-injection#scoped) a poskytuje `IOptionsSnapshot<T>` snímek možností v době, kdy je objekt vytvořen. Možnosti snímky jsou určeny pro použití s přechodnými závislostmi a závislostmi s vymezeným oborem.

Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*). Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`. Uložte soubor *appsettings.json.* Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions

Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.

Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření. Pojmenované možnosti rozlišují malá a velká písmena.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.* `named_options_2`hodnoty jsou poskytovány:

* Delegát `named_options_2` v `ConfigureServices` `Option1`pro .
* Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Přidejte do `Startup.ConfigureServices` metody ručně následující kód:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně. Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).

## <a name="optionsbuilder-api"></a>Rozhraní API pro stavitele možností

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních. Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurace možností pomocí služeb DI

Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují používat až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.

Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb. 

## <a name="options-validation"></a>Ověření možností

Ověření možností umožňuje ověřit možnosti při konfiguraci možností. Volání `Validate` s metodou ověření, která vrátí, `true` pokud jsou možnosti platné a `false` pokud nejsou platné:

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

Předchozí příklad nastaví instanci `optionalOptionsName`pojmenovaných možností na . Výchozí instance možností `Options.DefaultName`je .

Ověření se spustí při vytvoření instance možností. Instance možností je zaručeno, že projít ověření při prvním přístupu.

> [!IMPORTANT]
> Ověření možností nechrání před úpravami možností po vytvoření instance možností. Například `IOptionsSnapshot` možnosti jsou vytvořeny a ověřeny jednou na požadavek při prvním přístupu k možnostem. Možnosti `IOptionsSnapshot` nejsou znovu ověřeny při následných pokusech o přístup *pro stejný požadavek*.

Metoda `Validate` přijímá `Func<TOptions, bool>`. Chcete-li plně `IValidateOptions<TOptions>`přizpůsobit ověření, implementujte , který umožňuje:

* Ověření více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`Ověřuje:

* Konkrétní instance pojmenovaných možností.
* Všechny možnosti, kdy `name` je `null`.

Vraťte `ValidateOptionsResult` a z implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Ověření založené na datových poznámkách je k dispozici v balíčku [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) voláním <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> metody na . `OptionsBuilder<TOptions>` `Microsoft.Extensions.Options.DataAnnotations`je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

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

Eager ověření (selhání rychle při spuštění) je v úvahu pro budoucí verzi.

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy softwarového inženýrství:

* [Princip segregace rozhraní (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře zapouzdření (třídy), které závisí na nastavení konfigurace, závisí pouze na nastavení konfigurace, které používají.
* [Nastavení oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; pro různé části aplikace není závislé nebo vzájemně se nespřahouje.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace naleznete v části [Možnosti ověření.](#options-validation)

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.Extensions.Options.ConfigurationExtensions.](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)

## <a name="options-interfaces"></a>Rozhraní možností

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a `TOptions` správě oznámení o možnostech pro instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Změnit oznámení
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu načítatelná konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Selektivní možnosti<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>zneplatnění ( )

[Scénáře po konfiguraci](#options-post-configuration) umožňují nastavit nebo <xref:Microsoft.Extensions.Options.IConfigureOptions%601> změnit možnosti po všech konfiguracích.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>je zodpovědný za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace trvá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> všechny <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> registrované a spustí všechny konfigurace jako první, následuje post-konfigurace. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> pouze volá příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>slouží <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> k ukládání `TOptions` instancí do mezipaměti. Zruší <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> platnost instance možností v monitoru tak, aby hodnota<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>byla přepočítána ( ). Hodnoty lze ručně zavést <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>pomocí . Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá, když všechny pojmenované instance by měly být znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečné ve scénářích, kde by měly být přepočítány možnosti na každém požadavku. Další informace naleznete v části [Znovu načíst konfigurační data pomocí iOptionsSnapshot.](#reload-configuration-data-with-ioptionssnapshot)

<xref:Microsoft.Extensions.Options.IOptions%601>lze použít k podpoře možností. Však <xref:Microsoft.Extensions.Options.IOptions%601> nepodporuje předchozí scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>. Můžete pokračovat v <xref:Microsoft.Extensions.Options.IOptions%601> používání v existujících architekturách <xref:Microsoft.Extensions.Options.IOptions%601> a knihovnách, které již používají <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>rozhraní a nevyžadují scénáře poskytované aplikací .

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Obecná konfigurace možností je v ukázkové aplikaci demonstrována jako Příklad 1.

Třída options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`, má dvě `Option1` `Option2`vlastnosti a . Nastavení výchozích hodnot je volitelné, ale konstruktor třídy `Option1`v následujícím příkladu nastaví výchozí hodnotu . `Option2`má výchozí hodnotu nastavenou přímou inicializací vlastnosti *(Models/MyOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída `MyOptions` je přidána do <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> kontejneru služby s konfigurací a vázána na konfiguraci:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přístupem k nastavení *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Ukázkový soubor *appsettings.json* určuje hodnoty `option1` `option2`pro a :

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Při použití <xref:System.Configuration.ConfigurationBuilder> vlastní konfigurace možností z souboru nastavení zkontrolujte, zda je základní cesta nastavena správně:
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
> Explicitní nastavení základní cesty není nutné při načítání konfigurace <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>možností z souboru nastavení prostřednictvím aplikace .

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností s delegátem

Konfigurace jednoduchých možností s delegátem je v ukázkové aplikaci demonstrována jako příklad 2.

Pomocí delegáta nastavte hodnoty možností. Ukázková aplikace `MyOptionsWithDelegateConfig` používá třídu *(Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána druhá služba. Používá delegáta ke konfiguraci `MyOptionsWithDelegateConfig`vazby pomocí aplikace :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Zprostředkovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použity v pořadí, ve kterém jsou registrovány. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions%601> službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty `Option2` a jsou zadány v *souboru appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurovaným delegátem.

Pokud je povolena více než jedna konfigurační služba, *vyhrává* poslední zadaný zdroj konfigurace a nastavuje hodnotu konfigurace. Při spuštění aplikace `OnGet` metoda modelu stránky vrátí řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace dílčích možností

Konfigurace dílčích možností je v ukázkové aplikaci demonstrována jako Příklad 3.

Aplikace by měly vytvářet třídy možností, které se v aplikaci připojují ke konkrétním skupinám scénářů (třídám). Části aplikace, které vyžadují hodnoty konfigurace by měl mít přístup pouze k hodnotám konfigurace, které používají.

Při vazbě možnosti konfigurace, každá vlastnost v typu možnosti `property[:sub-property:]`je vázánna na konfigurační klíč formuláře . Například `MyOptions.Option1` vlastnost je vázána `Option1`na klíč , `option1` který je číst z vlastnosti v *appsettings.json*.

V následujícím kódu je <xref:Microsoft.Extensions.Options.IConfigureOptions%601> do kontejneru služby přidána třetí služba. Váže `MySubOptions` se na `subsection` část souboru *appsettings.json:*

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda `GetSection` vyžaduje <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> obor názvů.

Ukázkový soubor *appsettings.json* definuje `subsection` člen s `suboption1` klíči pro a `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída `MySubOptions` definuje vlastnosti `SubOption1` `SubOption2`a , chcete-li podržet hodnoty možností *(Models/MySubOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami voleb *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující hodnoty třídy dílčí možnosti:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti poskytované modelem pohledu nebo přímým vkládáním pohledu

Možnosti poskytované model zobrazení nebo přímé zobrazení vkládání je demonstrován jako příklad 4 v ukázkové aplikaci.

Možnosti lze dodat v modelu zobrazení <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nebo vložením přímo do zobrazení *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, `IOptionsMonitor<MyOptions>` jak `@inject` vložit direktivu:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Při spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností Option1: value1_from_json a Option2: -1 jsou načteny z modelu a vstřikováním do pohledu.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Opětovné načtení konfiguračních dat pomocí aplikace IOptionsSnapshot

Opětovné načtení <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> konfiguračních dat s je znázorněno v příkladu 5 v ukázkové aplikaci.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje možnosti opětovného načtení s minimální režií zpracování.

Možnosti jsou vypočítány jednou na požadavek při přístupu a ukládání do mezipaměti po dobu životnosti požadavku.

Následující příklad ukazuje, jak <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je vytvořen nový po *appsettings.json* změny (*Pages/Index.cshtml.cs*). Více požadavků na server vrátí konstantní hodnoty poskytované souborem *appsettings.json,* dokud se soubor nezmění a konfigurace se znovu nenačte.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek znázorňuje počáteční `option1` hodnoty načtené `option2` ze souboru *appsettings.json:*

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appsettings.json* na `value1_from_json UPDATED` a `200`. Uložte soubor *appsettings.json.* Aktualizujte prohlížeč, abyste viděli, že jsou aktualizovány hodnoty možností:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností pomocí nástroje IConfigureNamedOptions

Pojmenované možnosti <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> podpora s je demonstrován jako příklad 6 v ukázkové aplikaci.

Podpora pojmenovaných možností umožňuje aplikaci rozlišovat mezi konfiguracemi pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [> ConfigureNamedOptions\<TOptions. Nakonfigurujte](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metodu rozšíření. Pojmenované možnosti rozlišují malá a velká písmena.

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> pomocí *(Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Spuštění ukázkové aplikace, pojmenované možnosti jsou vráceny:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`hodnoty jsou k dispozici z konfigurace, které jsou načteny ze souboru *appsettings.json.* `named_options_2`hodnoty jsou poskytovány:

* Delegát `named_options_2` v `ConfigureServices` `Option1`pro .
* Výchozí hodnota `Option2` pro poskytované `MyOptions` třídy.

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance možností pomocí metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Přidejte do `Startup.ConfigureServices` metody ručně následující kód:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, která je `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také provádí <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace má <xref:Microsoft.Extensions.Options.IOptionsFactory%601> logiku použít každý vhodně. Pojmenovaná `null` možnost se používá k cílení na všechny pojmenované<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> instance <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> namísto konkrétní pojmenované instance (a použít tuto konvenci).

## <a name="optionsbuilder-api"></a>Rozhraní API pro stavitele možností

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>slouží ke `TOptions` konfiguraci instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože se `AddOptions<TOptions>(string optionsName)` jedná pouze o jeden parametr počátečního volání namísto zobrazení ve všech následných voláních. Možnosti ověření `ConfigureOptions` a přetížení, které přijímají závislosti `OptionsBuilder`služby jsou k dispozici pouze prostřednictvím .

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Konfigurace možností pomocí služeb DI

Můžete přistupovat k dalším službám z vkládání závislostí při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [konfigurovat na](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurovat,](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) které umožňují používat až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, <xref:Microsoft.Extensions.Options.IConfigureOptions%601> <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> který implementuje nebo a zaregistrujte typ jako službu.

Doporučujeme předat delegáta konfigurace [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), protože vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní tomu, co pro vás framework dělá při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registruje přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný , který má konstruktor, který přijímá zadané obecné typy služeb. 

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavte post-konfiguraci s <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>. Po konfiguraci se <xref:Microsoft.Extensions.Options.IConfigureOptions%601> spustí po všech konfiguracích:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro následné konfigurace pojmenovaných možností:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Slouží <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> k následné konfiguraci všech instancí konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnostem při spuštění

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze použít `Startup.Configure`v , protože `Configure` služby jsou sestaveny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> ani <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Nekonzistentní možnosti stavu může existovat z důvodu řazení registrace služby.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
