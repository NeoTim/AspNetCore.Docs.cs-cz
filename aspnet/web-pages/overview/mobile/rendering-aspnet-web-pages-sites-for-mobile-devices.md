---
uid: web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
title: Vykreslování v prostředí ASP.NET stránky (Razor) serverů pro mobilní zařízení | Dokumentace Microsoftu
author: Rick-Anderson
description: 'Tento článek popisuje, jak vytvářet stránky na webu rozhraní ASP.NET Web Pages (Razor), která se vykreslí správně na mobilních zařízeních. Co se dozvíte: jak budete...'
ms.author: riande
ms.date: 02/17/2014
ms.assetid: f15ab392-c05e-4269-83bf-7c6d2b8c8ec8
msc.legacyurl: /web-pages/overview/mobile/rendering-aspnet-web-pages-sites-for-mobile-devices
msc.type: authoredcontent
ms.openlocfilehash: dd06a54d396bd85eeef7c004ee115828d541a2c1
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51020934"
---
<a name="rendering-aspnet-web-pages-razor-sites-for-mobile-devices"></a>Vykreslení webů s ASP.NET webovými stránkami (Razor) pro mobilní zařízení
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje, jak vytvářet stránky na webu rozhraní ASP.NET Web Pages (Razor), která se vykreslí správně na mobilních zařízeních.
> 
> Co se dozvíte:
> 
> - Jak používat zásady vytváření názvů určit, že na stránce je určený speciálně pro mobilní zařízení.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Webové stránky ASP.NET (Razor) 3
>   
> 
> V tomto kurzu se také pracuje s ASP.NET Web Pages 2.


Webové stránky ASP.NET umožňuje vytvářet vlastní zobrazení pro vykreslení obsahu na mobilní telefon nebo jiné zařízení.

Nejjednodušší způsob, jak vytvořit stránku specifická pro zařízení na webu rozhraní ASP.NET Web Pages je pomocí vzoru pro pojmenovávání souborů následujícím způsobem: <em>FileName.</em> <em>Mobile</em><em>.cshtml</em>. Můžete vytvořit dvě verze stránky (například jeden s názvem <em>MyFile.cshtml</em> a jeden s názvem <em>MyFile.Mobile.cshtml</em>). V době, když mobilní zařízení požádá o spuštění <em>MyFile.cshtml</em>, rozhraní ASP.NET vykreslí obsah z <em>MyFile.Mobile.cshtml</em>. V opačném případě <em>MyFile.cshtml</em> vykreslením.

Následující příklad ukazuje, jak povolit mobilní vykreslování přidáním stránku obsahu pro mobilní zařízení. *Page1.cshtml* obsahuje obsah a bočním panelu navigace. *Page1.Mobile.cshtml* obsahuje stejný obsah, ale vynechá na bočním panelu.

1. Na webu rozhraní ASP.NET Web Pages, vytvořte soubor s názvem *Page1.cshtml* a nahraďte následující značky na aktuální obsah.

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample1.html)]
2. Vytvořte soubor s názvem *Page1.Mobile.cshtml* a nahraďte existující obsah následujícím kódem. Všimněte si, že mobilní verzi stránce vynechá části navigace pro lepší vykreslování na obrazovce menší.

    [!code-html[Main](rendering-aspnet-web-pages-sites-for-mobile-devices/samples/sample2.html)]
3. Spustit prohlížeč pro stolní počítač a přejděte do *Page1.cshtml*. ![mobilesites-1](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image1.png)
4. Spustit mobilní prohlížeče (nebo emulátoru mobilního zařízení) a přejděte do *Page1.cshtml*. (Všimněte si, že není zadána *.mobile.* jako část adresy URL.) I když je požadavek *Page1.cshtml*, rozhraní ASP.NET vykreslí *Page1.Mobile.cshtml*.

    ![mobilesites-2](rendering-aspnet-web-pages-sites-for-mobile-devices/_static/image2.png)

> [!NOTE]
> K otestování mobilních stránek, můžete použít simulátor mobilní zařízení, na kterém běží na stolním počítači. Tento nástroj umožňuje testování webových stránek, jak by vypadala na mobilních zařízeních (to znamená, obvykle s mnohem menší umožňuje zobrazit oblast). Jedním z příkladů simulátoru je [doplňků uživatelského agenta přepínání](http://addons.mozilla.org/firefox/addon/user-agent-switcher/) pro Mozilla Firefox, která vám umožňuje emulovat různá mobilní prohlížeče v desktopové verzi Firefox.


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Emulátor Windows Phone](https://msdn.microsoft.com/library/ff402563(v=VS.92).aspx)
