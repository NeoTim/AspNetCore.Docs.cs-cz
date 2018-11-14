---
title: Vazby modelu v ASP.NET Core
author: tdykstra
description: Zjistěte, jak vazby modelu v ASP.NET Core MVC mapují data požadavků HTTP na parametry metod akce.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: tdykstra
ms.date: 11/13/2018
uid: mvc/models/model-binding
ms.openlocfilehash: 1dc9b41328ed78440622acc1865b6f088d394403
ms.sourcegitcommit: 1d6ab43eed9cb3df6211c22b97bb3a9351ec4419
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597781"
---
# <a name="model-binding-in-aspnet-core"></a>Vazby modelu v ASP.NET Core

Podle [Rachel Appel](https://github.com/rachelappel)

## <a name="introduction-to-model-binding"></a>Úvod do vazby modelu

Vazby modelu v ASP.NET Core MVC mapují data požadavků HTTP na parametry metod akce. Parametry mohou být jednoduché typy, jako jsou řetězce, celá čísla nebo float, nebo mohou být komplexní typy. Totiž skvělou funkcí služby MVC mapování příchozích dat protějšek je často opakovaných scénář, bez ohledu na velikost a složitost dat. MVC tento problém řeší tím abstrahovat vazby hned, aby vývojáři nemuseli kvůli zachovat přepisování trochu odlišná verze tohoto stejného kódu v každé aplikaci. Zápis vlastní text, který má typ převaděče kód je únavné a náchylné k chybám.

## <a name="how-model-binding-works"></a>Princip vazby modelu

Když MVC obdrží požadavek HTTP, nasměruje ho do metody konkrétní akce kontroleru. Určuje, jakou metodu akce spouštět až na to, co je v datech trasy a potom naváže hodnoty z požadavku HTTP na parametry této metodě akce. Představte si třeba následující adresu URL:

`http://contoso.com/movies/edit/2`

Vzhledem k tomu, že šablona trasy vypadá takto, `{controller=Home}/{action=Index}/{id?}`, `movies/edit/2` směruje `Movies` řadič a jeho `Edit` metody akce. Také přijímá volitelný parametr s názvem `id`. Kód pro metodu akce by měl vypadat přibližně takto:

```csharp
public IActionResult Edit(int? id)
   ```

Poznámka: Řetězce v trasu adresy URL nejsou velká a malá písmena.

MVC se pokusí vytvořit vazbu data požadavku na parametry akce podle názvu. MVC bude hledat hodnoty pro každý parametr pomocí názvu parametru a názvy jeho veřejné nastavitelné vlastnosti. V předchozím příkladu je parametr jedinou akcí s názvem `id`, který MVC váže hodnotu s názvem v hodnoty trasy. Kromě hodnoty trasy MVC vytvoří vazbu mezi data z různých částí žádosti a provádí se v určitém pořadí. Níže je seznam zdrojů dat v pořadí, ve kterém vazby modelu vypadá přes ně:

1. `Form values`: Toto jsou hodnot formuláře, které najdete v požadavku HTTP pomocí metody POST. (včetně požadavků jQuery POST).

2. `Route values`: Sadu hodnot trasy poskytované [směrování](xref:fundamentals/routing)

3. `Query strings`: Řetězec část dotazu identifikátoru URI.

<!-- DocFX BUG
The link works but generates an error when building with DocFX
@fundamentals/routing
[Routing](xref:fundamentals/routing)
-->

Poznámka: Tvoří hodnoty, data směrování a dotazů řetězce se ukládají jako dvojice název hodnota.

Vzhledem k tomu vyzváni vazby modelu pro klíč s názvem `id` a nic s názvem `id` hodnoty formulář přesune do hodnoty trasy pro daný klíč hledání. V našem příkladu je shoda. Vázání a hodnota je převedena na celé číslo. 2. Stejný požadavek pomocí úpravy (id řetězce) by převést na řetězec "2".

Pokud v příkladu jednoduché typy. V aplikaci MVC jednoduché typy jsou všechny primitivní typ formátu .NET nebo typ s konvertor typu řetězec. Parametr metody akce, jako byly třídu `Movie` typ, který obsahuje jednoduché a komplexní typy jako krásně vlastnosti, MVC model vazby budou nadále ji zpracovat. Reflexe a rekurze používá k procházení vlastnosti komplexní typy hledání shody. Vazby modelu hledá vzor *parameter_name.property_name* k vázání hodnot vlastností. Pokud se nenajde odpovídající hodnoty tohoto formuláře, pokusí se vytvořit vazbu pomocí názvu vlastnosti. Pro tyto typy, jako `Collection` typy vazby modelu hledá odpovídající *parameter_name [index]* nebo jen *[index]*. Model vazba zpracuje `Dictionary` typy podobně s žádostí o *parameter_name [klíč]* nebo jen *[klíč]*, jako jsou klíče jednoduché typy. Klíče, které jsou podporovány odpovídat názvům pole HTML a pomocných rutin značek, které jsou generovány pro stejný typ modelu. To umožňuje verzemi hodnoty tak, aby pole formuláře vyplněné se vstupem uživatele pro jejich pohodlí, například při zůstat vázaných dat od vytvoření nebo úprava úspěšně ověření.

Aby vazba modelu je to možné, třídě musí mít veřejný výchozí konstruktor a veřejný zapisovatelný vlastnosti pro vytvoření vazby. Pokud dojde k vazbě modelu, třída je vytvořen pomocí veřejný výchozí konstruktor a pak můžete nastavit vlastnosti.

Při vazbu parametru vazby modelu zastaví hledání pro hodnoty s tímto názvem a se přejde k další parametr vazby. V opačném případě výchozí chování vazby modelu nastaví na výchozí hodnoty v závislosti na jejich typu parametry:

* `T[]`: S výjimkou produktů pole typu `byte[]`, vazba nastaví parametry typu `T[]` k `Array.Empty<T>()`. Pole typu `byte[]` jsou nastaveny na `null`.

* Typy odkazu: Vazby vytvoří instanci třídy s výchozím konstruktorem bez nastavování vlastností. Model však vazba sady `string` parametry `null`.

* Typy připouštějící hodnotu NULL: Typy s možnou hodnotou Null jsou nastaveny na `null`. V příkladu výše, model vazby sady `id` k `null` vzhledem k tomu, že je typu `int?`.

* Typy hodnot: Typy neumožňující hodnotu typu `T` jsou nastaveny na `default(T)`. Vazby modelu bude třeba nastavit parametr `int id` na hodnotu 0. Zvažte použití ověření modelu nebo typů s povolenou hodnotou Null, spíše než spoléhání se na výchozí hodnoty.

Pokud se vazba se nezdaří, MVC nevyvolá chybu. Zkontrolujte všechny akce, která přijímá vstup uživatele `ModelState.IsValid` vlastnost.

Poznámka: Každá položka v kontroleru `ModelState` je vlastnost `ModelStateEntry` obsahující `Errors` vlastnost. Je zřídka nezbytné k této kolekci dotazování sami. Místo nich se používá `ModelState.IsValid`.

Kromě toho jsou některé speciální datové typy, které MVC nutné vzít v úvahu při provádění vazby modelu:

* `IFormFile`, `IEnumerable<IFormFile>`: Jeden nebo více nahraných souborů, které jsou součástí požadavku HTTP.

* `CancellationToken`: Používá se pro zrušení aktivity v asynchronní řadiče.

Tyto typy mohou být vázány na parametry akce nebo vlastností typu třídy.

Po dokončení, vazby modelu [ověření](validation.md) vyvolá. Vazby modelu výchozí dobře funguje pro většinu scénářů vývoje. Je také rozšiřitelný, pokud máte jedinečné požadavky můžete přizpůsobit předdefinované chování.

## <a name="customize-model-binding-behavior-with-attributes"></a>Přizpůsobení chování vazby modelu s atributy

MVC obsahuje několik atributů, které vám umožní směrovat její výchozí chování vazby modelu do jiného zdroje. Například můžete určit, jestli se vyžaduje pro vlastnost vazby, nebo pokud jej by nemělo nikdy dojít vůbec pomocí `[BindRequired]` nebo `[BindNever]` atributy. Alternativně můžete přepsat výchozí zdroj dat a určete zdroj dat vazače modelu. Tady je seznam atributů vazby modelu:

* `[BindRequired]`: Tento atribut přidá chyby stavu modelu, pokud vazba nebyla vytvořena.

* `[BindNever]`: Přikáže vazač modelu pro nikdy vazbu pro tento parametr.

* `[FromHeader]`, `[FromQuery]`, `[FromRoute]`, `[FromForm]`: Tyto slouží k určení zdroje připojení přesně chcete použít.

* `[FromServices]`: Tento atribut používá [injektáž závislostí](../../fundamentals/dependency-injection.md) pro svázání parametrů ze služby.

* `[FromBody]`: Použijte nakonfigurované formátovacích modulů k vytvoření vazby dat z textu požadavku. Formátovací modul se určí typ obsahu požadavku.

* `[ModelBinder]`: Používá se k přepsání výchozí vazač modelu, zdroj vazby a název.

Atributy jsou velmi užitečné nástroje při budete muset změnit výchozí chování vazby modelu.

## <a name="customize-model-binding-and-validation-globally"></a>Upravit vazby modelu a ověření globálně

Model vazby a ověřování systému chování doprovází [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) , který popisuje:

* Jak modelu má být vázána.
* Jak ověřování dochází na typu a jeho vlastnosti.

Aspekty chování systému je globálně nakonfigurovat tak, že přidáte podrobnosti zprostředkovatele, aby [MvcOptions.ModelMetadataDetailsProviders](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions.modelmetadatadetailsproviders#Microsoft_AspNetCore_Mvc_MvcOptions_ModelMetadataDetailsProviders). MVC má několik poskytovatelů integrované podrobnosti, které umožňují že konfigurace chování těchto jako zakázání ověření nebo vazby modelu pro určité typy.

Chcete-li zakázat vazby modelu všech modelů určitého typu, přidejte [ExcludeBindingMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.excludebindingmetadataprovider) v `Startup.ConfigureServices`. Například s vazbou modelu zakázat na všech modelů typu `System.Version`:

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new ExcludeBindingMetadataProvider(typeof(System.Version))));
```

Chcete-li zakázat ověřování na vlastnosti určitého typu, přidejte [SuppressChildValidationMetadataProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.suppresschildvalidationmetadataprovider) v `Startup.ConfigureServices`. Například chcete-li zakázat ověřování na vlastnosti typu `System.Guid`:

```csharp
services.AddMvc().AddMvcOptions(options =>
    options.ModelMetadataDetailsProviders.Add(
        new SuppressChildValidationMetadataProvider(typeof(System.Guid))));
```

## <a name="bind-formatted-data-from-the-request-body"></a>Vytvoření vazby formátovaných dat z textu požadavku

Data žádosti můžou mít širokou škálu formátů, včetně JSON, XML a mnohé další. Při použití atributu [FromBody] k označení, že chcete svázat parametr s daty v textu požadavku aplikace MVC používá nakonfigurovanou sadu formátovacích modulů pro zpracování žádosti o data na základě jeho typu obsahu. Ve výchozím nastavení obsahuje MVC `JsonInputFormatter` třídy pro zpracování dat JSON, ale můžete přidat další formátovacích modulů pro zpracování XML a dalších vlastních formátů.

> [!NOTE]
> Může existovat maximálně jeden parametr na akce, které jsou vybaveny `[FromBody]`. Runtime ASP.NET Core MVC deleguje na starost datový proud požadavku pro čtení k formátování. Jakmile datový proud požadavku je pro čtení pro parametr, není obecně možné číst datový proud požadavku znovu pro vazbu druhé `[FromBody]` parametry.

> [!NOTE]
> `JsonInputFormatter` Je výchozí formátování a je založena na [Json.NET](https://www.newtonsoft.com/json).

ASP.NET Core vybere vstupní formátovacích modulů na základě [Content-Type](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html) záhlaví a typ parametru, pokud neexistuje atribut použitý k jeho zadání jinak. Pokud chcete použít XML nebo jiný formát je nutné ji nakonfigurovat v *Startup.cs* soubor, ale nejdřív muset získat odkaz na `Microsoft.AspNetCore.Mvc.Formatters.Xml` pomocí nástroje NuGet. Spouštěcí kód by měl vypadat přibližně takto:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddXmlSerializerFormatters();
   }
```

Kód v *Startup.cs* obsahuje soubor `ConfigureServices` metodu s `services` argument můžete použít k vytvoření služby pro aplikaci ASP.NET Core. V příkladu přidáváme formátovací modul XML jako služba, která bude poskytovat MVC pro tuto aplikaci. `options` Argument předaný `AddMvc` metoda umožňuje přidávat a spravovat filtry, formátování a další možnosti systému z MVC po spuštění aplikace. Následně použít `Consumes` atribut třídy kontroler nebo akce metody pro práci s formátem chcete.

### <a name="custom-model-binding"></a>Vlastní vazba modelu

Psaní vlastních vazače vlastního modelu, můžete rozšířit vazby modelu. Další informace o [vlastní vazba modelu](../advanced/custom-model-binding.md).
