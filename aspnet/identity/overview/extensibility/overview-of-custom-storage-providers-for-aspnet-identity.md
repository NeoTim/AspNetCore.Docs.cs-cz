---
uid: identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity
title: Přehled poskytovatelů vlastního úložiště pro ASP.NET Identity | Dokumentace Microsoftu
author: Rick-Anderson
description: ASP.NET Identity je rozšiřitelný systém, který vám umožní vytvořit poskytovatele úložiště a zařadit ho do své aplikace bez znovu práce aplikace...
ms.author: riande
ms.date: 10/13/2014
ms.assetid: 681a9204-462e-4260-9a0b-19f0644d6ad7
msc.legacyurl: /identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: e7461098f93bf64d6ff0d0e4ecdb64338f96be8b
ms.sourcegitcommit: 2d3e5422d530203efdaf2014d1d7df31f88d08d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51021765"
---
<a name="overview-of-custom-storage-providers-for-aspnet-identity"></a>Přehled poskytovatelů vlastního úložiště pro ASP.NET Identity
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> ASP.NET Identity je rozšiřitelný systém, který vám umožní vytvořit poskytovatele úložiště a připojte ho do své aplikace bez přepracování aplikace. Toto téma popisuje, jak vytvořit vlastní úložiště poskytovatele pro ASP.NET Identity. Popisuje důležité koncepty pro vytvoření vlastního zprostředkovatele úložiště, ale není podrobný postup implementace vlastního poskytovatele úložiště.
> 
> Příklad implementace vlastního poskytovatele úložiště najdete v tématu [implementace vlastního poskytovatele úložiště MySQL ASP.NET Identity](implementing-a-custom-mysql-aspnet-identity-storage-provider.md).
> 
> Toto téma byl aktualizovaný pro technologii ASP.NET 2.0 Identity.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Visual Studio 2013 s aktualizací Update 2
> - ASP.NET Identity 2


## <a name="introduction"></a>Úvod

Ve výchozím nastavení systém identit technologie ASP.NET ukládá informace o uživateli v databázi SQL serveru a používá Entity Framework Code First k vytvoření databáze. Pro mnoho aplikací tento přístup dobře funguje. Ale možná dáte přednost použití jiného typu mechanismu trvalosti, jako je Azure Table Storage, nebo už můžete mít databázových tabulek s velmi liší od výchozí implementace struktury. V obou případech můžete napsat vlastní zprostředkovatele pro mechanizmus pro ukládání a zařadit tohoto zprostředkovatele do vaší aplikace.

ASP.NET Identity je zahrnuté ve výchozím nastavení do šablony sady Visual Studio 2013. Aktualizace můžete získávat na ASP.NET Identity prostřednictvím [balíček EntityFramework NuGet identita Microsoft AspNet](http://www.nuget.org/packages/Microsoft.AspNet.Identity.EntityFramework/).

Toto téma obsahuje následující oddíly:

- [Pomůže porozumět architektuře](#architecture)
- [Pochopení dat, která je uložena](#data)
- [Vytvoření vrstvy přístupu k datům](#dal)
- [Přizpůsobit třídu uživatelů](#user)
- [Přizpůsobení úložiště uživatele](#userstore)
- [Vlastní nastavení role třídy](#role)
- [Přizpůsobení úložiště rolí](#rolestore)
- [Změna konfigurace aplikace pro použití nového poskytovatele úložiště](#reconfigure)
- [Jiné implementace poskytovatelé vlastního úložiště](#other)

<a id="architecture"></a>
## <a name="understand-the-architecture"></a>Pomůže porozumět architektuře

ASP.NET Identity se skládá z tříd pojmenovanou manažerů a úložiště. Správci jsou základní třídy, které vývojář aplikace používá k provádění operací, jako je například vytváření s uživatelem v systému identit technologie ASP.NET. Úložiště jsou třídy nižší úrovně, které určují, jak jsou entity, jako jsou uživatelé a role, trvalé. Úložiště jsou úzce párovaný pomocí mechanismu trvalosti, ale správci jsou oddělené od úložiště, což znamená, že nahradíte mechanismu trvalosti pravidla bez narušení běžného celé aplikace.

Následující diagram znázorňuje, jak se vaše webová aplikace komunikuje s manažerů a úložiště využívat vrstvy přístupu k datům.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image1.png)

K vytvoření poskytovatele vlastní úložiště pro ASP.NET Identity, je nutné vytvořit zdroj dat, vrstva přístupu k datům a třídy úložiště, které pracují s této vrstvy přístupu k datům. Můžete pokračovat v používání stejného správce rozhraní API k provedení operace s daty na uživatele, ale teď, když se ukládají do jiného úložiště systému.

Není potřeba přizpůsobit třídy správce, protože při vytváření nové instance objektu UserManager nebo RoleManager zadat typ třídy uživatele a předat jako argument instanci třídy úložiště. Tento přístup umožňuje pružný vaše vlastní třídy stávající struktury. Uvidíte jak se vaše vlastní úložiště třídy v části vytvořit instanci objektu UserManager a RoleManager [změna konfigurace aplikace pro použití nového poskytovatele úložiště](#reconfigure).

<a id="data"></a>
## <a name="understand-the-data-that-is-stored"></a>Pochopení dat, která je uložena

Pro implementaci zprostředkovatele vlastního úložiště, musíte porozumět typům dat používat s ASP.NET Identity a rozhodnout, které funkce jsou relevantní pro vaši aplikaci.

| Data | Popis |
| --- | --- |
| Uživatelé | Registrovaným uživatelům svého webu. Zahrnuje Id a uživatelské jméno uživatele. Může obsahovat hodnoty hash hesla, pokud se uživatelé přihlásit se pomocí přihlašovacích údajů, které jsou specifické pro váš web (spíše než pomocí přihlašovacích údajů z externího webu, jako je Facebook) a razítko zabezpečení k označení, zda něco změnilo přihlašovací údaje uživatele. Může také obsahovat e-mailovou adresu, telefonní číslo, zda je povoleno dvoufaktorové ověřování, aktuální počet neúspěšných přihlášení, a zda je účet uzamčen. |
| Deklarace identity uživatelů | Sada příkazů (nebo deklarace identity), informace o uživateli, který reprezentuje identitu uživatele. Můžete povolit výrazu greater identity uživatele, než lze dosáhnout pomocí rolí. |
| Přihlášení uživatele | Informace o poskytovateli služeb externího ověřování (například Facebook) pro použití při přihlášení uživatele. |
| Role | Autorizačních skupin pro váš web. Obsahuje název Id a rolí role (jako "Admin" nebo "Employee"). |

<a id="dal"></a>
## <a name="create-the-data-access-layer"></a>Vytvoření vrstvy přístupu k datům

Toto téma předpokládá, že máte zkušenosti s trvalost mechanismus, který se chystáte používat a jak vytvářet entity pro tento mechanismus. Toto téma neobsahuje podrobnosti o tom, jak vytvořit úložišť nebo datových tříd přístup; Místo toho poskytuje některé tipy rozhodnutí o návrhu, že budete muset udělat při práci s ASP.NET Identity.

Máte spoustu svobodu při navrhování úložišť pro vlastní zprostředkovatele úložiště. Potřebujete vytvořit úložiště pro funkce, které chcete používat ve vaší aplikaci. Například pokud nepoužíváte role ve vaší aplikaci, není potřeba vytvořit úložiště pro role nebo role uživatele. Struktura, která se velmi liší od výchozí implementace ASP.NET Identity může vyžadovat technologie a stávající infrastruktury. Ve vaší vrstvy přístupu k datům poskytují logiku pro práci s strukturu vašich úložišť.

MySQL čerpat úložišť dat pro technologii ASP.NET 2.0 Identity, najdete v části [MySQLIdentity.sql](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLIdentity.sql).

V vrstvy přístupu k datům poskytují logiku pro uložení dat do zdroje dat z ASP.NET Identity. Vrstva přístupu k datům pro vašeho poskytovatele přizpůsobená úložišť může obsahovat následující třídy k ukládání informací o uživatelích a role.

| Třída | Popis | Příklad |
| --- | --- | --- |
| Kontext | Zapouzdří informace pro připojení k vaší mechanismu trvalosti a spouštění dotazů. Tato třída je vaše vrstvy přístupu k datům. Datové třídy bude vyžadovat instance této třídy provádět své operace. Můžete také inicializuje třídy úložiště se instance této třídy. | [MySQLDatabase](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/MySQLDatabase.cs) |
| Úložiště uživatelů | Ukládá a načítá uživatelské informace (například uživatelské jméno a heslo hash). | [UserTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserTable.cs) |
| Role úložiště | Ukládá a načítá informace o rolích (jako je například název role). | [RoleTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleTable.cs) |
| Úložiště objektu UserClaims. | Ukládá a načítá informace o deklaraci identity uživatele (například typ deklarace identity a hodnota). | [UserClaimsTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserClaimsTable.cs) |
| Úložiště entit přihlášení uživatele | Ukládá a načítá uživatelské přihlašovací údaje (například externí zprostředkovatel ověřování). | [UserLoginsTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserLoginsTable.cs) |
| Položka UserRole úložiště | Ukládá a načítá které role je přiřazená uživateli. | [UserRoleTable (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserRoleTable.cs) |

Znovu stačí k implementaci třídy, které chcete používat ve vaší aplikaci.

Ve třídách přístupu dat zadáte kód k provedení operace s daty pro vaše konkrétní trvalost mechanismus. Například v rámci implementace MySQL UserTable třída obsahuje metodu k vložení nového záznamu do databázové tabulky uživatelů. Proměnné s názvem `_database` je instance třídy MySQLDatabase.

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample1.cs)]

Po vytvoření tříd pro přístup k vaše data, musíte vytvořit třídy úložiště, které volání konkrétní metody v vrstvy přístupu k datům.

<a id="user"></a>
## <a name="customize-the-user-class"></a>Přizpůsobit třídu uživatelů

Při implementaci poskytovatele úložiště, je nutné vytvořit třídu uživatelů, která je ekvivalentní [IdentityUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser(v=vs.108).aspx) třídy v [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) obor názvů:

Následující diagram znázorňuje IdentityUser třídu, která je nutné vytvořit a rozhraní k implementaci této třídy.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image2.png)

[IUser&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613291(v=vs.108).aspx) rozhraní definuje vlastnosti, které se pokouší volat při provádění požadované operace objektu UserManager. Rozhraní obsahuje dvě vlastnosti - Id a uživatelského jména. [IUser&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613291(v=vs.108).aspx) rozhraní umožňuje určit typ klíče pro daného uživatele prostřednictvím obecného **TKey** parametru. Typ vlastnosti Id odpovídá hodnotě parametru TKey.

Také poskytuje architekturu identit [IUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.iuser(v=vs.108).aspx) rozhraní (bez obecný parametr) Pokud chcete použít hodnotu řetězce klíče.

Třída IdentityUser implementuje IUser a obsahuje všechny další vlastnosti nebo konstruktory pro uživatele na webovém serveru. Následující příklad ukazuje třídu IdentityUser, který používá pro klíč celé číslo. Pole Id je nastaveno na **int** tak, aby odpovídala hodnotě obecného parametru. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample2.cs)]

 Úplnou implementaci, najdete v části [IdentityUser (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityUser.cs). 

<a id="userstore"></a>
## <a name="customize-the-user-store"></a>Přizpůsobení úložiště uživatele

Také vytvoříte třídu úložiště UserStore, který poskytuje metody pro všechny operace s daty na uživatele. Tato třída je ekvivalentní [objektu UserStore&lt;TUser&gt; ](https://msdn.microsoft.com/library/dn315446(v=vs.108).aspx) třídy v [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) oboru názvů. Ve své třídě objektu UserStore implementujete [úložiště IUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613276(v=vs.108).aspx) a veškeré volitelné rozhraní. Vyberete rozhraní k implementaci, která volitelné závislosti na funkcích, které chcete poskytnout ve vaší aplikaci.

Následující obrázek ukazuje třídy objektu UserStore, kterou je nutné vytvořit a odpovídající rozhraní.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image3.png)

Výchozí šablona projektu v sadě Visual Studio obsahuje kód, který předpokládá, že mnoho volitelné rozhraní byl implementován v úložišti uživatele. Pokud používáte výchozí šablonu s úložištěm na vlastní uživatele, musí implementovat rozhraní volitelné ve vašem úložišti uživatele nebo změnit kód šablony už volání metody v rozhraní, které jste neimplementovali.

 Následující příklad ukazuje třídu úložiště jednoduché uživatelské. **TUser** obecný parametr přebírá typ vaší třídy uživatele, který je obvykle třídu IdentityUser jste definovali. **TKey** obecný parametr typu uživatelský klíč. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample3.cs)]

 V tomto příkladu konstruktor, který přijímá parametr s názvem *databáze* typu ExampleDatabase je pouze ilustraci toho, jak předat ve své třídě data access. Například v implementaci MySQL tento konstruktor přijímá parametr typu MySQLDatabase. 

V rámci vaší třídy objektu UserStore použití tříd pro přístup k dat, které jste vytvořili k provádění operací. Třída úložiště UserStore má v implementaci MySQL asynchronně metodu, která používá instanci UserTable k vložení nového záznamu. **Vložit** metodu **userTable** objekt je stejné metody, které se zobrazilo v předchozí části. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample4.cs)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Rozhraní k implementaci při přizpůsobování úložiště uživatele

Další podrobnosti o funkcích, které jsou definované v každé rozhraní na dalším obrázku. Z úložiště IUserStore dědí všechny volitelné rozhraní.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image4.png)

- **Úložiště IUserStore**  
  [Úložiště IUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613278(v=vs.108).aspx) rozhraní je pouze rozhraní, je nutné implementovat v úložišti uživatele. Definuje metody pro vytváření, aktualizaci, odstranění a získávání uživatelů.
- **IUserClaimStore**  
  [IUserClaimStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613265(v=vs.108).aspx) rozhraní definuje metody je nutné implementovat v úložišti uživatele povolující deklarace identity uživatele. Obsahuje metody nebo přidání, odebrání a načítají deklarace identity uživatelů.
- **IUserLoginStore**  
  [IUserLoginStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613272(v=vs.108).aspx) definuje metody je nutné implementovat v obchodě uživatele a povolit zprostředkovatele externího ověřování. Obsahuje metody pro přidávání, odebírání a načítání přihlášení uživatele a metodu pro načtení uživatele na základě informací o přihlášení.
- **IUserRoleStore**  
  [IUserRoleStore&lt;TKey, TUser&gt; ](https://msdn.microsoft.com/library/dn613276(v=vs.108).aspx) rozhraní definuje metody je nutné implementovat v úložišti pro mapování uživatele k roli uživatele. Obsahuje metody pro přidání, odebrání a načíst role uživatele a metodu ke kontrole, zda uživatel je přiřazena role.
- **IUserPasswordStore**  
  [IUserPasswordStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613273(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat v úložišti uživatele k uchování hodnoty hash hesla. Obsahuje metody pro získání a nastavení hodnoty hash hesla a metodu, která označuje, zda má uživatel nastavit heslo.
- **IUserSecurityStampStore**  
  [IUserSecurityStampStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613277(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat v úložišti uživatelů pro účely zabezpečení razítko označující, jestli se změnila informace o účtu uživatele . Toto razítko se aktualizuje, když uživatel změní heslo, nebo přidá nebo odebere přihlášení. Obsahuje metody pro získání a nastavení razítko zabezpečení.
- **IUserTwoFactorStore**  
  [IUserTwoFactorStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613279(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat pro implementace dvoufaktorové ověřování. Obsahuje metody pro získání a nastavení, jestli je pro uživatele povoleno dvoufaktorové ověřování.
- **IUserPhoneNumberStore**  
  [IUserPhoneNumberStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613275(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat pro uložení telefonních čísel. Obsahuje metody pro získání a nastavení a určuje, zda telefonní číslo je potvrzeno telefonní číslo.
- **IUserEmailStore**  
  [IUserEmailStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613143(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat pro uložení e-mailové adresy uživatele. Obsahuje metody pro získání a nastavení e-mailovou adresu a určuje, zda e-mail je potvrzen.
- **IUserLockoutStore**  
  [IUserLockoutStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613271(v=vs.108).aspx) rozhraní definuje metody, je nutné implementovat pro ukládání informací o uzamčení účtu. Obsahuje metody pro získání aktuální počet neúspěšných pokusů o přístup, získání a nastavení, jestli účet může být uzamčen, získání a nastavení datum ukončení uzamčení, zvyšování počtu neúspěšných pokusů o a resetuje počet neúspěšných pokusů o přihlášení.
- **IQueryableUserStore**  
  [IQueryableUserStore&lt;TUser, TKey&gt; ](https://msdn.microsoft.com/library/dn613267(v=vs.108).aspx) rozhraní definuje členy, je nutné implementovat poskytnout dotazovatelné úložiště uživatelů. Obsahuje vlastnost, která obsahuje dotazovatelné uživatelů.

  Implementovat rozhraní, které jsou potřeba v aplikaci; IUserClaimStore, IUserLoginStore, IUserRoleStore, IUserPasswordStore a IUserSecurityStampStore rozhraní, jako vidíte níže. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample5.cs)]

Úplnou implementaci (včetně všech rozhraní), najdete v části [objektu UserStore (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/UserStore.cs).

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim IdentityUserLogin a IdentityUserRole

Obor názvů Microsoft.AspNet.Identity.EntityFramework obsahuje implementace [IdentityUserClaim](https://msdn.microsoft.com/library/dn613250(v=vs.108).aspx), [IdentityUserLogin](https://msdn.microsoft.com/library/dn613251(v=vs.108).aspx), a [IdentityUserRole](https://msdn.microsoft.com/library/dn613252(v=vs.108).aspx) třídy. Pokud používáte tyto funkce, můžete vytvořit vlastní verze těchto tříd a definovat vlastnosti pro vaši aplikaci. Někdy je však mnohem efektivnější nenačetla tyto entity do paměti při provádění základních operací (například přidávání nebo odebírání deklarace identity uživatele). Třídy úložiště back-endu můžete místo toho provádění těchto operací přímo na zdroj dat. Například metoda UserStore.GetClaimsAsync() lze volat userClaimTable.FindByUserId(user. ID) metoda při spuštění dotazu na tabulky přímo, který vrátí seznam deklarací identity.

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample6.cs)]

<a id="role"></a>
## <a name="customize-the-role-class"></a>Vlastní nastavení role třídy

Při implementaci poskytovatele úložiště, je nutné vytvořit třídu role, která je ekvivalentní [IdentityRole](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityrole(v=vs.108).aspx) třídy v [Microsoft.ASP.NET.Identity.EntityFramework](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework(v=vs.108).aspx) obor názvů:

Následující diagram znázorňuje IdentityRole třídu, která je nutné vytvořit a rozhraní k implementaci této třídy.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image5.png)

[IRole&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613268(v=vs.108).aspx) rozhraní definuje vlastnosti, které RoleManager pokusí volat při provádění požadované operace. Rozhraní obsahuje dvě vlastnosti - Id a název. [IRole&lt;TKey&gt; ](https://msdn.microsoft.com/library/dn613268(v=vs.108).aspx) rozhraní umožňuje určit typ klíče pro dané roli pomocí obecného **TKey** parametru. Typ vlastnosti Id odpovídá hodnotě parametru TKey.

Také poskytuje architekturu identit [IRole](https://msdn.microsoft.com/library/microsoft.aspnet.identity.irole(v=vs.108).aspx) rozhraní (bez obecný parametr) Pokud chcete použít hodnotu řetězce klíče.

Následující příklad ukazuje třídu IdentityRole, který používá pro klíč celé číslo. Pole Id je nastaveno na celé číslo tak, aby odpovídala hodnotě obecného parametru. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample7.cs)]

 Úplnou implementaci, najdete v části [IdentityRole (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/IdentityRole.cs). 

<a id="rolestore"></a>
## <a name="customize-the-role-store"></a>Přizpůsobení úložiště rolí

Můžete také vytvořit úložiště RoleStore třídu, která poskytuje metody pro všechny operace s daty o rolích. Tato třída je ekvivalentní [úložiště RoleStore&lt;TRole&gt; ](https://msdn.microsoft.com/library/dn468181(v=vs.108).aspx) třídy v oboru názvů Microsoft.ASP.NET.Identity.EntityFramework. Ve své třídě úložiště RoleStore implementujete [úložiště IRoleStore&lt;TRole, TKey&gt; ](https://msdn.microsoft.com/library/dn613266(v=vs.108).aspx) a volitelně také [IQueryableRoleStore&lt;TRole, TKey&gt; ](https://msdn.microsoft.com/library/dn613262(v=vs.108).aspx) rozhraní.

![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image6.png)

Následující příklad ukazuje třídu úložiště rolí. Obecný parametr TRole převezme typ třídy vaší role, která je obvykle IdentityRole třídy, které jste definovali. Obecný parametr TKey přijímá typ klíče vaší role. 

[!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample8.cs)]

- **IRoleStore&lt;TRole&gt;**  
  [Úložiště IRoleStore](https://msdn.microsoft.com/library/dn468195.aspx) rozhraní definuje metody k implementaci ve třídě úložiště rolí. Obsahuje metody pro vytváření, aktualizaci, odstranění a načítání rolí.
- **RoleStore&lt;TRole&gt;**  
  Přizpůsobí úložiště RoleStore vytvořte třídu, která implementuje rozhraní úložiště IRoleStore. Budete muset implementaci této třídy, pokud chcete použít role ve vašem systému. Konstruktor, který přijímá parametr s názvem *databáze* typu ExampleDatabase je pouze ilustraci toho, jak předat ve své třídě data access. Například v implementaci MySQL tento konstruktor přijímá parametr typu MySQLDatabase.  
  
  Úplnou implementaci, najdete v části [úložiště RoleStore (MySQL)](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/AspNet.Identity.MySQL/RoleStore.cs) .

<a id="reconfigure"></a>
## <a name="reconfigure-application-to-use-new-storage-provider"></a>Změna konfigurace aplikace pro použití nového poskytovatele úložiště

Jste implementovali svého nového poskytovatele úložiště. Teď musíte nakonfigurovat aplikaci k používání tohoto poskytovatele úložiště. Je-li výchozí zprostředkovatel úložiště je zahrnutý v projektu, musíte odebrat výchozího zprostředkovatele a nahraďte ji metodou poskytovatele.

### <a name="replace-default-storage-provider-in-mvc-project"></a>Nahraďte výchozí zprostředkovatel úložiště v projektu MVC

1. V **spravovat balíčky NuGet** okna, odinstalujte **Microsoft ASP.NET Identity EntityFramework** balíčku. Tento balíček můžete najít tak, že v balíčcích nainstalováno Identity.EntityFramework.  
    ![](overview-of-custom-storage-providers-for-aspnet-identity/_static/image7.png) Zobrazí se výzva, pokud chcete také odinstalovat rozhraní Entity Framework. Pokud v ostatních částech aplikace ho nepotřebujete, můžete ji odinstalovat.
2. V souboru IdentityModels.cs ve složce modely, odstranit nebo okomentovat **ApplicationUser** a **ApplicationDbContext** třídy. V aplikaci MVC můžete odstranit celý soubor IdentityModels.cs. V aplikaci Web Forms odstraňte dvě třídy, ale ujistěte se, že je pomocná třída, která se nachází v souboru IdentityModels.cs zachovat.
3. Pokud váš poskytovatel úložiště nachází v samostatném projektu, přidejte na ni odkaz ve webové aplikaci.
4. Nahraďte všechny odkazy na `using Microsoft.AspNet.Identity.EntityFramework;` pomocí příkazu pro obor názvů poskytovatele úložiště.
5. V **Startup.Auth.cs** tříd, změnit **ConfigureAuth** metodu použít jednu instanci odpovídající kontext. 

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample9.cs?highlight=3)]
6. V aplikaci\_spouštěcí složka, otevřete **IdentityConfig.cs**. Ve třídě ApplicationUserManager změnit **vytvořit** metoda vrátí uživatele správce, který používá vaše přizpůsobené uživatelské úložiště. 

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample10.cs?highlight=3)]
7. Nahraďte všechny odkazy na **ApplicationUser** s **IdentityUser**.
8. Výchozí nastavení projektu obsahuje některé členy ve třídě uživatele, které nejsou definovány v rozhraní IUser; například e-mailu, PasswordHash a GenerateUserIdentityAsync. Pokud tyto členy, nemá žádné třídy uživatelů, musíte je implementovat nebo kód, který používá tyto členy změnit.
9. Pokud všechny instance RoleManager, kterou jste vytvořili, změňte tento kód použít vaši novou třídu úložiště RoleStore.  

    [!code-csharp[Main](overview-of-custom-storage-providers-for-aspnet-identity/samples/sample11.cs)]
10. Výchozí projekt je určen pro uživatele třídu, která se má řetězec hodnotu pro klíč. Pokud třídy uživatelů má jiný typ klíče (například celé číslo), musíte změnit projektu pro práci s typu. Zobrazit [změnit primární klíč pro uživatele v identitě ASP.NET](change-primary-key-for-users-in-aspnet-identity.md).
11. V případě potřeby přidáte připojovací řetězec v souboru Web.config.

<a id="other"></a>
## <a name="other-resources"></a>Další zdroje

- Blog: [implementace ASP.NET Identity](http://odetocode.com/blogs/scott/archive/2014/01/20/implementing-asp-net-identity.aspx)
- Kurz a GIT kódu: [Simple.Data zprostředkovatele Identity technologie Asp.Net](http://designcoderelease.blogspot.co.uk/2015/03/simpledata-aspnet-identity-provider.html)
- Kurz:[nastavením základní Identity účtů a odkazující na externí DB](http://typecastexception.com/post/2013/10/27/Configuring-Db-Connection-and-Code-First-Migration-for-Identity-Accounts-in-ASPNET-MVC-5-and-Visual-Studio-2013.aspx). Podle [ @xivSolutions ](https://twitter.com/xivSolutions).
- Kurz[: implementace poskytovatele úložiště vlastní MySQL ASP.NET Identity](implementing-a-custom-mysql-aspnet-identity-storage-provider.md)
- [Entity CodeFluent](http://blog.codefluententities.com/2014/04/30/asp-net-identity-v2-and-codefluent-entities/) podle [SoftFluent](http://www.softfluent.com/)
- [Azure Table Storage](https://www.nuget.org/packages/accidentalfish.aspnet.identity.azure/) podle James Randall.
- Azure Table Storage: [AspNet.Identity.TableStorage](https://github.com/stuartleeks/leeksnet.AspNet.Identity.TableStorage) podle [ @stuartleeks ](https://twitter.com/stuartleeks).
- [CouchDB / Cloudant podle Wertheim ADAM.](https://github.com/danielwertheim/mycouch.aspnet.identity)
- Elastické ledat[h: elastické Identity](https://github.com/bmbsqd/elastic-identity) podle Bombsquad AB.
- [MongoDB](http://www.nuget.org/packages/MongoDB.AspNet.Identity/) podle Jonathan Sheely Jonathan Sheely.
- [NHibernate.AspNet.Identity](https://github.com/milesibastos/NHibernate.AspNet.Identity) podle Antônio Milesi Bastos.
- [RavenDB](http://www.nuget.org/packages/AspNet.Identity.RavenDB/1.0.0) podle [ @tourismgeek ](https://twitter.com/tourismgeek).
- [RavenDB.AspNet.Identity](https://github.com/ILMServices/RavenDB.AspNet.Identity) podle [ILMServices](http://www.ilmservice.com/).
- Redis: [Redis.AspNet.Identity](https://github.com/aminjam/Redis.AspNet.Identity)
- Šablony T4 pro generování EF code pro úložiště "databáze nejprve" uživatele: [AspNet.Identity.EntityFramework](https://github.com/cbfrank/AspNet.Identity.EntityFramework)
