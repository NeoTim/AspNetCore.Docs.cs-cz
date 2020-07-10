---
title: Vkládání závislostí do zobrazení v ASP.NET Core
author: ardalis
description: Přečtěte si, jak ASP.NET Core podporuje vkládání závislostí do zobrazení MVC.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/dependency-injection
ms.openlocfilehash: 335eac4b1533f4156e00f9d0140f921fa492e71a
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212966"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>Vkládání závislostí do zobrazení v ASP.NET Core

[Steve Smith](https://ardalis.com/)

ASP.NET Core podporuje [vkládání závislostí](xref:fundamentals/dependency-injection) do zobrazení. To může být užitečné pro služby specifické pro zobrazení, jako je například lokalizace nebo data požadovaná pouze pro naplnění prvků zobrazení. Měli byste se pokusit udržet [oddělení otázek](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) mezi řadiči a zobrazeními. Většina dat, která jsou zobrazena v zobrazení, by měla být předána z kontroleru.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>Vkládání konfigurace

*appsettings.jsna* hodnoty lze vložit přímo do zobrazení.

Příklad *appsettings.jsv* souboru:

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

Syntaxe pro `@inject` :`@inject <type> <name>`

Příklad použití `@inject` :

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>Vložení služby

Službu lze vložit do zobrazení pomocí `@inject` direktivy. Můžete si představit `@inject` jako přidání vlastnosti do zobrazení a naplnění vlastnosti pomocí di.

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Toto zobrazení obsahuje seznam `ToDoItem` instancí spolu se souhrnem znázorňujícím celkovou statistiku. Shrnutí se vyplní ze vložených `StatisticsService` . Tato služba je zaregistrovaná pro vkládání závislostí v `ConfigureServices` v *Startup.cs*:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService`Provede některé výpočty se sadou `ToDoItem` instancí, ke kterým přistupuje prostřednictvím úložiště:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Ukázkové úložiště používá kolekci v paměti. Výše uvedená implementace (která funguje na všech datech v paměti) se nedoporučuje pro velké, vzdáleně dostupné datové sady.

Ukázka zobrazuje data z modelu svázaného se zobrazením a služba je vložena do zobrazení:

![Chcete-li zobrazit seznam položek celkem, dokončené položky, průměrná priorita a seznam úkolů s jejich úrovněmi priority a logickými hodnotami, které označují dokončení.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Naplnění dat vyhledávání

Injektáže zobrazení může být užitečné k naplnění možností v prvcích uživatelského rozhraní, jako jsou rozevírací seznamy. Vezměte v úvahu formulář profilu uživatele, který obsahuje možnosti pro určení rovnosti, stavu a dalších předvoleb. Vykreslení takového formuláře pomocí přístupu ke standardní MVC by vyžadovalo, aby kontroler požadoval služby Data Access pro každou z těchto sad možností a pak naplnil model nebo `ViewBag` každou sadu možností, které se mají svázat.

Alternativní metoda přímo vloží služby do zobrazení a získá možnosti. Tím se minimalizuje množství kódu, který vyžaduje kontroler, přesunutím této logiky konstrukce zobrazení do samotného zobrazení. Akce kontroleru pro zobrazení formuláře pro úpravy profilu musí předat pouze formulář instance profilu:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Formulář HTML použitý k aktualizaci těchto předvoleb obsahuje rozevírací seznamy pro tři vlastnosti:

![Aktualizujte zobrazení profilu s použitím formuláře, který umožňuje zadání názvu, pohlaví, stavu a oblíbené barvy.](dependency-injection/_static/updateprofile.png)

Tyto seznamy jsou vyplněny službou, která byla vložena do zobrazení:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService`Je služba na úrovni uživatelského rozhraní navržená tak, aby poskytovala pouze data potřebná pro tento formulář:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Nezapomeňte zaregistrovat typy, které požadujete, prostřednictvím injektáže závislosti v `Startup.ConfigureServices` . Neregistrovaný typ vyvolá výjimku za běhu, protože poskytovatel služby je interně dotazován prostřednictvím [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).

## <a name="overriding-services"></a>Přepsání služeb

Kromě vkládání nových služeb se tato technika dá také použít k přepsání dříve vložených služeb na stránce. Následující obrázek ukazuje všechna pole, která jsou k dispozici na stránce použitá v prvním příkladu:

![Kontextová nabídka technologie IntelliSense na typovaném typu @ symbol seznam polí HTML, Component, StatsService a URL](dependency-injection/_static/razor-fields.png)

Jak vidíte, výchozí pole zahrnují `Html` , `Component` a `Url` (a také `StatsService` vložené). Pokud byste chtěli například nahradit výchozí pomocníky HTML vlastními, můžete to snadno použít `@inject` :

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Pokud chcete stávající služby zvětšit, můžete jednoduše použít tuto techniku a zároveň dědit z nebo zabalení stávající implementace s vlastními.

## <a name="see-also"></a>Viz také

* Blog Simon Timms: [získávání dat vyhledávání do zobrazení](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
