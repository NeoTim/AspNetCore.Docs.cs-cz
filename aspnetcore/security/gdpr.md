---
title: Podpora Obecné nařízení o ochraně osobních údajů (GDPR) v ASP.NET Core
author: rick-anderson
description: Naučte se přistupovat k bodům rozšíření GDPR ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 6392a22e316f903da18cd1a91d1eb779d8dde1b3
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020012"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="dcfa8-103">Podpora GDPR (EU Obecné nařízení o ochraně osobních údajů) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcfa8-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="dcfa8-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dcfa8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dcfa8-105">ASP.NET Core poskytuje rozhraní API a šablony, které vám pomůžou splnit některé z požadavků [EU obecné nařízení o ochraně osobních údajů (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="dcfa8-106">Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit zásadami ochrany osobních údajů a cookie používání.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="dcfa8-107">Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="dcfa8-108">Pokud chcete povolit cookie funkci výchozího souhlasu, kterou najdete v šablonách ASP.NET Core 2,2 v aplikaci ASP.NET Core 3,0 vygenerované šablonou:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="dcfa8-109">Přidejte `using Microsoft.AspNetCore.Http` do seznamu direktiv using.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="dcfa8-110">Přidat [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) do `Startup.ConfigureServices` a [použít Cookie zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) pro `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-110">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) to `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="dcfa8-111">Přidejte do cookie souboru *_Layout. cshtml* část souhlasu:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-111">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="dcfa8-112">Přidejte do projektu soubor \* \_ Cookie ConsentPartial. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-112">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="dcfa8-113">Pokud si chcete přečíst informace o funkci pro vyjádření souhlasu, vyberte verzi tohoto článku ASP.NET Core 2,2 cookie .</span><span class="sxs-lookup"><span data-stu-id="dcfa8-113">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="dcfa8-114">Šablony projektů obsahují Rozšiřovací body a podložit značky, které můžete nahradit zásadami ochrany osobních údajů a cookie používání.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-114">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="dcfa8-115">cookieFunkce pro vyjádření souhlasu vám umožní požádat o udělení souhlasu (a sledovat), aby uživatelé mohli ukládat osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-115">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="dcfa8-116">Pokud uživatel nesouhlasí s shromažďováním dat a aplikace má [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) nastavenou na `true` , nepostradatelné cookie zprávy se neodesílají do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="dcfa8-117">Cookies může být označena jako základní.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-117">Cookies can be marked as essential.</span></span> <span data-ttu-id="dcfa8-118">Základní cookie s se odesílají do prohlížeče i v případě, že uživatel není odsouhlasený a sledování je zakázané.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-118">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="dcfa8-119">[TempData a relace cookie ](#tempdata) nejsou funkční, když je sledování zakázané.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-119">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="dcfa8-120">Stránka [ Identity Správa](#pd) poskytuje odkaz na stažení a odstranění uživatelských dat.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-120">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="dcfa8-121">[Ukázková aplikace](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umožňuje testovat většinu bodů rozšíření GDPR a rozhraní API přidaných do šablon ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="dcfa8-122">Pokyny k testování najdete v souboru [Readme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) .</span><span class="sxs-lookup"><span data-stu-id="dcfa8-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="dcfa8-123">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="dcfa8-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="dcfa8-124">Podpora ASP.NET Core GDPR v kódu generovaném šablonou</span><span class="sxs-lookup"><span data-stu-id="dcfa8-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="dcfa8-125">RazorStránky a projekty MVC vytvořené pomocí šablon projektů zahrnují následující podporu GDPR:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-125">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="dcfa8-126">[ Cookie Zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) [ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a use jsou nastaveny ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-126">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="dcfa8-127">[Částečné zobrazení](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) \* \_ Cookie ConsentPartial. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="dcfa8-127">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="dcfa8-128">V tomto souboru je zahrnuté tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="dcfa8-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="dcfa8-129">Když uživatel klikne na tlačítko **přijmout** , je k dispozici souhlas s uložením cookie s.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-129">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="dcfa8-130">Zobrazení stránky */soukromí. cshtml* nebo *zobrazení/ochrana osobních údajů. cshtml* poskytuje stránku s podrobnostmi o zásadách ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="dcfa8-131">Soubor \* \_ Cookie ConsentPartial. cshtml\* vygeneruje odkaz na stránku ochrany osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-131">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="dcfa8-132">Pro aplikace vytvořené pomocí individuálních uživatelských účtů poskytují stránky Správa odkazy na stažení a odstranění [osobních uživatelských dat](#pd).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="dcfa8-133">CookiePolicyOptions a použití Cookie zásad</span><span class="sxs-lookup"><span data-stu-id="dcfa8-133">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="dcfa8-134">[ Cookie PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) jsou inicializovány v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-134">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="dcfa8-135">[Použití Cookie Zásady](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se volají v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-135">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="dcfa8-136">\_CookieConsentPartial. cshtml – částečné zobrazení</span><span class="sxs-lookup"><span data-stu-id="dcfa8-136">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="dcfa8-137">Částečné zobrazení \* \_ Cookie ConsentPartial. cshtml\* :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-137">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="dcfa8-138">Tato částečná:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-138">This partial:</span></span>

* <span data-ttu-id="dcfa8-139">Získá stav sledování pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="dcfa8-140">Pokud je aplikace nakonfigurovaná tak, aby vyžadovala souhlas, musí uživatel souhlasit, než cookie bude možné sledovat.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-140">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="dcfa8-141">Je-li vyžadován souhlas, cookie je panel souhlasu opraven v horní části navigačního panelu vytvořeného souborem \* \_ layout. cshtml\* .</span><span class="sxs-lookup"><span data-stu-id="dcfa8-141">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="dcfa8-142">Poskytuje prvek HTML `<p>` pro Shrnutí ochrany osobních údajů a cookie používání zásad.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-142">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="dcfa8-143">Poskytuje odkaz na stránku nebo zobrazení ochrany osobních údajů, kde můžete podrobně určit zásady ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="dcfa8-144">Základní cookie s</span><span class="sxs-lookup"><span data-stu-id="dcfa8-144">Essential cookies</span></span>

<span data-ttu-id="dcfa8-145">Pokud se nezadá souhlas s úložištěm cookie , cookie pošle se do prohlížeče jenom s označením důležité.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-145">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="dcfa8-146">Následující kód je cookie základem:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-146">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="dcfa8-147">Poskytovatel TempData a stav relace cookie s nejsou nezbytné.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-147">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="dcfa8-148">[Poskytovatel TempData](xref:fundamentals/app-state#tempdata) cookie není nezbytný.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-148">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="dcfa8-149">Pokud je sledování zakázané, zprostředkovatel TempData není funkční.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="dcfa8-150">Pokud chcete poskytovatele TempData povolit, když je sledování zakázané, označte TempData cookie jako základní v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dcfa8-150">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="dcfa8-151">[Stav relace](xref:fundamentals/app-state) cookie s nejsou důležité.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-151">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="dcfa8-152">Stav relace není funkční, pokud je zakázané sledování.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="dcfa8-153">Následující kód provede relaci cookie v podstatě:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-153">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="dcfa8-154">Osobní údaje</span><span class="sxs-lookup"><span data-stu-id="dcfa8-154">Personal data</span></span>

<span data-ttu-id="dcfa8-155">Aplikace ASP.NET Core vytvořené pomocí individuálních uživatelských účtů obsahují kód ke stažení a odstranění osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="dcfa8-156">Vyberte uživatelské jméno a pak vyberte **osobní údaje**:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-156">Select the user name and then select **Personal data**:</span></span>

![Stránka spravovat osobní data](gdpr/_static/pd.png)

<span data-ttu-id="dcfa8-158">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-158">Notes:</span></span>

* <span data-ttu-id="dcfa8-159">Chcete-li vygenerovat `Account/Manage` kód, viz [generování uživatelského rozhraní Identity ](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-159">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="dcfa8-160">Odkazy na **odstranění** a **stažení** se chovají jenom s výchozími daty identity.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="dcfa8-161">Aplikace, které vytvářejí vlastní uživatelská data, je nutné rozšířit tak, aby se odstranily nebo stáhly vlastní uživatelská data.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="dcfa8-162">Další informace najdete v tématu [Přidání, stažení a odstranění vlastních uživatelských dat na Identity ](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-162">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="dcfa8-163">Uložené tokeny pro uživatele, které jsou uložené v Identity tabulce databáze, `AspNetUserTokens` se odstraní, když se uživatel odstraní prostřednictvím chování při Kaskádovém odstraňování z důvodu [cizího klíče](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-163">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="dcfa8-164">[Ověřování externího poskytovatele](xref:security/authentication/social/index), jako je Facebook a Google, není k dispozici před cookie přijetím zásad.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="dcfa8-165">Šifrování neaktivních uložených dat</span><span class="sxs-lookup"><span data-stu-id="dcfa8-165">Encryption at rest</span></span>

<span data-ttu-id="dcfa8-166">Některé databáze a mechanismy úložiště umožňují šifrování v klidovém umístění.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="dcfa8-167">Šifrování v klidovém umístění:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-167">Encryption at rest:</span></span>

* <span data-ttu-id="dcfa8-168">Automaticky šifruje uložená data.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="dcfa8-169">Šifruje bez konfigurace, programování nebo jiné práce pro software, který přistupuje k datům.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="dcfa8-170">Je nejjednodušší a nejbezpečnější možnost.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="dcfa8-171">Umožňuje databázi spravovat klíče a šifrování.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="dcfa8-172">Například:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-172">For example:</span></span>

* <span data-ttu-id="dcfa8-173">Microsoft SQL a Azure SQL poskytují [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="dcfa8-174">SQL Azure šifruje databázi ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="dcfa8-175">[Objekty blob Azure, soubory, tabulky a Queue Storage jsou ve výchozím nastavení šifrované](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="dcfa8-176">Pro databáze, které neposkytují integrované šifrování v klidovém provozu, můžete k zajištění stejné ochrany použít šifrování disku.</span><span class="sxs-lookup"><span data-stu-id="dcfa8-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="dcfa8-177">Například:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-177">For example:</span></span>

* [<span data-ttu-id="dcfa8-178">BitLocker pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="dcfa8-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="dcfa8-179">Linux:</span><span class="sxs-lookup"><span data-stu-id="dcfa8-179">Linux:</span></span>
  * [<span data-ttu-id="dcfa8-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="dcfa8-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="dcfa8-181">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="dcfa8-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dcfa8-182">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dcfa8-182">Additional resources</span></span>

* [<span data-ttu-id="dcfa8-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="dcfa8-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [<span data-ttu-id="dcfa8-184">GDPR – přidání tlačítka pro odvolání souhlasu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dcfa8-184">GDPR - Adding a Revoke Consent Button in ASP.NET Core</span></span>](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
