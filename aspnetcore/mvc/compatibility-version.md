---
title: Verze kompatibility pro ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
uid: mvc/compatibility-version
ms.openlocfilehash: b29e2ee49aaf0f557f1acd0cf03e9e82d5ea0105
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75357728"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="afc2a-103">Verze kompatibility pro ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="afc2a-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="afc2a-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="afc2a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="afc2a-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> metoda není pro aplikace ASP.NET Core 3,0 žádná op.</span><span class="sxs-lookup"><span data-stu-id="afc2a-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="afc2a-106">To znamená, že volání `SetCompatibilityVersion` s jakoukoliv hodnotou <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> nemá žádný vliv na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="afc2a-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="afc2a-107">Další podverze ASP.NET Core může poskytovat novou hodnotu `CompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="afc2a-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="afc2a-108">hodnoty `CompatibilityVersion` `Version_2_0` prostřednictvím `Version_2_2` jsou označeny `[Obsolete(...)]`.</span><span class="sxs-lookup"><span data-stu-id="afc2a-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="afc2a-109">Přečtěte si téma [přerušení změn rozhraní API v antipadělání, CORS, diagnostice, MVC a směrování](https://github.com/aspnet/Announcements/issues/387).</span><span class="sxs-lookup"><span data-stu-id="afc2a-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="afc2a-110">Tento seznam obsahuje zásadní změny pro přepínače kompatibility.</span><span class="sxs-lookup"><span data-stu-id="afc2a-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="afc2a-111">Pokud chcete zjistit, jak `SetCompatibilityVersion` pracuje s aplikacemi ASP.NET Core 2. x, vyberte [verzi ASP.NET Core 2,2 tohoto článku](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="afc2a-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](https://docs.microsoft.com/aspnet/core/mvc/compatibility-version?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="afc2a-112">Metoda <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> umožňuje, aby se aplikace ASP.NET Core 2. x odhlásila nebo výslovný souhlas se změnami potenciálně odrušujících chování, které byly představeny v ASP.NET Core MVC 2,1 nebo 2,2.</span><span class="sxs-lookup"><span data-stu-id="afc2a-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="afc2a-113">Tyto potenciálně narušující změny chování jsou všeobecně ve způsobu, jakým se subsystém MVC chová a jak je **váš kód** volán modulem runtime.</span><span class="sxs-lookup"><span data-stu-id="afc2a-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="afc2a-114">Když se rozhodnete, získáte nejnovější chování a dlouhodobé chování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="afc2a-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="afc2a-115">Následující kód nastaví režim kompatibility na ASP.NET Core 2,2:</span><span class="sxs-lookup"><span data-stu-id="afc2a-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="afc2a-116">Doporučujeme, abyste aplikaci otestovali pomocí nejnovější verze (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="afc2a-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="afc2a-117">Předpokládáme, že většina aplikací nebude mít změny chování v nejnovější verzi.</span><span class="sxs-lookup"><span data-stu-id="afc2a-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="afc2a-118">Aplikace, které volají `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`, jsou chráněné před potenciálně rozbitými změnami chování, které byly představené ve verzích ASP.NET Core 2.1/2.2 MVC.</span><span class="sxs-lookup"><span data-stu-id="afc2a-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="afc2a-119">Tato ochrana:</span><span class="sxs-lookup"><span data-stu-id="afc2a-119">This protection:</span></span>

* <span data-ttu-id="afc2a-120">Neplatí pro všechny 2,1 a pozdější změny, je cílem potenciálně poškodit ASP.NET Core změny chování modulu runtime v podsystému MVC.</span><span class="sxs-lookup"><span data-stu-id="afc2a-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="afc2a-121">Nerozšiřuje na ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="afc2a-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="afc2a-122">Výchozí kompatibilita pro aplikace ASP.NET Core 2,1 a 2,2, které **nevolají `SetCompatibilityVersion`** , je 2,0 kompatibilní.</span><span class="sxs-lookup"><span data-stu-id="afc2a-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="afc2a-123">To znamená, že nevolá `SetCompatibilityVersion` je stejná jako volání `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="afc2a-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="afc2a-124">Následující kód nastaví režim kompatibility na ASP.NET Core 2,2, s výjimkou následujícího chování:</span><span class="sxs-lookup"><span data-stu-id="afc2a-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="afc2a-125">U aplikací, které narazí na změny chování, použijte příslušné přepínače kompatibility:</span><span class="sxs-lookup"><span data-stu-id="afc2a-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="afc2a-126">Umožňuje použít nejnovější verzi a vyjádřit výslovný souhlas s konkrétními změnami porušení chování.</span><span class="sxs-lookup"><span data-stu-id="afc2a-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="afc2a-127">Poskytuje čas k aktualizaci aplikace tak, aby fungovala s nejnovějšími změnami.</span><span class="sxs-lookup"><span data-stu-id="afc2a-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="afc2a-128">V dokumentaci k <xref:Microsoft.AspNetCore.Mvc.MvcOptions> se dobře vysvětlí, co se změnilo a proč jsou změny lepší pro většinu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="afc2a-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="afc2a-129">U ASP.NET Core 3,0 se odebralo staré chování podporované přepínači kompatibility.</span><span class="sxs-lookup"><span data-stu-id="afc2a-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="afc2a-130">Myslíme na to, že jsou kladné změny využívání téměř všemi uživateli.</span><span class="sxs-lookup"><span data-stu-id="afc2a-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="afc2a-131">Díky zavedení těchto změn v 2,1 a 2,2 může většina aplikací těžit, zatímco ostatní mají čas na aktualizaci.</span><span class="sxs-lookup"><span data-stu-id="afc2a-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end
