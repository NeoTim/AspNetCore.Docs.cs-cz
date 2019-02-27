---
title: Poskytovatelé vlastního úložiště pro ASP.NET Core Identity
author: ardalis
description: Zjistěte, jak nakonfigurovat poskytovatelé vlastního úložiště pro ASP.NET Core Identity.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: ccd56d0c15639e1ad29094e947f8055702ee2264
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833667"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Poskytovatelé vlastního úložiště pro ASP.NET Core Identity

Podle [Steve Smith](https://ardalis.com/)

ASP.NET Core Identity je rozšiřitelný systém, který umožňuje vytvořit vlastního poskytovatele úložiště a připojte ho do své aplikace. Toto téma popisuje, jak vytvořit vlastní úložiště poskytovatele pro ASP.NET Core Identity. Popisuje důležité koncepty pro vytvoření vlastního zprostředkovatele úložiště, ale není podrobný postup.

[Zobrazit nebo stáhnout ukázky z Githubu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Úvod

Ve výchozím nastavení systém technologie ASP.NET Core Identity ukládá informace o uživateli v databázi SQL serveru pomocí Entity Framework Core. Pro mnoho aplikací tento přístup dobře funguje. Ale budete chtít použít mechanismus různých trvalého nebo schéma dat. Příklad:

* Použijete [Azure Table Storage](/azure/storage/) nebo jiného.
* Databázové tabulky mít odlišnou strukturu. 
* Možná budete chtít použít přístup využívající přístup jiná data, například [Dapperem](https://github.com/StackExchange/Dapper). 

Ve všech těchto případech můžete napsat vlastní zprostředkovatele pro váš mechanismus úložiště a připojte tohoto zprostředkovatele do vaší aplikace.

ASP.NET Core Identity je součástí šablony projektů v sadě Visual Studio s parametrem "Jednotlivých uživatelských účtů".

Při použití rozhraní příkazového řádku .NET Core, přidejte `-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>Architektura ASP.NET Core Identity

ASP.NET Core Identity se skládá z tříd pojmenovanou manažerů a úložiště. *Správci* jsou základní třídy, které vývojář aplikace používá k provádění operací, jako je například vytváření Identity user. *Úložiště* jsou třídy nižší úrovně, které určují, jak jsou entity, jako jsou uživatelé a role, trvalé. Úložiště podle tohoto vzoru vytvořené úložiště a jsou úzce svázány s mechanismus trvalosti. Správci jsou oddělené od úložiště, což znamená, že nahradíte mechanismu trvalosti beze změny kódu aplikace (s výjimkou konfigurace).

Následující diagram znázorňuje, jak webová aplikace komunikuje s správce, zatímco úložiště interakci s vrstvy přístupu k datům.

![Aplikace ASP.NET Core pracovat s správce (například "Nastavení UserManager", "RoleManager"). Správci pracovat s úložišti (například "úložiště UserStore"), které komunikují se zdrojem dat pomocí knihovny jako Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Vytvoření vlastního poskytovatele úložiště, vytvořte zdroj dat, vrstva přístupu k datům a třídy úložiště, které pracují s této vrstvy přístupu k datům (zelenou a šedou políček v diagramu výše). Není nutné přizpůsobit správci nebo kód vaší aplikace, která komunikuje s nimi (polích modrá, který je výše).

Při vytváření nové instance objektu `UserManager` nebo `RoleManager` zadejte typ třídy uživatele a předat jako argument instanci třídy úložiště. Tento přístup umožňuje pružný vaše vlastní třídy ASP.NET Core. 

[Změna konfigurace aplikace pro použití nového poskytovatele úložiště](#reconfigure-app-to-use-a-new-storage-provider) ukazuje, jak vytvořit instanci `UserManager` a `RoleManager` s vlastní úložiště.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity ukládá datové typy

[ASP.NET Core Identity](https://github.com/aspnet/identity) datové typy jsou podrobně popsané v následujících částech:

### <a name="users"></a>Uživatelé

Registrovaným uživatelům svého webu. [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) typ mohou rozšířit nebo jako příklad je použita pro vlastní typ. Není nutné pro dědění z určitého typu, k implementaci vlastních řešení úložiště identit.

### <a name="user-claims"></a>Deklarace identity uživatelů

Sada příkazů (nebo [deklarace identity](/dotnet/api/system.security.claims.claim)) informace o uživateli, který reprezentuje identitu uživatele. Můžete povolit výrazu greater identity uživatele, než lze dosáhnout pomocí rolí.

### <a name="user-logins"></a>Přihlášení uživatele

Informace o poskytovateli služeb externího ověřování (jako je Facebook nebo účet Microsoft) pro použití při přihlášení uživatele. [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Role

Autorizačních skupin pro váš web. Obsahuje název Id a rolí role (jako "Admin" nebo "Employee"). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Vrstva přístupu k datům

Toto téma předpokládá, že máte zkušenosti s trvalost mechanismus, který se chystáte používat a jak vytvářet entity pro tento mechanismus. Toto téma neposkytuje informace o tom, jak vytvořit úložišť nebo datových tříd přístup; poskytuje několik návrhů o rozhodnutí o návrhu při práci s ASP.NET Core Identity.

Máte velké množství volného při navrhování vrstvy přístupu k datům pro poskytovatele vlastní úložiště. Potřebujete vytvořit mechanismus trvalosti pro funkce, které chcete používat ve vaší aplikaci. Například pokud nepoužíváte role ve vaší aplikaci, není nutné vytvořit úložiště pro role nebo přiřazení rolí uživatelů. Technologie a existující infrastruktura může vyžadovat strukturu, která se velmi liší od výchozí implementace technologie ASP.NET Core Identity. Ve vaší vrstvy přístupu k datům je poskytnout logiku pro práci s strukturu vaší implementace úložiště.

Vrstva přístupu k datům poskytuje logiku pro uložení dat do zdroje dat z ASP.NET Core Identity. Vrstva přístupu k datům pro vašeho poskytovatele přizpůsobená úložišť může obsahovat následující třídy k ukládání informací o uživatelích a role.

### <a name="context-class"></a>context – třída

Zapouzdří informace pro připojení k vaší mechanismu trvalosti a spouštění dotazů. Několik datových tříd vyžadují instanci této třídy, obvykle k dispozici prostřednictvím vkládání závislostí. [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Úložiště uživatelů

Ukládá a načítá uživatelské informace (například uživatelské jméno a heslo hash). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Role úložiště

Ukládá a načítá informace o rolích (jako je například název role). [Příklad](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Úložiště objektu UserClaims.

Ukládá a načítá informace o deklaraci identity uživatele (například typ deklarace identity a hodnota). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Úložiště entit přihlášení uživatele

Ukládá a načítá uživatelské přihlašovací údaje (například externí zprostředkovatel ověřování). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Položka UserRole úložiště

Ukládá a načítá, které role jsou přiřazeny jednotlivým uživatelům. [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**TIP:** Implementujte jenom třídám, které chcete používat ve vaší aplikaci.

Datové třídy přístup zadejte kód k provedení operace s daty pro váš mechanismus trvalosti. Například v rámci vlastního zprostředkovatele, může mít následující kód k vytvoření nového uživatele v *ukládání* třídy:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Implementační logika pro vytvoření uživatele se v `_usersTable.CreateAsync` níže uvedená metoda.

## <a name="customize-the-user-class"></a>Přizpůsobit třídu uživatelů

Při implementaci zprostředkovatele úložiště, vytvořte třídu uživatelů, která je ekvivalentní [IdentityUser třídy](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Minimálně musí obsahovat třídy uživatelů `Id` a `UserName` vlastnost.

`IdentityUser` Třídy definuje vlastnosti, která `UserManager` volání při provádění požadované operace. Výchozí typ `Id` vlastnosti je řetězec, ale může dědit z `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` a zadejte jiný typ. Rozhraní framework očekává, že implementace úložiště pro zpracování konverzí datových typů.

## <a name="customize-the-user-store"></a>Přizpůsobení úložiště uživatele

Vytvoření `UserStore` třídu, která poskytuje metody pro všechny operace s daty na uživatele. Tato třída je ekvivalentní [objektu UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) třídy. Ve vaší `UserStore` třídy, implementujte `IUserStore<TUser>` a volitelné rozhraní vyžaduje. Vyberete volitelné rozhraní, která implementují podle funkce poskytované ve vaší aplikaci.

### <a name="optional-interfaces"></a>Volitelné rozhraní

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

Volitelné rozhraní Zdědit `IUserStore<TUser>`. Vidíte ukládání v částečně implementováno ukázkového uživatele [ukázkovou aplikaci](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).

V rámci `UserStore` třídy, použijte datových tříd přístupu, které jste vytvořili k provádění operací. Tyto jsou předány v využitím vkládání závislostí. Například v systému SQL Server s Dapper implementací `UserStore` třída nemá `CreateAsync` metodu, která používá instanci `DapperUsersTable` k vložení nového záznamu:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Rozhraní k implementaci při přizpůsobování úložiště uživatele

* **IUserStore**  
 [Úložiště IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) rozhraní je pouze rozhraní, je nutné implementovat v úložišti uživatele. Definuje metody pro vytváření, aktualizaci, odstranění a získávání uživatelů.
* **IUserClaimStore**  
 [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) rozhraní definuje metody, které implementují povolující deklarace identity uživatele. Obsahuje metody pro přidávání, odebírání a načítají deklarace identity uživatelů.
* **IUserLoginStore**  
 [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definuje metody, implementovat povolení externího zprostředkovatele ověřování. Obsahuje metody pro přidávání, odebírání a načítání přihlášení uživatele a metodu pro načtení uživatele na základě informací o přihlášení.
* **IUserRoleStore**  
 [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) rozhraní definuje metody implementaci pro mapování uživatele k roli. Obsahuje metody pro přidání, odebrání a načíst role uživatele a metodu ke kontrole, zda uživatel je přiřazena role.
* **IUserPasswordStore**  
 [IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) rozhraní definuje metody, které můžete implementovat pro uchování hodnoty hash hesla. Obsahuje metody pro získání a nastavení hodnoty hash hesla a metodu, která označuje, zda má uživatel nastavit heslo.
* **IUserSecurityStampStore**  
 [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) rozhraní definuje metody, implementovat pro účely zabezpečení razítko označující, jestli se změnila informace o účtu uživatele. Toto razítko se aktualizuje, když uživatel změní heslo, nebo přidá nebo odebere přihlášení. Obsahuje metody pro získání a nastavení razítko zabezpečení.
* **IUserTwoFactorStore**  
 [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) rozhraní definuje metody, které můžete implementovat pro podporu dvoufaktorové ověřování. Obsahuje metody pro získání a nastavení, jestli je pro uživatele povoleno dvoufaktorové ověřování.
* **IUserPhoneNumberStore**  
 [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) rozhraní definuje metody implementaci pro uložení telefonních čísel. Obsahuje metody pro získání a nastavení a určuje, zda telefonní číslo je potvrzeno telefonní číslo.
* **IUserEmailStore**  
 [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) rozhraní definuje metody implementaci pro uložení e-mailové adresy uživatele. Obsahuje metody pro získání a nastavení e-mailovou adresu a určuje, zda e-mail je potvrzen.
* **IUserLockoutStore**  
 [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) rozhraní definuje metody implementaci pro uložení informací o uzamčení účtu. Obsahuje metody pro sledování neúspěšných pokusů o přístup a uzamčení.
* **IQueryableUserStore**  
 [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) rozhraní definuje členy implementujete poskytuje dotazovatelné úložiště uživatelů.

Implementovat rozhraní, které jsou potřeba ve vaší aplikaci. Příklad:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim IdentityUserLogin a IdentityUserRole

`Microsoft.AspNet.Identity.EntityFramework` Obor názvů obsahuje implementace [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), a [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) třídy. Pokud používáte tyto funkce, můžete vytvořit vlastní verze těchto tříd a definovat vlastnosti pro vaši aplikaci. Někdy je však mnohem efektivnější nenačetla tyto entity do paměti při provádění základních operací (například přidávání nebo odebírání deklarace identity uživatele). Třídy úložiště back-endu můžete místo toho provádění těchto operací přímo na zdroj dat. Například `UserStore.GetClaimsAsync` můžete volat metodu `userClaimTable.FindByUserId(user.Id)` metoda při spuštění dotazu na tabulky přímo, který vrátí seznam deklarací identity.

## <a name="customize-the-role-class"></a>Vlastní nastavení role třídy

Při implementaci zprostředkovatele úložiště rolí, můžete vytvořit vlastní roli typu. To nemusí implementovat určité rozhraní, ale musí mít `Id` a obvykle bude mít `Name` vlastnost.

Tady je příklad role třídy:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Přizpůsobení úložiště rolí

Můžete vytvořit `RoleStore` třídu, která poskytuje metody pro všechny operace s daty o rolích. Tato třída je ekvivalentní [úložiště RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) třídy. V `RoleStore` implementovat třídy, `IRoleStore<TRole>` a volitelně také `IQueryableRoleStore<TRole>` rozhraní.

* **IRoleStore&lt;TRole&gt;**  
 [Úložiště IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) rozhraní definuje metody k implementaci v třídě úložiště rolí. Obsahuje metody pro vytváření, aktualizaci, odstranění a načítání rolí.
* **RoleStore&lt;TRole&gt;**  
 Chcete-li přizpůsobit `RoleStore`, vytvořte třídu, která implementuje `IRoleStore<TRole>` rozhraní. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Změna konfigurace aplikace pro použití nového poskytovatele úložiště

Když naimplementujete poskytovatele úložiště, konfigurace aplikace pro použití. Pokud vaše aplikace použila výchozího zprostředkovatele, nahraďte ho vlastní poskytovatele.

1. Odeberte `Microsoft.AspNetCore.EntityFramework.Identity` balíček NuGet.
1. Pokud zprostředkovatele úložiště nachází v samostatném projektu nebo balíček, přidejte na ni odkaz.
1. Nahraďte všechny odkazy na `Microsoft.AspNetCore.EntityFramework.Identity` pomocí příkazu pro obor názvů poskytovatele úložiště.
1. V `ConfigureServices` změnit metodu `AddIdentity` metodu použít vlastní typy. Můžete vytvořit vlastní metody rozšíření pro tento účel. Zobrazit [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) příklad.
1. Pokud používáte role, aktualizujte `RoleManager` používat vaše `RoleStore` třídy.
1. Aktualizujte připojovací řetězec a přihlašovací údaje pro konfiguraci vaší aplikace.

Příklad:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Odkazy

* [Poskytovatelé vlastního úložiště pro ASP.NET 4.x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/aspnet/identity) &ndash; toto úložiště obsahuje odkazy na komunitní udržuje poskytovatele úložiště.
