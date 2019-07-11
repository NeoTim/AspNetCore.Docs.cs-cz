---
title: Zabránění útokům na otevřeném přesměrování v ASP.NET Core
author: ardalis
description: Ukazuje, jak zabránit v otevřeném přesměrování útoků, které aplikace ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815490"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="4dd81-103">Zabránění útokům na otevřeném přesměrování v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4dd81-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="4dd81-104">Webové aplikace, který přesměruje na adresu URL, která je zadáno pomocí požadavku, například data řetězce dotazu nebo formuláře může být potenciálně úmyslně přesměrovat uživatele na škodlivé, externí adresu URL.</span><span class="sxs-lookup"><span data-stu-id="4dd81-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="4dd81-105">Toto úmyslné poškozování, se nazývá otevřené přesměrování útoku.</span><span class="sxs-lookup"><span data-stu-id="4dd81-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="4dd81-106">Pokaždé, když se vaše aplikace logiky se přesměruje na zadané adresy URL, je nutné ověřit, že adresa URL pro přesměrování bylo neoprávněně manipulováno.</span><span class="sxs-lookup"><span data-stu-id="4dd81-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="4dd81-107">ASP.NET Core má integrované funkce k ochraně aplikace před útoky na otevřeném přesměrování (označované také jako otevřené přesměrování).</span><span class="sxs-lookup"><span data-stu-id="4dd81-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="4dd81-108">Co je útok otevřeném přesměrování?</span><span class="sxs-lookup"><span data-stu-id="4dd81-108">What is an open redirect attack?</span></span>

<span data-ttu-id="4dd81-109">Webové aplikace často přesměrovat uživatele na přihlašovací stránku, když přistupují k prostředkům, které vyžadují ověřování.</span><span class="sxs-lookup"><span data-stu-id="4dd81-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="4dd81-110">Obvykle zahrnuje přesměrování `returnUrl` parametr řetězce dotazu tak, aby uživatel může být vrácen na původně požadovanou URL adresu po jejich úspěšném přihlášení.</span><span class="sxs-lookup"><span data-stu-id="4dd81-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="4dd81-111">Poté, co se uživatel ověřuje, jsou přesměrováni na adresu URL bylo původně požadovali.</span><span class="sxs-lookup"><span data-stu-id="4dd81-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="4dd81-112">Protože cílová adresa URL je uveden v řetězci dotazu požadavku, uživateli se zlými úmysly může manipulovat s řetězec dotazu.</span><span class="sxs-lookup"><span data-stu-id="4dd81-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="4dd81-113">Zmanipulovanou řetězce dotazu může umožnit, aby přesměruje uživatele na škodlivý web externí web.</span><span class="sxs-lookup"><span data-stu-id="4dd81-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="4dd81-114">Tato technika se nazývá útoku otevřené přesměrování (nebo přesměrování).</span><span class="sxs-lookup"><span data-stu-id="4dd81-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="4dd81-115">Příklad útoku</span><span class="sxs-lookup"><span data-stu-id="4dd81-115">An example attack</span></span>

<span data-ttu-id="4dd81-116">Uživatel se zlými úmysly můžete vyvíjet útoku určený k tomu, uživatel se zlými úmysly přístup k přihlašovacím údajům uživatele nebo citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4dd81-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="4dd81-117">Zahájit útok, uživateli se zlými úmysly convinces uživatel kliknutím na odkaz na přihlašovací stránku vašeho webu pomocí `returnUrl` přidána k adrese URL hodnotu řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="4dd81-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="4dd81-118">Zvažte například aplikaci na `contoso.com` , který obsahuje přihlašovací stránku na `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="4dd81-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="4dd81-119">Útok zahrnuje následující kroky:</span><span class="sxs-lookup"><span data-stu-id="4dd81-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="4dd81-120">Uživatel klikne škodlivý odkaz `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (druhý adresa URL je "contoso**1**.com", ne "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="4dd81-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="4dd81-121">Přihlášení uživatele úspěšně.</span><span class="sxs-lookup"><span data-stu-id="4dd81-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="4dd81-122">Uživatel je přesměrován (lokalitou) na `http://contoso1.com/Account/LogOn` (škodlivým webům, který může vypadat přesně skutečné stránky).</span><span class="sxs-lookup"><span data-stu-id="4dd81-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="4dd81-123">Uživatel znovu přihlásí (poskytuje škodlivý web přihlašovacích údajů) a je přesměrován zpět na skutečné stránky.</span><span class="sxs-lookup"><span data-stu-id="4dd81-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="4dd81-124">Uživatel pravděpodobně domnívá, že jejich první pokus o přihlášení se nezdařilo a jestli jejich druhý pokus se úspěšně dokončila.</span><span class="sxs-lookup"><span data-stu-id="4dd81-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="4dd81-125">Uživatel zůstane pravděpodobně vědět, že jsou dojde k ohrožení bezpečnosti přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="4dd81-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Proces útoku otevřené přesměrování](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="4dd81-127">Kromě stránek přihlášení některé weby poskytují stránek přesměrování nebo koncové body.</span><span class="sxs-lookup"><span data-stu-id="4dd81-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="4dd81-128">Představte si vaše aplikace obsahuje stránku s otevřeném přesměrování `/Home/Redirect`.</span><span class="sxs-lookup"><span data-stu-id="4dd81-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="4dd81-129">Útočník by mohl vytvořit, například pomocí odkazu v e-mailu, který směřuje na `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="4dd81-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="4dd81-130">Běžný uživatel se podívá na adresu URL a vidět, že začíná názvem vaší lokality.</span><span class="sxs-lookup"><span data-stu-id="4dd81-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="4dd81-131">Které důvěřující, bude kliknou na odkaz.</span><span class="sxs-lookup"><span data-stu-id="4dd81-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="4dd81-132">Otevřeném přesměrování by pak uživatele poslat na lokalitě útoky phishing, která vypadá identické té vaší, a uživatel by pravděpodobně přihlášení budou věřit, je váš web.</span><span class="sxs-lookup"><span data-stu-id="4dd81-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="4dd81-133">Ochrana před útoky na otevřeném přesměrování</span><span class="sxs-lookup"><span data-stu-id="4dd81-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="4dd81-134">Při vývoji webových aplikací, zpracovávat všechny uživatelsky zadaných dat jako nedůvěryhodné.</span><span class="sxs-lookup"><span data-stu-id="4dd81-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="4dd81-135">Pokud má vaše aplikace funkcí, které přesměruje uživatele na základě obsahu adresy URL, ujistěte se, že takové přesměrování jsou pouze dělat místně v rámci vaší aplikace (nebo známou adresu URL, nikoliv adresu URL, která může být zadána v řetězec dotazu).</span><span class="sxs-lookup"><span data-stu-id="4dd81-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="4dd81-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="4dd81-136">LocalRedirect</span></span>

<span data-ttu-id="4dd81-137">Použití `LocalRedirect` pomocnou metodu v základní třídě `Controller` třídy:</span><span class="sxs-lookup"><span data-stu-id="4dd81-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="4dd81-138">`LocalRedirect` vyvolá výjimku, pokud je zadána adresa URL není místní.</span><span class="sxs-lookup"><span data-stu-id="4dd81-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="4dd81-139">V opačném případě se chová stejně jako `Redirect` metody.</span><span class="sxs-lookup"><span data-stu-id="4dd81-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="4dd81-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="4dd81-140">IsLocalUrl</span></span>

<span data-ttu-id="4dd81-141">Použití [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) metoda test adresy URL před přesměrování:</span><span class="sxs-lookup"><span data-stu-id="4dd81-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="4dd81-142">Následující příklad ukazuje, jak zjistit, jestli je adresa URL místní před přesměrování.</span><span class="sxs-lookup"><span data-stu-id="4dd81-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

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

<span data-ttu-id="4dd81-143">`IsLocalUrl` Metoda chrání uživatele od nedopatřením se přesměrovává na škodlivý web.</span><span class="sxs-lookup"><span data-stu-id="4dd81-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="4dd81-144">Přihlaste se na podrobnosti o adresu URL, která byla k dispozici, pokud jiné než místní adresa URL je zadáno v situaci, kde byl očekáván místní adresu URL.</span><span class="sxs-lookup"><span data-stu-id="4dd81-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="4dd81-145">Protokolování přesměrování adresy URL může pomoci při diagnostice přesměrování útoky.</span><span class="sxs-lookup"><span data-stu-id="4dd81-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
