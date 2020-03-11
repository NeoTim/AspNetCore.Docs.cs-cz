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
# <a name="work-with-samesite-cookies-in-aspnet-core"></a>Práce s SameSite soubory cookie v ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

SameSite je Konceptový standard [IETF](https://ietf.org/about/) navržený tak, aby poskytoval určitou ochranu proti útokům prostřednictvím CSRF (pro falšování požadavků mezi lokalitami). Původně koncept v [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)se aktualizoval koncept standardu v [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00). Aktualizovaný Standard není zpětně kompatibilní s předchozím standardem, přičemž následující je největší znatelné rozdíly:

* Soubory cookie bez hlavičky SameSite se ve výchozím nastavení považují za `SameSite=Lax`.
* aby bylo možné používat soubory cookie pro více webů, je třeba použít `SameSite=None`.
* Soubory cookie, které vyhodnotí `SameSite=None`, musí být také označeny jako `Secure`.
* U aplikací, které používají [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) , může docházet k problémům s `sameSite=Lax` nebo `sameSite=Strict` soubory cookie, protože `<iframe>` se považují za scénáře mezi lokalitami.
* Hodnota `SameSite=None` není povolena [standardem 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07) a způsobí, že některé implementace považují takové soubory cookie za `SameSite=Strict`. Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.

Nastavení `SameSite=Lax` funguje pro většinu souborů cookie aplikace. Některé formy ověřování, jako je [OpenID Connect](https://openid.net/connect/) (OIDC) a [WS-Federation](https://auth0.com/docs/protocols/ws-fed) , jako výchozí vystavení přesměrování na základě. Přesměrování na základě příspěvku spustí ochranu prohlížeče SameSite, takže pro tyto součásti je SameSite zakázaný. Většina přihlášení [OAuth](https://oauth.net/) není ovlivněná kvůli rozdílům ve způsobu, jakým jsou požadavky v toku.

Každá součást ASP.NET Core, která generuje soubory cookie, musí rozhodnout, zda je SameSite vhodná.

## <a name="samesite-test-sample-code"></a>Ukázkový kód testu SameSite

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

Následující ukázky lze stáhnout a otestovat:

| Ukázka               | Dokument |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Následující ukázku lze stáhnout a otestovat:


| Ukázka               | Dokument |
| ----------------- | ------------ |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a>Podpora .NET Core pro atribut sameSite

.NET Core 2,2 podporuje pracovní Standard 2019 pro SameSite od vydání aktualizací v prosinci 2019. Vývojáři mohou programově řídit hodnotu atributu sameSite pomocí vlastnosti `HttpCookie.SameSite`. Nastavením vlastnosti `SameSite` na hodnotu Strict, LAX nebo None dojde k vypsání hodnot v síti pomocí souboru cookie. Nastavení rovná se (SameSiteMode) (-1) znamená, že v síti se souborem cookie by neměl být zahrnut žádný atribut sameSite

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

.NET Core 3,0 podporuje aktualizované hodnoty SameSite a přidává další hodnotu výčtu `SameSiteMode.Unspecified` výčtu `SameSiteMode`.
Tato nová hodnota znamená, že by se soubor cookie neměl odesílat žádná sameSite.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a>Prosince změny chování opravy

Konkrétní změna chování pro .NET Framework a .NET Core 2,1 je způsob, jakým vlastnost `SameSite` interpretuje hodnotu `None`. Předtím, než je hodnota `None` určena "negenerovat atribut na All", po opravě to znamená "vygenerovat atribut s hodnotou `None`". Po opravě `SameSite` hodnota `(SameSiteMode)(-1)` způsobí, že se atribut neemituje.

Výchozí hodnota SameSite pro ověřování pomocí formulářů a soubory cookie stavu relace se změnila z `None` na `Lax`.

::: moniker-end

## <a name="api-usage-with-samesite"></a>Použití rozhraní API s SameSite

[HttpContext. Response. cookies. připojovat](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) výchozí hodnoty `Unspecified`, což znamená, že se do souboru cookie nepřidal žádný atribut SameSite a klient použije výchozí chování (Lax pro nové prohlížeče, žádné pro staré). Následující kód ukazuje, jak změnit hodnotu SameSite souboru cookie na `SameSiteMode.Lax`:

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

Všechny součásti ASP.NET Core, které generují soubory cookie, potlačí předchozí výchozí nastavení s nastavením vhodným pro jejich scénáře. Přepsané předchozí výchozí hodnoty se nezměnily.

| Komponenta | soubor | Výchozí |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [SessionOptions. cookie](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [CookieTempDataProviderOptions. cookie](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [AntiforgeryOptions. cookie](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [Ověřování souborem cookie](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [CookieAuthenticationOptions. cookie](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [TwitterOptions.StateCookie](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions. CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None` |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [OpenIdConnectOptions.NonceCookie](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

ASP.NET Core 3,1 a novějším přináší následující podporu SameSite:

* Předefinuje chování `SameSiteMode.None` k vygenerování `SameSite=None`
* Přidá novou hodnotu `SameSiteMode.Unspecified` pro vynechání atributu SameSite.
* Všechna rozhraní API cookies jsou ve výchozím nastavení `Unspecified`. Některé součásti, které používají soubory cookie, nastavují hodnoty konkrétnější pro jejich scénáře. Příklady najdete v tabulce výše.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core 3,0 a novějších byly změněny výchozí hodnoty SameSite, aby nedocházelo ke konfliktům s nekonzistentními výchozími nastaveními klientů. Následující rozhraní API změnila výchozí hodnoty z `SameSiteMode.Lax ` na `-1`, aby nedocházelo k vygenerování atributu SameSite pro tyto soubory cookie:

* <xref:Microsoft.AspNetCore.Http.CookieOptions> použito s [HttpContext. Response. cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)
* <xref:Microsoft.AspNetCore.Http.CookieBuilder> používané jako továrna pro `CookieOptions`
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a>Historie a změny

Podpora SameSite byla poprvé implementována v ASP.NET Core v 2,0 pomocí [standardního konceptu 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1). Standard 2016 byl výslovný. ASP.NET Core se rozhodla-in nastavením několika souborů cookie na `Lax` ve výchozím nastavení. Po zjištění několika [problémů](https://github.com/aspnet/Announcements/issues/318) s ověřováním je většina využití SameSite [zakázaná](https://github.com/aspnet/Announcements/issues/348).

[Opravy](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) byly vydány v listopadu 2019, aby se aktualizovaly z standardu 2016 na standard 2019. [Koncept 2019 specifikace SameSite](https://github.com/aspnet/Announcements/issues/390):

* Není **zpětně** kompatibilní s konceptem 2016. Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.
* Určuje, že soubory cookie se ve výchozím nastavení považují za `SameSite=Lax`.
* Určuje soubory cookie, které explicitně vyhodnotí `SameSite=None`, aby bylo možné povolit doručování mezi weby, musí být označeno jako `Secure`. `None` je nová položka k odhlášení.
* Je podporován opravami vydanými pro ASP.NET Core 2,1, 2,2 a 3,0. ASP.NET Core 3,1 má další podporu SameSite.
* Ve výchozím nastavení je naplánovaná podpora [Chrome](https://chromestatus.com/feature/5088147346030592) v [únoru 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html). Prohlížeče začaly při přesunu do tohoto standardu v 2019.

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a>Rozhraní API, která mají vliv na změnu z konceptu Standard 2016 SameSite na 2019 koncept Standard

* [Http. SameSiteMode](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [CookieOptions.SameSite](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [CookieBuilder.SameSite](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [CookiePolicyOptions.MinimumSameSitePolicy](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a>Podpora starších prohlížečů

SameSite standardně vyhodnocuje, že neznámé hodnoty musí být považovány za `SameSite=Strict` hodnoty. 2016 Aplikace, ke kterým se přistupoval ze starších prohlížečů, které podporují SameSite úrovně Standard 2016, se můžou přerušit, když získají vlastnost SameSite s hodnotou `None`. Webové aplikace musí implementovat detekci prohlížeče, pokud chtějí podporovat starší prohlížeče. ASP.NET Core neimplementuje detekci prohlížeče, protože hodnoty uživatelských agentů jsou vysoce nestálé a často se mění. Rozšiřovací bod v <xref:Microsoft.AspNetCore.CookiePolicy> umožňuje zapojení do logiky specifické pro uživatele.

V `Startup.Configure`přidejte kód, který volá <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> před voláním <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> nebo *libovolné* metody, která zapisuje soubory cookie:

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

V `Startup.ConfigureServices`přidejte kód podobný následujícímu:

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

V předchozí ukázce `MyUserAgentDetectionLib.DisallowsSameSiteNone` je uživatelem zadaná knihovna, která detekuje, jestli Agent pro uživatele nepodporuje SameSite `None`:

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

Následující kód ukazuje ukázkovou `DisallowsSameSiteNone` metodu:

> [!WARNING]
> Následující kód je určen pouze pro ukázku:
> * Neměla by být považována za dokončenou.
> * Není udržována ani podporována.

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a>Testování aplikací pro problémy s SameSite

Aplikace, které komunikují se vzdálenými lokalitami, jako je třeba přihlášení třetí strany, potřebují:

* Otestujte interakci na více prohlížečích.
* Použijte [detekci a zmírnění v prohlížeči CookiePolicy](#sob) popsané v tomto dokumentu.

Otestujte webové aplikace pomocí verze klienta, která se může přihlásit k novému chování SameSite. Pro Chrome, Firefox a chrom Edge mají všechny nové příznaky funkcí pro výslovný souhlas, které lze použít k testování. Po použití oprav SameSite se aplikace testuje se staršími verzemi klientů, zejména Safari. Další informace najdete v tématu [Podpora starších prohlížečů](#sob) v tomto dokumentu.

### <a name="test-with-chrome"></a>Testování s použitím Chromu

Chrome 78 + poskytuje zavádějící výsledky, protože má na zpracování dočasné omezení. Chrome 78 + dočasné zmírnění umožňuje, aby soubory cookie byly starší než dvě minuty. Chrome 76 nebo 77 s povolenými vhodnými příznakem testu poskytuje přesnější výsledky. Chcete-li otestovat nové chování SameSite, přepněte `chrome://flags/#same-site-by-default-cookies` na **povoleno**. Starší verze Chrome (75 a novější) jsou hlášeny jako neúspěšné s novým nastavením `None`. Viz [Podpora starších prohlížečů](#sob) v tomto dokumentu.

Google nezpřístupňuje starší verze Chrome. Postupujte podle pokynů v části [stažení Chromu](https://www.chromium.org/getting-involved/download-chromium) a otestujte starší verze Chrome. Nestahujte Chrome z odkazů **, které jsou** k dispozici, hledáním ve starších verzích Chrome.

* [Chróm 76 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [Chróm 74 Win64](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

Počínaje `80.0.3975.0`ou zkušební verze je možné zakázat dočasné zmírnění LAX + POST pro účely testování pomocí nového příznaku `--enable-features=SameSiteDefaultChecksMethodRigorously`, který umožňuje testování lokalit a služeb v konečném stavu funkce, ve které bylo řešení zmírnění odstraněno. Další informace najdete v tématu [aktualizace SameSite](https://www.chromium.org/updates/same-site) v projektech Chromu.

### <a name="test-with-safari"></a>Testování pomocí Safari

Prohlížeč Safari 12 striktně implementuje předchozí koncept a v případě, že je nová hodnota `None` v souboru cookie, se nezdařila. `None` se vyhnete prostřednictvím kódu detekce prohlížeče, který [podporuje starší prohlížeče](#sob) v tomto dokumentu. Pomocí MSAL, ADAL nebo libovolné knihovny, kterou používáte, otestujte přihlašovací údaje pro WebKit Safari 12, Safari 13 a na bázi. Problém závisí na základní verzi operačního systému. OSX Mojave (10,14) a iOS 12 se nazývají problémy s kompatibilitou s novým chováním SameSite. Upgrade operačního systému na OSX Catalina (10,15) nebo iOS 13 opravuje problém. Prohlížeč Safari aktuálně nemá příznak výslovných přihlášení pro testování nového chování specifikace.

### <a name="test-with-firefox"></a>Testování pomocí Firefox

Podporu aplikace Firefox pro nový standard lze testovat na verzi 68 + tím, že na stránce `about:config` `network.cookie.sameSite.laxByDefault`příznak funkce. Nebyly zjištěny žádné zprávy o problémech s kompatibilitou se staršími verzemi aplikace Firefox.

### <a name="test-with-edge-browser"></a>Testování pomocí prohlížeče Edge

Edge podporuje starý SameSite Standard. Edge verze 44 nemá žádné známé problémy s kompatibilitou s novým standardem.

### <a name="test-with-edge-chromium"></a>Test s hranou (chrom)

Příznaky SameSite jsou nastaveny na stránce `edge://flags/#same-site-by-default-cookies`. U Edge Chromu se nezjistily žádné problémy s kompatibilitou.

### <a name="test-with-electron"></a>Testování s elektronem

K verzím elektronů patří starší verze Chromu. Například verze elektronicky používané týmy je chrom 66, který vykazuje starší chování. Je nutné provést vlastní testování kompatibility s verzí elektronů, kterou váš produkt používá. Viz [Podpora starších prohlížečů](#sob) v následující části.

## <a name="additional-resources"></a>Další zdroje

* [Chromový blog: vývojáři: Připravte se na nové SameSite = None; Nastavení zabezpečeného souboru cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [Vysvětlení souborů cookie SameSite](https://web.dev/samesite-cookies-explained/)
* [Opravy od listopadu 2019](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| Ukázka               | Dokument |
| ----------------- | ------------ |
| [.NET Core MVC](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| Ukázka               | Dokument |
| ----------------- | ------------ |
| [Razor Pages .NET Core](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end
