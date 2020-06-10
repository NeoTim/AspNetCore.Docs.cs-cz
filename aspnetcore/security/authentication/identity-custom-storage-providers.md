---
title: Vlastní poskytovatelé úložiště pro ASP.NET CoreIdentity
author: ardalis
description: Přečtěte si, jak nakonfigurovat vlastní poskytovatele úložiště pro ASP.NET Core Identity .
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 792a9e5f776e345fbee5726b676fe148ecaf1657
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106582"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Vlastní poskytovatelé úložiště pro ASP.NET CoreIdentity

[Steve Smith](https://ardalis.com/)

ASP.NET Core Identity je rozšiřitelný systém, který umožňuje vytvořit vlastního poskytovatele úložiště a připojit ho k vaší aplikaci. Toto téma popisuje, jak vytvořit přizpůsobeného poskytovatele úložiště pro ASP.NET Core Identity . Popisuje důležité koncepty pro vytváření vlastního poskytovatele úložiště, ale není to podrobný návod.

[Zobrazení nebo stažení ukázky z GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Úvod

Ve výchozím nastavení systém ASP.NET Core Identity ukládá informace o uživatelích do databáze SQL Server pomocí Entity Framework Core. U mnoha aplikací funguje tento přístup dobře. Můžete ale chtít použít jiný mechanismus trvalosti nebo schéma dat. Například:

* Používáte [Azure Table Storage](/azure/storage/) nebo jiné úložiště dat.
* Tabulky databáze mají jinou strukturu. 
* Můžete chtít použít jiný přístup k datům, jako je například [dapperem](https://github.com/StackExchange/Dapper). 

V každém z těchto případů můžete napsat přizpůsobeného poskytovatele úložného mechanismu a připojit tohoto poskytovatele k vaší aplikaci.

ASP.NET Core Identity je součástí šablon projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.

Při použití .NET Core CLI přidejte `-au Individual` :

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>Architektura ASP.NET Core Identity

ASP.NET Core Identity se skládá ze tříd nazvaných manažeři a obchody. *Manažeři* jsou třídy vysoké úrovně, které vývojář aplikace používá k provádění operací, jako je například vytvoření Identity uživatele. *Úložiště* jsou třídy nižší úrovně, které určují, jak jsou trvalé entity, jako jsou uživatelé a role. Úložiště se řídí vzorem úložiště a jsou úzce spojeny s mechanismem trvalosti. Manažeři jsou oddělené od úložišť, což znamená, že můžete nahradit mechanismus trvalosti beze změny kódu aplikace (s výjimkou konfigurace).

Následující diagram znázorňuje, jak webová aplikace komunikuje s manažery, zatímco ukládá interakce s vrstvami přístupu k datům.

![ASP.NET Core aplikace pracují se správci (například "UserManager", "RoleManager"). Manažeři pracují s obchody (například "UserStore"), které komunikují se zdrojem dat pomocí knihovny, jako je Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Chcete-li vytvořit vlastního poskytovatele úložiště, vytvořte zdroj dat, vrstvu přístupu k datům a třídy úložiště, které pracují s touto vrstvou přístupu k datům (zelená a šedá pole v diagramu výše). Nemusíte přizpůsobovat manažery ani kód vaší aplikace, který s nimi komunikuje (modré čtverečky výše).

Při vytváření nové instance nebo zadání `UserManager` `RoleManager` typu třídy uživatele a předání instance třídy úložiště jako argumentu. Tento přístup umožňuje zapojit přizpůsobené třídy do ASP.NET Core. 

Změna [Konfigurace aplikace na použití nového poskytovatele úložiště](#reconfigure-app-to-use-a-new-storage-provider) ukazuje, jak vytvářet instance `UserManager` a `RoleManager` s přizpůsobeným úložištěm.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core Identity ukládá datové typy

[ASP.NET Core Identity ](https://github.com/aspnet/identity) datové typy jsou podrobně popsané v následujících částech:

### <a name="users"></a>Uživatelé

Registrovaní uživatelé vašeho webu. Typ [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) může být rozšířen nebo použit jako příklad pro vlastní typ. K implementaci vlastního řešení úložiště identity není nutné dědit z konkrétního typu.

### <a name="user-claims"></a>Deklarace identity uživatele

Sada příkazů (nebo [deklarací identity](/dotnet/api/system.security.claims.claim)) o uživateli, který představuje identitu uživatele. Může povolit větší výraz identity uživatele, než je možné dosáhnout prostřednictvím rolí.

### <a name="user-logins"></a>Přihlášení uživatelů

Informace o externím poskytovateli ověřování (například Facebooku nebo účet Microsoft), který se použije při přihlašování uživatele [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Role

Skupiny autorizace pro váš web. Zahrnuje ID role a název role (například admin nebo zaměstnanec). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Vrstva přístupu k datům

V tomto tématu se předpokládá, že jste obeznámeni s mechanismem trvalosti, který budete používat, a vytváření entit pro tento mechanismus. Toto téma neposkytuje podrobné informace o tom, jak vytvořit úložiště nebo třídy přístupu k datům. poskytuje některé návrhy na rozhodování o návrhu při práci s ASP.NET Core Identity .

Při navrhování vrstvy přístupu k datům pro přizpůsobeného poskytovatele úložiště máte spoustu volnosti. K funkcím, které máte v úmyslu používat, je třeba vytvořit mechanismy trvalosti. Pokud například v aplikaci nepoužíváte role, nemusíte vytvářet úložiště pro role nebo přidružení rolí uživatele. Vaše technologie a stávající infrastruktura mohou vyžadovat strukturu, která je velmi odlišná od výchozí implementace ASP.NET Core Identity . Ve vrstvě pro přístup k datům poskytnete logiku pro práci se strukturou implementace úložiště.

Vrstva přístupu k datům poskytuje logiku pro uložení dat z ASP.NET Core Identity do zdroje dat. Vrstva přístupu k datům pro vlastního poskytovatele úložiště může obsahovat následující třídy pro ukládání informací o uživatelích a rolích.

### <a name="context-class"></a>context – třída

Zapouzdřuje informace pro připojení k vašemu mechanismu trvalosti a provádění dotazů. Několik datových tříd vyžaduje instanci této třídy, která je obvykle poskytována prostřednictvím injektáže závislosti. [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1):

### <a name="user-storage"></a>Uživatelské úložiště

Ukládá a načítá informace o uživateli (například uživatelské jméno a hodnota hash hesla). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Úložiště rolí

Ukládá a načítá informace o rolích (například název role). [Příklad](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Úložiště UserClaims

Ukládá a načítá informace o deklaraci identity uživatele (například typ a hodnotu deklarace identity). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>Úložiště UserLogins

Ukládá a načítá přihlašovací informace uživatele (například externího poskytovatele ověřování). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>Úložiště položky UserRole

Ukládá a načítá, které role jsou přiřazeny uživatelům. [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**Tip:** Implementujte jenom třídy, které chcete ve své aplikaci použít.

Ve třídách pro přístup k datům poskytněte kód pro provádění operací s daty pro svůj mechanismus trvalosti. Například v rámci vlastního zprostředkovatele můžete mít následující kód k vytvoření nového uživatele ve třídě *úložiště* :

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Logika implementace pro vytvoření uživatele je v `_usersTable.CreateAsync` metodě, která je znázorněna níže.

## <a name="customize-the-user-class"></a>Přizpůsobení třídy uživatele

Při implementaci poskytovatele úložiště vytvořte třídu uživatele, která je ekvivalentní [třídě IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Třída uživatele musí obsahovat minimálně `Id` `UserName` vlastnost a.

`IdentityUser`Třída definuje vlastnosti, které `UserManager` volání při provádění požadovaných operací. Výchozí typ `Id` vlastnosti je řetězec, ale můžete dědit z `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` a zadat jiný typ. Rozhraní očekává, že implementace úložiště zpracovává převody datových typů.

## <a name="customize-the-user-store"></a>Přizpůsobení úložiště uživatele

Vytvořte `UserStore` třídu, která poskytuje metody pro všechny operace s daty na uživateli. Tato třída je ekvivalentní třídě [UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . Ve vaší `UserStore` třídě implementujte `IUserStore<TUser>` a volitelné požadované rozhraní. Můžete vybrat, která volitelná rozhraní se implementují na základě funkcí poskytovaných ve vaší aplikaci.

### <a name="optional-interfaces"></a>Volitelná rozhraní

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

Volitelná rozhraní dědí z `IUserStore<TUser>` . V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)uvidíte částečně implementované úložiště ukázkových uživatelů.

V rámci `UserStore` třídy použijete třídy pro přístup k datům, které jste vytvořili k provádění operací. Tyto jsou předány pomocí injektáže závislostí. Například v SQL Server s implementací Dapperem `UserStore` má třída `CreateAsync` metodu, která používá instanci `DapperUsersTable` pro vložení nového záznamu:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Rozhraní k implementaci při přizpůsobení úložiště uživatele

* **IUserStore**  
 Rozhraní [ &lt; &gt; TUser IUserStore](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) je jediné rozhraní, které musíte implementovat v úložišti uživatele. Definuje metody pro vytváření, aktualizaci, odstraňování a načítání uživatelů.
* **IUserClaimStore**  
 Rozhraní [ &lt; &gt; TUser IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) definuje metody, které implementujete pro povolení deklarací identity uživatelů. Obsahuje metody pro přidání, odebrání a načtení deklarací identity uživatelů.
* **IUserLoginStore**  
 [IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definuje metody, které implementujete, aby bylo možné povolit externí poskytovatele ověřování. Obsahuje metody pro přidání, odebrání a načtení přihlášení uživatelů a metodu pro načtení uživatele na základě přihlašovacích údajů.
* **IUserRoleStore**  
 Rozhraní [ &lt; &gt; TUser IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) definuje metody, které implementujete k namapování uživatele na roli. Obsahuje metody pro přidání, odebrání a načtení rolí uživatele a metodu, která zkontroluje, jestli je uživatel přiřazený k roli.
* **IUserPasswordStore**  
 Rozhraní [ &lt; &gt; TUser IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) definuje metody, které implementujete pro zachování hesel hash. Obsahuje metody pro získání a nastavení hash hesla a metodu, která označuje, jestli uživatel nastavil heslo.
* **IUserSecurityStampStore**  
 Rozhraní [ &lt; &gt; TUser IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) definuje metody, které implementujete pro použití bezpečnostního razítka pro označení, zda se změnily informace o účtu uživatele. Toto razítko se aktualizuje, když uživatel změní heslo, nebo přidá nebo odebere přihlášení. Obsahuje metody pro získání a nastavení razítka zabezpečení.
* **IUserTwoFactorStore**  
 Rozhraní [IUserTwoFactorStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) definuje metody, které implementujete pro podporu dvojúrovňového ověřování. Obsahuje metody pro získání a nastavení, zda je pro uživatele povoleno dvojúrovňové ověřování.
* **IUserPhoneNumberStore**  
 Rozhraní [ &lt; &gt; TUser IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) definuje metody, které implementujete pro ukládání uživatelských telefonních čísel. Obsahuje metody pro získání a nastavení telefonního čísla a zda je telefonní číslo potvrzené.
* **IUserEmailStore**  
 Rozhraní [ &lt; &gt; TUser IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) definuje metody, které implementujete pro ukládání e-mailových adres uživatele. Obsahuje metody pro získání a nastavení e-mailové adresy a toho, jestli se e-mail potvrdí.
* **IUserLockoutStore**  
 Rozhraní [ &lt; &gt; TUser IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) definuje metody, které implementujete pro ukládání informací o zamykání účtu. Obsahuje metody pro sledování neúspěšných pokusů o přístup a uzamčení.
* **IQueryableUserStore**  
 Rozhraní [IQueryableUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) definuje členy, které implementujete k poskytnutí úložiště uživatele Queryable.

Implementujete pouze rozhraní, která jsou potřebná v aplikaci. Například:

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

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim, IdentityUserLogin a IdentityUserRole

`Microsoft.AspNet.Identity.EntityFramework`Obor názvů obsahuje implementace tříd [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)a [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Pokud tyto funkce používáte, možná budete chtít vytvořit vlastní verze těchto tříd a definovat vlastnosti pro vaši aplikaci. V některých případech je ale při provádění základních operací (například přidání nebo odebrání deklarace identity uživatele) efektivnější tyto entity nečítat do paměti. Místo toho mohou třídy úložiště back-end provádět tyto operace přímo na zdroji dat. Například `UserStore.GetClaimsAsync` metoda může volat `userClaimTable.FindByUserId(user.Id)` metodu pro provedení dotazu přímo v této tabulce a vrácení seznamu deklarací.

## <a name="customize-the-role-class"></a>Přizpůsobení třídy role

Při implementaci poskytovatele úložiště rolí můžete vytvořit vlastní typ role. Nevyžaduje implementaci konkrétního rozhraní, ale musí mít `Id` a obvykle mít `Name` vlastnost.

Následuje příklad třídy role:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Přizpůsobení úložiště rolí

Můžete vytvořit `RoleStore` třídu, která poskytuje metody pro všechny operace s daty v rolích. Tato třída je ekvivalentní třídě [RoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . Ve `RoleStore` třídě implementujete `IRoleStore<TRole>` a volitelně `IQueryableRoleStore<TRole>` rozhraní.

* **IRoleStore &lt; TRole&gt;**  
 Rozhraní [IRoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) definuje metody, které mají být implementovány ve třídě úložiště rolí. Obsahuje metody pro vytváření, aktualizaci, odstraňování a načítání rolí.
* **RoleStore &lt; TRole&gt;**  
 Chcete-li přizpůsobit `RoleStore` , vytvořte třídu, která implementuje `IRoleStore<TRole>` rozhraní. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Překonfigurujte aplikaci tak, aby používala nového poskytovatele úložiště.

Po implementaci poskytovatele úložiště můžete aplikaci nakonfigurovat tak, aby ji používala. Pokud vaše aplikace použila výchozího poskytovatele, nahraďte ji vlastním poskytovatelem.

1. Odeberte `Microsoft.AspNetCore.EntityFramework.Identity` balíček NuGet.
1. Pokud se poskytovatel úložiště nachází v samostatném projektu nebo balíčku, přidejte na něj odkaz.
1. Nahraďte všechny odkazy na pomocí `Microsoft.AspNetCore.EntityFramework.Identity` příkazu Using pro obor názvů vašeho poskytovatele úložiště.
1. V `ConfigureServices` metodě změňte `AddIdentity` metodu tak, aby používala vaše vlastní typy. Pro tento účel můžete vytvořit vlastní metody rozšíření. Příklad najdete v tématu [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Pokud používáte role, aktualizujte, `RoleManager` aby se použila vaše `RoleStore` Třída.
1. Aktualizujte připojovací řetězec a přihlašovací údaje na konfiguraci vaší aplikace.

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

## <a name="references"></a>Reference

* [Vlastní poskytovatelé úložiště pro ASP.NET 4. xIdentity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity ](https://github.com/dotnet/AspNetCore/tree/master/src/Identity): Toto úložiště obsahuje odkazy na komunitní poskytovatele uchovávaného úložiště.
