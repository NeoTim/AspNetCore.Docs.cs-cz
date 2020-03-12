---
title: Vynutili zásady zabezpečení obsahu pro ASP.NET Core Blazor
author: guardrex
description: Naučte se používat zásady zabezpečení obsahu (CSP) s aplikacemi ASP.NET Core Blazor, které vám pomůžou chránit před útoky XSS (mezi lokalitami).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964548"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a>Vynutili zásady zabezpečení obsahu pro ASP.NET Core Blazor

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Skriptování mezi weby (XSS)](xref:security/cross-site-scripting) je ohrožení zabezpečení v případě, kdy útočník umístí do vykresleného obsahu aplikace jeden nebo více škodlivých skriptů na straně klienta. Zásady zabezpečení obsahu (CSP) pomáhají chránit před útoky XSS pomocí informování prohlížeče o platných verzích:

* Zdroje pro načtený obsah, včetně skriptů, šablon stylů a obrázků.
* Akce provedená stránkou a určením povolených cílů adres URL formulářů.
* Moduly plug-in, které lze načíst.

Pokud chcete použít zprostředkovatele CSP pro aplikaci, vývojář určí několik *direktiv* zabezpečení obsahu CSP v jednom nebo více `Content-Security-Policy` hlaviček nebo `<meta>` tagů.

Zásady jsou vyhodnocovány prohlížečem během načítání stránky. Prohlížeč zkontroluje zdroje stránky a určí, zda splňují požadavky na direktivy zabezpečení obsahu. Pokud se pro prostředek nesplní směrnice zásad, prohlížeč nenačte prostředek. Představte si třeba zásadu, která nepovoluje skripty třetích stran. Pokud stránka obsahuje značku `<script>` se zdrojem třetí strany v atributu `src`, prohlížeč brání načtení skriptu.

CSP se podporuje ve většině moderních desktopových a mobilních prohlížečích, včetně Chrome, Edge, Firefox, Operau a Safari. Pro aplikace Blazor se doporučuje CSP.

## <a name="policy-directives"></a>Direktivy zásad

Minimálně zadejte následující direktivy a zdroje pro aplikace Blazor. Podle potřeby přidejte další direktivy a zdroje. V části [použití zásad](#apply-the-policy) v tomto článku se používají následující direktivy, kde jsou k dispozici příklady zásad zabezpečení pro Blazor WebAssembly a Blazor Server:

* [Base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; omezují adresy URL pro značku `<base>` stránky. Zadejte `self` pro označení, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
* [blok-All-Mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; znemožňuje načtení smíšeného obsahu HTTP a HTTPS.
* [Výchozí hodnota-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; označuje zálohu pro zdrojové direktivy, které nejsou explicitně určeny zásadou. Zadejte `self` pro označení, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; označuje platné zdroje pro obrázky.
  * Zadejte `data:` pro povolení načítání imagí z adres URL `data:`.
  * Zadejte `https:` pro povolení načítání imagí z koncových bodů HTTPS.
* [objekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; označuje platné zdroje pro značky `<object>`, `<embed>`a `<applet>`. Zadejte `none`, aby se zabránilo všem zdrojům adresy URL.
* [skript-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; označuje platné zdroje pro skripty.
  * Zadejte zdroj hostitele `https://stackpath.bootstrapcdn.com/` pro skripty Bootstrap.
  * Zadejte `self` pro označení, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
  * V Blazor aplikaci WebAssembly:
    * Zadejte následující hodnoty hash, aby bylo možné načíst požadované Blazor vložené skripty protokolu WebAssembly:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Zadejte `unsafe-eval` pro použití `eval()` a metod pro vytvoření kódu z řetězců.
  * V aplikaci Blazor Server zadejte hodnotu hash `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` vloženého skriptu, který provádí záložní detekci pro šablony stylů.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; označuje platné zdroje pro šablony stylů.
  * Zadejte zdroj hostitele `https://stackpath.bootstrapcdn.com/` pro spouštěcí šablony stylů.
  * Zadejte `self` pro označení, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
  * Zadejte `unsafe-inline`, aby bylo možné použít vložené styly. Vložená deklarace je vyžadována pro uživatelské rozhraní v aplikacích Blazor Server pro opětovné připojení klienta a serveru po počátečním požadavku. V budoucí verzi je možné odebrat vložené styly, aby se `unsafe-inline` už nevyžadovala.
* [upgrade – nezabezpečené – požadavky](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; označují, že adresy URL obsahu z nezabezpečených (http) zdrojů by měly být zabezpečeny prostřednictvím protokolu HTTPS.

Předchozí direktivy jsou podporovány všemi prohlížeči kromě aplikace Microsoft Internet Explorer.

Získání hodnot hash SHA pro další vložené skripty:

* Použijte zprostředkovatele CSP, který je uveden v části [použití zásad](#apply-the-policy) .
* Přístup ke konzole nástroje pro vývojáře v prohlížeči, při které se aplikace spouští místně Prohlížeč vypočítá a zobrazí hodnoty hash pro blokované skripty, pokud je k dispozici hlavička CSP nebo `meta` značka.
* Zkopírujte hodnoty hash poskytované prohlížečem do zdrojů `script-src`. Kolem každé hodnoty hash použijte jednoduché uvozovky.

Informace o podpoře prohlížečů úrovně zásad zabezpečení obsahu 2 najdete v tématu [Jak můžu použít: úroveň zásad zabezpečení obsahu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Použít zásady

Použití značky `<meta>` k aplikování zásad:

* Nastavte hodnotu atributu `http-equiv` na `Content-Security-Policy`.
* Direktivy umístěte do hodnoty atributu `content`. Oddělte direktivy středníkem (`;`).
* Vždy umístit značku `meta` do obsahu `<head>`.

V následujících částech jsou uvedeny příklady zásad pro Blazor WebAssembly a Blazor Server. Tyto příklady jsou ve verzi tohoto článku pro každou verzi Blazor. Pokud chcete použít verzi vhodnou pro vaši verzi, vyberte na této webové stránce verzi dokumentu s rozevíracím selektorem **verzí** .

### <a name="opno-locblazor-webassembly"></a>Blazor WebAssembly

V `<head>`ovém obsahu stránky hostitele *wwwroot/index.html* použijte direktivy popsané v části [direktivy zásad](#policy-directives) :

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="opno-locblazor-server"></a>Server Blazor

V `<head>` obsahu stránky hostitele *stránky/_Host. cshtml* použijte direktivy popsané v části [direktivy zásad](#policy-directives) :

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Omezení meta značek

Zásady značky `<meta>` nepodporují následující direktivy:

* [předchůdci v rámci](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [Sestava – do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [identifikátor URI sestavy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [úložišti](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Pro podporu předchozích direktiv použijte záhlaví s názvem `Content-Security-Policy`. Řetězec direktivy je hodnota hlavičky.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testování zásad a přijímání sestav o porušení

Testování pomáhá ověřit, jestli se skripty třetích stran při vytváření počátečních zásad nechtěně nezablokovaly.

Chcete-li testovat zásadu v časovém intervalu bez vynucení direktiv zásad, nastavte atribut `http-equiv` `<meta>` značky nebo název záhlaví zásady na základě hlaviček na `Content-Security-Policy-Report-Only`. Zprávy o chybách se odesílají do zadané adresy URL jako dokumenty JSON. Další informace najdete v tématu [MDN web Docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Pro vytváření sestav o porušeních, když je zásada aktivní, se podívejte na následující články:

* [Sestava – do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [identifikátor URI sestavy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

I když se už `report-uri` nedoporučuje používat, měli byste použít obě direktivy, dokud `report-to` nepodporují všechny hlavní prohlížeče. Nepoužívejte výhradně `report-uri`, protože podpora `report-uri` se *v* těchto prohlížečích může kdykoli vyřadit. Pokud je `report-to` plně podporovaná, odeberte podporu pro `report-uri` v zásadách. Pokud chcete sledovat přijetí `report-to`, přečtěte si téma [možné použití: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Otestujte a aktualizujte zásady aplikace při každé vydané verzi.

## <a name="troubleshoot"></a>Řešení potíží

* V konzole nástroje pro vývojáře v prohlížeči se zobrazí chyby. Prohlížeče poskytují informace o:
  * Prvky, které nejsou v rozporu s touto zásadou.
  * Postup úpravy zásady tak, aby umožňovaly blokovanou položku.
* Zásady jsou zcela účinné pouze v případě, že prohlížeč klienta podporuje všechny zahrnuté direktivy. Aktuální matici podpory prohlížeče najdete v tématu [použití: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Další zdroje

* [MDN web Docs: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Úroveň zásad zabezpečení obsahu 2](https://www.w3.org/TR/CSP2/)
* [Vyhodnocovací filtr Google CSP](https://csp-evaluator.withgoogle.com/)
