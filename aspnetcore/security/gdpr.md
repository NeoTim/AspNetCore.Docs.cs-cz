---
title: General Data Protection Regulation (GDPR) podporují v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak získat přístup k GDPR Rozšiřovací body ve webové aplikaci ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 01d2f8943c0995c1400122b89c4ca7c459a85279
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724566"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="36afb-103">Podpora EU obecného Regulation (GDPR) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36afb-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="36afb-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="36afb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="36afb-105">ASP.NET Core nabízí rozhraní API a šablony, které vám pomohou splnit některé [EU obecného Regulation (GDPR)](https://www.eugdpr.org/) požadavky:</span><span class="sxs-lookup"><span data-stu-id="36afb-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="36afb-106">Šablony projektů zahrnout Rozšiřovací body a provizorní kód, který můžete nahradit ochrany osobních údajů a zásady použití souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="36afb-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="36afb-107">*Pages/Privacy.cshtml* stránky nebo *Views/Home/Privacy.cshtml* zobrazení obsahuje stránku, kterou chcete podrobnosti o zásadách ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="36afb-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="36afb-108">Součástí šablony ASP.NET Core 2.2 v aplikaci ASP.NET Core 3.0 vygenerovanou šablonu k povolení této funkce výchozí soubor cookie souhlas s tímto způsobem:</span><span class="sxs-lookup"><span data-stu-id="36afb-108">To enable the default cookie consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="36afb-109">Přidat [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) příliš `Startup.ConfigureServices` a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) k `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36afb-109">Add [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) too `Startup.ConfigureServices` and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="36afb-110">Přidejte částečnou souhlasu soubor cookie k *_Layout.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="36afb-110">Add the cookie consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="36afb-111">Přidat  *\_CookieConsentPartial.cshtml* soubor do projektu:</span><span class="sxs-lookup"><span data-stu-id="36afb-111">Add the *\_CookieConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* <span data-ttu-id="36afb-112">Vyberte verzi 2.2 technologie ASP.NET Core tohoto článku, přečtěte si informace o funkci soubor cookie souhlas.</span><span class="sxs-lookup"><span data-stu-id="36afb-112">Select the ASP.NET Core 2.2 version of this article to read about the cookie consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="36afb-113">Šablony projektů zahrnout Rozšiřovací body a provizorní kód, který můžete nahradit ochrany osobních údajů a zásady použití souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="36afb-113">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="36afb-114">Souhlas funkci soubor cookie umožňuje požádat o (a sledovat) souhlas uživatele pro ukládání osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="36afb-114">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="36afb-115">Pokud uživatel nevyjádřil souhlas shromažďování dat a k němu má aplikace [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) nastavena na `true`, které není nezbytné soubory cookie se neodesílají do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="36afb-115">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="36afb-116">Soubory cookie, může být označený jako důležité.</span><span class="sxs-lookup"><span data-stu-id="36afb-116">Cookies can be marked as essential.</span></span> <span data-ttu-id="36afb-117">Základní cookie se odesílají do prohlížeče, i v případě, že uživatel nevyjádřil a sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="36afb-117">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="36afb-118">[Soubory cookie TempData a relace](#tempdata) nejsou funkční při sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="36afb-118">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="36afb-119">[Spravovat Identity](#pd) stránka obsahuje odkaz ke stažení a odstranění dat uživatele.</span><span class="sxs-lookup"><span data-stu-id="36afb-119">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="36afb-120">[Ukázkovou aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) umožňuje testování většinu Rozšiřovací body podle nařízení GDPR a přidali rozhraní API pro ASP.NET Core 2.1 šablony.</span><span class="sxs-lookup"><span data-stu-id="36afb-120">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="36afb-121">Zobrazit [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) souboru pokynů.</span><span class="sxs-lookup"><span data-stu-id="36afb-121">See the [ReadMe](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="36afb-122">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36afb-122">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="36afb-123">ASP.NET Core GDPR podporují v šabloně generovaný kód</span><span class="sxs-lookup"><span data-stu-id="36afb-123">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="36afb-124">Stránky Razor a MVC projekty vytvořené pomocí šablony projektu zahrnují následující podpory nařízení GDPR:</span><span class="sxs-lookup"><span data-stu-id="36afb-124">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="36afb-125">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) jsou nastaveny `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="36afb-125">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="36afb-126">*\_CookieConsentPartial.cshtml* [částečné zobrazení](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="36afb-126">The *\_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="36afb-127">**Přijmout** tlačítko je zahrnuta v tomto souboru.</span><span class="sxs-lookup"><span data-stu-id="36afb-127">An **Accept** button is included in this file.</span></span> <span data-ttu-id="36afb-128">Pokud uživatel klikne **přijmout** tlačítko, vyjadřoval souhlas k ukládání souborů cookie je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="36afb-128">When the user clicks the **Accept** button, consent to store cookies is provided.</span></span>
* <span data-ttu-id="36afb-129">*Pages/Privacy.cshtml* stránky nebo *Views/Home/Privacy.cshtml* zobrazení obsahuje stránku, kterou chcete podrobnosti o zásadách ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="36afb-129">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="36afb-130">*\_CookieConsentPartial.cshtml* souboru vytvoří odkaz na stránku o ochraně osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="36afb-130">The *\_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="36afb-131">U aplikací vytvořených pomocí jednotlivých uživatelských účtů, Správa stránka obsahuje odkazy na stažení a odstranění [osobní údaje](#pd).</span><span class="sxs-lookup"><span data-stu-id="36afb-131">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="36afb-132">CookiePolicyOptions a UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="36afb-132">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="36afb-133">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) jsou inicializovány v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36afb-133">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="36afb-134">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) je volána `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36afb-134">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="36afb-135">\_CookieConsentPartial.cshtml částečného zobrazení</span><span class="sxs-lookup"><span data-stu-id="36afb-135">\_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="36afb-136">*\_CookieConsentPartial.cshtml* částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="36afb-136">The *\_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="36afb-137">Tato části:</span><span class="sxs-lookup"><span data-stu-id="36afb-137">This partial:</span></span>

* <span data-ttu-id="36afb-138">Získá stav sledování pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="36afb-138">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="36afb-139">Pokud aplikace je nakonfigurovaná tak, aby vyžadovala souhlasu, musí uživatel souhlasit předtím, než lze sledovat soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="36afb-139">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="36afb-140">Pokud se vyžaduje souhlas, panelu souhlasu souboru cookie vyřešen v horní navigační panel vytvořené  *\_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="36afb-140">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="36afb-141">Poskytuje HTML `<p>` element slouží ke shrnutí vašich osobních údajů a soubory cookie použít zásady.</span><span class="sxs-lookup"><span data-stu-id="36afb-141">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="36afb-142">Obsahuje odkaz na stránku o ochraně osobních údajů nebo zobrazení, ve kterém můžete podrobně popisují zásady ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="36afb-142">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="36afb-143">Základní soubory cookie</span><span class="sxs-lookup"><span data-stu-id="36afb-143">Essential cookies</span></span>

<span data-ttu-id="36afb-144">Pokud souhlas k ukládání souborů cookie nebyl zadán, jsou jenom soubory cookie označené základní odesláno prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="36afb-144">If consent to store cookies hasn't been provided, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="36afb-145">Následující kód provede základní cookie:</span><span class="sxs-lookup"><span data-stu-id="36afb-145">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a><span data-ttu-id="36afb-146">Soubory cookie stavu TempData zprostředkovatele a relace nejsou essential</span><span class="sxs-lookup"><span data-stu-id="36afb-146">TempData provider and session state cookies aren't essential</span></span>

<span data-ttu-id="36afb-147">[TempData poskytovatele](xref:fundamentals/app-state#tempdata) souboru cookie není podstatné.</span><span class="sxs-lookup"><span data-stu-id="36afb-147">The [TempData provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="36afb-148">Pokud sledování je vypnuté, zprostředkovatel TempData není funkční.</span><span class="sxs-lookup"><span data-stu-id="36afb-148">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="36afb-149">K povolení zprostředkovatele TempData při sledování je vypnuté, označte TempData soubor cookie jako důležité pro `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36afb-149">To enable the TempData provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="36afb-150">[Stav relace](xref:fundamentals/app-state) soubory cookie nejsou nezbytně nutné.</span><span class="sxs-lookup"><span data-stu-id="36afb-150">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="36afb-151">Stav relace není funkční, při sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="36afb-151">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="36afb-152">Následující kód provede nezbytné soubory cookie relace:</span><span class="sxs-lookup"><span data-stu-id="36afb-152">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="36afb-153">Osobní údaje</span><span class="sxs-lookup"><span data-stu-id="36afb-153">Personal data</span></span>

<span data-ttu-id="36afb-154">Aplikace ASP.NET Core s jednotlivými uživatelskými účty vytvořené zahrnout kód ke stažení a odstranění osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="36afb-154">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="36afb-155">Vyberte uživatelské jméno a pak vyberte **osobní údaje**:</span><span class="sxs-lookup"><span data-stu-id="36afb-155">Select the user name and then select **Personal data**:</span></span>

![Spravovat stránky osobních údajů](gdpr/_static/pd.png)

<span data-ttu-id="36afb-157">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="36afb-157">Notes:</span></span>

* <span data-ttu-id="36afb-158">Ke generování `Account/Manage` kódu naleznete v tématu [vygenerované uživatelské rozhraní Identity](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="36afb-158">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="36afb-159">**Odstranit** a **Stáhnout** odkazy reagovat jen na výchozí identifikační údaje.</span><span class="sxs-lookup"><span data-stu-id="36afb-159">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="36afb-160">Aplikace, které vytvořit vlastní uživatelská data musí být rozšířené na odstranění a stažení vlastní uživatelská data.</span><span class="sxs-lookup"><span data-stu-id="36afb-160">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="36afb-161">Další informace najdete v tématu [přidat, stahování a odstranit vlastní uživatelská data na identitu](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="36afb-161">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="36afb-162">Uložit tokeny pro daného uživatele, které jsou uložené v tabulce databáze Identity `AspNetUserTokens` se odstraní při odstranění uživatele prostřednictvím kaskádové odstranění chování kvůli [cizí klíč](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="36afb-162">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="36afb-163">[Externí zprostředkovatel ověřování](xref:security/authentication/social/index), jako je Facebook nebo Google, není k dispozici, před přijetím zásady souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="36afb-163">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="36afb-164">Šifrování v klidovém stavu</span><span class="sxs-lookup"><span data-stu-id="36afb-164">Encryption at rest</span></span>

<span data-ttu-id="36afb-165">Některé databáze a úložiště mechanismy povolit šifrování v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="36afb-165">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="36afb-166">Šifrování v klidovém stavu:</span><span class="sxs-lookup"><span data-stu-id="36afb-166">Encryption at rest:</span></span>

* <span data-ttu-id="36afb-167">Uložená data automaticky šifruje.</span><span class="sxs-lookup"><span data-stu-id="36afb-167">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="36afb-168">Šifruje bez konfigurace, programování a další práci pro software, který přistupuje k datům.</span><span class="sxs-lookup"><span data-stu-id="36afb-168">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="36afb-169">Nejsnadnější a nejbezpečnější způsob je.</span><span class="sxs-lookup"><span data-stu-id="36afb-169">Is the easiest and safest option.</span></span>
* <span data-ttu-id="36afb-170">Umožňuje databáze, kterou chcete spravovat klíče a šifrování.</span><span class="sxs-lookup"><span data-stu-id="36afb-170">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="36afb-171">Příklad:</span><span class="sxs-lookup"><span data-stu-id="36afb-171">For example:</span></span>

* <span data-ttu-id="36afb-172">Microsoft SQL a Azure SQL poskytují [transparentního šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="36afb-172">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="36afb-173">SQL Azure ve výchozím nastavení šifruje databázi</span><span class="sxs-lookup"><span data-stu-id="36afb-173">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="36afb-174">[Ve výchozím nastavení jsou zašifrovány Azure objekty BLOB, soubory, tabulky a fronty úložiště](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="36afb-174">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="36afb-175">Pro databáze, které neposkytují integrované šifrování v klidovém stavu budete moci použít šifrování disku k poskytnutí stejnou úroveň ochrany.</span><span class="sxs-lookup"><span data-stu-id="36afb-175">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="36afb-176">Příklad:</span><span class="sxs-lookup"><span data-stu-id="36afb-176">For example:</span></span>

* [<span data-ttu-id="36afb-177">Nástroj BitLocker pro systém Windows Server</span><span class="sxs-lookup"><span data-stu-id="36afb-177">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="36afb-178">Linux:</span><span class="sxs-lookup"><span data-stu-id="36afb-178">Linux:</span></span>
  * [<span data-ttu-id="36afb-179">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="36afb-179">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="36afb-180">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="36afb-180">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36afb-181">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="36afb-181">Additional resources</span></span>

* [<span data-ttu-id="36afb-182">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="36afb-182">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
