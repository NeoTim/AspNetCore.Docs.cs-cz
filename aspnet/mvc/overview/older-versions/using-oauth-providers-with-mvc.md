---
uid: mvc/overview/older-versions/using-oauth-providers-with-mvc
title: Použití poskytovatelů OAuth v MVC 4 | Dokumentace Microsoftu
author: Rick-Anderson
description: V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET MVC 4, který umožňuje uživatelům přihlásit se pomocí přihlašovacích údajů z externího poskytovatele, jako je například Facebo...
ms.author: riande
ms.date: 06/19/2013
ms.assetid: 7a87f16f-0e19-4f15-a88a-094ae866c4a2
msc.legacyurl: /mvc/overview/older-versions/using-oauth-providers-with-mvc
msc.type: authoredcontent
ms.openlocfilehash: d0203b62c911056fc56ed103c1c42f67816cbbf0
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021752"
---
<a name="using-oauth-providers-with-mvc-4"></a>Použití poskytovatelů OAuth v MVC 4
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> V tomto kurzu se dozvíte, jak vytvořit webovou aplikaci ASP.NET MVC 4, který umožňuje uživatelům přihlášení pomocí přihlašovacích údajů z externího poskytovatele, jako je Facebook, Twitter, Microsoft nebo Google a pak do ní některé funkce z těchto zprostředkovatelů do vaší Webová aplikace. Pro zjednodušení tento kurz se zaměřuje na práci s přihlašovacími údaji ze sítě Facebook.
> 
> Externí přihlašovací údaje používané k webové aplikaci ASP.NET MVC 5, naleznete v tématu [vytvoření aplikace ASP.NET MVC 5 pomocí Facebooku a Google OAuth2 nebo OpenID Sign-on](../security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
> 
> Povolení tyto přihlašovací údaje ve webových stránek poskytuje výraznou výhodu, protože milionům uživatelů již mají účty u těchto externích poskytovatelů. Tito uživatelé mohou být více ochotni zaregistrujte svůj web, pokud nemají k vytvoření a zapamatovat si novou sadu přihlašovacích údajů. Navíc po přihlášení uživatele prostřednictvím některého z těchto zprostředkovatelů, můžete začlenit sociální operace od poskytovatele.


## <a name="what-youll-build"></a>Co budete vytvářet

V tomto kurzu existují dva hlavní cíle:

1. Povolte tak uživateli přihlášení pomocí přihlašovacích údajů od poskytovatele OAuth.
2. Načíst informace o účtu ze zprostředkovatele a integrovat tyto informace se registrace účtu pro vašeho webu.

I když se v příkladech v tomto kurzu se zaměřit na jako zprostředkovatel ověřování pomocí Facebooku, můžete upravit kód, který použije některý zprostředkovatele. Postup implementace jakýkoli poskytovatel jsou velmi podobné kroky, které se zobrazí v tomto kurzu. Lze si pouze všimnout významné rozdíly při vytvoření přímého volání rozhraní API poskytovatele nastavení.

## <a name="prerequisites"></a>Požadavky

- [Microsoft Visual Studio 2012](https://www.microsoft.com/visualstudio/eng/downloads#vs) nebo [Microsoft Visual Studio Express 2012 pro Web](https://www.microsoft.com/visualstudio/eng/downloads#d-2012-express)

Nebo

- Microsoft Visual Studio 2010 SP1 nebo [Visual Web Developer Express 2010 SP1](https://www.microsoft.com/visualstudio/eng/downloads#d-2010-express)
- [ASP.NET MVC 4](https://go.microsoft.com/fwlink/?LinkId=243392)

Kromě toho toto téma předpokládá, že máte základní znalosti o ASP.NET MVC a sady Visual Studio. Pokud potřebujete Úvod do ASP.NET MVC 4, přečtěte si [Úvod do ASP.NET MVC 4](getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md).

## <a name="create-the-project"></a>Vytvoření projektu

V sadě Visual Studio vytvořte novou aplikaci ASP.NET MVC 4 Web s názvem &quot;OAuthMVC&quot;. Můžete cílit rozhraní .NET Framework 4.5 nebo 4.

![Vytvoření projektu](using-oauth-providers-with-mvc/_static/image1.png)

V okně nového projektu ASP.NET MVC 4 vyberte **internetovou aplikaci** a nechat **Razor** jako zobrazovací modul.

![Vyberte Internetové aplikace.](using-oauth-providers-with-mvc/_static/image2.png)

## <a name="enable-a-provider"></a>Povolit zprostředkovatele

Při vytváření webové aplikace MVC 4 s šablonou Internetové aplikace se projekt vytvoří se soubor s názvem AuthConfig.cs v aplikaci\_spouštěcí složka.

![Soubor AuthConfig](using-oauth-providers-with-mvc/_static/image3.png)

AuthConfig soubor obsahuje kód pro registraci klientů u zprostředkovatele externího ověřování. Ve výchozím nastavení, tento kód je zakomentovaný, takže žádná externí zprostředkovatele není povolena.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample1.cs)]

Musíte zrušením komentáře u tohoto kódu pro použití externího ověřování klienta. Zrušením komentáře u zprostředkovatelů, které chcete zahrnout do vaší lokality. Pro účely tohoto kurzu vám umožní pouze pověření služby Facebook.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample2.cs)]

Všimněte si, že v příkladu výše, že metoda obsahuje prázdné řetězce pro registračních parametrů. Pokud se pokusíte spustit nyní aplikaci, aplikace vyvolá výjimku argument, protože nejsou povolené prázdné řetězce pro parametry. Poskytnout platné hodnoty, musíte zaregistrovat svůj web u externích poskytovatelů, jak je znázorněno v následující části.

## <a name="registering-with-an-external-provider"></a>Registrace u externího poskytovatele

K ověřování uživatelů pomocí přihlašovacích údajů z externího poskytovatele, musíte zaregistrovat svůj web s tímto poskytovatelem. Při registraci vašeho webu, zobrazí se parametry (například klíč nebo id a tajný klíč) Pokud chcete zahrnout při registraci klienta. Účet musí mít s poskytovateli, kterou chcete použít.

Tento kurz neukazuje všechny kroky, které musíte provést při registraci těchto poskytovatelů. Kroky nejsou obvykle obtížné. Zajištění úspěšné registrace vašeho webu, postupujte podle pokynů uvedených v těchto lokalitách. Abyste mohli začít s registrací vašeho webu, najdete v článku webu pro vývojáře pro:

- [Facebook](https://developers.facebook.com/)
- [Google](https://developers.google.com/)
- [Microsoft](http://manage.dev.live.com/)
- [Twitter](https://dev.twitter.com/)

Při registraci vašeho webu pomocí Facebooku, můžete zadat &quot;localhost&quot; domény, lokality a `&quot; http://localhost/&quot;` pro adresu URL, jak je znázorněno na následujícím obrázku. Pomocí místního hostitele spolupracuje s poskytovateli většinu, ale aktuálně nefunguje pro zprostředkovatele společnosti Microsoft. Pro poskytovatele Microsoft uvést adresu URL webu platná.

![registrace serveru](using-oauth-providers-with-mvc/_static/image4.png)

Na předchozím obrázku byly odebrány hodnoty pro id aplikace, tajný klíč aplikace a kontaktní e-mailu. Při registraci ve skutečnosti vašeho webu, tyto hodnoty budou k dispozici. Můžete si hodnoty pro id aplikace a tajný kód aplikace, vzhledem k tomu, které přidáte do vaší aplikace.

## <a name="creating-test-users"></a>Vytvoření testovacích uživatelů

Pokud vám nevadí, otestujte svůj web pomocí existujícího účtu sítě Facebook, můžete tuto část přeskočit.

Můžete snadno vytvořit testovací uživatele pro vaši aplikaci v rámci stránky správy aplikace Facebook. Můžete je použít testovací účty pro přihlášení k webu. Vytvoření testovacích uživatelů kliknutím **role** v levém navigačním podokně a klepněte na odkaz **vytvořit** odkaz.

![Vytvoření testovacích uživatelů](using-oauth-providers-with-mvc/_static/image5.png)

Facebook lokality automaticky vytvoří počet testovací účty, které požadujete.

## <a name="adding-application-id-and-secret-from-the-provider"></a>Přidání id aplikace a tajný kód od poskytovatele

Teď, když jste obdrželi id a tajný kód ze sítě Facebook, vraťte se do souboru AuthConfig a přidejte je jako hodnoty parametrů. Hodnoty zobrazené níže nejsou skutečné hodnoty.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample3.cs)]

## <a name="log-in-with-external-credentials"></a>Přihlaste se pomocí přihlašovacích údajů externí

To je všechno, co musíte udělat, abyste povolte externí přihlašovací údaje na vašem webu. Spusťte aplikaci a klikněte na odkaz přihlášení v pravém horním rohu. Šablona automaticky rozpozná registrovány jako poskytovatel sítě Facebook a obsahuje tlačítko pro zprostředkovatele. Když si zaregistrujete několik zprostředkovatelů, tlačítko pro každé z nich je automaticky zahrnuty.

![externí přihlášení](using-oauth-providers-with-mvc/_static/image6.png)

V tomto kurzu ale nenajdete, jak přizpůsobit přihlašovací tlačítka pro externí poskytovatele. Informace najdete v tématu [přizpůsobení uživatelské rozhraní pro přihlášení při použití účtu OAuth/OpenID](https://blogs.msdn.com/b/pranav_rastogi/archive/2012/08/24/customizing-the-login-ui-when-using-oauth-openid.aspx).

Klikněte na tlačítko Facebooku se chcete přihlásit pomocí přihlašovacích údajů k Facebooku. Při výběru jednoho z externích poskytovatelů, jsou přesměrován na dané lokalitě a tyto služby výzva k přihlášení.

Následující obrázek ukazuje na přihlašovací obrazovce pro Facebook. Poznamenává, že používáte Facebookový účet k přihlášení na web s názvem oauthmvcexample.

![ověřování facebooku](using-oauth-providers-with-mvc/_static/image7.png)

Po přihlášení pomocí přihlašovacích údajů k Facebooku, na stránce informuje uživatele, že web bude mít přístup k základní informace.

![žádost o oprávnění](using-oauth-providers-with-mvc/_static/image8.png)

Po výběru **přejít do aplikace**, uživatel musí zaregistrovat pro lokalitu. Následující obrázek ukazuje na registrační stránku po uživatel přihlásil se pomocí přihlašovacích údajů k Facebooku. Uživatelské jméno je obvykle předem vyplněná název od poskytovatele.

![register](using-oauth-providers-with-mvc/_static/image9.png)

Klikněte na tlačítko **zaregistrovat** dokončit registraci. Zavřete prohlížeč.

Uvidíte, že byl přidán nový účet k vaší databázi. V Průzkumníku serveru, otevřete **objekt DefaultConnection** databáze a otevřete **tabulky** složky.

![tabulky databáze](using-oauth-providers-with-mvc/_static/image10.png)

Klikněte pravým tlačítkem myši **UserProfile** tabulce a vybrat **zobrazit Data tabulky**.

![zobrazení dat](using-oauth-providers-with-mvc/_static/image11.png)

Zobrazí se nový účet, který jste přidali. Podívejte se na data v **webová stránka\_OAuthMembership** tabulky. Zobrazí se další data související s externí zprostředkovatele pro účet, který jste právě přidali.

Pokud chcete povolit externí ověřování, jste hotovi. Ale můžete dál integrovat informace od poskytovatele do nový proces registrace uživatele, jak je znázorněno v následujících částech.

## <a name="create-models-for-additional-user-information"></a>Vytváření modelů pro dalších informací o uživatelích

Protože jste si všimli v předchozích částech, není potřeba žádné další informace pro registraci předdefinovaný účet pro práci. Většina externích poskytovatelů však předat zpět Další informace o uživateli. Následující části vysvětlují, jak zachovat tyto informace a uložte ho do databáze. Konkrétně si zachovají hodnoty pro celé jméno uživatele, identifikátor URI uživatele osobní webové stránky a hodnotu, která označuje, zda má Facebooku ověřit účet.

Budete používat [migrace Code First](https://msdn.microsoft.com/data/jj591621) přidat tabulku pro ukládání dalších informací o uživatelích. V tabulce přidáváte do existující databáze, proto musíte nejdřív vytvořit snímek aktuální databázi. Vytvořením snímek aktuální databázi, můžete později vytvořit migraci, která obsahuje pouze nové tabulky. Chcete-li vytvořit snímek aktuální databázi:

1. Otevřít **Konzola správce balíčků**
2. Spusťte příkaz **povolení migrace**
3. Spusťte příkaz **přidat migrace počáteční – IgnoreChanges**
4. Spusťte příkaz **aktualizace databáze**

Nyní přidáte nové vlastnosti. Ve složce modely otevřete soubor AccountModels.cs a najít RegisterExternalLoginModel třídu. Třída RegisterExternalLoginModel obsahuje hodnoty, které vrátit ze zprostředkovatele ověřování. Přidáte vlastnosti s názvem FullName a odkaz, jak je zdůrazněno níže.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample4.cs?highlight=9-13)]

Také v AccountModels.cs, přidejte novou třídu s názvem ExtraUserInformation. Tato třída představuje nové tabulky, který se vytvoří v databázi.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample5.cs)]

Ve třídě UsersContext přidáte zvýrazněný kód uvedený níže pro vytvoření vlastnosti DbSet pro novou třídu.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample6.cs?highlight=9)]

Nyní jste připraveni vytvořit novou tabulku. Otevřete konzolu Správce balíčků znovu a tentokrát:

1. Spusťte příkaz **AddExtraUserInformation přidat migrace**
2. Spusťte příkaz **aktualizace databáze**

Nová tabulka nyní v databázi existuje.

## <a name="retrieve-the-additional-data"></a>Načíst další data.

Existují dva způsoby, jak načíst další uživatelská data. Prvním způsobem je chcete zachovat data uživatele, který je předán zpět, ve výchozím nastavení, během žádosti o ověření. Druhý způsob je konkrétně volat rozhraní API poskytovatele a požádat o další informace. Hodnoty pro jméno a příjmení a odkazu jsou automaticky předány zpět pomocí Facebooku. Přistoupíte přímo pomocí volání rozhraní API Facebooku se načte hodnotu určující, zda má Facebooku ověřit účet. Nejprve bude vyplněné hodnoty pro jméno a příjmení a odkaz a později získáte hodnotu ověřené.

Chcete-li načíst další uživatelská data, otevřete **AccountController.cs** soubor **řadiče** složky.

Tento soubor obsahuje logiku pro protokolování, registrace a správy účtů. Zejména, Všimněte si, že volání metody **ExternalLoginCallback** a **ExternalLoginConfirmation**. V rámci těchto metod můžete přidat kód pro přizpůsobení operací externí přihlášení pro vaši aplikaci. První řádek **ExternalLoginCallback** metoda obsahuje:

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample7.cs)]

Další uživatelská data je předán **ExtraData** vlastnost **AuthenticationResult** objekt, který je vrácen z **VerifyAuthentication** metody. Klient sítě Facebook obsahuje následující hodnoty **ExtraData** vlastnost:

- id
- name
- Odkaz
- pohlaví
- accesstoken

Ostatní zprostředkovatelé budou mít podobné, ale mírně odlišné data ve vlastnosti ExtraData.

Pokud uživatel je nový na váš web, bude načítat některé další data a předat data do zobrazení potvrzení. Poslední blok kódu v metodě se spustí pouze v případě, že uživatel je nový na váš web. Nahraďte následující řádek:

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample8.cs)]

Tento řádek:

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample9.cs)]

Tato změna zahrnuje pouze hodnoty vlastností FullName a odkaz.

V **ExternalLoginConfirmation** metoda, upravte kód zvýraznily níže můžete uložit dalších informací o uživatelích.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample10.cs?highlight=4,7-13)]

## <a name="adjusting-the-view"></a>Úprava zobrazení

Zobrazí se další uživatelská data, která se načítají ze zprostředkovatele na stránce registrace.

V **zobrazení**/**účet** složku, otevřete **ExternalLoginConfirmation.cshtml**. Pod existující pole pro uživatelské jméno přidejte pole pro jméno a příjmení, propojení a PictureLink.

[!code-cshtml[Main](using-oauth-providers-with-mvc/samples/sample11.cshtml)]

Nyní jste téměř připraveni aplikaci spustit a zaregistrovat nového uživatele s dodatečnými informacemi, které jsou uloženy. Musíte mít účet, který není serveru již zaregistrován. Můžete buď použití různých testovacích účtu nebo odstranit řádky v tabulce **UserProfile** a **webové stránky\_OAuthMembership** tabulky pro účet, který chcete znovu použít. Odstraněním tyto řádky zajistí, že účet je zaregistrovat znovu.

Spusťte aplikaci a zaregistrovat nové uživatele. Všimněte si, že tentokrát stránku potvrzení obsahuje více hodnot.

![register](using-oauth-providers-with-mvc/_static/image12.png)

Po dokončení registrace, ukončete prohlížeč. Podívejte se v databázi a Všimněte si nových hodnot v **ExtraUserInformation** tabulky.

## <a name="install-nuget-package-for-facebook-api"></a>Nainstalujte balíček NuGet pro rozhraní API Facebooku

Poskytuje služby Facebook [API](https://developers.facebook.com/docs/reference/apis/) , že můžete volat k provádění operací. Můžete volat rozhraní API Facebooku přesměrováním odesílání požadavků HTTP nebo pomocí instalace balíčku NuGet, který usnadňuje odesílání těchto požadavků. Pomocí balíčku NuGet se zobrazí v tomto kurzu, ale instalace NuGet balíček není nezbytné. Tento kurz ukazuje, jak použít balíček SDK Facebooku jazyk C#. Existují další balíčky NuGet, které vám pomůže s volání rozhraní API sítě Facebook.

Z **spravovat balíčky NuGet** windows, vyberte balíček SDK Facebooku jazyk C#.

![instalovat balíček](using-oauth-providers-with-mvc/_static/image13.png)

Facebook C# SDK bude používat k volání operace, která vyžaduje přístupový token pro daného uživatele. V další části ukazuje, jak získat přístupový token.

## <a name="retrieve-access-token"></a>Načtení přístupového tokenu

Většina externích poskytovatelů předávání zpátky přístupový token po ověření přihlašovacích údajů uživatele. Tento přístupový token je velmi důležité, protože umožňuje volat operace, které jsou dostupné pouze pro ověřené uživatele. Načítání a ukládání přístupového tokenu je proto zásadní Pokud byste chtěli poskytnout více funkcí.

V závislosti na externího zprostředkovatele může být přístupový token platný pro pouze omezené množství času. Aby bylo zajištěno, že máte platný přístupový token, se budou načítat ho pokaždé, když uživatel přihlásí a uložte ho jako hodnotu relace místo uložení do databáze.

V **ExternalLoginCallback** metoda, přístupový token je také se předat zpátky **ExtraData** vlastnost **AuthenticationResult** objektu. Přidejte zvýrazněný kód, který **ExternalLoginCallback** uložit přístupový token v **relace** objektu. Tento kód spuštěný pokaždé, když se uživatel přihlásí pomocí účtu sítě Facebook.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample12.cs?highlight=11-14)]

Přestože tento příklad načte přístupový token z Facebooku, můžete načíst přístupový token z jakékoli externího poskytovatele pomocí stejný klíč s názvem &quot;accesstoken&quot;.

## <a name="logging-off"></a>Odhlášení

Výchozí hodnota **odhlášení** metoda přihlásí uživatele přesunete mimo svou aplikaci, ale není přihlášení uživatele z externího poskytovatele. Přihlášení uživatele z Facebooku a zabránit token uchování až uživatel odhlásí, můžete přidat následující zvýrazněný kód **odhlášení** metodu AccountController.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample13.cs?highlight=6-14)]

Hodnota je zadat v `next` parametr je adresa URL pro použití po uživatel přihlásí ze sítě Facebook. Při testování v místním počítači, by se správným číslem portu zadejte pro místní lokalitu. V produkčním prostředí by poskytují výchozí stránku, třeba contoso.com.

## <a name="retrieve-user-information-that-requires-the-access-token"></a>Načítání informací o uživatelích, které vyžaduje přístupový token

Teď, když máte uložené přístupový token a nainstalován balíček SDK Facebooku jazyk C#, můžete je použít společně k vyžádání dalších informací o uživatelích ze sítě Facebook. V **ExternalLoginConfirmation** metody vytvoření instance **FacebookClient** třídy předáním hodnoty přístupový token. Požádat o hodnotu **ověřit** vlastnost pro aktuální, ověřeného uživatele. **Ověřit** vlastnost určuje, zda Facebooku ověřila účtu pomocí jiné prostředky, jako je odeslání zprávy na mobilní telefon. Uloží tuto hodnotu v databázi.

[!code-csharp[Main](using-oauth-providers-with-mvc/samples/sample14.cs?highlight=7-18,25)]

Bude nutné znovu odstranit záznamy v databázi pro uživatele, nebo použijte jiný účet Facebook.

Spusťte aplikaci a zaregistrovat nové uživatele. Podívejte se na **ExtraUserInformation** tabulky a zobrazit tak hodnotu pro vlastnost ověřeno.

## <a name="conclusion"></a>Závěr

V tomto kurzu jste vytvořili lokalitu, která je integrovaná se službou Facebook pro ověřování uživatelů a registrační data. Jste se dozvěděli o výchozí chování, který je nastaven pro webové aplikace MVC 4 a tom, jak přizpůsobit výchozí chování.

## <a name="related-topics"></a>Související témata

- [Vytvoření aplikace ASP.NET MVC pomocí ověření a SQL DB a nasazení do služby Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)
