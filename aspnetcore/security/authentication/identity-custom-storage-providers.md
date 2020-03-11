---
title: Vlastní poskytovatelé úložiště pro ASP.NET Core identity
author: ardalis
description: Přečtěte si, jak nakonfigurovat vlastní poskytovatele úložiště pro ASP.NET Core identitu.
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 574e66e4dedaf0bfd01d600c3ded4bfb5d1865cd
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664477"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>Vlastní poskytovatelé úložiště pro ASP.NET Core identity

[Steve Smith](https://ardalis.com/)

ASP.NET Core identity je rozšiřitelný systém, který umožňuje vytvořit vlastního poskytovatele úložiště a připojit ho k aplikaci. Toto téma popisuje, jak vytvořit přizpůsobeného poskytovatele úložiště pro ASP.NET Core identitu. Popisuje důležité koncepty pro vytváření vlastního poskytovatele úložiště, ale není to podrobný návod.

[Zobrazení nebo stažení ukázky z GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Úvod

Ve výchozím nastavení ukládá systém ASP.NET Core identity informace o uživatelích do databáze SQL Server pomocí Entity Framework Core. U mnoha aplikací funguje tento přístup dobře. Můžete ale chtít použít jiný mechanismus trvalosti nebo schéma dat. Příklad:

* Používáte [Azure Table Storage](/azure/storage/) nebo jiné úložiště dat.
* Tabulky databáze mají jinou strukturu. 
* Můžete chtít použít jiný přístup k datům, jako je například [dapperem](https://github.com/StackExchange/Dapper). 

V každém z těchto případů můžete napsat přizpůsobeného poskytovatele úložného mechanismu a připojit tohoto poskytovatele k vaší aplikaci.

ASP.NET Core identita je zahrnuta v šablonách projektů v aplikaci Visual Studio s možností jednotlivé uživatelské účty.

Při použití .NET Core CLI přidejte `-au Individual`:

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>Architektura ASP.NET Core identity

ASP.NET Core identita se skládá ze tříd nazvaných manažeři a obchody. *Správci* jsou třídy vysoké úrovně, které vývojář aplikace používá k provádění operací, jako je například vytvoření uživatele identity. *Úložiště* jsou třídy nižší úrovně, které určují, jak jsou trvalé entity, jako jsou uživatelé a role. Úložiště se řídí vzorem úložiště a jsou úzce spojeny s mechanismem trvalosti. Manažeři jsou oddělené od úložišť, což znamená, že můžete nahradit mechanismus trvalosti beze změny kódu aplikace (s výjimkou konfigurace).

Následující diagram znázorňuje, jak webová aplikace komunikuje s manažery, zatímco ukládá interakce s vrstvami přístupu k datům.

![ASP.NET Core aplikace pracují se správci (například "UserManager", "RoleManager"). Manažeři pracují s obchody (například "UserStore"), které komunikují se zdrojem dat pomocí knihovny, jako je Entity Framework Core.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Chcete-li vytvořit vlastního poskytovatele úložiště, vytvořte zdroj dat, vrstvu přístupu k datům a třídy úložiště, které pracují s touto vrstvou přístupu k datům (zelená a šedá pole v diagramu výše). Nemusíte přizpůsobovat manažery ani kód vaší aplikace, který s nimi komunikuje (modré čtverečky výše).

Při vytváření nové instance `UserManager` nebo `RoleManager` zadejte typ třídy uživatele a předejte instanci třídy úložiště jako argument. Tento přístup umožňuje zapojit přizpůsobené třídy do ASP.NET Core. 

[Překonfigurování aplikace na použití nového poskytovatele úložiště](#reconfigure-app-to-use-a-new-storage-provider) ukazuje, jak vytvořit instanci `UserManager` a `RoleManager` s přizpůsobeným úložištěm.

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core identity ukládá datové typy

[ASP.NET Core](https://github.com/aspnet/identity) datové typy identity jsou podrobně popsány v následujících částech:

### <a name="users"></a>Uživatelé

Registrovaní uživatelé vašeho webu. Typ [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) může být rozšířen nebo použit jako příklad pro vlastní typ. K implementaci vlastního řešení úložiště identity není nutné dědit z konkrétního typu.

### <a name="user-claims"></a>Deklarace identity uživatelů

Sada příkazů (nebo [deklarací identity](/dotnet/api/system.security.claims.claim)) o uživateli, který představuje identitu uživatele. Může povolit větší výraz identity uživatele, než je možné dosáhnout prostřednictvím rolí.

### <a name="user-logins"></a>Přihlášení uživatelů

Informace o externím poskytovateli ověřování (například Facebooku nebo účet Microsoft), který se použije při přihlašování uživatele [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Role

Skupiny autorizace pro váš web. Zahrnuje ID role a název role (například admin nebo zaměstnanec). [Příklad](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Vrstva přístupu k datům

V tomto tématu se předpokládá, že jste obeznámeni s mechanismem trvalosti, který budete používat, a vytváření entit pro tento mechanismus. Toto téma neposkytuje podrobné informace o tom, jak vytvořit úložiště nebo třídy přístupu k datům. poskytuje některé návrhy na rozhodování o návrhu při práci s ASP.NET Core identitou.

Při navrhování vrstvy přístupu k datům pro přizpůsobeného poskytovatele úložiště máte spoustu volnosti. K funkcím, které máte v úmyslu používat, je třeba vytvořit mechanismy trvalosti. Pokud například v aplikaci nepoužíváte role, nemusíte vytvářet úložiště pro role nebo přidružení rolí uživatele. Vaše technologie a stávající infrastruktura můžou vyžadovat strukturu, která se velmi liší od výchozí implementace ASP.NET Core identity. Ve vrstvě pro přístup k datům poskytnete logiku pro práci se strukturou implementace úložiště.

Vrstva přístupu k datům poskytuje logiku pro uložení dat z ASP.NET Core identity do zdroje dat. Vrstva přístupu k datům pro vlastního poskytovatele úložiště může obsahovat následující třídy pro ukládání informací o uživatelích a rolích.

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

Logika implementace pro vytvoření uživatele je v metodě `_usersTable.CreateAsync`, která je uvedená níže.

## <a name="customize-the-user-class"></a>Přizpůsobení třídy uživatele

Při implementaci poskytovatele úložiště vytvořte třídu uživatele, která je ekvivalentní [třídě IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).

Třída uživatele musí obsahovat minimálně `Id` a vlastnost `UserName`.

Třída `IdentityUser` definuje vlastnosti, které `UserManager` volá při provádění požadovaných operací. Výchozí typ `Id` vlastnosti je řetězec, ale můžete dědit z `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` a zadat jiný typ. Rozhraní očekává, že implementace úložiště zpracovává převody datových typů.

## <a name="customize-the-user-store"></a>Přizpůsobení úložiště uživatele

Vytvořte třídu `UserStore`, která poskytuje metody pro všechny operace s daty na uživateli. Tato třída je ekvivalentem třídy [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) . Ve vaší třídě `UserStore` implementujte `IUserStore<TUser>` a požadovaná volitelné rozhraní. Můžete vybrat, která volitelná rozhraní se implementují na základě funkcí poskytovaných ve vaší aplikaci.

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

Volitelná rozhraní dědí od `IUserStore<TUser>`. V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)uvidíte částečně implementované úložiště ukázkových uživatelů.

V rámci třídy `UserStore` používáte třídy pro přístup k datům, které jste vytvořili k provádění operací. Tyto jsou předány pomocí injektáže závislostí. Například v SQL Server s implementací Dapperem má třída `UserStore` metodu `CreateAsync`, která používá instanci `DapperUsersTable` k vložení nového záznamu:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Rozhraní k implementaci při přizpůsobení úložiště uživatele

* **IUserStore**  
 Rozhraní [&gt;IUserStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) je jediné rozhraní, které musíte implementovat v úložišti uživatele. Definuje metody pro vytváření, aktualizaci, odstraňování a načítání uživatelů.
* **IUserClaimStore**  
 Rozhraní [&gt;IUserClaimStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) definuje metody, které implementujete pro povolení deklarací identity uživatelů. Obsahuje metody pro přidání, odebrání a načtení deklarací identity uživatelů.
* **IUserLoginStore**  
 [&gt;IUserLoginStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) definuje metody, které implementujete, aby bylo možné povolit externí poskytovatele ověřování. Obsahuje metody pro přidání, odebrání a načtení přihlášení uživatelů a metodu pro načtení uživatele na základě přihlašovacích údajů.
* **IUserRoleStore**  
 Rozhraní [&gt;IUserRoleStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) definuje metody, které implementujete k namapování uživatele na roli. Obsahuje metody pro přidání, odebrání a načtení rolí uživatele a metodu, která zkontroluje, jestli je uživatel přiřazený k roli.
* **IUserPasswordStore**  
 Rozhraní [&gt;IUserPasswordStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) definuje metody, které implementujete pro zachování hesel hash. Obsahuje metody pro získání a nastavení hash hesla a metodu, která označuje, jestli uživatel nastavil heslo.
* **IUserSecurityStampStore**  
 Rozhraní [&gt;IUserSecurityStampStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) definuje metody, které implementujete pro použití bezpečnostního razítka k označení, zda se změnily informace o účtu uživatele. Toto razítko se aktualizuje, když uživatel změní heslo, nebo přidá nebo odebere přihlášení. Obsahuje metody pro získání a nastavení razítka zabezpečení.
* **IUserTwoFactorStore**  
 Rozhraní [&gt;IUserTwoFactorStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) definuje metody, které implementujete pro podporu dvojúrovňového ověřování. Obsahuje metody pro získání a nastavení, zda je pro uživatele povoleno dvojúrovňové ověřování.
* **IUserPhoneNumberStore**  
 Rozhraní [&gt;IUserPhoneNumberStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) definuje metody, které implementujete pro ukládání uživatelských telefonních čísel. Obsahuje metody pro získání a nastavení telefonního čísla a zda je telefonní číslo potvrzené.
* **IUserEmailStore**  
 Rozhraní [&gt;IUserEmailStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) definuje metody, které implementujete pro ukládání e-mailových adres uživatele. Obsahuje metody pro získání a nastavení e-mailové adresy a toho, jestli se e-mail potvrdí.
* **IUserLockoutStore**  
 Rozhraní [&gt;IUserLockoutStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) definuje metody, které implementujete pro ukládání informací o zamykání účtu. Obsahuje metody pro sledování neúspěšných pokusů o přístup a uzamčení.
* **IQueryableUserStore**  
 Rozhraní [&gt;IQueryableUserStore&lt;TUser](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) definuje členy, které implementujete k poskytnutí úložiště uživatele Queryable.

Implementujete pouze rozhraní, která jsou potřebná v aplikaci. Příklad:

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

Obor názvů `Microsoft.AspNet.Identity.EntityFramework` obsahuje implementace tříd [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)a [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) . Pokud tyto funkce používáte, možná budete chtít vytvořit vlastní verze těchto tříd a definovat vlastnosti pro vaši aplikaci. V některých případech je ale při provádění základních operací (například přidání nebo odebrání deklarace identity uživatele) efektivnější tyto entity nečítat do paměti. Místo toho mohou třídy úložiště back-end provádět tyto operace přímo na zdroji dat. Například metoda `UserStore.GetClaimsAsync` může volat metodu `userClaimTable.FindByUserId(user.Id)` a spustit dotaz přímo v této tabulce a vrátit seznam deklarací.

## <a name="customize-the-role-class"></a>Přizpůsobení třídy role

Při implementaci poskytovatele úložiště rolí můžete vytvořit vlastní typ role. Nevyžaduje implementaci konkrétního rozhraní, ale musí mít `Id` a obvykle má vlastnost `Name`.

Následuje příklad třídy role:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Přizpůsobení úložiště rolí

Můžete vytvořit třídu `RoleStore`, která poskytuje metody pro všechny operace s daty v rolích. Tato třída je ekvivalentem třídy [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) . Ve třídě `RoleStore` implementujete `IRoleStore<TRole>` a případně rozhraní `IQueryableRoleStore<TRole>`.

* **IRoleStore&lt;TRole&gt;**  
 Rozhraní [&gt;IRoleStore&lt;TRole](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) definuje metody, které se mají implementovat do třídy úložiště rolí. Obsahuje metody pro vytváření, aktualizaci, odstraňování a načítání rolí.
* **RoleStore&lt;TRole&gt;**  
 Chcete-li přizpůsobit `RoleStore`, vytvořte třídu, která implementuje rozhraní `IRoleStore<TRole>`. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Překonfigurujte aplikaci tak, aby používala nového poskytovatele úložiště.

Po implementaci poskytovatele úložiště můžete aplikaci nakonfigurovat tak, aby ji používala. Pokud vaše aplikace použila výchozího poskytovatele, nahraďte ji vlastním poskytovatelem.

1. Odeberte balíček NuGet `Microsoft.AspNetCore.EntityFramework.Identity`.
1. Pokud se poskytovatel úložiště nachází v samostatném projektu nebo balíčku, přidejte na něj odkaz.
1. Nahraďte všechny odkazy na `Microsoft.AspNetCore.EntityFramework.Identity` pomocí příkazu Using pro obor názvů vašeho poskytovatele úložiště.
1. V metodě `ConfigureServices` změňte metodu `AddIdentity` tak, aby používala vaše vlastní typy. Pro tento účel můžete vytvořit vlastní metody rozšíření. Příklad najdete v tématu [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Pokud používáte role, aktualizujte `RoleManager` pro použití vaší třídy `RoleStore`.
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

## <a name="references"></a>Odkazy

* [Vlastní poskytovatelé úložiště pro identitu ASP.NET 4. x](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; toto úložiště obsahuje odkazy na spravované poskytovatele obchodů v komunitě.
