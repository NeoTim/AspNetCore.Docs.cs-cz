---
title: Práce s SameSite soubory cookie v ASP.NET Core
author: rick-anderson
description: Naučte se používat k SameSite souborů cookie v ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Electron
uid: security/samesite
ms.openlocfilehash: eeba2c4403d33312692ed187021a125c22df5d08
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667739"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="65c17-103">Práce s SameSite soubory cookie v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65c17-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="65c17-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65c17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="65c17-105">SameSite je Konceptový standard [IETF](https://ietf.org/about/) navržený tak, aby poskytoval určitou ochranu proti útokům prostřednictvím CSRF (pro falšování požadavků mezi lokalitami).</span><span class="sxs-lookup"><span data-stu-id="65c17-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="65c17-106">Původně koncept v [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)se aktualizoval koncept standardu v [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="65c17-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="65c17-107">Aktualizovaný Standard není zpětně kompatibilní s předchozím standardem, přičemž následující je největší znatelné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="65c17-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="65c17-108">Soubory cookie bez hlavičky SameSite se ve výchozím nastavení považují za `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="65c17-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="65c17-109">aby bylo možné používat soubory cookie pro více webů, je třeba použít `SameSite=None`.</span><span class="sxs-lookup"><span data-stu-id="65c17-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="65c17-110">Soubory cookie, které vyhodnotí `SameSite=None`, musí být také označeny jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="65c17-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="65c17-111">U aplikací, které používají [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) , může docházet k problémům s `sameSite=Lax` nebo `sameSite=Strict` soubory cookie, protože `<iframe>` se považují za scénáře mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="65c17-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="65c17-112">Hodnota `SameSite=None` není povolena [standardem 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) a způsobí, že některé implementace považují takové soubory cookie za `SameSite=Strict`.</span><span class="sxs-lookup"><span data-stu-id="65c17-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="65c17-113">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="65c17-114">Nastavení `SameSite=Lax` funguje pro většinu souborů cookie aplikace.</span><span class="sxs-lookup"><span data-stu-id="65c17-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="65c17-115">Některé formy ověřování, jako je [OpenID Connect](https://openid.net/connect/) (OIDC) a [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , jako výchozí vystavení přesměrování na základě.</span><span class="sxs-lookup"><span data-stu-id="65c17-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="65c17-116">Přesměrování na základě příspěvku spustí ochranu prohlížeče SameSite, takže pro tyto součásti je SameSite zakázaný.</span><span class="sxs-lookup"><span data-stu-id="65c17-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="65c17-117">Většina přihlášení [OAuth](https://oauth.net/) není ovlivněná kvůli rozdílům ve způsobu, jakým jsou požadavky v toku.</span><span class="sxs-lookup"><span data-stu-id="65c17-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="65c17-118">Každá součást ASP.NET Core, která generuje soubory cookie, musí rozhodnout, zda je SameSite vhodná.</span><span class="sxs-lookup"><span data-stu-id="65c17-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="65c17-119">Ukázkový kód testu SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="65c17-120">Následující ukázky lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="65c17-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="65c17-121">Ukázka</span><span class="sxs-lookup"><span data-stu-id="65c17-121">Sample</span></span>               | <span data-ttu-id="65c17-122">Dokument</span><span class="sxs-lookup"><span data-stu-id="65c17-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="65c17-123">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="65c17-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="65c17-124">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="65c17-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65c17-125">Následující ukázku lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="65c17-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="65c17-126">Ukázka</span><span class="sxs-lookup"><span data-stu-id="65c17-126">Sample</span></span>               | <span data-ttu-id="65c17-127">Dokument</span><span class="sxs-lookup"><span data-stu-id="65c17-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="65c17-128">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="65c17-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="65c17-129">Podpora .NET Core pro atribut sameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="65c17-130">.NET Core 2,2 podporuje pracovní Standard 2019 pro SameSite od vydání aktualizací v prosinci 2019.</span><span class="sxs-lookup"><span data-stu-id="65c17-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="65c17-131">Vývojáři mohou programově řídit hodnotu atributu sameSite pomocí vlastnosti `HttpCookie.SameSite`.</span><span class="sxs-lookup"><span data-stu-id="65c17-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="65c17-132">Nastavením vlastnosti `SameSite` na hodnotu Strict, LAX nebo None dojde k vypsání hodnot v síti pomocí souboru cookie.</span><span class="sxs-lookup"><span data-stu-id="65c17-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="65c17-133">Nastavení rovná se (SameSiteMode) (-1) znamená, že v síti se souborem cookie by neměl být zahrnut žádný atribut sameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="65c17-134">.NET Core 3,0 podporuje aktualizované hodnoty SameSite a přidává další hodnotu výčtu `SameSiteMode.Unspecified` výčtu `SameSiteMode`.</span><span class="sxs-lookup"><span data-stu-id="65c17-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="65c17-135">Tato nová hodnota znamená, že by se soubor cookie neměl odesílat žádná sameSite.</span><span class="sxs-lookup"><span data-stu-id="65c17-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="65c17-136">Prosince změny chování opravy</span><span class="sxs-lookup"><span data-stu-id="65c17-136">December patch behavior changes</span></span>

<span data-ttu-id="65c17-137">Konkrétní změna chování pro .NET Framework a .NET Core 2,1 je způsob, jakým vlastnost `SameSite` interpretuje hodnotu `None`.</span><span class="sxs-lookup"><span data-stu-id="65c17-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="65c17-138">Předtím, než je hodnota `None` určena "negenerovat atribut na All", po opravě to znamená "vygenerovat atribut s hodnotou `None`".</span><span class="sxs-lookup"><span data-stu-id="65c17-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="65c17-139">Po opravě `SameSite` hodnota `(SameSiteMode)(-1)` způsobí, že se atribut neemituje.</span><span class="sxs-lookup"><span data-stu-id="65c17-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="65c17-140">Výchozí hodnota SameSite pro ověřování pomocí formulářů a soubory cookie stavu relace se změnila z `None` na `Lax`.</span><span class="sxs-lookup"><span data-stu-id="65c17-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="65c17-141">Použití rozhraní API s SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-141">API usage with SameSite</span></span>

<span data-ttu-id="65c17-142">[HttpContext. Response. cookies. připojovat](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) výchozí hodnoty `Unspecified`, což znamená, že se do souboru cookie nepřidal žádný atribut SameSite a klient použije výchozí chování (Lax pro nové prohlížeče, žádné pro staré).</span><span class="sxs-lookup"><span data-stu-id="65c17-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="65c17-143">Následující kód ukazuje, jak změnit hodnotu SameSite souboru cookie na `SameSiteMode.Lax`:</span><span class="sxs-lookup"><span data-stu-id="65c17-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="65c17-144">Všechny součásti ASP.NET Core, které generují soubory cookie, potlačí předchozí výchozí nastavení s nastavením vhodným pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="65c17-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="65c17-145">Přepsané předchozí výchozí hodnoty se nezměnily.</span><span class="sxs-lookup"><span data-stu-id="65c17-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="65c17-146">Komponenta</span><span class="sxs-lookup"><span data-stu-id="65c17-146">Component</span></span> | <span data-ttu-id="65c17-147">soubor</span><span class="sxs-lookup"><span data-stu-id="65c17-147">cookie</span></span> | <span data-ttu-id="65c17-148">Výchozí</span><span class="sxs-lookup"><span data-stu-id="65c17-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="65c17-149">SessionOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="65c17-150">CookieTempDataProviderOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="65c17-151">AntiforgeryOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="65c17-152">Ověřování souborem cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="65c17-153">CookieAuthenticationOptions. cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="65c17-154">TwitterOptions.StateCookie</span><span class="sxs-lookup"><span data-stu-id="65c17-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="65c17-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="65c17-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="65c17-156">OpenIdConnectOptions.NonceCookie</span><span class="sxs-lookup"><span data-stu-id="65c17-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="65c17-157">HttpContext. Response. cookies. Append</span><span class="sxs-lookup"><span data-stu-id="65c17-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="65c17-158">ASP.NET Core 3,1 a novějším přináší následující podporu SameSite:</span><span class="sxs-lookup"><span data-stu-id="65c17-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="65c17-159">Předefinuje chování `SameSiteMode.None` k vygenerování `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="65c17-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="65c17-160">Přidá novou hodnotu `SameSiteMode.Unspecified` pro vynechání atributu SameSite.</span><span class="sxs-lookup"><span data-stu-id="65c17-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="65c17-161">Všechna rozhraní API cookies jsou ve výchozím nastavení `Unspecified`.</span><span class="sxs-lookup"><span data-stu-id="65c17-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="65c17-162">Některé součásti, které používají soubory cookie, nastavují hodnoty konkrétnější pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="65c17-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="65c17-163">Příklady najdete v tabulce výše.</span><span class="sxs-lookup"><span data-stu-id="65c17-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="65c17-164">V ASP.NET Core 3,0 a novějších byly změněny výchozí hodnoty SameSite, aby nedocházelo ke konfliktům s nekonzistentními výchozími nastaveními klientů.</span><span class="sxs-lookup"><span data-stu-id="65c17-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="65c17-165">Následující rozhraní API změnila výchozí hodnoty z `SameSiteMode.Lax ` na `-1`, aby nedocházelo k vygenerování atributu SameSite pro tyto soubory cookie:</span><span class="sxs-lookup"><span data-stu-id="65c17-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="65c17-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> použito s [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="65c17-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="65c17-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder> používané jako továrna pro `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="65c17-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="65c17-168">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="65c17-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="65c17-169">Historie a změny</span><span class="sxs-lookup"><span data-stu-id="65c17-169">History and changes</span></span>

<span data-ttu-id="65c17-170">Podpora SameSite byla poprvé implementována v ASP.NET Core v 2,0 pomocí [standardního konceptu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="65c17-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="65c17-171">Standard 2016 byl výslovný.</span><span class="sxs-lookup"><span data-stu-id="65c17-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="65c17-172">ASP.NET Core se rozhodla-in nastavením několika souborů cookie na `Lax` ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="65c17-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="65c17-173">Po zjištění několika [problémů](https://github.com/aspnet/Announcements/issues/318) s ověřováním je většina využití SameSite [zakázaná](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="65c17-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="65c17-174">[Opravy](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) byly vydány v listopadu 2019, aby se aktualizovaly z standardu 2016 na standard 2019.</span><span class="sxs-lookup"><span data-stu-id="65c17-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="65c17-175">[Koncept 2019 specifikace SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="65c17-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="65c17-176">Není **zpětně** kompatibilní s konceptem 2016.</span><span class="sxs-lookup"><span data-stu-id="65c17-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="65c17-177">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="65c17-178">Určuje, že soubory cookie se ve výchozím nastavení považují za `SameSite=Lax`.</span><span class="sxs-lookup"><span data-stu-id="65c17-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="65c17-179">Určuje soubory cookie, které explicitně vyhodnotí `SameSite=None`, aby bylo možné povolit doručování mezi weby, musí být označeno jako `Secure`.</span><span class="sxs-lookup"><span data-stu-id="65c17-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="65c17-180">`None` je nová položka k odhlášení.</span><span class="sxs-lookup"><span data-stu-id="65c17-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="65c17-181">Je podporován opravami vydanými pro ASP.NET Core 2,1, 2,2 a 3,0.</span><span class="sxs-lookup"><span data-stu-id="65c17-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="65c17-182">ASP.NET Core 3,1 má další podporu SameSite.</span><span class="sxs-lookup"><span data-stu-id="65c17-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="65c17-183">Ve výchozím nastavení je naplánovaná podpora [Chrome](https://chromestatus.com/feature/5088147346030592) v [únoru 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="65c17-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="65c17-184">Prohlížeče začaly při přesunu do tohoto standardu v 2019.</span><span class="sxs-lookup"><span data-stu-id="65c17-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="65c17-185">Rozhraní API, která mají vliv na změnu z konceptu Standard 2016 SameSite na 2019 koncept Standard</span><span class="sxs-lookup"><span data-stu-id="65c17-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="65c17-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="65c17-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="65c17-187">CookieOptions.SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="65c17-188">CookieBuilder.SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="65c17-189">CookiePolicyOptions.MinimumSameSitePolicy</span><span class="sxs-lookup"><span data-stu-id="65c17-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="65c17-190">Podpora starších prohlížečů</span><span class="sxs-lookup"><span data-stu-id="65c17-190">Supporting older browsers</span></span>

<span data-ttu-id="65c17-191">SameSite standardně vyhodnocuje, že neznámé hodnoty musí být považovány za `SameSite=Strict` hodnoty. 2016</span><span class="sxs-lookup"><span data-stu-id="65c17-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="65c17-192">Aplikace, ke kterým se přistupoval ze starších prohlížečů, které podporují SameSite úrovně Standard 2016, se můžou přerušit, když získají vlastnost SameSite s hodnotou `None`.</span><span class="sxs-lookup"><span data-stu-id="65c17-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="65c17-193">Webové aplikace musí implementovat detekci prohlížeče, pokud chtějí podporovat starší prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="65c17-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="65c17-194">ASP.NET Core neimplementuje detekci prohlížeče, protože hodnoty uživatelských agentů jsou vysoce nestálé a často se mění.</span><span class="sxs-lookup"><span data-stu-id="65c17-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="65c17-195">Rozšiřovací bod v <xref:Microsoft.AspNetCore.CookiePolicy> umožňuje zapojení do logiky specifické pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="65c17-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="65c17-196">V `Startup.Configure`přidejte kód, který volá <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo *libovolné* metody, která zapisuje soubory cookie:</span><span class="sxs-lookup"><span data-stu-id="65c17-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="65c17-197">V `Startup.ConfigureServices`přidejte kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="65c17-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="65c17-198">V předchozí ukázce `MyUserAgentDetectionLib.DisallowsSameSiteNone` je uživatelem zadaná knihovna, která detekuje, jestli Agent pro uživatele nepodporuje SameSite `None`:</span><span class="sxs-lookup"><span data-stu-id="65c17-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="65c17-199">Následující kód ukazuje ukázkovou `DisallowsSameSiteNone` metodu:</span><span class="sxs-lookup"><span data-stu-id="65c17-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="65c17-200">Následující kód je určen pouze pro ukázku:</span><span class="sxs-lookup"><span data-stu-id="65c17-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="65c17-201">Neměla by být považována za dokončenou.</span><span class="sxs-lookup"><span data-stu-id="65c17-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="65c17-202">Není udržována ani podporována.</span><span class="sxs-lookup"><span data-stu-id="65c17-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="65c17-203">Testování aplikací pro problémy s SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="65c17-204">Aplikace, které komunikují se vzdálenými lokalitami, jako je třeba přihlášení třetí strany, potřebují:</span><span class="sxs-lookup"><span data-stu-id="65c17-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="65c17-205">Otestujte interakci na více prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="65c17-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="65c17-206">Použijte [detekci a zmírnění v prohlížeči CookiePolicy](#sob) popsané v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="65c17-207">Otestujte webové aplikace pomocí verze klienta, která se může přihlásit k novému chování SameSite.</span><span class="sxs-lookup"><span data-stu-id="65c17-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="65c17-208">Pro Chrome, Firefox a chrom Edge mají všechny nové příznaky funkcí pro výslovný souhlas, které lze použít k testování.</span><span class="sxs-lookup"><span data-stu-id="65c17-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="65c17-209">Po použití oprav SameSite se aplikace testuje se staršími verzemi klientů, zejména Safari.</span><span class="sxs-lookup"><span data-stu-id="65c17-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="65c17-210">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="65c17-211">Testování s použitím Chromu</span><span class="sxs-lookup"><span data-stu-id="65c17-211">Test with Chrome</span></span>

<span data-ttu-id="65c17-212">Chrome 78 + poskytuje zavádějící výsledky, protože má na zpracování dočasné omezení.</span><span class="sxs-lookup"><span data-stu-id="65c17-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="65c17-213">Chrome 78 + dočasné zmírnění umožňuje, aby soubory cookie byly starší než dvě minuty.</span><span class="sxs-lookup"><span data-stu-id="65c17-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="65c17-214">Chrome 76 nebo 77 s povolenými vhodnými příznakem testu poskytuje přesnější výsledky.</span><span class="sxs-lookup"><span data-stu-id="65c17-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="65c17-215">Chcete-li otestovat nové chování SameSite, přepněte `chrome://flags/#same-site-by-default-cookies` na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="65c17-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="65c17-216">Starší verze Chrome (75 a novější) jsou hlášeny jako neúspěšné s novým nastavením `None`.</span><span class="sxs-lookup"><span data-stu-id="65c17-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="65c17-217">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="65c17-218">Google nezpřístupňuje starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="65c17-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="65c17-219">Postupujte podle pokynů v části [stažení Chromu](https://www.chromium.org/getting-involved/download-chromium) a otestujte starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="65c17-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="65c17-220">Nestahujte Chrome z odkazů **, které jsou** k dispozici, hledáním ve starších verzích Chrome.</span><span class="sxs-lookup"><span data-stu-id="65c17-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="65c17-221">Chróm 76 Win64</span><span class="sxs-lookup"><span data-stu-id="65c17-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="65c17-222">Chróm 74 Win64</span><span class="sxs-lookup"><span data-stu-id="65c17-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="65c17-223">Počínaje `80.0.3975.0`ou zkušební verze je možné zakázat dočasné zmírnění LAX + POST pro účely testování pomocí nového příznaku `--enable-features=SameSiteDefaultChecksMethodRigorously`, který umožňuje testování lokalit a služeb v konečném stavu funkce, ve které bylo řešení zmírnění odstraněno.</span><span class="sxs-lookup"><span data-stu-id="65c17-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="65c17-224">Další informace najdete v tématu [aktualizace SameSite](https://www.chromium.org/updates/same-site) v projektech Chromu.</span><span class="sxs-lookup"><span data-stu-id="65c17-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="65c17-225">Testování pomocí Safari</span><span class="sxs-lookup"><span data-stu-id="65c17-225">Test with Safari</span></span>

<span data-ttu-id="65c17-226">Prohlížeč Safari 12 striktně implementuje předchozí koncept a v případě, že je nová hodnota `None` v souboru cookie, se nezdařila.</span><span class="sxs-lookup"><span data-stu-id="65c17-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="65c17-227">`None` se vyhnete prostřednictvím kódu detekce prohlížeče, který [podporuje starší prohlížeče](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="65c17-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="65c17-228">Pomocí MSAL, ADAL nebo libovolné knihovny, kterou používáte, otestujte přihlašovací údaje pro WebKit Safari 12, Safari 13 a na bázi.</span><span class="sxs-lookup"><span data-stu-id="65c17-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="65c17-229">Problém závisí na základní verzi operačního systému.</span><span class="sxs-lookup"><span data-stu-id="65c17-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="65c17-230">OSX Mojave (10,14) a iOS 12 se nazývají problémy s kompatibilitou s novým chováním SameSite.</span><span class="sxs-lookup"><span data-stu-id="65c17-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="65c17-231">Upgrade operačního systému na OSX Catalina (10,15) nebo iOS 13 opravuje problém.</span><span class="sxs-lookup"><span data-stu-id="65c17-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="65c17-232">Prohlížeč Safari aktuálně nemá příznak výslovných přihlášení pro testování nového chování specifikace.</span><span class="sxs-lookup"><span data-stu-id="65c17-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="65c17-233">Testování pomocí Firefox</span><span class="sxs-lookup"><span data-stu-id="65c17-233">Test with Firefox</span></span>

<span data-ttu-id="65c17-234">Podporu aplikace Firefox pro nový standard lze testovat na verzi 68 + tím, že na stránce `about:config` `network.cookie.sameSite.laxByDefault`příznak funkce.</span><span class="sxs-lookup"><span data-stu-id="65c17-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="65c17-235">Nebyly zjištěny žádné zprávy o problémech s kompatibilitou se staršími verzemi aplikace Firefox.</span><span class="sxs-lookup"><span data-stu-id="65c17-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="65c17-236">Testování pomocí prohlížeče Edge</span><span class="sxs-lookup"><span data-stu-id="65c17-236">Test with Edge browser</span></span>

<span data-ttu-id="65c17-237">Edge podporuje starý SameSite Standard.</span><span class="sxs-lookup"><span data-stu-id="65c17-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="65c17-238">Edge verze 44 nemá žádné známé problémy s kompatibilitou s novým standardem.</span><span class="sxs-lookup"><span data-stu-id="65c17-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="65c17-239">Test s hranou (chrom)</span><span class="sxs-lookup"><span data-stu-id="65c17-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="65c17-240">Příznaky SameSite jsou nastaveny na stránce `edge://flags/#same-site-by-default-cookies`.</span><span class="sxs-lookup"><span data-stu-id="65c17-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="65c17-241">U Edge Chromu se nezjistily žádné problémy s kompatibilitou.</span><span class="sxs-lookup"><span data-stu-id="65c17-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="65c17-242">Testování s elektronem</span><span class="sxs-lookup"><span data-stu-id="65c17-242">Test with Electron</span></span>

<span data-ttu-id="65c17-243">K verzím elektronů patří starší verze Chromu.</span><span class="sxs-lookup"><span data-stu-id="65c17-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="65c17-244">Například verze elektronicky používané týmy je chrom 66, který vykazuje starší chování.</span><span class="sxs-lookup"><span data-stu-id="65c17-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="65c17-245">Je nutné provést vlastní testování kompatibility s verzí elektronů, kterou váš produkt používá.</span><span class="sxs-lookup"><span data-stu-id="65c17-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="65c17-246">Viz [Podpora starších prohlížečů](#sob) v následující části.</span><span class="sxs-lookup"><span data-stu-id="65c17-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65c17-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="65c17-247">Additional resources</span></span>

* [<span data-ttu-id="65c17-248">Chromový blog: vývojáři: Připravte se na nové SameSite = None; Nastavení zabezpečeného souboru cookie</span><span class="sxs-lookup"><span data-stu-id="65c17-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="65c17-249">Vysvětlení souborů cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="65c17-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="65c17-250">Opravy od listopadu 2019</span><span class="sxs-lookup"><span data-stu-id="65c17-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="65c17-251">Ukázka</span><span class="sxs-lookup"><span data-stu-id="65c17-251">Sample</span></span>               | <span data-ttu-id="65c17-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="65c17-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="65c17-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="65c17-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="65c17-254">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="65c17-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="65c17-255">Ukázka</span><span class="sxs-lookup"><span data-stu-id="65c17-255">Sample</span></span>               | <span data-ttu-id="65c17-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="65c17-256">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="65c17-257">Razor Pages .NET Core</span><span class="sxs-lookup"><span data-stu-id="65c17-257">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
