---
title: Zabránění útokům na otevřeném přesměrování v ASP.NET Core
author: ardalis
description: Ukazuje, jak zabránit v otevřeném přesměrování útoků, které aplikace ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 0896189d2caaccb19647eb7c6d57f29dfc0290dd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898299"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Zabránění útokům na otevřeném přesměrování v ASP.NET Core

Webové aplikace, který přesměruje na adresu URL, která je zadáno pomocí požadavku, například data řetězce dotazu nebo formuláře může být potenciálně úmyslně přesměrovat uživatele na škodlivé, externí adresu URL. Toto úmyslné poškozování, se nazývá otevřené přesměrování útoku.

Pokaždé, když se vaše aplikace logiky se přesměruje na zadané adresy URL, je nutné ověřit, že adresa URL pro přesměrování bylo neoprávněně manipulováno. ASP.NET Core má integrované funkce k ochraně aplikace před útoky na otevřeném přesměrování (označované také jako otevřené přesměrování).

## <a name="what-is-an-open-redirect-attack"></a>Co je útok otevřeném přesměrování?

Webové aplikace často přesměrovat uživatele na přihlašovací stránku, když přistupují k prostředkům, které vyžadují ověřování. Obvykle zahrnuje přesměrování `returnUrl` parametr řetězce dotazu tak, aby uživatel může být vrácen na původně požadovanou URL adresu po jejich úspěšném přihlášení. Poté, co se uživatel ověřuje, jsou přesměrováni na adresu URL bylo původně požadovali.

Protože cílová adresa URL je uveden v řetězci dotazu požadavku, uživateli se zlými úmysly může manipulovat s řetězec dotazu. Zmanipulovanou řetězce dotazu může umožnit, aby přesměruje uživatele na škodlivý web externí web. Tato technika se nazývá útoku otevřené přesměrování (nebo přesměrování).

### <a name="an-example-attack"></a>Příklad útoku

Uživatel se zlými úmysly můžete vyvíjet útoku určený k tomu, uživatel se zlými úmysly přístup k přihlašovacím údajům uživatele nebo citlivé informace. Zahájit útok, uživateli se zlými úmysly convinces uživatel kliknutím na odkaz na přihlašovací stránku vašeho webu pomocí `returnUrl` přidána k adrese URL hodnotu řetězce dotazu. Zvažte například aplikaci na `contoso.com` , který obsahuje přihlašovací stránku na `http://contoso.com/Account/LogOn?returnUrl=/Home/About`. Útok zahrnuje následující kroky:

1. Uživatel klikne škodlivý odkaz `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (druhý adresa URL je "contoso**1**.com", ne "contoso.com").
2. Přihlášení uživatele úspěšně.
3. Uživatel je přesměrován (lokalitou) na `http://contoso1.com/Account/LogOn` (škodlivým webům, který může vypadat přesně skutečné stránky).
4. Uživatel znovu přihlásí (poskytuje škodlivý web přihlašovacích údajů) a je přesměrován zpět na skutečné stránky.

Uživatel pravděpodobně domnívá, že jejich první pokus o přihlášení se nezdařilo a jestli jejich druhý pokus se úspěšně dokončila. Uživatel zůstane pravděpodobně vědět, že jsou dojde k ohrožení bezpečnosti přihlašovacích údajů.

![Proces útoku otevřené přesměrování](preventing-open-redirects/_static/open-redirection-attack-process.png)

Kromě stránek přihlášení některé weby poskytují stránek přesměrování nebo koncové body. Představte si vaše aplikace obsahuje stránku s otevřeném přesměrování `/Home/Redirect`. Útočník by mohl vytvořit, například pomocí odkazu v e-mailu, který směřuje na `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Běžný uživatel se podívá na adresu URL a vidět, že začíná názvem vaší lokality. Které důvěřující, bude kliknou na odkaz. Otevřeném přesměrování by pak uživatele poslat na lokalitě útoky phishing, která vypadá identické té vaší, a uživatel by pravděpodobně přihlášení budou věřit, je váš web.

## <a name="protecting-against-open-redirect-attacks"></a>Ochrana před útoky na otevřeném přesměrování

Při vývoji webových aplikací, zpracovávat všechny uživatelsky zadaných dat jako nedůvěryhodné. Pokud má vaše aplikace funkcí, které přesměruje uživatele na základě obsahu adresy URL, ujistěte se, že takové přesměrování jsou pouze dělat místně v rámci vaší aplikace (nebo známou adresu URL, nikoliv adresu URL, která může být zadána v řetězec dotazu).

### <a name="localredirect"></a>LocalRedirect

Použití `LocalRedirect` pomocnou metodu v základní třídě `Controller` třídy:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` vyvolá výjimku, pokud je zadána adresa URL není místní. V opačném případě se chová stejně jako `Redirect` metody.

### <a name="islocalurl"></a>IsLocalUrl

Použití [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) metoda test adresy URL před přesměrování:

Následující příklad ukazuje, jak zjistit, jestli je adresa URL místní před přesměrování.

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

`IsLocalUrl` Metoda chrání uživatele od nedopatřením se přesměrovává na škodlivý web. Přihlaste se na podrobnosti o adresu URL, která byla k dispozici, pokud jiné než místní adresa URL je zadáno v situaci, kde byl očekáván místní adresu URL. Protokolování přesměrování adresy URL může pomoci při diagnostice přesměrování útoky.
