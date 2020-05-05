---
title: Verze kompatibility pro ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/compatibility-version
ms.openlocfilehash: 45eca0bedc2e4e5c74936ae5d1bf525774467b2a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774207"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a>Verze kompatibility pro ASP.NET Core MVC

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda je no-op pro aplikace ASP.NET Core 3,0. To znamená, že `SetCompatibilityVersion` volání s jakoukoliv hodnotou <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> nemá žádný vliv na aplikaci.

* Další podverze ASP.NET Core může poskytovat novou `CompatibilityVersion` hodnotu.
* `CompatibilityVersion`hodnoty `Version_2_0` prostřednictvím `Version_2_2` jsou označeny `[Obsolete(...)]`.
* Přečtěte si téma [přerušení změn rozhraní API v antipadělání, CORS, diagnostice, MVC a směrování](https://github.com/aspnet/Announcements/issues/387). Tento seznam obsahuje zásadní změny pro přepínače kompatibility.

Pokud chcete zjistit `SetCompatibilityVersion` , jak funguje s aplikacemi ASP.NET Core 2. x, vyberte [verzi ASP.NET Core 2,2 tohoto článku](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikaci ASP.NET Core 2. x odsouhlasit nebo odhlásit potenciálně odstraněné změny chování, které byly představeny v ASP.NET Core MVC 2,1 nebo 2,2. Tyto potenciálně narušující změny chování jsou všeobecně ve způsobu, jakým se subsystém MVC chová a jak je **váš kód** volán modulem runtime. Když se rozhodnete, získáte nejnovější chování a dlouhodobé chování ASP.NET Core.

Následující kód nastaví režim kompatibility na ASP.NET Core 2,2:

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

Doporučujeme, abyste aplikaci otestovali pomocí nejnovější verze (`CompatibilityVersion.Latest`). Předpokládáme, že většina aplikací nebude mít změny chování v nejnovější verzi.

Aplikace, které `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` volají, jsou chráněny před potenciálně rozbitými změnami chování, které byly představeny ve verzích ASP.NET Core 2.1/2.2 MVC. Tato ochrana:

* Neplatí pro všechny 2,1 a pozdější změny, je cílem potenciálně poškodit ASP.NET Core změny chování modulu runtime v podsystému MVC.
* Nerozšiřuje na ASP.NET Core 3,0.

Výchozí kompatibilita pro aplikace ASP.NET Core 2,1 a 2,2, které **nevolají** `SetCompatibilityVersion` , je 2,0 kompatibilita. To znamená, že není `SetCompatibilityVersion` voláno stejně jako volání `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.

Následující kód nastaví režim kompatibility na ASP.NET Core 2,2, s výjimkou následujícího chování:

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

U aplikací, které narazí na změny chování, použijte příslušné přepínače kompatibility:

* Umožňuje použít nejnovější verzi a vyjádřit výslovný souhlas s konkrétními změnami porušení chování.
* Poskytuje čas k aktualizaci aplikace tak, aby fungovala s nejnovějšími změnami.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions> Dokumentace má dobré vysvětlení toho, co se změnilo a proč jsou změny pro většinu uživatelů lepší.

U ASP.NET Core 3,0 se odebralo staré chování podporované přepínači kompatibility. Myslíme na to, že jsou kladné změny využívání téměř všemi uživateli. Díky zavedení těchto změn v 2,1 a 2,2 může většina aplikací těžit, zatímco ostatní mají čas na aktualizaci.
::: moniker-end
