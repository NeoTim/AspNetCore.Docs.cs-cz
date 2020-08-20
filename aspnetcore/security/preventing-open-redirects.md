---
title: Zabránit útokům v otevřeném přesměrování v ASP.NET Core
author: ardalis
description: Ukazuje, jak zabránit otevírání útoků přesměrování proti ASP.NET Core aplikaci.
ms.author: riande
ms.date: 07/07/2017
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
uid: security/preventing-open-redirects
ms.openlocfilehash: 5226e301960a56145b94b6128d0034c40b86bffd
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633458"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="10a03-103">Zabránit útokům v otevřeném přesměrování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10a03-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="10a03-104">Webová aplikace, která přesměrovává na adresu URL zadanou prostřednictvím žádosti, jako je například dotaz nebo data formuláře, může být úmyslně poškozena pro přesměrování uživatelů na externí škodlivou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="10a03-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="10a03-105">Tato manipulace se nazývá útok typu otevřené přesměrování.</span><span class="sxs-lookup"><span data-stu-id="10a03-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="10a03-106">Vždy, když vaše logika aplikace přesměrovává na zadanou adresu URL, je nutné ověřit, že adresa URL pro přesměrování nebyla úmyslně poškozena.</span><span class="sxs-lookup"><span data-stu-id="10a03-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="10a03-107">ASP.NET Core má integrovanou funkcionalitu, která vám může pomáhat chránit aplikace před otevřeným přesměrováním (označuje se také jako otevřené přesměrování).</span><span class="sxs-lookup"><span data-stu-id="10a03-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="10a03-108">Co je otevřený útok na přesměrování?</span><span class="sxs-lookup"><span data-stu-id="10a03-108">What is an open redirect attack?</span></span>

<span data-ttu-id="10a03-109">Webové aplikace často přesměrují uživatele na přihlašovací stránku, když přistupují k prostředkům, které vyžadují ověření.</span><span class="sxs-lookup"><span data-stu-id="10a03-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="10a03-110">Přesměrování obvykle obsahuje `returnUrl` parametr QueryString, aby se uživatel mohl vrátit na původně požadovanou adresu URL po úspěšném přihlášení.</span><span class="sxs-lookup"><span data-stu-id="10a03-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="10a03-111">Po ověření se uživatel přesměruje na adresu URL, kterou si původně požadoval.</span><span class="sxs-lookup"><span data-stu-id="10a03-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="10a03-112">Vzhledem k tomu, že cílová adresa URL je zadána v řetězci QueryString žádosti, může uživatel se zlými úmysly manipulovat s řetězcem dotazu.</span><span class="sxs-lookup"><span data-stu-id="10a03-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="10a03-113">Neoprávněný řetězec QueryString může webovému serveru přesměrovat uživatele na externí, škodlivý web.</span><span class="sxs-lookup"><span data-stu-id="10a03-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="10a03-114">Tato technika se nazývá otevřené útoky přesměrování (nebo přesměrování).</span><span class="sxs-lookup"><span data-stu-id="10a03-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="10a03-115">Ukázkový útok</span><span class="sxs-lookup"><span data-stu-id="10a03-115">An example attack</span></span>

<span data-ttu-id="10a03-116">Uživatel se zlými úmysly může vyvíjet útok s cílem umožnit uživateli se zlými úmysly přístup k přihlašovacím údajům uživatele nebo citlivým informacím.</span><span class="sxs-lookup"><span data-stu-id="10a03-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="10a03-117">K zahájení útoku uživatel se zlými úmysly přesvědčit uživatele, aby po kliknutí na odkaz na přihlašovací stránku vaší lokality s `returnUrl` hodnotou QueryString přidané do adresy URL.</span><span class="sxs-lookup"><span data-stu-id="10a03-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="10a03-118">Představte si například aplikaci `contoso.com` , která obsahuje přihlašovací stránku na adrese `http://contoso.com/Account/LogOn?returnUrl=/Home/About` .</span><span class="sxs-lookup"><span data-stu-id="10a03-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="10a03-119">Útok se řídí těmito kroky:</span><span class="sxs-lookup"><span data-stu-id="10a03-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="10a03-120">Uživatel klikne na škodlivý odkaz na `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (druhá adresa URL je contoso**1**. com ", nikoli" contoso.com ").</span><span class="sxs-lookup"><span data-stu-id="10a03-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="10a03-121">Uživatel se úspěšně přihlásil.</span><span class="sxs-lookup"><span data-stu-id="10a03-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="10a03-122">Uživatel se přesměruje (na webu) na `http://contoso1.com/Account/LogOn` (škodlivá lokalita, která vypadá přesně jako skutečný Web).</span><span class="sxs-lookup"><span data-stu-id="10a03-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="10a03-123">Uživatel se znovu přihlásí (uvede přihlašovací údaje pro škodlivý web) a bude přesměrován zpět na skutečný Web.</span><span class="sxs-lookup"><span data-stu-id="10a03-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="10a03-124">Uživatel se nejspíš domnívá, že se první pokus o přihlášení nezdařil a že jejich druhý pokus je úspěšný.</span><span class="sxs-lookup"><span data-stu-id="10a03-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="10a03-125">Uživatel pravděpodobně stále neví, že jejich přihlašovací údaje jsou ohroženy.</span><span class="sxs-lookup"><span data-stu-id="10a03-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Otevřít proces útoku přesměrování](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="10a03-127">Kromě přihlašovacích stránek poskytují některé lokality stránky přesměrování nebo koncové body.</span><span class="sxs-lookup"><span data-stu-id="10a03-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="10a03-128">Představte si, že vaše aplikace má stránku s otevřeným přesměrováním `/Home/Redirect` .</span><span class="sxs-lookup"><span data-stu-id="10a03-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="10a03-129">Útočník by mohl vytvořit například odkaz v e-mailu, který odkazuje na `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login` .</span><span class="sxs-lookup"><span data-stu-id="10a03-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="10a03-130">Typický uživatel se podívá na adresu URL a zjistí, že začíná názvem vaší lokality.</span><span class="sxs-lookup"><span data-stu-id="10a03-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="10a03-131">Důvěřujete to tím, že kliknete na odkaz.</span><span class="sxs-lookup"><span data-stu-id="10a03-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="10a03-132">Otevřené přesměrování by pak poslalo uživateli na web útoku phishing, který vypadá stejně jako váš a uživatel se pravděpodobně přihlásí k tomu, co se domníváte, že se jedná o Web.</span><span class="sxs-lookup"><span data-stu-id="10a03-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="10a03-133">Ochrana před útoky typu otevřené přesměrování</span><span class="sxs-lookup"><span data-stu-id="10a03-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="10a03-134">Při vývoji webových aplikací zachází všechna uživatelsky zadaná data jako nedůvěryhodná.</span><span class="sxs-lookup"><span data-stu-id="10a03-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="10a03-135">Pokud má vaše aplikace funkcionalitu, která přesměruje uživatele na základě obsahu adresy URL, zajistěte, aby se tyto přesměrování prováděly pouze místně v rámci aplikace (nebo na známou adresu URL, nikoli na adresu URL, která může být zadána v řetězci QueryString).</span><span class="sxs-lookup"><span data-stu-id="10a03-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="10a03-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="10a03-136">LocalRedirect</span></span>

<span data-ttu-id="10a03-137">Použijte `LocalRedirect` pomocnou metodu ze základní `Controller` třídy:</span><span class="sxs-lookup"><span data-stu-id="10a03-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="10a03-138">`LocalRedirect` vyvolá výjimku, pokud je zadána adresa URL, která není místní.</span><span class="sxs-lookup"><span data-stu-id="10a03-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="10a03-139">V opačném případě se chová stejně jako `Redirect` metoda.</span><span class="sxs-lookup"><span data-stu-id="10a03-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="10a03-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="10a03-140">IsLocalUrl</span></span>

<span data-ttu-id="10a03-141">Před přesměrováním otestujte adresy URL pomocí metody [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) :</span><span class="sxs-lookup"><span data-stu-id="10a03-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="10a03-142">Následující příklad ukazuje, jak ověřit, zda je adresa URL místní před přesměrováním.</span><span class="sxs-lookup"><span data-stu-id="10a03-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="10a03-143">`IsLocalUrl`Metoda chrání uživatele před neúmyslným přesměrováním na škodlivý web.</span><span class="sxs-lookup"><span data-stu-id="10a03-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="10a03-144">V případě, že jste očekávali místní adresu URL, můžete protokolovat podrobnosti adresy URL, které byly poskytnuty při zadání jiné než místní adresy URL.</span><span class="sxs-lookup"><span data-stu-id="10a03-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="10a03-145">Protokolování adres URL pro přesměrování může pomáhat při diagnostice útoků přesměrování.</span><span class="sxs-lookup"><span data-stu-id="10a03-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
