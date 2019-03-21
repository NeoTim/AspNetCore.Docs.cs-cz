---
title: Vzor možnosti v ASP.NET Core
author: guardrex
description: Objevte, jak použít model možnosti k reprezentování skupiny související nastavení v aplikacích ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: 8d219658b7116ad7e1f2a73678ef76ea1ce69595
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209184"
---
# <a name="options-pattern-in-aspnet-core"></a>Vzor možnosti v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range="<= aspnetcore-1.1"

1.1 verzi tohoto tématu, stáhněte si [vzor možnosti v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf).

::: moniker-end

Možnosti vzor používá k reprezentování skupiny související nastavení třídy. Když [nastavení konfigurace](xref:fundamentals/configuration/index) jsou izolované aplikace dodržuje podle scénáře do samostatné třídy, dvě důležité softwarového inženýrství zásady:

* [Princip oddělení rozhraní (ISP) nebo zapouzdření](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; scénáře (třídy), které závisí na nastavení konfigurace záviset pouze na nastavení konfigurace, které používají.
* [Oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; nastavení pro různé části aplikace nejsou závislé nebo propojených mezi sebou.

Možnosti taky mělo poskytovat mechanismus pro ověření konfigurační data. Další informace najdete v tématu [možnosti ověření](#options-validation) oddílu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíčku.

## <a name="options-interfaces"></a>Možnosti rozhraní

<xref:Microsoft.Extensions.Options.IOptionsMonitor`1> slouží k načtení možností a spravovat oznámení možnosti pro `TOptions` instancí. <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> podporuje následující scénáře:

* Oznámení o změnách
* [Pojmenované možnosti](#named-options-support-with-iconfigurenamedoptions)
* [Konfigurace možností](#reload-configuration-data-with-ioptionssnapshot)
* Selektivní možnosti zneplatnění (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1>)

[Po konfiguraci](#options-post-configuration) scénářů umožňují nastavit nebo změnit možnosti koneckonců <xref:Microsoft.Extensions.Options.IConfigureOptions`1> konfiguraci dochází.

<xref:Microsoft.Extensions.Options.IOptionsFactory`1> zodpovídá za vytvoření nové instance možností. Má jediný <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> metody. Výchozí implementace používá všech registrovaných <xref:Microsoft.Extensions.Options.IConfigureOptions`1> a <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1> a spustí všechny konfigurace nejprve, za nímž následuje po konfiguraci. Rozlišuje mezi <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> a <xref:Microsoft.Extensions.Options.IConfigureOptions`1> a jen volá odpovídající rozhraní.

<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> používá <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> do mezipaměti `TOptions` instancí. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> Zruší platnost instancí možnosti v monitorování, tak, aby přepočítány hodnoty (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>). Hodnotami může být ručně zavedeno s <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>. <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> Metoda se používá, když všechny pojmenované instance by měl být znovu vytvořit na vyžádání.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> je užitečné v situacích, kdy by měla být možnosti přepočítány u každého požadavku. Další informace najdete v tématu [znovu načíst konfigurační data s IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) oddílu.

<xref:Microsoft.Extensions.Options.IOptions`1> slouží k možnosti podpory. Ale <xref:Microsoft.Extensions.Options.IOptions`1> nepodporuje předchozího scénáře <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>. Můžete dál používat <xref:Microsoft.Extensions.Options.IOptions`1> stávajících architektur a knihoven, které už používají <xref:Microsoft.Extensions.Options.IOptions`1> rozhraní a nevyžadují scénáře poskytované <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>.

## <a name="general-options-configuration"></a>Konfigurace obecných možností

Konfigurace obecných možností je znázorněn příklad &num;1 v ukázkové aplikaci.

Třída možností musí být neabstraktní s veřejným konstruktorem bez parametrů. Následující třídy `MyOptions`, má dvě vlastnosti `Option1` a `Option2`. Nastavení výchozích hodnot je nepovinný, ale konstruktoru třídy v následujícím příkladu nastaví na výchozí hodnotu `Option1`. `Option2` má výchozí hodnotu nastavenou vlastnost inicializace přímo (*Models/MyOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

`MyOptions` Třídy se přidá do kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> a vázán ke konfiguraci:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

Na následující stránce používá model [injektáž závislostí konstruktor](xref:mvc/controllers/dependency-injection) s <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> pro přístup k nastavení (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

Ukázkové *appsettings.json* souboru určuje hodnoty pro `option1` a `option2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> Při použití vlastního <xref:System.Configuration.ConfigurationBuilder> Pokud chcete načíst ze souboru nastavení konfigurace možností, potvrďte, že je správně nastavena základní cesta:
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
> Explicitním nastavením základní cesta není nutné při načítání konfigurace možností z nastavení souboru prostřednictvím <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.

## <a name="configure-simple-options-with-a-delegate"></a>Konfigurace jednoduchých možností s delegáta

Konfigurace jednoduchých možností s delegáta je znázorněn příklad &num;2 v ukázkové aplikaci.

Použití delegáta k nastavení hodnot možností. Tato ukázková aplikace používá `MyOptionsWithDelegateConfig` třídy (*Models/MyOptionsWithDelegateConfig.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

V následujícím kódu druhý <xref:Microsoft.Extensions.Options.IConfigureOptions`1> služby se přidá do kontejneru služby. Delegát používá ke konfiguraci vazby s `MyOptionsWithDelegateConfig`:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

*Index.cshtml.cs*:

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

Můžete přidat několik poskytovatelů konfigurace. Poskytovatelé konfigurace jsou k dispozici z balíčků NuGet a jsou použita popořadě, že jejich registrace. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.

Každé volání <xref:Microsoft.Extensions.Options.IConfigureOptions`1.Configure*> přidá <xref:Microsoft.Extensions.Options.IConfigureOptions`1> služby service container. V předchozím příkladu hodnoty `Option1` a `Option2` jsou určené v *appsettings.json*, ale hodnoty `Option1` a `Option2` jsou přepsány nakonfigurované delegáta.

Pokud je povoleno více než jedna služba konfigurace, poslední zdroj konfigurace zadaná *wins* a nastaví hodnotu konfigurace. Při spuštění aplikace, model stránky `OnGet` metoda vrátí řetězec zobrazující možnost hodnoty třídy:

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a>Konfigurace suboptions

Konfigurace suboptions je znázorněn příklad &num;3 v ukázkové aplikaci.

Aplikace by měl vytvořit třídy možnosti, které se vztahují na konkrétní scénář skupiny (třídy) v aplikaci. Části aplikace, které vyžadují konfigurační hodnoty by měl mít přístup pouze na hodnoty konfigurace, které používají.

Při vytváření vazby možnosti konfigurace, každou vlastnost v typu možnosti je vázán na konfigurační klíč ve formátu `property[:sub-property:]`. Například `MyOptions.Option1` vlastnost je vázána na klíč `Option1`, který je pro čtení z `option1` vlastnost v *appsettings.json*.

V následujícím kódu, třetí <xref:Microsoft.Extensions.Options.IConfigureOptions`1> služby se přidá do kontejneru služby. Vytvoří vazbu mezi `MySubOptions` do části `subsection` z *appsettings.json* souboru:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

`GetSection` – Metoda rozšíření vyžaduje [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) balíček NuGet. Pokud aplikace využívá [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější), balíček je automaticky přidána.

Ukázkové *appsettings.json* soubor definuje `subsection` člena s klíči pro `suboption1` a `suboption2`:

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

`MySubOptions` Třídy definuje vlastnosti, `SubOption1` a `SubOption2`, pro uchování hodnoty možnosti (*Models/MySubOptions.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

Model stránky `OnGet` metoda vrátí řetězec s hodnotami možnosti (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

Při spuštění aplikace `OnGet` metoda vrátí řetězec zobrazující přepínači hodnoty třídy:

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a>Možnosti zobrazení modelu nebo pomocí vkládání přímé zobrazení

Možnosti zobrazení modelu nebo s přístupem zobrazení vkládání je znázorněn příklad &num;4 v ukázkové aplikaci.

Možnosti může být zadán v zobrazení modelu nebo vložením <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> přímo do zobrazení (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

Ukázková aplikace ukazuje, jak vložit `IOptionsMonitor<MyOptions>` s `@inject` – direktiva:

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

Při spuštění aplikace se hodnoty možnosti jsou zobrazeny na vykreslené stránce:

![Možnosti hodnot možnost1: value1_from_json a možnost2: -1 se načítají z modelu a vkládání do zobrazení.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a>Znovu načíst konfigurační data s IOptionsSnapshot

Probíhá opětovné načtení dat konfigurace pomocí <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> je ukázáno v příkladu &num;5 v ukázkové aplikaci.

<xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> podporuje možnosti s minimální režie zpracování znovu načíst.

Možnosti jsou vypočten jednou každý požadavek při přistupovat a uložili do mezipaměti po dobu platnosti požadavku.

Následující příklad ukazuje, jak nové <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> vytvořené po *appsettings.json* změny (*Pages/Index.cshtml.cs*). Více požadavků na server vrátit konstantní hodnoty podle *appsettings.json* souborů, dokud je soubor změněn a konfigurace znovu načte.

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

Následující obrázek ukazuje úvodní `option1` a `option2` hodnoty načtené z *appsettings.json* souboru:

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

Změňte hodnoty *appsettings.json* do souboru `value1_from_json UPDATED` a `200`. Uložit *appsettings.json* souboru. Aktualizujte prohlížeč, pokud chcete zobrazit, že jsou aktualizované hodnoty možnosti:

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a>Možnosti podpory s IConfigureNamedOptions s názvem

Možnosti podpory s názvem <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> je znázorněn příklad &num;6 v ukázkové aplikaci.

*S názvem možnosti* podpora umožňuje, aby aplikace k rozlišení mezi pojmenované možnosti konfigurace. V ukázkové aplikaci s názvem možnosti jsou deklarovány pomocí [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), který volá [ConfigureNamedOptions\<TOptions >. Konfigurace](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) – metoda rozšíření:

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

Ukázková aplikace přistupuje k pojmenované možnosti s <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

Spuštěním ukázkové aplikace, jsou vráceny pojmenované možnosti:

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

`named_options_1` hodnoty jsou k dispozici z konfigurace, které jsou načteny z *appsettings.json* souboru. `named_options_2` hodnoty jsou k dispozici podle:

* `named_options_2` Delegování v `ConfigureServices` pro `Option1`.
* Výchozí hodnota pro `Option2` poskytované `MyOptions` třídy.

## <a name="configure-all-options-with-the-configureall-method"></a>Všechny možnosti nakonfigurovat ConfigureAll – metoda

Nakonfigurujte všechny možnosti instance s <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> metody. Následující kód konfiguruje `Option1` pro všechny konfigurace instance s hodnotou běžné. Přidejte následující kód do ručně `Startup.ConfigureServices` metody:

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
> Všechny možnosti jsou pojmenované instance. Existující <xref:Microsoft.Extensions.Options.IConfigureOptions`1> instancí jsou považovány za cílení `Options.DefaultName` instanci, která je `string.Empty`. <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> také implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions`1>. Výchozí implementace <xref:Microsoft.Extensions.Options.IOptionsFactory`1> obsahuje logiku pro každý odpovídajícím způsobem používat. `null` Pojmenované možnost se používá cílit na všechny pojmenované instance místo konkrétní pojmenovanou instanci (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> a <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> Tato konvence).

## <a name="optionsbuilder-api"></a>OptionsBuilder API

<xref:Microsoft.Extensions.Options.OptionsBuilder`1> slouží ke konfiguraci `TOptions` instancí. `OptionsBuilder` zjednodušuje vytváření, s názvem možnosti, jako je pouze jeden parametr do původní `AddOptions<TOptions>(string optionsName)` volat bez povolí, všechny následné volání. Možnosti ověřování a `ConfigureOptions` přetížení, které přijímají závislostí služby jsou dostupné jen přes `OptionsBuilder`.

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a>Slouží ke konfiguraci možností služeb DI

Přístupu k jiným službám od vkládání závislostí při konfiguraci možnosti dvěma způsoby:

* Předání delegáta konfigurace k [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) na [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1). [OptionsBuilder\<TOptions >](xref:Microsoft.Extensions.Options.OptionsBuilder`1) poskytuje přetížení [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) , které umožňují používat až pět služby můžete nakonfigurovat možnosti:

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* Vytvořit vlastní typ, který implementuje <xref:Microsoft.Extensions.Options.IConfigureOptions`1> nebo <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> a zaregistrujte se jako služba typu.

Doporučujeme, abyste předání konfigurace delegáta, kterého [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), od vytvoření služby je složitější. Vytváří se vlastní typ je ekvivalentní k co rozhraní udělá za vás při použití [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*). Volání [konfigurovat](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zaregistruje přechodné obecný <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, které má konstruktor, který přijímá typy obecné služby určené. 

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
* Ověření, který závisí na jiný typ možnosti: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

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

Data ověřování na základě poznámek je k dispozici [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) balíčku voláním `ValidateDataAnnotations` metodu na `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 nebo vyšší).

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

V úvahu pro budoucí verzi se nemůžou dočkat, až ověřování (selhání rychle při spuštění).

::: moniker-end

## <a name="options-post-configuration"></a>Po konfiguraci možností

Po nastavení s <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1>. Po konfiguraci spuštění po všech <xref:Microsoft.Extensions.Options.IConfigureOptions`1> vyvolá konfigurace:

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> je k dispozici po konfigurace s názvem možností:

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

Použití <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> po konfiguraci všechny instance konfigurace:

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a>Přístup k možnosti při spuštění

<xref:Microsoft.Extensions.Options.IOptions`1> a <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> lze použít v `Startup.Configure`, protože služby jsou sestaveny dříve, než `Configure` metody.

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

Nepoužívejte <xref:Microsoft.Extensions.Options.IOptions`1> nebo <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> v `Startup.ConfigureServices`. Příčinou je pořadí registrace služby můžou existovat nekonzistentní možnosti dostane do stavu.

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/configuration/index>
