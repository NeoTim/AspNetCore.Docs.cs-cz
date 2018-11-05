---
uid: web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
title: Přidání sociálních sítí na rozhraní ASP.NET Web Pages servery (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tato kapitola vysvětluje, jak integrovat svůj web pomocí služby pro sociální sítě. V této kapitole se dozvíte, jak umožnit lidem/odkazu na záložku webu...
ms.author: riande
ms.date: 02/21/2014
ms.assetid: 03c342f9-b35c-4d7c-b9ed-cd9aaaffedb6
msc.legacyurl: /web-pages/overview/getting-started/13-adding-social-networking-to-your-web-site
msc.type: authoredcontent
ms.openlocfilehash: d2b970e7b80e4129d0a912f648f9c4a54df531b2
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021154"
---
<a name="adding-social-networking-to-aspnet-web-pages-razor-sites"></a>Přidání sociálních sítí pro ASP.NET Web Pages servery (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje postup přidání sociálních sítí odkazy pro Facebook, Twitter, Reddit a Digg na stránky na webu rozhraní ASP.NET Web Pages (Razor) a jak zahrnout Twitterové kanály, karty hráče Xbox a Gravatar obrázků.
> 
> Co se dozvíte:
> 
> - Jak umožnit lidem záložku nebo propojení vaší lokality.
> - Jak přidat informační kanál sítě Twitter.
> - Postup přidání Facebook **jako** tlačítko na stránky.
> - Jak lze vykreslit Gravatar.com imagí.
> - Jak zobrazit pomocí karty hráče Xbox ve vaší lokalitě.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Technologie ASP.NET webové pomocné knihovny (balíček NuGet)
>   
> 
> V tomto kurzu funguje taky s 3 webových stránek ASP.NET, s výjimkou částí, které používají knihovnu ASP.NET Web pomocné rutiny.


<a id="Linking_Your_Website"></a>
## <a name="linking-your-website-on-social-networking-sites"></a>Propojení svůj web na weby sociálních sítí

Pokud se lidé, jako je něco na webu, často chtějí sdílet s přáteli. Můžete provést to snadno zobrazením piktogramů (ikon), které lze kliknout na sdílet stránku na Digg, Reddit, Facebook, Twitter nebo podobné servery.

Chcete-li zobrazit tyto piktogramy, přidejte `LinkSharecode` pomocné rutiny na stránku. Uživatelé, kteří navštíví stránku můžete kliknout na jednotlivé glyfů. Pokud mají účet s sociální síťové lokality, se pak odeslat odkaz na stránku dané lokality.

![Obrázek 1](13-adding-social-networking-to-your-web-site/_static/image1.jpg)

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud už jste nepřidali ní – vytvoření stránky s názvem *ListLinkShare.cshtml* a přidat Následující kód:

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample1.cshtml)]

    V tomto příkladu když `LinkShare` spuštění pomocné rutiny, název stránky je předán jako parametr, který pak předá název stránky na sociálních sítí Web. Ale mohli předat v libovolný řetězec, který chcete. Tento příklad také určuje, jaké weby sociálních sítí mají být zahrnuty do seznamu. Můžete zadat sociálních sítí, které se vztahují na váš web.
2. Spustit *ListLinkShare.cshtml* stránku v prohlížeči. (Ujistěte se, že je vybrána na stránce v **soubory** pracovního prostoru před jeho spuštěním.)
3. Klikněte na tlačítko glyfů pro jednu z nich, které jste zaregistrovaní na. Odkaz přejdete na stránku na webu vybrané sociálních sítí, kde můžete sdílet odkaz. Například pokud kliknete na odkaz Reddit, potom se přesunete na `submit to reddit` stránky na webu Reddit.

     ![Obrázek 2](13-adding-social-networking-to-your-web-site/_static/image2.jpg)

<a id="Adding_a_Twitter_Feed"></a>
## <a name="adding-a-twitter-feed"></a>Přidání na twitteru přes kanál

Informace o použití pomocné rutiny na Twitteru, který je kompatibilní s aktuální verzí rozhraní Twitter API najdete v tématu [Pomocník Twitter](../ui-layouts-and-themes/twitter-helper.md). Tento příklad ukazuje, jak psát vlastní pomocné rutiny, můžete snadno opakovaně použít kód z mnoho stránek.

<a id="Displaying_a_Facebook_Button"></a>
## <a name="displaying-a-facebook-quotlikequot-button"></a>Zobrazení Facebook &quot;jako&quot; tlačítko

V některých případech je nejlepší možnost získat kód přímo ze sociálních sítí poskytovatele spíše než spoléhání se na pomocné rutiny. To platí zejména pokud poskytovateli sociální sítě aktualizuje jeho možnosti rychleji, než se aktualizuje pomocné rutiny.

Přidávání funkcí Facebooku (jako je tlačítko) do vaší lokality, můžete načíst fragmentů kódu z [developers.facebook.com](https://developers.facebook.com/) lokality. Na stránce Facebooku pomocí svých nástrojů se ke generování fragmentu kódu, které se týkají vašeho webu.

Následující zvýrazněný kód je kód, který byl načten z nástroje jako tlačítko na webu developers.facebook.com. Je nutné zadat vlastní ID aplikace.

[!code-html[Main](13-adding-social-networking-to-your-web-site/samples/sample2.html?highlight=7-14,16-17)]

<a id="Rendering_a_Gravatar_Image"></a>
## <a name="rendering-a-gravatar-image"></a>Vykreslování obrázků Gravatar

A *Gravatar* ( &quot;globálně uznávané avatar&quot;) je obrázek, který může sloužit jako svou miniaturu na více webech &#8212; tedy image, která představuje vám. Například můžete Gravatar identifikaci osoby v příspěvku fóra, do komentáře na blogu a tak dále. (Můžete zaregistrovat na webu Gravatar na vlastní Gravatar [ http://www.gravatar.com/ ](http://www.gravatar.com/).) Pokud chcete zobrazit obrázky vedle jména nebo e-mailové adresy lidí na vašem webu, můžete použít Gravatar pomocné rutiny.

V tomto příkladu používáte jeden Gravatar, který představuje sami. Dalším způsobem, jak používat Gravatar je umožnit lidem, zadejte jeho adresu Gravatar po jejich registraci ve vaší lokalitě. (Můžete zjistěte, jak umožnit lidem zaregistrovat v [přidání zabezpečení a s členstvím na server webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202904).) Pokaždé, když zobrazíte informace pro tohoto uživatele, můžete přidat jenom Gravatar kde zobrazit uživatelské jméno.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
2. Vytvoření nové webové stránky s názvem *Gravatar.cshtml*.
3. Přidejte následující kód do souboru: 

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample3.cshtml)]

    `Gravatar.GetHtml` Metoda zobrazí obrázek Gravatar na stránce. Chcete-li změnit velikost bitové kopie, může obsahovat číslo jako druhý parametr. Výchozí velikost je 80. Čísla menší než 80 vytvořit bitovou kopii menší. Čísla větší než 80 zvětšit na obrázku.
4. V `Gravatar.GetHtml` metody, nahradí `<Your Gravatar account here>` e-mailovou adresou, který používáte pro svůj účet Gravatar. (Pokud nemáte účet Gravatar, můžete použít e-mailovou adresu osoby, která nepodporuje.)
5. Na stránce spusťte v prohlížeči. Na stránce se zobrazí dvě bitové kopie Gravatar e-mailových adres, který jste zadali. Druhý obrázek je menší než první. 

    ![Obrázek 4](13-adding-social-networking-to-your-web-site/_static/image3.jpg)

<a id="Displaying_an_Xbox_Gamer_Card"></a>
## <a name="displaying-an-xbox-gamer-card"></a>Zobrazení karty, která hráče Xbox

Když uživatelé hry pro Microsoft Xbox online, každý uživatel má jedinečné ID. Statistiky jsou udržovány pro oba hráči v podobě hráče karty, která zobrazuje jejich reputace, skóre hráče a nedávno hrají hry. Pokud jste hráče Xbox, můžete zobrazit hráče karty na stránkách na webu pomocí `GamerCard` pomocné rutiny.

1. Přidejte knihovnu ASP.NET Web Helpers na váš web, jak je popsáno v [instalace pomocné rutiny na webu technologie ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkId=252372), pokud jste tak již neučinili.
2. Vytvoří novou stránku s názvem *XboxGamer.cshtml* a přidejte následující kód.

    [!code-cshtml[Main](13-adding-social-networking-to-your-web-site/samples/sample4.cshtml)]

    Můžete použít `GamerCard.GetHtml` vlastnosti a určit tak alias pro hráče karty, který se má zobrazit.
3. Na stránce spusťte v prohlížeči. Na stránce se zobrazí karta hráče Xbox, který jste zadali.

    ![Obrázek 5](13-adding-social-networking-to-your-web-site/_static/image4.jpg)
