---
title: Verze kompatibility pro ASP.NET Core MVC
author: rick-anderson
description: Zjistěte, jak třídu pro spuštění v ASP.NET Core konfiguruje služby a kanál žádosti o aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/20/2018
uid: mvc/compatibility-version
ms.openlocfilehash: bedeeba07dcca19176e779f3541c445e94efcd07
ms.sourcegitcommit: 5a2456cbf429069dc48aaa2823cde14100e4c438
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "41910021"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="1476d-103">Verze kompatibility pro ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="1476d-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="1476d-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1476d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1476d-105"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> Metoda umožňuje aplikacím vyjádřit výslovný souhlas nebo výslovný nesouhlas s potenciálně rozbíjející změny chování zavedení v ASP.NET Core MVC 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="1476d-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span> <span data-ttu-id="1476d-106">Potenciálně rozbíjející změny chování jsou obecně v způsob, jakým se chová subsystému MVC a jak **kódu** je volána modulem runtime.</span><span class="sxs-lookup"><span data-stu-id="1476d-106">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="1476d-107">Vyjádření výslovného souhlasu, získáte nejnovější chování a dlouhodobé chování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1476d-107">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="1476d-108">Následující kód nastaví režim kompatibility ASP.NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="1476d-108">The following code sets the compatibility mode to ASP.NET Core 2.1:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="1476d-109">Doporučujeme, abyste testování aplikace pomocí nejnovější verze (`CompatibilityVersion.Version_2_1`).</span><span class="sxs-lookup"><span data-stu-id="1476d-109">We recommend you test your app using the latest version (`CompatibilityVersion.Version_2_1`).</span></span> <span data-ttu-id="1476d-110">Předpokládáme, že většina aplikací nebudou mít nejnovější změny chování pomocí nejnovější verze.</span><span class="sxs-lookup"><span data-stu-id="1476d-110">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="1476d-111">Aplikace, které volají `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` chráněná před potenciálně rozbíjející změny chování zavedené v ASP.NET Core 2.1 MVC a novější verze 2.x.</span><span class="sxs-lookup"><span data-stu-id="1476d-111">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1 MVC and later 2.x versions.</span></span> <span data-ttu-id="1476d-112">Tato ochrana:</span><span class="sxs-lookup"><span data-stu-id="1476d-112">This protection:</span></span>

* <span data-ttu-id="1476d-113">Se nevztahují na všechny změny, 2.1 nebo novější, je určen potenciálně rozbíjející změny v chování modulu runtime ASP.NET Core v subsystému MVC.</span><span class="sxs-lookup"><span data-stu-id="1476d-113">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="1476d-114">Nevztahuje se na další hlavní verze.</span><span class="sxs-lookup"><span data-stu-id="1476d-114">Does not extend to the next major version.</span></span>

<span data-ttu-id="1476d-115">Výchozí kompatibilitu pro ASP.NET Core 2.1 a vyšší 2.x aplikací, které toho **není** volání `SetCompatibilityVersion` je 2.0 kompatibility.</span><span class="sxs-lookup"><span data-stu-id="1476d-115">The default compatibility for ASP.NET Core 2.1 and later 2.x apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="1476d-116">To znamená, že není volání `SetCompatibilityVersion` je stejný jako volání funkce `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="1476d-116">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="1476d-117">Následující kód nastaví režim kompatibility ASP.NET Core 2.1, s výjimkou následujícího chování:</span><span class="sxs-lookup"><span data-stu-id="1476d-117">The following code sets the compatibility mode to ASP.NET Core 2.1, except for the following behaviors:</span></span>

* [<span data-ttu-id="1476d-118">AllowCombiningAuthorizeFilters</span><span class="sxs-lookup"><span data-stu-id="1476d-118">AllowCombiningAuthorizeFilters</span></span>](https://github.com/aspnet/Mvc/blob/master/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs)
* [<span data-ttu-id="1476d-119">InputFormatterExceptionPolicy</span><span class="sxs-lookup"><span data-stu-id="1476d-119">InputFormatterExceptionPolicy</span></span>](https://github.com/aspnet/Mvc/blob/master/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs)

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="1476d-120">Pro aplikace, dojde k rozbíjející změny chování, pomocí příslušné kompatibilita přepínačů:</span><span class="sxs-lookup"><span data-stu-id="1476d-120">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="1476d-121">Umožňuje použít nejnovější verzi a vyjádřit výslovný nesouhlas zvláštní nejnovější změny chování.</span><span class="sxs-lookup"><span data-stu-id="1476d-121">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="1476d-122">Získáte čas na aktualizaci aplikace, funguje s nejnovějšími změnami.</span><span class="sxs-lookup"><span data-stu-id="1476d-122">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="1476d-123">[MvcOptions](https://github.com/aspnet/Mvc/blob/master/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs) komentáře zdrojové třídy mají dobrou vysvětlení co změnil a proč změny jsou vylepšení pro většinu uživatelů.</span><span class="sxs-lookup"><span data-stu-id="1476d-123">The [MvcOptions](https://github.com/aspnet/Mvc/blob/master/src/Microsoft.AspNetCore.Mvc.Core/MvcOptions.cs) class source comments have a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="1476d-124">V některé budoucí datum, bude [verze technologie ASP.NET Core 3.0](https://github.com/aspnet/Home/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="1476d-124">At some future date, there will be an [ASP.NET Core 3.0 version](https://github.com/aspnet/Home/wiki/Roadmap).</span></span> <span data-ttu-id="1476d-125">Ve verzi 3.0 se odebere staré chování podporuje přepínače kompatibility.</span><span class="sxs-lookup"><span data-stu-id="1476d-125">Old behaviors supported by compatibility switches will be removed in the 3.0 version.</span></span> <span data-ttu-id="1476d-126">Domníváme, že se že jedná pozitivní změny, které téměř všechny uživatele.</span><span class="sxs-lookup"><span data-stu-id="1476d-126">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="1476d-127">Zavedením teď tyto změny, mohou nyní využívat většinu aplikací a ostatní bude mít čas aktualizovat svoje aplikace.</span><span class="sxs-lookup"><span data-stu-id="1476d-127">By introducing these changes now, most apps can benefit now, and the others will have time to update their apps.</span></span>
