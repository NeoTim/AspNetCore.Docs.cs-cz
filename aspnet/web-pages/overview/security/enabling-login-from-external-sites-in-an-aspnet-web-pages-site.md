---
uid: web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
title: Přihlášení pomocí externí weby v ASP.NET Web stránky webu (Razor) | Dokumentace Microsoftu
author: Rick-Anderson
description: Tento článek vysvětluje, jak se přihlaste k webu webových stránek ASP.NET (Razor) pomocí služby Facebook, Google, Twitter, Yahoo a jiných lokalitách – to znamená, jak podporovat...
ms.author: riande
ms.date: 02/21/2014
ms.assetid: ef852096-a5bf-47b3-9945-125cde065093
msc.legacyurl: /web-pages/overview/security/enabling-login-from-external-sites-in-an-aspnet-web-pages-site
msc.type: authoredcontent
ms.openlocfilehash: 188a9203ba7b04f5a88d0f802f1a05bf35d58d8c
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021128"
---
<a name="logging-in-using-external-sites-in-an-aspnet-web-pages-razor-site"></a>Přihlášení pomocí externí weby na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek vysvětluje, jak se přihlaste k webu webových stránek ASP.NET (Razor) pomocí služby Facebook, Google, Twitter, Yahoo a jiných lokalitách – to znamená, jak podporovat OAuth a OpenID ve vaší lokalitě.
> 
> Co se dozvíte:
> 
> - Postup povolení přihlášení z jiných webů, když použijete šablonu Starter web služby WebMatrix.
> 
> Toto je funkce technologie ASP.NET zavedené v následujícím článku:
> 
> - `OAuthWebSecurity` Pomocné rutiny.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Rozhraní ASP.NET Web Pages (Razor) 2
> - Služba WebMatrix 3

Zahrnuje podporu pro rozhraní ASP.NET Web Pages [OAuth](http://oauth.net/) a [OpenID](http://openid.net/) poskytovatelů. Pomocí těchto zprostředkovatelů, můžete nechat přihlášení uživatelů do vašeho webu pomocí existujících přihlašovacích údajů z Facebooku, Twitteru, Microsoft a Googlu. K přihlášení pomocí účtu sítě Facebook, například uživatele můžete prostě vybrat ikonu Facebooku, který přesměruje na přihlašovací stránku Facebooku, kde jsou informace o uživateli zadat. Přihlášení k Facebooku, pak můžete přiřadit ke svému účtu na webu. Související vylepšení funkcí členství webové stránky je, že uživatelé mohou přidružit více přihlašovací jména (včetně přihlašování ze sociálních sítí) pomocí jednoho účtu na vašem webu.

Tento obrázek ukazuje na přihlašovací stránku z **Starter Site** šablony, ve kterém může uživatel vybrat Facebook, Twitter, Google nebo Microsoft ikonu povolíte protokolování s využitím externí účet:

![externí zprostředkovatele](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image1.png)

Můžete povolit OAuth a OpenID členství uncommenting pár řádků kódu **Starter Site** šablony. Metody a vlastnosti můžete použít pro práci s OAuth a OpenID zprostředkovatelé jsou v `WebMatrix.Security.OAuthWebSecurity` třídy. **Starter Site** Šablona zahrnuje úplné členství infrastruktury, s přihlašovací stránky, databáze členství a veškerý kód, je potřeba nechat přihlášení uživatelů do vašeho webu pomocí pověření pro místní nebo z jiné lokality .

Tato část poskytuje příklad toho, jak umožnit uživatelům přihlášení z externích webů k lokalitě, která je založena na **Starter Site** šablony. Po vytvoření první web, můžete postupujte (podrobnosti):

- Weby, které používají zprostředkovatele OAuth (Facebook, Twitter a Microsoft) vytvořit aplikaci na externí web. To vám dává klíče aplikace, které budete potřebovat k vyvolání funkce přihlášení pro tyto lokality.
- Pro servery, které využívají poskytovatele OpenID (Google) není nutné k vytvoření aplikace. Pro všechny tyto servery musí mít účet k přihlášení a k vytváření aplikací pro vývojáře.

    > [!NOTE]
    > Aplikace Microsoftu přijímat pouze za provozu adresu URL pro webovou stránku, funkční tak adresy URL místního webu nelze použít pro testování přihlášení.
- Pokud chcete zadat poskytovatele příslušný ověřovací upravit několik souborů na vašem webu a odeslat přihlášení k webu, kterou chcete použít.

Tento článek poskytuje zvláštní pokyny pro následující úlohy:

- [Povolení přihlášení Google](#To_enable_Google_logins)
- [Povolení přihlášení k Facebooku](#To_enable_Facebook_logins)
- [Povolení přihlášení k Twitteru](#To_enable_Twitter_logins)

<a id="To_enable_Google_logins"></a>
## <a name="enabling-google-logins"></a>Povolení přihlášení Google

1. Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.
2. Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář u následující řádek kódu. 

    [!code-css[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample1.css)]

### <a name="testing-google-login"></a>Testování Google přihlášení

1. Spustit *stránku default.cshtml* stránku vašeho webu a zvolte **přihlášení** tlačítko.
2. Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte buď **Google** nebo **Yahoo** tlačítko Odeslat. Tento příklad používá přihlášení Google. 

    Webové stránky přesměruje požadavek na přihlašovací stránku služby Google.

    ![Google přihlášení](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image2.png)
3. Zadejte přihlašovací údaje pro účet Google.
4. Pokud Google se zeptá, jestli chcete povolit *Localhost* Pokud chcete informace z účtu, klikněte na tlačítko **povolit**.

    Kód používá Google token k ověření uživatele a vrátí na tuto stránku na vašem webu. Tato stránka umožňuje uživatelům přihlášení jejich Google přidružit účet na webu, nebo se můžete zaregistrovat nový účet na webu pro přidružení externí přihlášení s.

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image3.png)
5. Zvolte **přidružit** tlačítko. Prohlížeč se vrátí na domovskou stránku vaší aplikace.

<a id="To_enable_Facebook_logins"></a>
## <a name="enabling-facebook-logins"></a>Povolení přihlášení k Facebooku

1. Přejděte [webu vývojáře služby Facebook](https://developers.facebook.com/apps) (v případě se přihlaste se ještě nepřihlásili).
2. Zvolte **vytvořit novou aplikaci** tlačítko a pak postupujte podle výzev a pojmenujte a vytvořit novou aplikaci.
3. V části **vyberte, jak se vaše aplikace bude integrovat s Facebook**, zvolte **webu** oddílu.
4. Vyplňte **adresa URL webu** pole s adresou URL vašeho webu (například `http://www.example.com`). **Domény** pole je volitelné; může být využit k zajištění ověřování pro celou doménu (například *example.com*). 

    > [!NOTE]
    > Pokud používáte v místním počítači pomocí adresy URL webu, jako jsou `http://localhost:12345` (kde číslo je číslo místního portu), můžete přidat tuto hodnotu **adresa URL webu** pole pro testování webu. Však kdykoli číslo portu změny vaší místní sítě, budete muset aktualizovat **adresa URL webu** pole vaší aplikace.
5. Zvolte **uložit změny** tlačítko.
6. Zvolte **aplikace** kartu znovu a potom zobrazit úvodní stránku pro vaši aplikaci.
7. Kopírovat **ID aplikace** a **tajný kód aplikace** hodnoty pro vaši aplikaci a vložte je do dočasné textového souboru. Tyto hodnoty předá k poskytovateli služby Facebook v kódu vašeho webu.
8. Ukončete webu pro vývojáře služby Facebook.

Teď provedete změny dvě stránky na webu tak, aby uživatelé mohli přihlásit k webu pomocí svých účtů služby Facebook.

1. Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.
2. Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář kódu pro zprostředkovatele OAuth pro Facebook. Blok neokomentovaném textu kódu vypadá takto: 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample2.cs)]
3. Kopírovat **ID aplikace** hodnotu z aplikace Facebook jako hodnotu `appId` parametrů (v uvozovkách).
4. Kopírování **tajný kód aplikace** hodnotu z aplikace Facebook, jako `appSecret` hodnotu parametru.
5. Soubor uložte a zavřete.

### <a name="testing-facebook-login"></a>Testování přihlášení k Facebooku

1. Spuštění tohoto webu *stránku default.cshtml* stránky a zvolte **přihlášení** tlačítko.
2. Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte **Facebook** ikonu. 

    Webové stránky přesměruje požadavek na přihlašovací stránku Facebooku.

    ![OAuth 2](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image4.png)
3. Přihlaste se ke Facebookový účet. 

    Kód používá token služby Facebook pro ověření a vrátí na stránku, kde můžete přidružit k přihlášení k Facebooku přihlašovací jméno vašeho webu. Uživatelského jména nebo e-mailové adresy se naplní do **e-mailu** pole ve formuláři.

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image5.png)
4. Zvolte **přidružit** tlačítko. 

    Vrátí prohlížeči na domovskou stránku a jste přihlášení.

<a id="To_enable_Twitter_logins"></a>
## <a name="enabling-twitter-logins"></a>Povolení přihlášení k Twitteru

1. Přejděte [webu vývojáře Twitteru](https://dev.twitter.com/).
2. Zvolte **vytvořit aplikaci** odkaz a přihlaste se do lokality.
3. Na **vytvořit aplikaci** formuláře, vyplňte **název** a **popis** pole.
4. V **webu** pole, zadejte adresu URL vašeho webu (například `http://www.example.com`). 

    > [!NOTE]
    > Pokud testujete místně webu (pomocí adresy URL `http://localhost:12345`), Twitter, nemusí přijmout adresu URL. Nicméně je možné používat zpětnou smyčku místní IP adresu (například `http://127.0.0.1:12345`). To zjednodušuje proces testování vaší aplikace v místním prostředí. Ale pokaždé, když se mění číslo portu z místní lokality, bude nutné aktualizovat **webu** pole vaší aplikace.
5. V **adresu URL zpětného volání** pole, zadejte adresu URL pro stránku na vašem webu, kterou mají uživatelé k vrácení po přihlášení na Twitteru. Například pokud chcete odeslat uživatelů na domovskou stránku Starter Site (který rozpozná stav jejich přihlášení), zadejte stejnou adresu URL, kterou jste zadali v **webu** pole.
6. Přijměte podmínky, zvolte **vytvoření aplikace Twitter** tlačítko.
7. Na **Moje aplikace** úvodní stránka, zvolte aplikaci, kterou jste vytvořili.
8. Na **podrobnosti** , posuňte dolů a klikněte na příkaz **vytvořit My Access Token** tlačítko.
9. Na **podrobnosti** kartu, zkopírujte **uživatelský klíč** a **uživatelský tajný klíč** hodnoty pro vaši aplikaci a vložte je do dočasné textového souboru. Tyto hodnoty budete předat poskytovatele Twitteru ve vašem kódu webu.
10. Ukončení serveru Twitter.

Teď provedete změny dvě stránky na webu tak, aby uživatelé se nebudou moct přihlásit k webu pomocí svých účtů na Twitteru.

1. Vytvořit nebo otevřít web rozhraní ASP.NET Web Pages, který je založen na šabloně Starter web služby WebMatrix.
2. Otevřít  *\_AppStart.cshtml* stránce a zrušte komentář kódu pro zprostředkovatele OAuth pro Twitter. Blok neokomentovaném textu kódu vypadá takto: 

    [!code-csharp[Main](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/samples/sample3.cs)]
3. Kopírovat **uživatelský klíč** hodnota Twitter application jako hodnotu `consumerKey` parametrů (v uvozovkách).
4. Kopírovat **uživatelský tajný klíč** hodnota Twitter application jako hodnotu `consumerSecret` parametru.
5. Soubor uložte a zavřete.

### <a name="testing-twitter-login"></a>Testování Twitteru přihlášení

1. Spustit *stránku default.cshtml* stránku vašeho webu a zvolte **přihlášení** tlačítko.
2. Na *přihlášení* stránku, **přihlášení pomocí jiné služby** zvolte **Twitteru** ikonu. 

    Webové stránky přesměruje požadavek na stránku Twitteru přihlášení pro aplikaci, kterou jste vytvořili.

    ![OAuth 4](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image6.png)
3. Přihlaste se ke účtu sítě Twitter.
4. Tento kód používá token služby Twitter. k ověření uživatele a pak se vrátíte na stránku ve kterém můžete přidružit vaše přihlášení pomocí svého účtu Web. Jména nebo e-mailové adresy se naplní do **e-mailu** pole ve formuláři.

    ![OAuth 5](enabling-login-from-external-sites-in-an-aspnet-web-pages-site/_static/image7.png)
5. Zvolte **přidružit** tlačítko. 

    Vrátí prohlížeči na domovskou stránku a jste přihlášení.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


- [Přizpůsobení chování v celém webu](https://go.microsoft.com/fwlink/?LinkId=202906)
- [Přidání zabezpečení a s členstvím na ASP.NET Web Pages](https://go.microsoft.com/fwlink/?LinkID=202904)
