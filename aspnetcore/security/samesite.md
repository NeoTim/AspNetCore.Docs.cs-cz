---
title: Práce s SameSite soubory cookie v ASP.NET Core
author: rick-anderson
description: Naučte se používat k SameSite souborů cookie v ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 43d5a3dbc5e202688e006355e0b105a86d721460
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775099"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="5ed22-103">Práce s SameSite soubory cookie v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5ed22-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="5ed22-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5ed22-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5ed22-105">SameSite je Konceptový standard [IETF](https://ietf.org/about/) navržený tak, aby poskytoval určitou ochranu proti útokům prostřednictvím CSRF (pro falšování požadavků mezi lokalitami).</span><span class="sxs-lookup"><span data-stu-id="5ed22-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="5ed22-106">Původně koncept v [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)se aktualizoval koncept standardu v [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="5ed22-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="5ed22-107">Aktualizovaný Standard není zpětně kompatibilní s předchozím standardem, přičemž následující je největší znatelné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="5ed22-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="5ed22-108">Soubory cookie bez hlavičky SameSite se považují `SameSite=Lax` za výchozí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="5ed22-109">`SameSite=None`se musí použít k povolení použití souborů cookie mezi weby.</span><span class="sxs-lookup"><span data-stu-id="5ed22-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="5ed22-110">Soubory cookie, `SameSite=None` které Assert musí být také `Secure`označeny jako.</span><span class="sxs-lookup"><span data-stu-id="5ed22-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="5ed22-111">V aplikacích, [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) které používají, může `sameSite=Lax` docházet k problémům s soubory nebo `sameSite=Strict` soubory cookie, protože `<iframe>` se považují za scénáře mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="5ed22-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="5ed22-112">Hodnota `SameSite=None` není povolena [standardem 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) a způsobí, že některé implementace považují takové soubory cookie za `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="5ed22-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="5ed22-113">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="5ed22-114">`SameSite=Lax` Nastavení funguje pro většinu souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="5ed22-115">Některé formy ověřování, jako je [OpenID Connect](https://openid.net/connect/) (OIDC) a [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , jako výchozí vystavení přesměrování na základě.</span><span class="sxs-lookup"><span data-stu-id="5ed22-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="5ed22-116">Přesměrování na základě příspěvku spustí ochranu prohlížeče SameSite, takže pro tyto součásti je SameSite zakázaný.</span><span class="sxs-lookup"><span data-stu-id="5ed22-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="5ed22-117">Většina přihlášení [OAuth](https://oauth.net/) není ovlivněná kvůli rozdílům ve způsobu, jakým jsou požadavky v toku.</span><span class="sxs-lookup"><span data-stu-id="5ed22-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="5ed22-118">Každá součást ASP.NET Core, která generuje soubory cookie, musí rozhodnout, zda je SameSite vhodná.</span><span class="sxs-lookup"><span data-stu-id="5ed22-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="5ed22-119">Ukázkový kód testu SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="5ed22-120">Následující ukázky lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="5ed22-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="5ed22-121">Ukázka</span><span class="sxs-lookup"><span data-stu-id="5ed22-121">Sample</span></span>               | <span data-ttu-id="5ed22-122">Dokument</span><span class="sxs-lookup"><span data-stu-id="5ed22-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5ed22-123">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5ed22-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="5ed22-124">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5ed22-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ed22-125">Následující ukázku lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="5ed22-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="5ed22-126">Ukázka</span><span class="sxs-lookup"><span data-stu-id="5ed22-126">Sample</span></span>               | <span data-ttu-id="5ed22-127">Dokument</span><span class="sxs-lookup"><span data-stu-id="5ed22-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5ed22-128">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5ed22-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="5ed22-129">Podpora .NET Core pro atribut sameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="5ed22-130">.NET Core 2,2 podporuje pracovní Standard 2019 pro SameSite od vydání aktualizací v prosinci 2019.</span><span class="sxs-lookup"><span data-stu-id="5ed22-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="5ed22-131">Vývojáři mohou programově řídit hodnotu atributu sameSite pomocí `HttpCookie.SameSite` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="5ed22-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="5ed22-132">Výsledkem nastavení `SameSite` vlastnosti na Strict, LAX nebo None jsou hodnoty, které jsou v síti zapisovány pomocí souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="5ed22-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="5ed22-133">Nastavení rovná se (SameSiteMode) (-1) znamená, že v síti se souborem cookie by neměl být zahrnut žádný atribut sameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="5ed22-134">.NET Core 3,0 podporuje aktualizované hodnoty SameSite a `SameSiteMode.Unspecified` do `SameSiteMode` výčtu přidá další hodnotu výčtu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="5ed22-135">Tato nová hodnota znamená, že by se soubor cookie neměl odesílat žádná sameSite.</span><span class="sxs-lookup"><span data-stu-id="5ed22-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="5ed22-136">Prosince změny chování opravy</span><span class="sxs-lookup"><span data-stu-id="5ed22-136">December patch behavior changes</span></span>

<span data-ttu-id="5ed22-137">Konkrétní změna chování pro .NET Framework a .NET Core 2,1 je způsob, `SameSite` jakým vlastnost interpretuje `None` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="5ed22-138">Před opravou hodnoty `None` "negenerovat atribut na všech", po opravě to znamená "vygenerovat atribut s hodnotou `None`".</span><span class="sxs-lookup"><span data-stu-id="5ed22-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="5ed22-139">Po opravě `SameSite` hodnoty `(SameSiteMode)(-1)` způsobí, že atribut nebude generován.</span><span class="sxs-lookup"><span data-stu-id="5ed22-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="5ed22-140">Výchozí hodnota SameSite pro ověřování pomocí formulářů a soubory cookie stavu relace se `None` změnila `Lax`z na.</span><span class="sxs-lookup"><span data-stu-id="5ed22-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="5ed22-141">Použití rozhraní API s SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-141">API usage with SameSite</span></span>

<span data-ttu-id="5ed22-142">[HttpContext. Response. cookies. připojí](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) výchozí hodnoty `Unspecified`, což znamená, že se do souboru cookie nepřidal žádný atribut SameSite a klient použije výchozí chování (Lax pro nové prohlížeče, žádné pro staré).</span><span class="sxs-lookup"><span data-stu-id="5ed22-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="5ed22-143">Následující kód ukazuje, jak změnit hodnotu SameSite souboru cookie na `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5ed22-144">Všechny součásti ASP.NET Core, které generují soubory cookie, potlačí předchozí výchozí nastavení s nastavením vhodným pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="5ed22-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="5ed22-145">Přepsané předchozí výchozí hodnoty se nezměnily.</span><span class="sxs-lookup"><span data-stu-id="5ed22-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="5ed22-146">Součást</span><span class="sxs-lookup"><span data-stu-id="5ed22-146">Component</span></span> | <span data-ttu-id="5ed22-147">cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-147">cookie</span></span> | <span data-ttu-id="5ed22-148">Výchozí</span><span class="sxs-lookup"><span data-stu-id="5ed22-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="5ed22-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="5ed22-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="5ed22-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="5ed22-152">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="5ed22-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="5ed22-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="5ed22-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="5ed22-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="5ed22-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="5ed22-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="5ed22-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="5ed22-158">ASP.NET Core 3,1 a novějším přináší následující podporu SameSite:</span><span class="sxs-lookup"><span data-stu-id="5ed22-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="5ed22-159">Předefinuje chování `SameSiteMode.None` pro vygenerování.`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="5ed22-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="5ed22-160">Přidá novou hodnotu `SameSiteMode.Unspecified` pro vynechání atributu SameSite.</span><span class="sxs-lookup"><span data-stu-id="5ed22-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="5ed22-161">Všechny soubory cookie rozhraní API `Unspecified`jsou ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="5ed22-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="5ed22-162">Některé součásti, které používají soubory cookie, nastavují hodnoty konkrétnější pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="5ed22-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="5ed22-163">Příklady najdete v tabulce výše.</span><span class="sxs-lookup"><span data-stu-id="5ed22-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5ed22-164">V ASP.NET Core 3,0 a novějších byly změněny výchozí hodnoty SameSite, aby nedocházelo ke konfliktům s nekonzistentními výchozími nastaveními klientů.</span><span class="sxs-lookup"><span data-stu-id="5ed22-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="5ed22-165">Následující rozhraní API změnila výchozí hodnoty z `SameSiteMode.Lax ` na `-1` na, aby nedocházelo k vygenerování atributu SameSite pro tyto soubory cookie:</span><span class="sxs-lookup"><span data-stu-id="5ed22-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="5ed22-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>používá se s parametrem [HttpContext. Response. cookies. Append.](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="5ed22-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="5ed22-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>používá se jako továrna pro`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="5ed22-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="5ed22-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5ed22-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="5ed22-169">Historie a změny</span><span class="sxs-lookup"><span data-stu-id="5ed22-169">History and changes</span></span>

<span data-ttu-id="5ed22-170">Podpora SameSite byla poprvé implementována v ASP.NET Core v 2,0 pomocí [standardního konceptu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="5ed22-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="5ed22-171">Standard 2016 byl výslovný.</span><span class="sxs-lookup"><span data-stu-id="5ed22-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="5ed22-172">ASP.NET Core, že je ve výchozím nastavení nastaveno několik `Lax` souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="5ed22-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="5ed22-173">Po zjištění několika [problémů](https://github.com/aspnet/Announcements/issues/318) s ověřováním je většina využití SameSite [zakázaná](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="5ed22-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="5ed22-174">[Opravy](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) byly vydány v listopadu 2019, aby se aktualizovaly z standardu 2016 na standard 2019.</span><span class="sxs-lookup"><span data-stu-id="5ed22-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="5ed22-175">[Koncept 2019 specifikace SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="5ed22-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="5ed22-176">Není **zpětně** kompatibilní s konceptem 2016.</span><span class="sxs-lookup"><span data-stu-id="5ed22-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="5ed22-177">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="5ed22-178">Určuje, `SameSite=Lax` že soubory cookie jsou ve výchozím nastavení považovány za výchozí.</span><span class="sxs-lookup"><span data-stu-id="5ed22-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="5ed22-179">Určuje soubory cookie, které `SameSite=None` explicitně vyhodnotí, aby bylo možné povolit doručování mezi `Secure`weby, musí být označeno jako.</span><span class="sxs-lookup"><span data-stu-id="5ed22-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="5ed22-180">`None`je nová položka, která se má odhlásit.</span><span class="sxs-lookup"><span data-stu-id="5ed22-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="5ed22-181">Je podporován opravami vydanými pro ASP.NET Core 2,1, 2,2 a 3,0.</span><span class="sxs-lookup"><span data-stu-id="5ed22-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="5ed22-182">ASP.NET Core 3,1 má další podporu SameSite.</span><span class="sxs-lookup"><span data-stu-id="5ed22-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="5ed22-183">Ve výchozím nastavení je naplánovaná podpora [Chrome](https://chromestatus.com/feature/5088147346030592) v [únoru 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="5ed22-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="5ed22-184">Prohlížeče začaly při přesunu do tohoto standardu v 2019.</span><span class="sxs-lookup"><span data-stu-id="5ed22-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="5ed22-185">Rozhraní API, která mají vliv na změnu z konceptu Standard 2016 SameSite na 2019 koncept Standard</span><span class="sxs-lookup"><span data-stu-id="5ed22-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="5ed22-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="5ed22-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="5ed22-187">CookieOptions.SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="5ed22-188">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="5ed22-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="5ed22-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="5ed22-190">Podpora starších prohlížečů</span><span class="sxs-lookup"><span data-stu-id="5ed22-190">Supporting older browsers</span></span>

<span data-ttu-id="5ed22-191">SameSite standardně vyhodnocuje, že neznámé hodnoty musí být považovány `SameSite=Strict` za hodnoty. 2016</span><span class="sxs-lookup"><span data-stu-id="5ed22-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="5ed22-192">Aplikace, ke kterým se přistupoval ze starších prohlížečů, které podporují SameSite Standard 2016, se můžou přerušit, když `None`získají vlastnost SameSite s hodnotou.</span><span class="sxs-lookup"><span data-stu-id="5ed22-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="5ed22-193">Webové aplikace musí implementovat detekci prohlížeče, pokud chtějí podporovat starší prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5ed22-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="5ed22-194">ASP.NET Core neimplementuje detekci prohlížeče, protože hodnoty uživatelských agentů jsou vysoce nestálé a často se mění.</span><span class="sxs-lookup"><span data-stu-id="5ed22-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="5ed22-195">Bod rozšíření v v <xref:Microsoft.AspNetCore.CookiePolicy> nástroji umožňuje zapojení do logiky specifické pro uživatele v agentovi.</span><span class="sxs-lookup"><span data-stu-id="5ed22-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="5ed22-196">V `Startup.Configure`přidejte kód, který volá <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo *jakoukoliv* metodu, která zapisuje soubory cookie:</span><span class="sxs-lookup"><span data-stu-id="5ed22-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="5ed22-197">V `Startup.ConfigureServices`přidejte kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5ed22-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="5ed22-198">V předchozím příkladu `MyUserAgentDetectionLib.DisallowsSameSiteNone` je uživatelem zadaná knihovna, která detekuje, jestli uživatelský agent nepodporuje SameSite `None`:</span><span class="sxs-lookup"><span data-stu-id="5ed22-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="5ed22-199">Následující kód ukazuje ukázkovou `DisallowsSameSiteNone` metodu:</span><span class="sxs-lookup"><span data-stu-id="5ed22-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="5ed22-200">Následující kód je určen pouze pro ukázku:</span><span class="sxs-lookup"><span data-stu-id="5ed22-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="5ed22-201">Neměla by být považována za dokončenou.</span><span class="sxs-lookup"><span data-stu-id="5ed22-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="5ed22-202">Není udržována ani podporována.</span><span class="sxs-lookup"><span data-stu-id="5ed22-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="5ed22-203">Testování aplikací pro problémy s SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="5ed22-204">Aplikace, které komunikují se vzdálenými lokalitami, jako je třeba přihlášení třetí strany, potřebují:</span><span class="sxs-lookup"><span data-stu-id="5ed22-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="5ed22-205">Otestujte interakci na více prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="5ed22-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="5ed22-206">Použijte [detekci a zmírnění v prohlížeči CookiePolicy](#sob) popsané v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="5ed22-207">Otestujte webové aplikace pomocí verze klienta, která se může přihlásit k novému chování SameSite.</span><span class="sxs-lookup"><span data-stu-id="5ed22-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="5ed22-208">Pro Chrome, Firefox a chrom Edge mají všechny nové příznaky funkcí pro výslovný souhlas, které lze použít k testování.</span><span class="sxs-lookup"><span data-stu-id="5ed22-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="5ed22-209">Po použití oprav SameSite se aplikace testuje se staršími verzemi klientů, zejména Safari.</span><span class="sxs-lookup"><span data-stu-id="5ed22-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="5ed22-210">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="5ed22-211">Testování s použitím Chromu</span><span class="sxs-lookup"><span data-stu-id="5ed22-211">Test with Chrome</span></span>

<span data-ttu-id="5ed22-212">Chrome 78 + poskytuje zavádějící výsledky, protože má na zpracování dočasné omezení.</span><span class="sxs-lookup"><span data-stu-id="5ed22-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="5ed22-213">Chrome 78 + dočasné zmírnění umožňuje, aby soubory cookie byly starší než dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="5ed22-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="5ed22-214">Chrome 76 nebo 77 s povolenými vhodnými příznakem testu poskytuje přesnější výsledky.</span><span class="sxs-lookup"><span data-stu-id="5ed22-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="5ed22-215">Chcete-li otestovat nové chování SameSite `chrome://flags/#same-site-by-default-cookies` , přepněte na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="5ed22-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="5ed22-216">Starší verze Chrome (75 a novější) se hlásí jako neúspěšné s novým `None` nastavením.</span><span class="sxs-lookup"><span data-stu-id="5ed22-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="5ed22-217">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="5ed22-218">Google nezpřístupňuje starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="5ed22-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="5ed22-219">Postupujte podle pokynů v části [stažení Chromu](https://www.chromium.org/getting-involved/download-chromium) a otestujte starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="5ed22-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="5ed22-220">Nestahujte Chrome z odkazů **, které jsou** k dispozici, hledáním ve starších verzích Chrome.</span><span class="sxs-lookup"><span data-stu-id="5ed22-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="5ed22-221">Chróm 76 Win64</span><span class="sxs-lookup"><span data-stu-id="5ed22-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="5ed22-222">Chróm 74 Win64</span><span class="sxs-lookup"><span data-stu-id="5ed22-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="5ed22-223">Počínaje verzí na Kanárských `80.0.3975.0`verzích je dočasné zmírnění LAX + post možné zakázat pro účely testování pomocí nového příznaku `--enable-features=SameSiteDefaultChecksMethodRigorously` , který umožňuje testování lokalit a služeb v konečném stavu funkce, ve které byla zmírnění odstraněna.</span><span class="sxs-lookup"><span data-stu-id="5ed22-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="5ed22-224">Další informace najdete v tématu [aktualizace SameSite](https://www.chromium.org/updates/same-site) v projektech Chromu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="5ed22-225">Testování pomocí Safari</span><span class="sxs-lookup"><span data-stu-id="5ed22-225">Test with Safari</span></span>

<span data-ttu-id="5ed22-226">Prohlížeč Safari 12 striktně implementuje předchozí koncept a v případě, že `None` je nová hodnota v souboru cookie, se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="5ed22-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="5ed22-227">`None`se vyhněte prostřednictvím kódu detekce prohlížeče, který [podporuje starší prohlížeče](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="5ed22-228">Pomocí MSAL, ADAL nebo libovolné knihovny, kterou používáte, otestujte přihlašovací údaje pro WebKit Safari 12, Safari 13 a na bázi.</span><span class="sxs-lookup"><span data-stu-id="5ed22-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="5ed22-229">Problém závisí na základní verzi operačního systému.</span><span class="sxs-lookup"><span data-stu-id="5ed22-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="5ed22-230">OSX Mojave (10,14) a iOS 12 se nazývají problémy s kompatibilitou s novým chováním SameSite.</span><span class="sxs-lookup"><span data-stu-id="5ed22-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="5ed22-231">Upgrade operačního systému na OSX Catalina (10,15) nebo iOS 13 opravuje problém.</span><span class="sxs-lookup"><span data-stu-id="5ed22-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="5ed22-232">Prohlížeč Safari aktuálně nemá příznak výslovných přihlášení pro testování nového chování specifikace.</span><span class="sxs-lookup"><span data-stu-id="5ed22-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="5ed22-233">Testování pomocí Firefox</span><span class="sxs-lookup"><span data-stu-id="5ed22-233">Test with Firefox</span></span>

<span data-ttu-id="5ed22-234">Podporu aplikace Firefox pro nový standard lze testovat na verzi 68 + tím, že na `about:config` stránce zapnete příznak `network.cookie.sameSite.laxByDefault`funkce.</span><span class="sxs-lookup"><span data-stu-id="5ed22-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="5ed22-235">Nebyly zjištěny žádné zprávy o problémech s kompatibilitou se staršími verzemi aplikace Firefox.</span><span class="sxs-lookup"><span data-stu-id="5ed22-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="5ed22-236">Testování pomocí prohlížeče Edge</span><span class="sxs-lookup"><span data-stu-id="5ed22-236">Test with Edge browser</span></span>

<span data-ttu-id="5ed22-237">Edge podporuje starý SameSite Standard.</span><span class="sxs-lookup"><span data-stu-id="5ed22-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="5ed22-238">Edge verze 44 nemá žádné známé problémy s kompatibilitou s novým standardem.</span><span class="sxs-lookup"><span data-stu-id="5ed22-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="5ed22-239">Test s hranou (chrom)</span><span class="sxs-lookup"><span data-stu-id="5ed22-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="5ed22-240">Příznaky SameSite jsou nastaveny na `edge://flags/#same-site-by-default-cookies` stránce.</span><span class="sxs-lookup"><span data-stu-id="5ed22-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="5ed22-241">U Edge Chromu se nezjistily žádné problémy s kompatibilitou.</span><span class="sxs-lookup"><span data-stu-id="5ed22-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="5ed22-242">Testování s elektronem</span><span class="sxs-lookup"><span data-stu-id="5ed22-242">Test with Electron</span></span>

<span data-ttu-id="5ed22-243">K verzím elektronů patří starší verze Chromu.</span><span class="sxs-lookup"><span data-stu-id="5ed22-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="5ed22-244">Například verze elektronicky používané týmy je chrom 66, který vykazuje starší chování.</span><span class="sxs-lookup"><span data-stu-id="5ed22-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="5ed22-245">Je nutné provést vlastní testování kompatibility s verzí elektronů, kterou váš produkt používá.</span><span class="sxs-lookup"><span data-stu-id="5ed22-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="5ed22-246">Viz [Podpora starších prohlížečů](#sob) v následující části.</span><span class="sxs-lookup"><span data-stu-id="5ed22-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5ed22-247">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="5ed22-247">Additional resources</span></span>

* [<span data-ttu-id="5ed22-248">Chromový blog: vývojáři: Připravte se na nové SameSite = None; Nastavení zabezpečeného souboru cookie</span><span class="sxs-lookup"><span data-stu-id="5ed22-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="5ed22-249">Vysvětlení souborů cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="5ed22-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="5ed22-250">Opravy od listopadu 2019</span><span class="sxs-lookup"><span data-stu-id="5ed22-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="5ed22-251">Ukázka</span><span class="sxs-lookup"><span data-stu-id="5ed22-251">Sample</span></span>               | <span data-ttu-id="5ed22-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="5ed22-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="5ed22-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="5ed22-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="5ed22-254">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="5ed22-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="5ed22-255">Ukázka</span><span class="sxs-lookup"><span data-stu-id="5ed22-255">Sample</span></span>               | <span data-ttu-id="5ed22-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="5ed22-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5ed22-257">[Stránky .NET Razor Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="5ed22-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
