---
title: Vynutili zásady zabezpečení obsahu pro ASP.NET CoreBlazor
author: guardrex
description: Naučte se používat zásady zabezpečení obsahu (CSP) s aplikacemi ASP.NET Core Blazor k ochraně proti útokům skriptování mezi weby (XSS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 5c53ac64d3ae1b365b40c519eb119f913d58cad1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402439"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Vynutili zásady zabezpečení obsahu pro ASP.NET CoreBlazor

Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)

[Skriptování mezi weby (XSS)](xref:security/cross-site-scripting) je ohrožení zabezpečení v případě, kdy útočník umístí do vykresleného obsahu aplikace jeden nebo více škodlivých skriptů na straně klienta. Zásady zabezpečení obsahu (CSP) pomáhají chránit před útoky XSS pomocí informování prohlížeče o platných verzích:

* Zdroje pro načtený obsah, včetně skriptů, šablon stylů a obrázků.
* Akce provedená stránkou a určením povolených cílů adres URL formulářů.
* Moduly plug-in, které lze načíst.

Pokud chcete použít zprostředkovatele CSP pro aplikaci, vývojář určí několik *direktiv* zabezpečení obsahu CSP v jedné nebo několika `Content-Security-Policy` hlavičkách nebo `<meta>` značkách.

Zásady jsou vyhodnocovány prohlížečem během načítání stránky. Prohlížeč zkontroluje zdroje stránky a určí, zda splňují požadavky na direktivy zabezpečení obsahu. Pokud se pro prostředek nesplní směrnice zásad, prohlížeč nenačte prostředek. Představte si třeba zásadu, která nepovoluje skripty třetích stran. Pokud stránka obsahuje `<script>` značku se zdrojem třetí strany v `src` atributu, prohlížeč brání načtení skriptu.

CSP se podporuje ve většině moderních desktopových a mobilních prohlížečích, včetně Chrome, Edge, Firefox, Operau a Safari. Pro aplikace se doporučuje CSP Blazor .

## <a name="policy-directives"></a>Direktivy zásad

Minimálně zadejte následující direktivy a zdroje pro Blazor aplikace. Podle potřeby přidejte další direktivy a zdroje. V části [použití zásad](#apply-the-policy) v tomto článku se používají následující direktivy, kde jsou k dispozici příklady zásad zabezpečení pro Blazor WebAssembly a Blazor Server :

* [Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): omezí adresy URL pro `<base>` značku stránky. Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
* [blokování všech smíšených obsahu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): zabraňuje načtení smíšeného obsahu HTTP a HTTPS.
* [Výchozí hodnota-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): označuje zálohu pro zdrojové direktivy, které nejsou explicitně určeny zásadou. Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): označuje platné zdroje pro obrázky.
  * Určuje `data:` , že se mají povolit načítání imagí z `data:` adres URL.
  * Určuje `https:` , že se mají povolit načítání imagí z koncových bodů https.
* [objekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): označuje platné zdroje pro `<object>` značky, `<embed>` a `<applet>` . Určete `none` , aby se zabránilo všem zdrojům adresy URL.
* [skript-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): označuje platné zdroje pro skripty.
  * Zadejte `https://stackpath.bootstrapcdn.com/` zdroj hostitele pro skripty Bootstrap.
  * Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
  * V Blazor WebAssembly aplikaci:
    * Zadejte následující hodnoty hash, aby bylo Blazor WebAssembly možné načíst požadované vložené skripty:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Určete `unsafe-eval` , že se mají použít `eval()` metody a pro vytvoření kódu z řetězců.
  * V Blazor Server aplikaci zadejte `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hodnotu hash vloženého skriptu, který provádí záložní detekci pro šablony stylů.
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): označuje platné zdroje pro šablony stylů.
  * Zadejte `https://stackpath.bootstrapcdn.com/` zdroj hostitele pro Bootstrap šablon stylů.
  * Určete `self` , aby bylo jasné, že původ aplikace, včetně schématu a čísla portu, je platným zdrojem.
  * Určete `unsafe-inline` , aby bylo možné použít vložené styly. Vložená deklarace se vyžaduje pro uživatelské rozhraní v Blazor Server aplikacích pro opětovné připojení klienta a serveru po počátečním požadavku. V budoucí verzi je možné odebrat vložené styly, aby `unsafe-inline` se už nevyžadovaly.
* [upgrade – nezabezpečené – požadavky](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): označuje, že adresy URL obsahu z nezabezpečených (http) zdrojů by měly být zabezpečeny prostřednictvím protokolu HTTPS.

Předchozí direktivy jsou podporovány všemi prohlížeči kromě aplikace Microsoft Internet Explorer.

Získání hodnot hash SHA pro další vložené skripty:

* Použijte zprostředkovatele CSP, který je uveden v části [použití zásad](#apply-the-policy) .
* Přístup ke konzole nástroje pro vývojáře v prohlížeči, při které se aplikace spouští místně Prohlížeč vypočítá a zobrazí hodnoty hash pro blokované skripty, když `meta` je přítomna hlavička nebo značka CSP.
* Zkopírujte hodnoty hash poskytované prohlížečem do `script-src` zdrojů. Kolem každé hodnoty hash použijte jednoduché uvozovky.

Informace o podpoře prohlížečů úrovně zásad zabezpečení obsahu 2 najdete v tématu [Jak můžu použít: úroveň zásad zabezpečení obsahu 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Použít zásady

Použijte `<meta>` značku pro aplikování zásad:

* Nastavte hodnotu `http-equiv` atributu na `Content-Security-Policy` .
* Umístěte direktivy do `content` hodnoty atributu. Oddělte direktivy středníkem ( `;` ).
* Vždy umístit `meta` značku do `<head>` obsahu.

V následujících částech jsou uvedeny příklady zásad pro Blazor WebAssembly a Blazor Server . Tyto příklady jsou ve verzi tohoto článku pro každou verzi Blazor . Pokud chcete použít verzi vhodnou pro vaši verzi, vyberte na této webové stránce verzi dokumentu s rozevíracím selektorem **verzí** .

### Blazor WebAssembly

V `<head>` obsahu `wwwroot/index.html` stránky hostitele použijte direktivy popsané v části [direktivy zásad](#policy-directives) :

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

### Blazor Server

V `<head>` obsahu `Pages/_Host.cshtml` stránky hostitele použijte direktivy popsané v části [direktivy zásad](#policy-directives) :

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

`<meta>`Zásady značek nepodporují následující direktivy:

* [předchůdci v rámci](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [Sestava – do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [identifikátor URI sestavy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [úložišti](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Pro podporu předchozích direktiv použijte záhlaví s názvem `Content-Security-Policy` . Řetězec direktivy je hodnota hlavičky.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testování zásad a přijímání sestav o porušení

Testování pomáhá ověřit, jestli se skripty třetích stran při vytváření počátečních zásad nechtěně nezablokovaly.

Chcete-li testovat zásadu v časovém intervalu bez vynucování direktiv zásad, nastavte `<meta>` `http-equiv` atribut značky nebo záhlaví zásady na základě záhlaví na `Content-Security-Policy-Report-Only` . Zprávy o chybách se odesílají do zadané adresy URL jako dokumenty JSON. Další informace najdete v tématu [MDN web Docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Pro vytváření sestav o porušeních, když je zásada aktivní, se podívejte na následující články:

* [Sestava – do](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [identifikátor URI sestavy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

I když `report-uri` se už nedoporučuje používat, obě direktivy by se měly používat, dokud `report-to` je nepodporují všechny hlavní prohlížeče. Nepoužívejte výhradně `report-uri` , protože podpora pro `report-uri` je předmětem vyřazení *at any time* z prohlížečů kdykoli. `report-uri`Pokud je podpora plně podporovaná, odeberte ji ve svých zásadách `report-to` . Informace o tom, jak se `report-to` dá sledovat, najdete v tématu [použití: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

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
