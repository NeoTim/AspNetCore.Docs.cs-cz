---
title: Injektáž závislostí do zobrazení v ASP.NET Core
author: ardalis
description: Zjistěte, jak ASP.NET Core podporuje injektáž závislostí do zobrazení MVC.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: b411b164bfea81f82c5c9fc1052e0ecfe65f0bc2
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517054"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a>Injektáž závislostí do zobrazení v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

Podporuje ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) do zobrazení. To může být užitečné pro zobrazení konkrétní služby, jako je lokalizace nebo data, vyžaduje se jenom pro naplnění zobrazení elementů. Snažte se zachovat [oddělení oblastí zájmu](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) mezi kontrolerů a zobrazení. Většina dat, které vaše zobrazení by měl předávat v kontroleru.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="configuration-injection"></a>Konfigurace vkládání

*appSettings.JSON* hodnoty můžete vloží přímo do zobrazení.

Příklad *appsettings.json* souboru:

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

Syntaxe pro `@inject`: `@inject <type> <name>`

Příklad použití `@inject`:

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a>Dokáže vložit službu

Služby můžete být vloženy do zobrazení pomocí `@inject` směrnice. Můžete si představit `@inject` jako přidání vlastnosti do zobrazení a naplnění danou vlastnost pomocí DI.

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

Toto zobrazení ukazuje seznam `ToDoItem` instancí, spolu se shrnutím zobrazující celkové statistiky. Tento souhrn se vyplní z vložené `StatisticsService`. Tato služba je zaregistrovaný pro injektáž závislostí v `ConfigureServices` v *Startup.cs*:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

`StatisticsService` Provádí pár výpočtů na sadu `ToDoItem` instance, které přistupuje prostřednictvím úložiště:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

Ukázkové úložiště používá kolekci v paměti. Implementace uvedené výše (která funguje na všechna data v paměti) se nedoporučuje pro velké a vzdáleně jenom datové sady.

Ukázka zobrazí data z model vázán k zobrazení a service vložené do zobrazení:

![Chcete-li zobrazit výpis celkový počet položek dokončení položky, průměrná priority a seznam úkolů s jejich úrovně priority a logické hodnoty označující dokončení.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a>Naplňování vyhledávání dat

Vkládání zobrazení může být užitečné k naplnění možnosti v prvky uživatelského rozhraní, jako je například rozevíracích seznamů. Vezměte v úvahu formuláře profilu uživatele, který obsahuje možnosti pro určení rovnosti, stavu a další předvolby. Vykreslování formulář, pomocí standardní metoda MVC by vyžadovaly kontroleru na žádost o služby data access services pro každou z těchto sad možností a potom naplnit modelu nebo `ViewBag` s každou sadu možností vázat.

Alternativním přístupem vloží přímo do zobrazení získat možnosti služby. Tím se minimalizují množství kódu vyžadovaného adaptérem přesun tuto logiku element vytváření zobrazení v samotném zobrazení. Akce kontroleru k zobrazení formuláře úprav profilu stačí předat formuláři instance profilu:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

Formuláře HTML, který používá k aktualizaci těchto předvolby zahrnuje rozevíracích seznamů pro tři vlastnosti:

![Zobrazení profil aktualizujte formulář umožňuje položka jméno, pohlaví, stavu a oblíbené barvy.](dependency-injection/_static/updateprofile.png)

Tyto seznamy se vyplní podle služby, které byly vloženy do zobrazení:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

`ProfileOptionsService` Je navrženo pro zajištění pouze data potřebná pro tento formulář služba úrovni uživatelského rozhraní:

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> Nezapomeňte k registraci typů, které požadujete prostřednictvím injektáž závislostí v `Startup.ConfigureServices`. Neregistrovaný typ dojde k výjimce za běhu, protože poskytovatel služeb je interně dotazován prostřednictvím [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).

## <a name="overriding-services"></a>Přepsání služby

Kromě vkládání nových služeb, můžete tento postup použít také k přepsání dříve vložený služby na stránce. Následující obrázek znázorňuje všechna pole, které jsou k dispozici na stránce použít v prvním příkladu:

![Kontextové nabídky technologie IntelliSense na zadaný symbol výpis pole Html, komponenty, StatsService a adresa Url @](dependency-injection/_static/razor-fields.png)

Jak je vidět, výchozí pole obsahují `Html`, `Component`, a `Url` (stejně jako `StatsService` , který jsme vloží). Například Kdybyste chtěli nahraďte pomocných rutin HTML výchozí vlastní, můžete snadno udělat pomocí `@inject`:

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

Pokud chcete rozšířit stávající služby, můžete jednoduše použít tento postup při dědění z nebo obtékání stávající implementaci vlastní.

## <a name="see-also"></a>Viz také

* Simon Timms blogu: [Načítání dat vyhledávací do zobrazení](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)
