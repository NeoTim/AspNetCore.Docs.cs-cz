---
uid: web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
title: Vytvoření čitelných adres URL v rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek popisuje směrování ve na webu rozhraní ASP.NET Web Pages (Razor) a jak to vám umožní používat adresy URL, které jsou čitelnější a lépe pro SEO. Co budete...
ms.author: riande
ms.date: 02/17/2014
ms.assetid: a8aac1ac-89de-4415-afe0-97a41c6423d2
msc.legacyurl: /web-pages/overview/routing/creating-readable-urls-in-aspnet-web-pages-sites
msc.type: authoredcontent
ms.openlocfilehash: 26d8f94b2e38fe5205a37e3d37b4e3bd509a3874
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021078"
---
<a name="creating-readable-urls-in-aspnet-web-pages-razor-sites"></a>Vytvoření čitelných adres URL webů s ASP.NET webovými stránkami (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje směrování ve na webu rozhraní ASP.NET Web Pages (Razor) a jak to vám umožní používat adresy URL, které jsou čitelnější a lépe pro SEO.
> 
> Co se dozvíte:
> 
> - Jak ASP.NET používá směrování, aby vám umožňují používat více čitelný a dohledatelné adresy URL.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


## <a name="about-routing"></a>O směrování

Adresy URL pro stránky ve vaší lokalitě může mít dopad na tom, jak dobře funguje webu. Adresa URL, která &quot;popisný&quot; usnadnit uživatelům webu. Může také pomoct s optimalizací vyhledávání (SEO) pro lokalitu. Webů ASP.NET zahrnuje možnost automaticky používat přátelské adresy URL.

Technologie ASP.NET umožňuje vytvářet smysluplné adresy URL, které popisují akce uživatelů místo jenom odkazuje na soubor na serveru. Vezměte v úvahu tyto adresy URL pro fiktivní blog:

- `http://www.contoso.com/Blog/blog.cshtml?categories=hardware`
- `http://www.contoso.com//Blog/blog.cshtml?startdate=2009-11-01&enddate=2009-11-30`

Porovnání těchto adres URL pro následující dotazy:

- `http://www.contoso.com/Blog/categories/hardware/`
- `http://www.contoso.com/Blog/2009/November`

V první pár uživatel musel vědět, že se zobrazí v blogu pomocí *blog.cshtml* stránce a pak je nutné vytvořit řetězec dotazu, který získá správné kategorie nebo rozsah. Druhá řada příklady jsou mnohem snadněji pochopit a vytvořit.

Adresy URL pro první příklad také odkazovat přímo na konkrétní soubor (*blog.cshtml*). Pokud z nějakého důvodu blogu byly přesunuty do jiné složky na serveru nebo na blogu nebyly přepsány, aby bylo použít jinou stránku, odkazy by být nesprávné. Druhá sada adres URL neodkazuje na konkrétní stránce, tak i v případě implementace blogu nebo umístění změní, adresy URL by stále platit.

ASP.NET Web Pages, můžete vytvořit příjemnější adresy URL jako v předchozích příkladech vzhledem k tomu používá ASP.NET *směrování*. Směrování vytvoří logické mapování z adresy URL stránky (nebo stránky), který požadavek můžete splnit. Protože je logické mapování (ne z fyzických prostředků, do konkrétního souboru), směrování poskytuje velmi flexibilní způsob definování adresy URL pro váš web.

## <a name="how-routing-works"></a>Jak funguje směrování

Když ASP.NET zpracovává žádost, načte adresu URL a zjistěte, jak ho směrovat. Technologie ASP.NET pokusí porovnat jednotlivých segmentů adresy URL pro soubory na disku, bude zleva doprava. Pokud se zjistí shoda, nic zbývajících v adrese URL je předána stránce jako *informace o cestě*.

Představte si, že někdo vytvoří žádost o tuto adresu URL:

`http://www.contoso.com/a/b/c`

Hledání prochází tímto způsobem:

1. Je k dispozici soubor s cestou a názvem */a/b/c.cshtml*? Pokud ano, spusťte tuto stránku a předat žádné informace. V opačném případě...
2. Je k dispozici soubor s cestou a názvem */a/b.cshtml*? Pokud ano, spusťte tuto stránku a předejte hodnotu `c` k němu. V opačném případě...
3. Je k dispozici soubor s cestou a názvem */a.cshtml*? Pokud ano, spusťte tuto stránku a předejte hodnotu `b/c` k němu.

Pokud hledání nenašel žádné přesně odpovídá *.cshtml* soubory v jejich zadaných složek ASP.NET pokračuje v hledání pro tyto soubory pak:

1. */a/b/c/default.cshtml* (informace o cestě).
2. */a/b/c/index.cshtml* (informace o cestě).

> [!NOTE]
> Jasno, požadavky na konkrétní stránky (to znamená, požadavky, které obsahují *.cshtml* příponu názvu souboru) pracovat stejně jako byste očekávali. Žádost, jako jsou `http://www.contoso.com/a/b.cshtml` poběží na stránce *b.cshtml* zcela v pořádku.


Uvnitř stránky, můžete získat informace o cestě na stránce `UrlData` vlastnost, která je do slovníku. Představte si, že máte soubor s názvem *ViewCustomers.cshtml* a webu získá této žádosti:

`http://mysite.com/myWebSite/ViewCustomers/1000`

Podle popisu ve výše uvedených pravidel, požadavek bude přejděte na stránku. Na stránce můžete použít k získání a zobrazit informace o cestě kód podobný tomuto (v tomto případě, hodnota &quot;1000&quot;):

[!code-html[Main](creating-readable-urls-in-aspnet-web-pages-sites/samples/sample1.html)]

> [!NOTE]
> Protože směrování nezahrnuje celý názvy, může být nejednoznačnost Pokud máte stránky, které mají stejné ale různé přípony názvu souboru (například *MyPage.cshtml* a *MyPage.html*) . Pokud se chcete vyhnout problémy se směrováním, je nejlepší, abyste měli jistotu, že není nutné stránek ve vaší lokalitě, jejichž názvy se liší pouze velikostí svého rozšíření.


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky

[Služba WebMatrix – adresy URL, UrlData a směrování pro SEO](http://www.mikesdotnetting.com/Article/165/WebMatrix-URLs-UrlData-and-Routing-for-SEO). Tuto položku blogu podle Mike Brind poskytuje některé další podrobnosti o tom, jak směrování funguje v ASP.NET Web Pages.
