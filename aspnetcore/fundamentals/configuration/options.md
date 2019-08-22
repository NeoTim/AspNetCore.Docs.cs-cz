---
title: Vzor možností v ASP.NET Core
author: guardrex
description: Zjistěte, jak pomocí vzoru možností znázornit skupiny souvisejících nastavení v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 22dde4b05ea20fedb696c6a4b144755a957e8c0d
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886378"
---
# <a name="options-pattern-in-aspnet-core"></a>Vzor možností v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Oznámení změny
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu Načítatelné konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()

Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti. Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností. Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601> Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.

Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`. Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`. `Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

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
> Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností pomocí delegáta

Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.

K nastavení hodnot možností použijte delegáta. Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba. Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.

Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace. Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace podNastavení

Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.

Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.

Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`. Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba. Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection` Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.

Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení

Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.

Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data pomocí IOptionsSnapshot

Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.

Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.

Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*). Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`. Uložte soubor *appSettings. JSON* . Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností s IConfigureNamedOptions

Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.

Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenovaným možnostem <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Když spustíte ukázkovou aplikaci, vrátí se pojmenované možnosti:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* . `named_options_2`hodnoty poskytuje:

* Delegát v `ConfigureServices` pro`Option1`. `named_options_2`
* Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Do `Startup.ConfigureServices` metody přidejte následující kód ručně:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít. Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb. 

## <a name="options-validation"></a>Ověřování možností

Ověřování možností umožňuje ověřit možnosti při konfiguraci možností. Zavolejte `Validate` na metodu ověřování, která vrátí `true` , pokud jsou možnosti platné `false` , a pokud nejsou platné:

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

Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`. Instance výchozích možností je `Options.DefaultName`.

Ověřování se spustí při vytvoření instance Options. Při prvním použití vaší instance možností je zaručeno předání ověření.

> [!IMPORTANT]
> Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.

`Validate` Metoda`Func<TOptions, bool>`přijímá. Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:

* Ověřování více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`ověří

* Konkrétní instance pojmenované možnosti.
* Všechny možnosti, `name` Pokud `null`je.

Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options.](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) DataAnnotations pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> volání metody `OptionsBuilder<TOptions>`on. `Microsoft.Extensions.Options.DataAnnotations`je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
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

Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na. Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:

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

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Oznámení změny
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu Načítatelné konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()

Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti. Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností. Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601> Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.

Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`. Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`. `Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

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
> Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností pomocí delegáta

Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.

K nastavení hodnot možností použijte delegáta. Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba. Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.

Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace. Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace podNastavení

Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.

Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.

Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`. Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba. Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection` Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.

Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení

Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.

Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data pomocí IOptionsSnapshot

Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.

Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.

Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*). Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`. Uložte soubor *appSettings. JSON* . Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností s IConfigureNamedOptions

Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.

Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:

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

`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* . `named_options_2`hodnoty poskytuje:

* Delegát v `ConfigureServices` pro`Option1`. `named_options_2`
* Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Do `Startup.ConfigureServices` metody přidejte následující kód ručně:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít. Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb. 

## <a name="options-validation"></a>Ověřování možností

Ověřování možností umožňuje ověřit možnosti při konfiguraci možností. Zavolejte `Validate` na metodu ověřování, která vrátí `true` , pokud jsou možnosti platné `false` , a pokud nejsou platné:

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

Předchozí příklad nastaví instanci pojmenované možnosti na `optionalOptionsName`. Instance výchozích možností je `Options.DefaultName`.

Ověřování se spustí při vytvoření instance Options. Při prvním použití vaší instance možností je zaručeno předání ověření.

> [!IMPORTANT]
> Ověřování možností nechrání proti změnám možností po počáteční konfiguraci a ověření možností.

`Validate` Metoda`Func<TOptions, bool>`přijímá. Pro úplné přizpůsobení ověřování, implementujte `IValidateOptions<TOptions>`, což umožňuje:

* Ověřování více typů možností:`class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Ověření, které závisí na jiném typu možnosti:`public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions`ověří

* Konkrétní instance pojmenované možnosti.
* Všechny možnosti, `name` Pokud `null`je.

Vraťte se `ValidateOptionsResult` z vaší implementace rozhraní:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Ověřování na základě datových poznámek je k dispozici v balíčku [Microsoft. Extensions. Options.](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) DataAnnotations pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> volání metody `OptionsBuilder<TOptions>`on. `Microsoft.Extensions.Options.DataAnnotations`je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,2 nebo novější).

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
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

Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na. Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:

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

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení. Když jsou [nastavení konfigurace](xref:fundamentals/configuration/index) izolována scénářem do samostatných tříd, aplikace dodržuje dva důležité principy technického řízení:

* [Princip oddělení (ISP) nebo](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénářů zapouzdření (třídy) závislých na nastavení konfigurace závisí jenom na nastavení konfigurace, která používají.
* [Oddělení obav](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Nastavení pro různé části aplikace nejsou závislá ani vzájemně nezávislá.

Možnosti také poskytují mechanismus pro ověření konfiguračních dat. Další informace najdete v části [ověřování možností](#options-validation) .

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) .

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor%601>slouží k načtení možností a správě oznámení možností pro `TOptions` instance. <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>podporuje následující scénáře:

* Oznámení změny
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Znovu Načítatelné konfigurace](#reload-configuration-data-with-ioptionssnapshot)
* Neplatných selektivních možností<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>()

Scénáře po [konfiguraci](#options-post-configuration) umožňují nastavit nebo změnit možnosti po výskytu všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací.

<xref:Microsoft.Extensions.Options.IOptionsFactory%601>zodpovídá za vytváření nových instancí možností. Má jedinou <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metodu. Výchozí implementace přebírá všechny <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurace a <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> a spustí nejprve všechny konfigurace, následované po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a <xref:Microsoft.Extensions.Options.IConfigureOptions%601> a volá pouze příslušné rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>je používána <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro ukládání instancí `TOptions` do mezipaměti. Zruší platnost instancí možností v monitorování tak, aby se hodnota přepočítala (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> Hodnoty lze ručně zavést pomocí <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. Metoda <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> se používá v případě, že by měly být všechny pojmenované instance znovu vytvořeny na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>je užitečná ve scénářích, kde by se měly přepočítat možnosti pro každý požadavek. Další informace najdete v části [opětovné načtení konfiguračních dat pomocí IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) .

<xref:Microsoft.Extensions.Options.IOptions%601>dá se použít k podpoře možností. Nicméně nepodporuje předchozí <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>scénáře. <xref:Microsoft.Extensions.Options.IOptions%601> Můžete pokračovat v používání <xref:Microsoft.Extensions.Options.IOptions%601> existujících architektur a knihoven, které již <xref:Microsoft.Extensions.Options.IOptions%601> rozhraní používají, a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>nevyžadují scénáře, které poskytuje.

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností se v ukázkové aplikaci &num;ukazuje jako příklad 1.

Třída Options musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třída `MyOptions`má dvě vlastnosti, `Option1` a `Option2`. Nastavení výchozích hodnot je volitelné, ale konstruktor třídy v následujícím příkladu nastaví výchozí hodnotu `Option1`. `Option2`má výchozí hodnotu nastavenou pomocí přímé inicializace vlastnosti (*modely/MyOptions. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

Třída je přidána do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> konfigurací a je vázána na konfiguraci: `MyOptions`

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Následující model stránky používá [vkládání závislostí konstruktoru](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> pro přístup k nastavení (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Soubor *appSettings. JSON* v ukázce určuje hodnoty pro `option1` a: `option2`

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

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
> Při načítání konfigurace možností ze souboru s nastavením prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>je explicitně nutné nastavit základní cestu.

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností pomocí delegáta

Konfigurace jednoduchých možností s delegátem je znázorněna jako &num;příklad 2 v ukázkové aplikaci.

K nastavení hodnot možností použijte delegáta. Ukázková aplikace používá `MyOptionsWithDelegateConfig` třídu (*modely/MyOptionsWithDelegateConfig. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána druhá služba. Pomocí delegáta konfiguruje vazbu s `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat více poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou aplikováni v pořadí, v jakém jsou registrováni. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidá službu do kontejneru služby. V předchozím příkladu jsou `Option1` hodnoty a `Option2` zadány v souboru `Option1` *appSettings. JSON*, ale hodnoty a `Option2` jsou přepsány nakonfigurovaným delegátem.

Je-li povolena více než jedna služba konfigurace, je v posledním zdroji konfigurace zadaný *Server WINS* a nastavena hodnota konfigurace. Při spuštění aplikace vrátí `OnGet` metoda modelu stránky řetězec zobrazující hodnoty třídy možností:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace podNastavení

Konfigurace dílčích možností je v ukázkové &num;aplikaci znázorněna jako příklad 3.

Aplikace by měly vytvořit třídy možností, které se týkají konkrétních skupin scénářů (tříd) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty, by měly mít přístup jenom k hodnotám konfigurace, které používají.

Při vytváření vazby mezi možnostmi konfigurace jsou jednotlivé vlastnosti v typu možnosti vázány na konfigurační klíč formuláře `property[:sub-property:]`. Například `MyOptions.Option1` vlastnost je svázána s klíčem `Option1`, `option1` který je načten z vlastnosti v souboru *appSettings. JSON*.

V následujícím kódu je do kontejneru služby <xref:Microsoft.Extensions.Options.IConfigureOptions%601> přidána třetí služba. Váže `MySubOptions` se k části `subsection` souboru *appSettings. JSON* :

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

Metoda rozšíření vyžaduje balíček [Microsoft. Extensions. Options. ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet. `GetSection` Pokud aplikace používá [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější), balíček je automaticky zahrnutý.

Soubor *appSettings. JSON* ukázky definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

Třída definuje `SubOption1` vlastnosti a`SubOption2`, aby obsahovala hodnoty možností (*modely/MySubOptions. cs*): `MySubOptions`

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

`OnGet` Metoda modelu stránky vrátí řetězec s hodnotami možností (*pages/index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění `OnGet` aplikace vrátí metoda řetězec zobrazující hodnoty třídy dílčích možností:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení

Možnosti poskytované modelem zobrazení nebo vkládáním přímých zobrazení jsou v ukázkové aplikaci znázorněné jako příklad &num;4.

Možnosti lze zadat v modelu zobrazení nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> přímým vložením do zobrazení (Pages */index. cshtml. cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` `@inject` pomocí direktivy:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Po spuštění aplikace se hodnoty možností zobrazí na vykreslené stránce:

![Hodnoty možností možnost1: value1_from_json a Možnost2:-1 jsou načteny z modelu a vložením do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data pomocí IOptionsSnapshot

Opětovné načtení konfiguračních dat pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> je v ukázkové aplikaci znázorněno v příkladu &num;5.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>podporuje opětovné načítání možností s minimální režií zpracování.

Možnosti jsou vypočítány jednou za požadavek při otevření a ukládání do mezipaměti po dobu života žádosti.

Následující příklad ukazuje, jak se vytvoří <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nový po změnách *appSettings. JSON* (*pages/index. cshtml. cs*). Více požadavků na server vrací konstantní hodnoty, které poskytuje soubor *appSettings. JSON* , dokud nedojde ke změně souboru a opětovnému načtení konfigurace.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje počáteční `option1` hodnoty a `option2` hodnoty načtené ze souboru *appSettings. JSON* :

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty v souboru *appSettings. JSON* na `value1_from_json UPDATED` a `200`. Uložte soubor *appSettings. JSON* . Aktualizujte prohlížeč, aby se zobrazily hodnoty možností, které jsou aktualizované:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Podpora pojmenovaných možností s IConfigureNamedOptions

Pojmenovaná podpora <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> s názvem je znázorněna jako příklad &num;6 v ukázkové aplikaci.

Podpora pojmenovaných *možností* umožňuje aplikaci rozlišovat konfigurace pojmenovaných možností. V ukázkové aplikaci jsou pojmenované možnosti deklarovány pomocí [OptionsServiceCollectionExtensions. Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) metody rozšíření:

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

`named_options_1`hodnoty se poskytují z konfigurace, které se načítají ze souboru *appSettings. JSON* . `named_options_2`hodnoty poskytuje:

* Delegát v `ConfigureServices` pro`Option1`. `named_options_2`
* Výchozí hodnota `Option2` poskytnutá `MyOptions` třídou

## <a name="configure-all-options-with-the-configureall-method"></a>Konfigurace všech možností pomocí metody ConfigureAll

Nakonfigurujte všechny instance možností pomocí <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny instance konfigurace se společnou hodnotou. Do `Startup.ConfigureServices` metody přidejte následující kód ručně:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instance jsou považovány za `Options.DefaultName` cílení instance, což `string.Empty`je. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601>. Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory%601> pro má logiku odpovídajícím způsobem použít. Pojmenovaná možnost se používá pro cílení na všechny pojmenované instance místo konkrétní pojmenované instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> použití této konvence). `null`

## <a name="optionsbuilder-api"></a>Rozhraní API pro OptionsBuilder

<xref:Microsoft.Extensions.Options.OptionsBuilder%601>se používá ke konfiguraci `TOptions` instancí. `OptionsBuilder`zjednodušuje vytváření pojmenovaných možností, protože je jenom jedním parametrem počátečního `AddOptions<TOptions>(string optionsName)` volání, takže se neobjeví ve všech dalších voláních. Ověřování možností a `ConfigureOptions` přetížení, která přijímají závislosti služby, jsou k dispozici `OptionsBuilder`pouze prostřednictvím.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Ke konfiguraci možností použijte DI Services.

K dalším službám můžete přistupovat pomocí injektáže závislosti při konfiguraci možností dvěma způsoby:

* Předejte delegáta konfigurace [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [\<OptionsBuilder TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [Konfigurace](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , která umožňují použít až pět služeb ke konfiguraci možností:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořte vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions%601> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> a zaregistruje typ jako službu.

Doporučujeme předat delegáta konfigurace, protože [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*)vytvoření služby je složitější. Vytvoření vlastního typu je ekvivalentní s tím, k čemu architektura slouží při použití možnosti [Konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) metody Configure provede přechodný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>obecný, který má konstruktor, který přijímá obecné zadané typy služeb. 

## <a name="options-post-configuration"></a>Možnosti po konfiguraci

Nastavení po konfiguraci <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>nastavte na. Po dokončení všech <xref:Microsoft.Extensions.Options.IConfigureOptions%601> konfigurací se spustí po konfiguraci:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*>je k dispozici pro nastavení po konfiguraci s názvem:

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

<xref:Microsoft.Extensions.Options.IOptions%601>a <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> lze jej použít v `Startup.Configure`, protože `Configure` služby jsou vytvořeny před spuštěním metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions%601> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> v `Startup.ConfigureServices`. Stav nekonzistentních možností může existovat v důsledku řazení registrací služby.

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
