---
uid: identity/overview/getting-started/introduction-to-aspnet-identity
title: Úvod do ASP.NET Identity | Dokumentace Microsoftu
author: jongalloway
description: Systém členství technologie ASP.NET byla zavedena v systémech ASP.NET 2.0 zpět v roce 2005 a protože a aby byla mnoho změn typicall způsoby webové aplikace...
ms.author: riande
ms.date: 01/22/2019
ms.assetid: 38717fc1-5989-43cf-952d-4007cc1dd923
msc.legacyurl: /identity/overview/getting-started/introduction-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 4a545e52d2d9ea04a10c37c116fd326c60de9f8f
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236442"
---
<a name="introduction-to-aspnet-identity"></a>Úvod do ASP.NET Identity
====================

> Systém členství technologie ASP.NET byla zavedena v systémech ASP.NET 2.0 zpět v roce 2005 a protože pak byly provedeny změny v mnoha způsoby webové aplikace obvykle zpracovávat ověřování a autorizace. ASP.NET Identity je čerstvé podívat, co systém členství by měla být při vytváření moderních aplikací pro web, telefon nebo tablet.


## <a name="background-membership-in-aspnet"></a>Na pozadí: Členství v ASP.NET

### <a name="aspnet-membership"></a>Členství v ASP.NET

[Členství technologie ASP.NET](https://msdn.microsoft.com/library/yh26yfzy(v=VS.100).aspx) je navržená k řešení požadavky na členství lokality, které bylo běžné v roce 2005 zahrnuta ověřování pomocí formulářů a databázi serveru SQL Server pro uživatelská jména, hesla a data profilu. V současnosti jsou mnohem širší pole s možností úložiště dat pro webové aplikace a Většina vývojářů chcete povolit své weby, používat poskytovatele sociálních identit pro ověřování a autorizace funkce. Omezení členství technologie ASP.NET návrhu velmi obtížné tohoto přechodu je:

- Schéma databáze je navržená pro SQL Server a nelze ji změnit. Můžete přidat informace o profilu, ale další data je zabalena do jiné tabulky, které je těžké pro přístup k jakýmkoli způsobem s výjimkou prostřednictvím rozhraní API poskytovatele profilu.
- Zprostředkovatel systému umožňuje změnit záložní úložiště dat, ale systém navržené s ohledem na předpoklady, které jsou vhodné pro relační databáze. Můžete napsat zprostředkovatele pro ukládání informací o členství v mechanismu nerelační úložiště, jako jsou tabulky Azure Storage, ale pak budete muset vyřešit relační návrhu napsáním většinu kódu a spoustu `System.NotImplementedException` výjimky pro metody, které ji nemají platí pro databáze NoSQL.
- Protože protokol/odhlášení funkce je založena na ověřování pomocí formulářů, nemůžete použít systém členství [OWIN](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md). OWIN obsahuje komponenty middlewaru ověřování, včetně podpory pro protokol se změnami pomocí externích zprostředkovatelů identity (třeba Accounts Microsoft, Facebook, Google, Twitter) a protokolu se změnami pomocí organizační účty z místní služby Active Directory nebo Azure Active Directory. OWIN také zahrnuje podporu pro OAuth 2.0 JWT a CORS.

### <a name="aspnet-simple-membership"></a>ASP.NET jednoduché členství

[Jednoduché členství technologie ASP.NET](../../../web-pages/overview/security/16-adding-security-and-membership.md) byl vyvinut jako systém členství pro rozhraní ASP.NET Web Pages. Byla vydána s nástrojem WebMatrix a Visual Studio 2010 SP1. Cílem jednoduché členství bylo usnadňují přidat členství funkce aplikace Web Pages.

Jednoduché členství usnadňují přizpůsobit informace profilu uživatele, ale stále sdílí ostatní problémy s členství technologie ASP.NET a má určitá omezení:

- Bylo obtížné zachovat data systému členství ve nerelační úložiště.
- Nelze ho použít s OWIN.
- Nebude fungovat s existující zprostředkovatelů členství technologie ASP.NET a není rozšiřitelný.

### <a name="aspnet-universal-providers"></a>ASP.NET Universal Providers

[Balíček ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx) byly vyvinuty, aby bylo možné zachovat informace o členství ve službě Microsoft Azure SQL Database a jejich fungovat i pro SQL Server Compact. Balíček Universal Providers byly vytvořeny v Entity Framework Code First, což znamená, že balíček Universal Providers je možné uchovávat data v libovolné úložiště podporuje EF. S Universal Providers schéma databáze byla vyčištěna poměrně hodně také.

Balíček Universal Providers jsou postavené na infrastrukturu členství technologie ASP.NET, tak stále provádění stejná omezení jako SqlMembership zprostředkovatele. To znamená že byly navrženy pro relační databáze a je obtížné upravit informace o profilu a uživatele. Tito poskytovatelé pořád použít ověřování pomocí formulářů pro funkci přihlašování a odhlašování.

## <a name="aspnet-identity"></a>ASP.NET Identity

Jako členství scénáře v technologii ASP.NET se průběžně vyvíjel let, tým ASP.NET má mnohem zjistili díky spolupráci se zpětnou vazbu od zákazníků.

Za předpokladu, že uživatelé se přihlašují tak, že zadáte uživatelské jméno a heslo, které jste zaregistrovali do vlastní aplikace již není platný. Na webu se stal více sociálních sítí. Uživatelé interagují navzájem v reálném čase prostřednictvím kanálů sociálních sítí, jako je Facebook, Twitter a další sociální weby. Vývojáři mají uživatelé byli schopní se přihlásit pomocí jejich sociálních identit, takže mají celou řadu možností na svých webech. Systém moderní členství musíte povolit na základě přesměrování protokolu se změnami pro zprostředkovatele ověřování, jako je Facebook, Twitter a další.

Jak se vývoj pro web, proto nebyla tyto vzory se dají vývoje pro web. Jednotky testování kódu aplikace se stalo základní problém pro vývojáře aplikací. Technologie ASP.NET v 2008 přidali nový rámec založena na vzoru Model-View-Controller (MVC), v části usnadňuje vývojářům vytváření jednotky testovatelných aplikací ASP.NET. Vývojáři, kteří chtěli jednotky testování své aplikace logiky také chtěli mít možnost to udělat pomocí systému členství.

Vzhledem k tomu, tyto změny při vývoji webové aplikace ASP.NET Identity byla vyvinuta sledovat tyto cíle:

- **ASP.NET Identity systémů**

    - ASP.NET Identity je možné se všemi rozhraní ASP.NET, jako je ASP.NET MVC, webových formulářů, webové stránky, webové rozhraní API a funkce SignalR.
    - ASP.NET Identity je možné při vytváření webové, phone, store nebo hybridní aplikace.
- **Snadné připojení v profilu data o uživateli**

    - Budete mít kontrolu nad schéma uživatele a informace o profilu. Například můžete snadno povolit systému umožňuje ukládat data narození uživatele zadané při registraci účtu ve vaší aplikaci.

- **Ovládací prvek trvalosti**

    - Ve výchozím nastavení systém identit technologie ASP.NET ukládá veškeré informace o uživateli v databázi. ASP.NET Identity používá Entity Framework Code First k implementaci všech svůj mechanismus trvalosti.
    - Protože můžete řídit schéma databáze, běžné úkoly, jako je například změna názvů tabulek nebo změna datový typ primárního klíče je snadno udělat.
    - Je snadné k mechanismy jiného úložiště, jako je SharePoint, tabulky služby Azure Storage, databáze NoSQL, atd., aniž byste museli throw `System.NotImplementedExceptions` výjimky.
- **Testování jednotek**

    - ASP.NET Identity vytvoří webovou aplikaci další jednotky testovatelné. Můžete psát testy jednotek pro částí aplikace využívající ASP.NET Identity.
- **Zprostředkovatel rolí**

    - Je zprostředkovatel rolí, které umožňuje omezit přístup k různým částem vaší aplikace tak, že role. Můžete snadno vytvořit role, například "Admin" a přidání uživatelů do rolí.
- **Na základě deklarací identity**

    - ASP.NET Identity podporuje ověřování nezaloženého na deklaracích, kde je identitu uživatele reprezentována jako sada deklarací identity. Deklarace identity umožňují vývojářům bude mnohem více výrazovými možnostmi při popisu identity uživatele, než dovolují role. Vzhledem k tomu členství v roli je pouze logická hodnota (člen nebo třetí), může obsahovat deklaraci identity široké škály informací o identitě uživatele a členství.
- **Poskytovatelé přihlášení prostřednictvím sociální sítě**

    - Můžete snadno přidat sociální moduly protokolu například Account Microsoft, Facebook, Twitter, Google a dalších do vaší aplikace a ukládání dat specifických pro uživatele ve vaší aplikaci.

- **Integrace OWIN**

    - Ověřování pomocí technologie ASP.NET je teď podle middleware OWIN, který jde použít na všechny hostitele na bázi OWIN. ASP.NET Identity nemá žádné závislosti na System.Web. Je plně kompatibilní rozhraní OWIN a lze použít v jakékoli OWIN hostované aplikace.
    - ASP.NET Identity používá ověřování OWIN pro v/log odhlášení uživatelů na webu. To znamená, že místo použití ověřování pomocí formulářů k vygenerování souboru cookie, aplikace používá OWIN CookieAuthentication, který chcete.
- **Balíček NuGet**

    - ASP.NET Identity je znovu distribuován jako balíček NuGet, který je nainstalovaný v šablonách technologie ASP.NET MVC, webových formulářů a webového rozhraní API, které se dodávají se sadou Visual Studio 2017. Tento balíček NuGet můžete stáhnout z Galerie NuGet.
    - Uvolnění ASP.NET Identity jako NuGet package usnadňuje tým ASP.NET iterovat nové funkce a opravy chyb a tyto vývojářům dodávat promptně.

## <a name="get-started-with-aspnet-identity"></a>Začínáme s ASP.NET Identity

ASP.NET Identity se používá v šablonách projektů sady Visual Studio 2017 pro ASP.NET MVC, webových formulářů, SPA a webového rozhraní API. V tomto názorném postupu jsme budete znázorňují použití ASP.NET Identity šablony projektů a přidat tak funkce pro registrace, přihlášení a odhlášení uživatele.

ASP.NET Identity je implementováno pomocí následujícího postupu. Cílem tohoto článku je poskytnout základní přehled ASP.NET Identity můžete na něho krok za krokem nebo jenom zobrazit podrobné informace. Podrobnější pokyny k vytvoření aplikace pomocí ASP.NET Identity, včetně pomocí nového rozhraní API pro přidání uživatelů rolí a informace o profilu, najdete v části Další kroky na konci tohoto článku.

1. Vytvoření aplikace ASP.NET MVC pomocí samostatné účty. Můžete používat ASP.NET Identity v ASP.NET MVC, webových formulářů, webové rozhraní API, SignalR atd. V tomto článku Začneme s aplikací ASP.NET MVC.  
  
    ![](introduction-to-aspnet-identity/_static/image1.png)
2. Vytvořený projekt obsahuje následující tři balíčky pro ASP.NET Identity.

    - [`Microsoft.AspNet.Identity.EntityFramework`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/)  
   Tento balíček obsahuje implementace Entity Framework ASP.NET Identity, který se zachová data technologie ASP.NET Identity a schémat na SQL Server.
    - [`Microsoft.AspNet.Identity.Core`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Core/)  
   Tento balíček obsahuje základní rozhraní pro ASP.NET Identity. Tento balíček je možné napsat implementaci pro identitu ASP.NET, že různé cíle trvalého úložiště, jako jsou Azure Table Storage, NoSQL databáze atd.
    - [`Microsoft.AspNet.Identity.OWIN`](http://www.nuget.org/packages/Microsoft.AspNet.Identity.Owin/)  
   Tento balíček obsahuje funkce, které se používá k ověřování OWIN s ASP.NET Identity v aplikacích technologie ASP.NET. Používá se při přidání přihlašování do funkce do vaší aplikace a volání do souboru Cookie ověřování OWIN middleware ke generování souboru cookie.
3. Vytvoření uživatele.  
   Spusťte aplikaci a potom klikněte na **zaregistrovat** odkaz pro vytvoření uživatele. Následující obrázek ukazuje stránku registrace, která shromažďuje uživatelské jméno a heslo.  
  
    ![](introduction-to-aspnet-identity/_static/image2.png)  
  
   Když uživatel vybere **zaregistrovat** tlačítko, `Register` akce kontroleru účet vytvoří uživatele voláním rozhraní API technologie ASP.NET Identity, jak je zdůrazněno níže:

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample1.cs?highlight=8-9)]
4. Přihlásit se.  
   Pokud byl uživatel vytvořen úspěšně, že je podepsán `SignInAsync` metody.  

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample6.cs?highlight=12)]


   `SignInManager.SignInAsync` Generuje metodu [ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). Protože ASP.NET Identity a ověřování souborů Cookie OWIN jsou založené na deklaracích systém, rozhraní framework vyžaduje, aby aplikace generovat objekt ClaimsIdentity pro daného uživatele. ClaimsIdentity obsahuje informace o všech deklarací identity pro uživatele, například ke kterým rolím uživatel patří.   
 
5. Odhlaste se.  
   Vyberte **Odhlásit** odkaz k volání odhlášení akce v kontroleru účtu. 

    [!code-csharp[Main](introduction-to-aspnet-identity/samples/sample5.cs?highlight=6)]

   Zvýrazněný kód výše ukazuje OWIN `AuthenticationManager.SignOut` metody. To je obdobou [FormsAuthentication.SignOut](https://msdn.microsoft.com/library/system.web.security.formsauthentication.signout.aspx) metodu používanou [FormsAuthentication](https://msdn.microsoft.com/library/system.web.security.formsauthenticationmodule.aspx) modulu ve webových formulářů.

## <a name="components-of-aspnet-identity"></a>Součástí ASP.NET Identity

Následující diagram znázorňuje komponenty systému ASP.NET Identity (vyberte na [to](introduction-to-aspnet-identity/_static/image3.png) nebo v diagramu ji zvětšete). Balíčky zeleně tvoří systém identit technologie ASP.NET. Všechny ostatní balíčky jsou závislosti, které jsou potřeba pro použití systému identit technologie ASP.NET v aplikacích technologie ASP.NET.

[![](introduction-to-aspnet-identity/_static/image5.png)](introduction-to-aspnet-identity/_static/image4.png)

Následuje stručný popis balíčků NuGet není již bylo zmíněno dříve:

- [Microsoft.Owin.Security.Cookies](http://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/)  
 Middleware, který umožňuje aplikaci používat soubor cookie na základě ověřování, podobně jako ASP. Ověřování pomocí formulářů pro síť.
- [EntityFramework](http://www.nuget.org/packages/EntityFramework/)  
 Entity Framework je technologie přístup doporučené dat společnosti Microsoft pro relační databáze.

## <a name="migrating-from-membership-to-aspnet-identity"></a>Migrace z členství na ASP.NET Identity

Věříme, že brzy poskytovat rady k migraci vaší stávající aplikace, které používají členství technologie ASP.NET nebo jednoduché členství na nový systém ASP.NET Identity.

## <a name="next-steps"></a>Další kroky

- [Vytvoření aplikace ASP.NET MVC 5 s Facebook a Google OAuth2 nebo OpenID přihlašování](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)  
 Tento kurz používá rozhraní API technologie ASP.NET Identity k přidání informace z profilu k uživatelské databázi a způsob ověřování pomocí Googlu a Facebooku.
- [Vytvoření aplikace ASP.NET MVC pomocí ověření a SQL DB a nasazení do služby Azure App Service](https://docs.microsoft.com/aspnet/core/security/authorization/secure-data)  
 Tento kurz ukazuje, jak použít rozhraní API Identity k přidání uživatelů a rolí.
- [https://github.com/rustd/AspnetIdentitySample](https://github.com/rustd/AspnetIdentitySample)  
 Ukázková aplikace, který ukazuje, jak přidat základní role a podpora uživatelů a rolí a správy uživatelů.
