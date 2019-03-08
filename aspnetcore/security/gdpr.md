---
title: General Data Protection Regulation (GDPR) podporují v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak získat přístup k GDPR Rozšiřovací body ve webové aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/29/2018
uid: security/gdpr
ms.openlocfilehash: bbb3b8e091b5a0be8e852d70ba1a5d7100782ba3
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665373"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="455d3-103">Podpora EU obecného Regulation (GDPR) v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="455d3-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="455d3-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="455d3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="455d3-105">ASP.NET Core nabízí rozhraní API a šablony, které vám pomohou splnit některé [EU obecného Regulation (GDPR)](https://www.eugdpr.org/) požadavky:</span><span class="sxs-lookup"><span data-stu-id="455d3-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements:</span></span>

* <span data-ttu-id="455d3-106">Šablony projektů zahrnout Rozšiřovací body a provizorní kód, který můžete nahradit ochrany osobních údajů a zásady použití souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="455d3-106">The project templates include extension points and stubbed markup that you can replace with your privacy and cookie use policy.</span></span>
* <span data-ttu-id="455d3-107">Souhlas funkci soubor cookie umožňuje požádat o (a sledovat) souhlas uživatele pro ukládání osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="455d3-107">A cookie consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="455d3-108">Pokud uživatel nevyjádřil souhlas shromažďování dat a k němu má aplikace [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) nastavena na `true`, které není nezbytné soubory cookie se neodesílají do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="455d3-108">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) set to `true`, non-essential cookies aren't sent to the browser.</span></span>
* <span data-ttu-id="455d3-109">Soubory cookie, může být označený jako důležité.</span><span class="sxs-lookup"><span data-stu-id="455d3-109">Cookies can be marked as essential.</span></span> <span data-ttu-id="455d3-110">Základní cookie se odesílají do prohlížeče, i v případě, že uživatel nevyjádřil a sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="455d3-110">Essential cookies are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="455d3-111">[Soubory cookie TempData a relace](#tempdata) nejsou funkční při sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="455d3-111">[TempData and Session cookies](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="455d3-112">[Spravovat Identity](#pd) stránka obsahuje odkaz ke stažení a odstranění dat uživatele.</span><span class="sxs-lookup"><span data-stu-id="455d3-112">The [Identity manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="455d3-113">[Ukázkovou aplikaci](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) umožňuje testování většinu Rozšiřovací body podle nařízení GDPR a přidali rozhraní API pro ASP.NET Core 2.1 šablony.</span><span class="sxs-lookup"><span data-stu-id="455d3-113">The [sample app](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="455d3-114">Zobrazit [ReadMe](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) souboru pokynů.</span><span class="sxs-lookup"><span data-stu-id="455d3-114">See the [ReadMe](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="455d3-115">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="455d3-115">[View or download sample code](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="455d3-116">ASP.NET Core podpory nařízení GDPR v šabloně generovaného kódu</span><span class="sxs-lookup"><span data-stu-id="455d3-116">ASP.NET Core GDPR support in template generated code</span></span>

<span data-ttu-id="455d3-117">Stránky Razor a MVC projekty vytvořené pomocí šablony projektu zahrnují následující podpory nařízení GDPR:</span><span class="sxs-lookup"><span data-stu-id="455d3-117">Razor Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="455d3-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) a [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) se nastavují v `Startup`.</span><span class="sxs-lookup"><span data-stu-id="455d3-118">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) and [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) are set in `Startup`.</span></span>
* <span data-ttu-id="455d3-119">*_CookieConsentPartial.cshtml* [částečné zobrazení](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="455d3-119">The *_CookieConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span>
* <span data-ttu-id="455d3-120">*Pages/Privacy.cshtml* stránky nebo *Views/Home/Privacy.cshtml* zobrazení obsahuje stránku, kterou chcete podrobnosti o zásadách ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="455d3-120">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="455d3-121">*_CookieConsentPartial.cshtml* souboru vytvoří odkaz na stránku o ochraně osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="455d3-121">The *_CookieConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="455d3-122">U aplikací vytvořených pomocí jednotlivých uživatelských účtů, Správa stránka obsahuje odkazy na stažení a odstranění [osobní údaje](#pd).</span><span class="sxs-lookup"><span data-stu-id="455d3-122">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a><span data-ttu-id="455d3-123">CookiePolicyOptions a UseCookiePolicy</span><span class="sxs-lookup"><span data-stu-id="455d3-123">CookiePolicyOptions and UseCookiePolicy</span></span>

<span data-ttu-id="455d3-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) jsou inicializovány v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="455d3-124">[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="455d3-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) je volána `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="455d3-125">[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="455d3-126">_CookieConsentPartial.cshtml částečného zobrazení</span><span class="sxs-lookup"><span data-stu-id="455d3-126">_CookieConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="455d3-127">*_CookieConsentPartial.cshtml* částečné zobrazení:</span><span class="sxs-lookup"><span data-stu-id="455d3-127">The *_CookieConsentPartial.cshtml* partial view:</span></span>

[!code-html[](gdpr/sample/RP/Pages/Shared/_CookieConsentPartial.cshtml)]

<span data-ttu-id="455d3-128">Tato části:</span><span class="sxs-lookup"><span data-stu-id="455d3-128">This partial:</span></span>

* <span data-ttu-id="455d3-129">Získá stav sledování pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="455d3-129">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="455d3-130">Pokud aplikace je nakonfigurovaná tak, aby vyžadovala souhlasu, musí uživatel souhlasit předtím, než lze sledovat soubory cookie.</span><span class="sxs-lookup"><span data-stu-id="455d3-130">If the app is configured to require consent, the user must consent before cookies can be tracked.</span></span> <span data-ttu-id="455d3-131">Pokud se vyžaduje souhlas, panelu souhlasu souboru cookie vyřešen v horní navigační panel vytvořené *_Layout.cshtml* souboru.</span><span class="sxs-lookup"><span data-stu-id="455d3-131">If consent is required, the cookie consent panel is fixed at top of the navigation bar created by the *_Layout.cshtml* file.</span></span>
* <span data-ttu-id="455d3-132">Poskytuje HTML `<p>` element slouží ke shrnutí vašich osobních údajů a soubory cookie použít zásady.</span><span class="sxs-lookup"><span data-stu-id="455d3-132">Provides an HTML `<p>` element to summarize your privacy and cookie use policy.</span></span>
* <span data-ttu-id="455d3-133">Obsahuje odkaz na stránku o ochraně osobních údajů nebo zobrazení, ve kterém můžete podrobně popisují zásady ochrany osobních údajů vašeho webu.</span><span class="sxs-lookup"><span data-stu-id="455d3-133">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-cookies"></a><span data-ttu-id="455d3-134">Základní soubory cookie</span><span class="sxs-lookup"><span data-stu-id="455d3-134">Essential cookies</span></span>

<span data-ttu-id="455d3-135">Pokud neudělil souhlas jenom soubory cookie označené základní odešlou do prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="455d3-135">If consent has not been given, only cookies marked essential are sent to the browser.</span></span> <span data-ttu-id="455d3-136">Následující kód provede základní cookie:</span><span class="sxs-lookup"><span data-stu-id="455d3-136">The following code makes a cookie essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

## <a name="tempdata-provider-and-session-state-cookies-are-not-essential"></a><span data-ttu-id="455d3-137">Soubory cookie stavu Tempdata zprostředkovatele a relace nejsou nezbytně nutné</span><span class="sxs-lookup"><span data-stu-id="455d3-137">Tempdata provider and session state cookies are not essential</span></span>

<span data-ttu-id="455d3-138">[Tempdata poskytovatele](xref:fundamentals/app-state#tempdata) souboru cookie není podstatné.</span><span class="sxs-lookup"><span data-stu-id="455d3-138">The [Tempdata provider](xref:fundamentals/app-state#tempdata) cookie isn't essential.</span></span> <span data-ttu-id="455d3-139">Pokud sledování je vypnuté, zprostředkovatel Tempdata není funkční.</span><span class="sxs-lookup"><span data-stu-id="455d3-139">If tracking is disabled, the Tempdata provider isn't functional.</span></span> <span data-ttu-id="455d3-140">K povolení zprostředkovatele Tempdata při sledování je vypnuté, označte TempData soubor cookie jako důležité pro `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="455d3-140">To enable the Tempdata provider when tracking is disabled, mark the TempData cookie as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP/Startup.cs?name=snippet1)]

<span data-ttu-id="455d3-141">[Stav relace](xref:fundamentals/app-state) soubory cookie nejsou nezbytně nutné.</span><span class="sxs-lookup"><span data-stu-id="455d3-141">[Session state](xref:fundamentals/app-state) cookies are not essential.</span></span> <span data-ttu-id="455d3-142">Stav relace není funkční, při sledování je vypnuté.</span><span class="sxs-lookup"><span data-stu-id="455d3-142">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="455d3-143">Následující kód provede nezbytné soubory cookie relace:</span><span class="sxs-lookup"><span data-stu-id="455d3-143">The following code makes session cookies essential:</span></span>

[!code-csharp[](gdpr/sample/RP/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="455d3-144">Osobní údaje</span><span class="sxs-lookup"><span data-stu-id="455d3-144">Personal data</span></span>

<span data-ttu-id="455d3-145">Aplikace ASP.NET Core s jednotlivými uživatelskými účty vytvořené zahrnout kód ke stažení a odstranění osobních údajů.</span><span class="sxs-lookup"><span data-stu-id="455d3-145">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="455d3-146">Vyberte uživatelské jméno a pak vyberte **osobní údaje**:</span><span class="sxs-lookup"><span data-stu-id="455d3-146">Select the user name and then select **Personal data**:</span></span>

![Spravovat stránky osobních údajů](gdpr/_static/pd.png)

<span data-ttu-id="455d3-148">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="455d3-148">Notes:</span></span>

* <span data-ttu-id="455d3-149">Ke generování `Account/Manage` kódu naleznete v tématu [vygenerované uživatelské rozhraní Identity](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="455d3-149">To generate the `Account/Manage` code, see [Scaffold Identity](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="455d3-150">**Odstranit** a **Stáhnout** odkazy reagovat jen na výchozí identifikační údaje.</span><span class="sxs-lookup"><span data-stu-id="455d3-150">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="455d3-151">Aplikace, které vytvořit vlastní uživatelská data musí být rozšířené na odstranění a stažení vlastní uživatelská data.</span><span class="sxs-lookup"><span data-stu-id="455d3-151">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="455d3-152">Další informace najdete v tématu [přidat, stahování a odstranit vlastní uživatelská data na identitu](xref:security/authentication/add-user-data).</span><span class="sxs-lookup"><span data-stu-id="455d3-152">For more information, see [Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="455d3-153">Uložit tokeny pro daného uživatele, které jsou uložené v tabulce databáze Identity `AspNetUserTokens` se odstraní při odstranění uživatele prostřednictvím kaskádové odstranění chování kvůli [cizí klíč](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span><span class="sxs-lookup"><span data-stu-id="455d3-153">Saved tokens for the user that are stored in the Identity database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).</span></span>
* <span data-ttu-id="455d3-154">[Externí zprostředkovatel ověřování](xref:security/authentication/social/index), jako je Facebook nebo Google, není k dispozici, před přijetím zásady souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="455d3-154">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the cookie policy is accepted.</span></span>

## <a name="encryption-at-rest"></a><span data-ttu-id="455d3-155">Šifrování v klidovém stavu</span><span class="sxs-lookup"><span data-stu-id="455d3-155">Encryption at rest</span></span>

<span data-ttu-id="455d3-156">Některé databáze a úložiště mechanismy povolit šifrování v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="455d3-156">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="455d3-157">Šifrování v klidovém stavu:</span><span class="sxs-lookup"><span data-stu-id="455d3-157">Encryption at rest:</span></span>

* <span data-ttu-id="455d3-158">Uložená data automaticky šifruje.</span><span class="sxs-lookup"><span data-stu-id="455d3-158">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="455d3-159">Šifruje bez konfigurace, programování a další práci pro software, který přistupuje k datům.</span><span class="sxs-lookup"><span data-stu-id="455d3-159">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="455d3-160">Nejsnadnější a nejbezpečnější způsob je.</span><span class="sxs-lookup"><span data-stu-id="455d3-160">Is the easiest and safest option.</span></span>
* <span data-ttu-id="455d3-161">Umožňuje databáze, kterou chcete spravovat klíče a šifrování.</span><span class="sxs-lookup"><span data-stu-id="455d3-161">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="455d3-162">Příklad:</span><span class="sxs-lookup"><span data-stu-id="455d3-162">For example:</span></span>

* <span data-ttu-id="455d3-163">Microsoft SQL a Azure SQL poskytují [transparentního šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span><span class="sxs-lookup"><span data-stu-id="455d3-163">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="455d3-164">SQL Azure ve výchozím nastavení šifruje databázi</span><span class="sxs-lookup"><span data-stu-id="455d3-164">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="455d3-165">[Ve výchozím nastavení jsou zašifrovány Azure objekty BLOB, soubory, tabulky a fronty úložiště](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span><span class="sxs-lookup"><span data-stu-id="455d3-165">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="455d3-166">Pro databáze, které neposkytují integrované šifrování v klidovém stavu budete moci použít šifrování disku k poskytnutí stejnou úroveň ochrany.</span><span class="sxs-lookup"><span data-stu-id="455d3-166">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="455d3-167">Příklad:</span><span class="sxs-lookup"><span data-stu-id="455d3-167">For example:</span></span>

* [<span data-ttu-id="455d3-168">Nástroj BitLocker pro systém Windows Server</span><span class="sxs-lookup"><span data-stu-id="455d3-168">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="455d3-169">Linux:</span><span class="sxs-lookup"><span data-stu-id="455d3-169">Linux:</span></span>
  * [<span data-ttu-id="455d3-170">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="455d3-170">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="455d3-171">[EncFS](https://github.com/vgough/encfs).</span><span class="sxs-lookup"><span data-stu-id="455d3-171">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="455d3-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="455d3-172">Additional resources</span></span>

* [<span data-ttu-id="455d3-173">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="455d3-173">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
