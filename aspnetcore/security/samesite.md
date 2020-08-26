---
title: Práce s SameSite cookie s v ASP.NET Core
author: rick-anderson
description: Naučte se používat SameSite cookie s v ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 3ba033b4165b19131d11311e5ae9d64e6afe48ca
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865437"
---
# <a name="work-with-samesite-no-loccookies-in-aspnet-core"></a><span data-ttu-id="4bc46-103">Práce s SameSite cookie s v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4bc46-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="4bc46-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bc46-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4bc46-105">SameSite je Konceptový standard [IETF](https://ietf.org/about/) navržený tak, aby poskytoval určitou ochranu proti útokům prostřednictvím CSRF (pro falšování požadavků mezi lokalitami).</span><span class="sxs-lookup"><span data-stu-id="4bc46-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="4bc46-106">Původně koncept v [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)se aktualizoval koncept standardu v [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span><span class="sxs-lookup"><span data-stu-id="4bc46-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="4bc46-107">Aktualizovaný Standard není zpětně kompatibilní s předchozím standardem, přičemž následující je největší znatelné rozdíly:</span><span class="sxs-lookup"><span data-stu-id="4bc46-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="4bc46-108">Cookies bez hlavičky SameSite se považují za `SameSite=Lax` výchozí.</span><span class="sxs-lookup"><span data-stu-id="4bc46-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="4bc46-109">`SameSite=None` se musí použít k povolení použití mezi weby cookie .</span><span class="sxs-lookup"><span data-stu-id="4bc46-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="4bc46-110">Cookieu tohoto kontrolního výrazu `SameSite=None` musí být také označena jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="4bc46-111">U aplikací, které používají, [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) může docházet k problémům s `sameSite=Lax` nebo `sameSite=Strict` cookie s, protože `<iframe>` se považují za scénáře mezi lokalitami.</span><span class="sxs-lookup"><span data-stu-id="4bc46-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="4bc46-112">Hodnota není `SameSite=None` povolena ve [standardu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) a způsobí, že některé implementace považují cookie za `SameSite=Strict` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="4bc46-113">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="4bc46-114">`SameSite=Lax`Nastavení funguje pro většinu aplikací cookie s.</span><span class="sxs-lookup"><span data-stu-id="4bc46-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="4bc46-115">Některé formy ověřování, jako je [OpenID Connect](https://openid.net/connect/) (OIDC) a [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , jako výchozí vystavení přesměrování na základě.</span><span class="sxs-lookup"><span data-stu-id="4bc46-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="4bc46-116">Přesměrování na základě příspěvku spustí ochranu prohlížeče SameSite, takže pro tyto součásti je SameSite zakázaný.</span><span class="sxs-lookup"><span data-stu-id="4bc46-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="4bc46-117">Většina přihlášení [OAuth](https://oauth.net/) není ovlivněná kvůli rozdílům ve způsobu, jakým jsou požadavky v toku.</span><span class="sxs-lookup"><span data-stu-id="4bc46-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="4bc46-118">Každá součást ASP.NET Core, která generuje, cookie musí rozhodnout, jestli je SameSite vhodná.</span><span class="sxs-lookup"><span data-stu-id="4bc46-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-and-no-locidentity"></a><span data-ttu-id="4bc46-119">SameSite a Identity</span><span class="sxs-lookup"><span data-stu-id="4bc46-119">SameSite and Identity</span></span>

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="samesite-test-sample-code"></a><span data-ttu-id="4bc46-120">Ukázkový kód testu SameSite</span><span class="sxs-lookup"><span data-stu-id="4bc46-120">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="4bc46-121">Následující ukázky lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="4bc46-121">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="4bc46-122">Ukázka</span><span class="sxs-lookup"><span data-stu-id="4bc46-122">Sample</span></span>               | <span data-ttu-id="4bc46-123">Dokument</span><span class="sxs-lookup"><span data-stu-id="4bc46-123">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="4bc46-124">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4bc46-124">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="4bc46-125">[Stránky .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="4bc46-125">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bc46-126">Následující ukázku lze stáhnout a otestovat:</span><span class="sxs-lookup"><span data-stu-id="4bc46-126">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="4bc46-127">Ukázka</span><span class="sxs-lookup"><span data-stu-id="4bc46-127">Sample</span></span>               | <span data-ttu-id="4bc46-128">Dokument</span><span class="sxs-lookup"><span data-stu-id="4bc46-128">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bc46-129">[Stránky .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="4bc46-129">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="4bc46-130">Podpora .NET Core pro atribut sameSite</span><span class="sxs-lookup"><span data-stu-id="4bc46-130">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="4bc46-131">.NET Core 2,2 a novější podporují verzi 2019 konceptu pro SameSite, protože vydání aktualizací v prosinci 2019.</span><span class="sxs-lookup"><span data-stu-id="4bc46-131">.NET Core 2.2 and later support the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="4bc46-132">Vývojáři mohou programově řídit hodnotu atributu sameSite pomocí `HttpCookie.SameSite` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="4bc46-132">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="4bc46-133">Nastavením `SameSite` vlastnosti na hodnotu Strict, LAX nebo None dojde k vypsání hodnot v síti pomocí cookie .</span><span class="sxs-lookup"><span data-stu-id="4bc46-133">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="4bc46-134">Nastavení rovná se `(SameSiteMode)(-1)` znamená, že v síti by neměl být součástí žádného atributu sameSite s cookie</span><span class="sxs-lookup"><span data-stu-id="4bc46-134">Setting it equal to `(SameSiteMode)(-1)` indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="4bc46-135">.NET Core 3,0 a novější podporují aktualizované hodnoty SameSite a ke výčtu přidávají další hodnotu výčtu `SameSiteMode.Unspecified` `SameSiteMode` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-135">.NET Core 3.0 and later support the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="4bc46-136">Tato nová hodnota znamená, že sameSite by neměl být odeslán pomocí cookie .</span><span class="sxs-lookup"><span data-stu-id="4bc46-136">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="4bc46-137">Prosince změny chování opravy</span><span class="sxs-lookup"><span data-stu-id="4bc46-137">December patch behavior changes</span></span>

<span data-ttu-id="4bc46-138">Konkrétní změna chování pro .NET Framework a .NET Core 2,1 je způsob, jakým `SameSite` vlastnost interpretuje `None` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-138">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="4bc46-139">Před opravou hodnoty `None` "negenerovat atribut na všech", po opravě to znamená "vygenerovat atribut s hodnotou `None` ".</span><span class="sxs-lookup"><span data-stu-id="4bc46-139">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="4bc46-140">Po opravě `SameSite` hodnoty `(SameSiteMode)(-1)` způsobí, že atribut nebude generován.</span><span class="sxs-lookup"><span data-stu-id="4bc46-140">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="4bc46-141">Výchozí hodnota SameSite pro ověřování pomocí formulářů a stav relace se cookie změnila z `None` na `Lax` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-141">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="4bc46-142">Použití rozhraní API s SameSite</span><span class="sxs-lookup"><span data-stu-id="4bc46-142">API usage with SameSite</span></span>

<span data-ttu-id="4bc46-143">[HttpContext. Response. Cookie s. připojovat](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) výchozí hodnoty `Unspecified` , což znamená, že žádný atribut SameSite není přidaný do cookie a klient použije výchozí chování (Lax pro nové prohlížeče, žádné pro staré).</span><span class="sxs-lookup"><span data-stu-id="4bc46-143">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="4bc46-144">Následující kód ukazuje, jak změnit cookie hodnotu SameSite na `SameSiteMode.Lax` :</span><span class="sxs-lookup"><span data-stu-id="4bc46-144">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4bc46-145">Všechny součásti ASP.NET Core, které emitují, cookie přepíší předchozí výchozí nastavení s nastavením vhodným pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="4bc46-145">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="4bc46-146">Přepsané předchozí výchozí hodnoty se nezměnily.</span><span class="sxs-lookup"><span data-stu-id="4bc46-146">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="4bc46-147">Součást</span><span class="sxs-lookup"><span data-stu-id="4bc46-147">Component</span></span> | cookie | <span data-ttu-id="4bc46-148">Výchozí</span><span class="sxs-lookup"><span data-stu-id="4bc46-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | <span data-ttu-id="4bc46-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="4bc46-149">[SessionOptions.Cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie)</span></span> |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | <span data-ttu-id="4bc46-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="4bc46-150">[CookieTempDataProviderOptions.Cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie)</span></span> | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | <span data-ttu-id="4bc46-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span><span class="sxs-lookup"><span data-stu-id="4bc46-151">[AntiforgeryOptions.Cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)</span></span>| `Strict` |
| <span data-ttu-id="4bc46-152">[Cookie Přihlašovací](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span><span class="sxs-lookup"><span data-stu-id="4bc46-152">[Cookie Authentication](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*)</span></span> | <span data-ttu-id="4bc46-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span><span class="sxs-lookup"><span data-stu-id="4bc46-153">[CookieAuthenticationOptions.Cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName)</span></span> | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | <span data-ttu-id="4bc46-154">[TwitterOptions. State Cookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span><span class="sxs-lookup"><span data-stu-id="4bc46-154">[TwitterOptions.StateCookie ](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie)</span></span> | `Lax`  |
| <span data-ttu-id="4bc46-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="4bc46-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | <span data-ttu-id="4bc46-156">[OpenIdConnectOptions. nonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span><span class="sxs-lookup"><span data-stu-id="4bc46-156">[OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)</span></span>| `None` |
| <span data-ttu-id="4bc46-157">[HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="4bc46-157">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span> | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="4bc46-158">ASP.NET Core 3,1 a novějším přináší následující podporu SameSite:</span><span class="sxs-lookup"><span data-stu-id="4bc46-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="4bc46-159">Předefinuje chování `SameSiteMode.None` pro vygenerování. `SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="4bc46-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="4bc46-160">Přidá novou hodnotu `SameSiteMode.Unspecified` pro vynechání atributu SameSite.</span><span class="sxs-lookup"><span data-stu-id="4bc46-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="4bc46-161">Všechna cookie rozhraní API s výchozím nastavením na `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="4bc46-162">Některé komponenty, které používají cookie hodnotu s, jsou specifické pro jejich scénáře.</span><span class="sxs-lookup"><span data-stu-id="4bc46-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="4bc46-163">Příklady najdete v tabulce výše.</span><span class="sxs-lookup"><span data-stu-id="4bc46-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bc46-164">V ASP.NET Core 3,0 a novějších byly změněny výchozí hodnoty SameSite, aby nedocházelo ke konfliktům s nekonzistentními výchozími nastaveními klientů.</span><span class="sxs-lookup"><span data-stu-id="4bc46-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="4bc46-165">Následující rozhraní API změnila výchozí hodnoty z `SameSiteMode.Lax ` na na, `-1` aby nedocházelo k vygenerování atributu SameSite pro tyto cookie prvky:</span><span class="sxs-lookup"><span data-stu-id="4bc46-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="4bc46-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> používá se s [HttpContext. Response. Cookie s. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span><span class="sxs-lookup"><span data-stu-id="4bc46-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="4bc46-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  používá se jako továrna pro `CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="4bc46-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* <span data-ttu-id="4bc46-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="4bc46-168">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="4bc46-169">Historie a změny</span><span class="sxs-lookup"><span data-stu-id="4bc46-169">History and changes</span></span>

<span data-ttu-id="4bc46-170">Podpora SameSite byla poprvé implementována v ASP.NET Core v 2,0 pomocí [standardního konceptu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span><span class="sxs-lookup"><span data-stu-id="4bc46-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="4bc46-171">Standard 2016 byl výslovný.</span><span class="sxs-lookup"><span data-stu-id="4bc46-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="4bc46-172">ASP.NET Core se pro nastavení nesouhlasí nastavením několika cookie s na `Lax` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="4bc46-173">Po zjištění několika [problémů](https://github.com/aspnet/Announcements/issues/318) s ověřováním je většina využití SameSite [zakázaná](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="4bc46-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="4bc46-174">[Opravy](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) byly vydány v listopadu 2019, aby se aktualizovaly z standardu 2016 na standard 2019.</span><span class="sxs-lookup"><span data-stu-id="4bc46-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="4bc46-175">[Koncept 2019 specifikace SameSite](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="4bc46-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="4bc46-176">Není **zpětně** kompatibilní s konceptem 2016.</span><span class="sxs-lookup"><span data-stu-id="4bc46-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="4bc46-177">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="4bc46-178">Určuje cookie , že s se `SameSite=Lax` ve výchozím nastavení považují.</span><span class="sxs-lookup"><span data-stu-id="4bc46-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="4bc46-179">Určuje cookie , že explicitní vyhodnocení, aby `SameSite=None` bylo možné povolit doručování mezi weby, musí být označeno jako `Secure` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="4bc46-180">`None` je nová položka, která se má odhlásit.</span><span class="sxs-lookup"><span data-stu-id="4bc46-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="4bc46-181">Je podporován opravami vydanými pro ASP.NET Core 2,1, 2,2 a 3,0.</span><span class="sxs-lookup"><span data-stu-id="4bc46-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="4bc46-182">ASP.NET Core 3,1 má další podporu SameSite.</span><span class="sxs-lookup"><span data-stu-id="4bc46-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="4bc46-183">Ve výchozím nastavení je naplánovaná podpora [Chrome](https://chromestatus.com/feature/5088147346030592) v [únoru 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span><span class="sxs-lookup"><span data-stu-id="4bc46-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="4bc46-184">Prohlížeče začaly při přesunu do tohoto standardu v 2019.</span><span class="sxs-lookup"><span data-stu-id="4bc46-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="4bc46-185">Rozhraní API, která mají vliv na změnu z konceptu Standard 2016 SameSite na 2019 koncept Standard</span><span class="sxs-lookup"><span data-stu-id="4bc46-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="4bc46-186">Http. SameSiteMode</span><span class="sxs-lookup"><span data-stu-id="4bc46-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* <span data-ttu-id="4bc46-187">[CookieOptions. SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span><span class="sxs-lookup"><span data-stu-id="4bc46-187">[CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)</span></span>
* <span data-ttu-id="4bc46-188">[CookieTvůrce. SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span><span class="sxs-lookup"><span data-stu-id="4bc46-188">[CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)</span></span>
* <span data-ttu-id="4bc46-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span><span class="sxs-lookup"><span data-stu-id="4bc46-189">[CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)</span></span>
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="4bc46-190">Podpora starších prohlížečů</span><span class="sxs-lookup"><span data-stu-id="4bc46-190">Supporting older browsers</span></span>

<span data-ttu-id="4bc46-191">SameSite standardně vyhodnocuje, že neznámé hodnoty musí být považovány za `SameSite=Strict` hodnoty. 2016</span><span class="sxs-lookup"><span data-stu-id="4bc46-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="4bc46-192">Aplikace, ke kterým se přistupoval ze starších prohlížečů, které podporují SameSite Standard 2016, se můžou přerušit, když získají vlastnost SameSite s hodnotou `None` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="4bc46-193">Webové aplikace musí implementovat detekci prohlížeče, pokud chtějí podporovat starší prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4bc46-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="4bc46-194">ASP.NET Core neimplementuje detekci prohlížeče, protože hodnoty uživatelských agentů jsou vysoce nestálé a často se mění.</span><span class="sxs-lookup"><span data-stu-id="4bc46-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="4bc46-195">Bod rozšíření v v nástroji <xref:Microsoft.AspNetCore.CookiePolicy> umožňuje zapojení do logiky specifické pro uživatele v agentovi.</span><span class="sxs-lookup"><span data-stu-id="4bc46-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="4bc46-196">V `Startup.Configure` přidejte kód, který volá <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo *jakoukoliv* metodu, která zapisuje cookie s:</span><span class="sxs-lookup"><span data-stu-id="4bc46-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="4bc46-197">V `Startup.ConfigureServices` přidejte kód podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="4bc46-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="4bc46-198">V předchozím příkladu `MyUserAgentDetectionLib.DisallowsSameSiteNone` je uživatelem zadaná knihovna, která detekuje, jestli uživatelský agent nepodporuje SameSite `None` :</span><span class="sxs-lookup"><span data-stu-id="4bc46-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="4bc46-199">Následující kód ukazuje ukázkovou `DisallowsSameSiteNone` metodu:</span><span class="sxs-lookup"><span data-stu-id="4bc46-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="4bc46-200">Následující kód je určen pouze pro ukázku:</span><span class="sxs-lookup"><span data-stu-id="4bc46-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="4bc46-201">Neměla by být považována za dokončenou.</span><span class="sxs-lookup"><span data-stu-id="4bc46-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="4bc46-202">Není udržována ani podporována.</span><span class="sxs-lookup"><span data-stu-id="4bc46-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="4bc46-203">Testování aplikací pro problémy s SameSite</span><span class="sxs-lookup"><span data-stu-id="4bc46-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="4bc46-204">Aplikace, které komunikují se vzdálenými lokalitami, jako je třeba přihlášení třetí strany, potřebují:</span><span class="sxs-lookup"><span data-stu-id="4bc46-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="4bc46-205">Otestujte interakci na více prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="4bc46-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="4bc46-206">Použijte [ Cookie zjišťování a zmírnění v prohlížeči zásad](#sob) , které jsou popsány v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="4bc46-207">Otestujte webové aplikace pomocí verze klienta, která se může přihlásit k novému chování SameSite.</span><span class="sxs-lookup"><span data-stu-id="4bc46-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="4bc46-208">Pro Chrome, Firefox a chrom Edge mají všechny nové příznaky funkcí pro výslovný souhlas, které lze použít k testování.</span><span class="sxs-lookup"><span data-stu-id="4bc46-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="4bc46-209">Po použití oprav SameSite se aplikace testuje se staršími verzemi klientů, zejména Safari.</span><span class="sxs-lookup"><span data-stu-id="4bc46-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="4bc46-210">Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="4bc46-211">Testování s použitím Chromu</span><span class="sxs-lookup"><span data-stu-id="4bc46-211">Test with Chrome</span></span>

<span data-ttu-id="4bc46-212">Chrome 78 + poskytuje zavádějící výsledky, protože má na zpracování dočasné omezení.</span><span class="sxs-lookup"><span data-stu-id="4bc46-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="4bc46-213">Rozhraní Chrome 78 + dočasné zmírnění rizik umožňuje cookie více než dvě minuty staré.</span><span class="sxs-lookup"><span data-stu-id="4bc46-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="4bc46-214">Chrome 76 nebo 77 s povolenými vhodnými příznakem testu poskytuje přesnější výsledky.</span><span class="sxs-lookup"><span data-stu-id="4bc46-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="4bc46-215">Chcete-li otestovat nové chování SameSite, přepněte `chrome://flags/#same-site-by-default-cookies` na **povoleno**.</span><span class="sxs-lookup"><span data-stu-id="4bc46-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="4bc46-216">Starší verze Chrome (75 a novější) se hlásí jako neúspěšné s novým `None` nastavením.</span><span class="sxs-lookup"><span data-stu-id="4bc46-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="4bc46-217">Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="4bc46-218">Google nezpřístupňuje starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="4bc46-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="4bc46-219">Postupujte podle pokynů v části [stažení Chromu](https://www.chromium.org/getting-involved/download-chromium) a otestujte starší verze Chrome.</span><span class="sxs-lookup"><span data-stu-id="4bc46-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="4bc46-220">Nestahujte Chrome z odkazů **, které jsou** k dispozici, hledáním ve starších verzích Chrome.</span><span class="sxs-lookup"><span data-stu-id="4bc46-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="4bc46-221">Chróm 76 Win64</span><span class="sxs-lookup"><span data-stu-id="4bc46-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="4bc46-222">Chróm 74 Win64</span><span class="sxs-lookup"><span data-stu-id="4bc46-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="4bc46-223">Počínaje verzí na Kanárských verzích je `80.0.3975.0` dočasné zmírnění LAX + post možné zakázat pro účely testování pomocí nového příznaku, `--enable-features=SameSiteDefaultChecksMethodRigorously` který umožňuje testování lokalit a služeb v konečném stavu funkce, ve které byla zmírnění odstraněna.</span><span class="sxs-lookup"><span data-stu-id="4bc46-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="4bc46-224">Další informace najdete v tématu [aktualizace SameSite](https://www.chromium.org/updates/same-site) v projektech Chromu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="4bc46-225">Testování pomocí Safari</span><span class="sxs-lookup"><span data-stu-id="4bc46-225">Test with Safari</span></span>

<span data-ttu-id="4bc46-226">Prohlížeč Safari 12 striktně implementuje předchozí koncept a v případě, že je nová hodnota v, dojde k chybě `None` cookie .</span><span class="sxs-lookup"><span data-stu-id="4bc46-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="4bc46-227">`None` se vyhněte prostřednictvím kódu detekce prohlížeče, který [podporuje starší prohlížeče](#sob) v tomto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="4bc46-228">Pomocí MSAL, ADAL nebo libovolné knihovny, kterou používáte, otestujte přihlašovací údaje pro WebKit Safari 12, Safari 13 a na bázi.</span><span class="sxs-lookup"><span data-stu-id="4bc46-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="4bc46-229">Problém závisí na základní verzi operačního systému.</span><span class="sxs-lookup"><span data-stu-id="4bc46-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="4bc46-230">OSX Mojave (10,14) a iOS 12 se nazývají problémy s kompatibilitou s novým chováním SameSite.</span><span class="sxs-lookup"><span data-stu-id="4bc46-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="4bc46-231">Upgrade operačního systému na OSX Catalina (10,15) nebo iOS 13 opravuje problém.</span><span class="sxs-lookup"><span data-stu-id="4bc46-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="4bc46-232">Prohlížeč Safari aktuálně nemá příznak výslovných přihlášení pro testování nového chování specifikace.</span><span class="sxs-lookup"><span data-stu-id="4bc46-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="4bc46-233">Testování pomocí Firefox</span><span class="sxs-lookup"><span data-stu-id="4bc46-233">Test with Firefox</span></span>

<span data-ttu-id="4bc46-234">Podporu aplikace Firefox pro nový standard lze testovat na verzi 68 + tím, že na stránce zapnete `about:config` příznak funkce `network.cookie.sameSite.laxByDefault` .</span><span class="sxs-lookup"><span data-stu-id="4bc46-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="4bc46-235">Nebyly zjištěny žádné zprávy o problémech s kompatibilitou se staršími verzemi aplikace Firefox.</span><span class="sxs-lookup"><span data-stu-id="4bc46-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="4bc46-236">Testování pomocí prohlížeče Edge</span><span class="sxs-lookup"><span data-stu-id="4bc46-236">Test with Edge browser</span></span>

<span data-ttu-id="4bc46-237">Edge podporuje starý SameSite Standard.</span><span class="sxs-lookup"><span data-stu-id="4bc46-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="4bc46-238">Edge verze 44 nemá žádné známé problémy s kompatibilitou s novým standardem.</span><span class="sxs-lookup"><span data-stu-id="4bc46-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="4bc46-239">Test s hranou (chrom)</span><span class="sxs-lookup"><span data-stu-id="4bc46-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="4bc46-240">Příznaky SameSite jsou nastaveny na `edge://flags/#same-site-by-default-cookies` stránce.</span><span class="sxs-lookup"><span data-stu-id="4bc46-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="4bc46-241">U Edge Chromu se nezjistily žádné problémy s kompatibilitou.</span><span class="sxs-lookup"><span data-stu-id="4bc46-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-no-locelectron"></a><span data-ttu-id="4bc46-242">Testovat s Electron</span><span class="sxs-lookup"><span data-stu-id="4bc46-242">Test with Electron</span></span>

<span data-ttu-id="4bc46-243">Verze sady Electron obsahují starší verze Chromu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="4bc46-244">Například verze Electron používaná týmy je chrom 66, který vykazuje starší chování.</span><span class="sxs-lookup"><span data-stu-id="4bc46-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="4bc46-245">Je nutné provést vlastní testování kompatibility s Electron použitím verze produktu.</span><span class="sxs-lookup"><span data-stu-id="4bc46-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="4bc46-246">Viz [Podpora starších prohlížečů](#sob) v následující části.</span><span class="sxs-lookup"><span data-stu-id="4bc46-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4bc46-247">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4bc46-247">Additional resources</span></span>

* [<span data-ttu-id="4bc46-248">Chromový blog: vývojáři: Připravte se na nové SameSite = None; Cookie Nastavení zabezpečení</span><span class="sxs-lookup"><span data-stu-id="4bc46-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* <span data-ttu-id="4bc46-249">[SameSite cookie s – vysvětlení](https://web.dev/samesite-cookies-explained/)</span><span class="sxs-lookup"><span data-stu-id="4bc46-249">[SameSite cookies explained](https://web.dev/samesite-cookies-explained/)</span></span>
* [<span data-ttu-id="4bc46-250">Opravy od listopadu 2019</span><span class="sxs-lookup"><span data-stu-id="4bc46-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="4bc46-251">Ukázka</span><span class="sxs-lookup"><span data-stu-id="4bc46-251">Sample</span></span>               | <span data-ttu-id="4bc46-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="4bc46-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="4bc46-253">.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="4bc46-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| <span data-ttu-id="4bc46-254">[Stránky .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span><span class="sxs-lookup"><span data-stu-id="4bc46-254">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)</span></span>  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="4bc46-255">Ukázka</span><span class="sxs-lookup"><span data-stu-id="4bc46-255">Sample</span></span>               | <span data-ttu-id="4bc46-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="4bc46-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="4bc46-257">[Stránky .NET Core Razor](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="4bc46-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
