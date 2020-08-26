---
title: Migrace z ověřování členství ASP.NET do ASP.NET Core 2,0 Identity
author: isaac2004
description: Naučte se migrovat existující aplikace ASP.NET pomocí ověřování členství na ASP.NET Core 2,0 Identity .
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: a9ec02381b156a6599042d8e504a476036246302
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865562"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-no-locidentity"></a>Migrace z ověřování členství ASP.NET do ASP.NET Core 2,0 Identity

Od [Petr Levin](https://isaaclevin.com)

Tento článek popisuje migraci schématu databáze pro aplikace ASP.NET pomocí ověřování členství na ASP.NET Core 2,0 Identity .

> [!NOTE]
> Tento dokument popisuje kroky potřebné k migraci schématu databáze pro aplikace založené na členství ASP.NET do schématu databáze používaného pro ASP.NET Core Identity . Další informace o migraci z ASP.NET ověřování na základě členství na ASP.NET najdete Identity v tématu [migrace stávající aplikace z členství SQL do ASP.NET Identity ](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Další informace o najdete v ASP.NET Core Identity tématu [úvod do Identity ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Kontrola schématu členství

Před ASP.NET 2,0 bylo vývojářům uděleno vytváření celého procesu ověřování a autorizace pro své aplikace. S ASP.NET 2,0 se zavedlo členství, které poskytuje často používané řešení pro zpracování zabezpečení v aplikacích ASP.NET. Vývojáři teď mohli do databáze SQL Server spustit schéma pomocí <https://docs.microsoft.com/previous-versions/ms229862(v=vs.140)> příkazu. Po spuštění tohoto příkazu byly v databázi vytvořeny následující tabulky.

  ![Tabulky členství](identity/_static/membership-tables.png)

Chcete-li migrovat existující aplikace na ASP.NET Core 2,0 Identity , musí být data v těchto tabulkách migrována do tabulek používaných novým Identity schématem.

## <a name="no-locaspnet-core-identity-20-schema"></a>ASP.NET Core Identity schéma 2,0

ASP.NET Core 2,0 odpovídá [Identity](/aspnet/identity/index) principu zavedenému v ASP.NET 4,5. I když je zásada sdílená, implementace mezi architekturami se liší i mezi verzemi ASP.NET Core (viz [migrace ověřování a Identity ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

Nejrychlejší způsob, jak zobrazit schéma pro ASP.NET Core 2,0, Identity je vytvoření nové aplikace ASP.NET Core 2,0. Postupujte podle těchto kroků v aplikaci Visual Studio 2017:

1. Vyberte **soubor**  >  **Nový**  >  **projekt**.
1. Vytvořte nový projekt **ASP.NET Core webové aplikace** s názvem *Core Identity Sample*.
1. V rozevíracím seznamu vyberte **ASP.NET Core 2,0** a potom vyberte **Webová aplikace**. Tato šablona vytvoří aplikaci [ Razor stránky](xref:razor-pages/index) . Než kliknete na **OK**, klikněte na **změnit ověřování**.
1. Vyberte **jednotlivé uživatelské účty** pro Identity šablony. Nakonec klikněte na **OK**a pak na **OK**. Visual Studio vytvoří projekt pomocí ASP.NET Core Identity šablony.
1. Vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** a otevřete tak okno **konzoly Správce balíčků** (PMC).
1. Přejděte do kořenového adresáře projektu v PMC a spusťte příkaz [Entity Framework (EF) Core](/ef/core) `Update-Database` .

    ASP.NET Core 2,0 Identity používá EF Core k interakci s databází, která ukládá ověřovací data. Aby nově vytvořená aplikace fungovala, musí být databáze pro uložení těchto dat. Po vytvoření nové aplikace je nejrychlejší způsob, jak zkontrolovat schéma v databázovém prostředí, vytvořit databázi pomocí [EF Core migrace](/ef/core/managing-schemas/migrations/). Tento proces vytvoří databázi buď místně, nebo jinde, což napodobuje toto schéma. Další informace najdete v předchozí dokumentaci.

    Příkazy EF Core používají připojovací řetězec pro databázi určenou v *appsettings.jsna*. Následující připojovací řetězec cílí na databázi na *localhost* s názvem *ASP-NET-Core-identity*. V tomto nastavení je EF Core nakonfigurován pro použití `DefaultConnection` připojovacího řetězce.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Vyberte **zobrazení**  >  **Průzkumník objektů systému SQL Server**. Rozbalte uzel odpovídající názvu databáze zadané ve `ConnectionStrings:DefaultConnection` vlastnosti *appsettings.jsv*.

    `Update-Database`Příkaz vytvořil databázi zadanou se schématem a všemi daty potřebnými k inicializaci aplikace. Následující obrázek znázorňuje strukturu tabulky, která je vytvořená pomocí předchozích kroků.

    ![::: No-Loc (identita)::: Tables](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrace schématu

Existují drobné rozdíly v strukturách tabulek a polích pro členství a ASP.NET Core Identity . Vzor se podstatně změnil pro ověřování nebo autorizaci pomocí aplikací ASP.NET a ASP.NET Core. Klíčové objekty, které se pořád používají, Identity jsou *Uživatelé* a *role*. Tady jsou mapování tabulek pro *uživatele*, *role*a *položka userroles může*.

### <a name="users"></a>Uživatelé

|Identity<br>( `dbo.AspNetUsers` ) sloupec  |Typ     |Členství<br>( `dbo.aspnet_Users`  /  `dbo.aspnet_Membership` ) sloupec|Typ      |
|-------------------------------------------|-----------------------------------------------------------------------|
| `Id`                            | `string`| `aspnet_Users.UserId`                                      | `string` |
| `UserName`                      | `string`| `aspnet_Users.UserName`                                    | `string` |
| `Email`                         | `string`| `aspnet_Membership.Email`                                  | `string` |
| `NormalizedUserName`            | `string`| `aspnet_Users.LoweredUserName`                             | `string` |
| `NormalizedEmail`               | `string`| `aspnet_Membership.LoweredEmail`                           | `string` |
| `PhoneNumber`                   | `string`| `aspnet_Users.MobileAlias`                                 | `string` |
| `LockoutEnabled`                | `bit`   | `aspnet_Membership.IsLockedOut`                            | `bit`    |

> [!NOTE]
> Ne všechna mapování polí se podobají relacím 1:1 z členství ASP.NET Core Identity . Předchozí tabulka převezme výchozí schéma uživatele členství a namapuje ho do ASP.NET Core Identity schématu. Všechna další vlastní pole, která byla použita pro členství, je nutné namapovat ručně. V tomto mapování není k dispozici žádná mapa hesel, protože mezi těmito dvěma kritérii hesla a resůl hesla nedojde k migraci. **Doporučuje se ponechat heslo jako null a požádat uživatele, aby obnovili hesla.** V ASP.NET Core Identity nástroji `LockoutEnd` by mělo být nastaveno na nějaké datum v budoucnu, pokud je uživatel uzamčen. Tato ukázka se zobrazí ve skriptu migrace.

### <a name="roles"></a>Role

|Identity<br>( `dbo.AspNetRoles` ) sloupec|Typ|Členství<br>( `dbo.aspnet_Roles` ) sloupec|Typ|
|----------------------------------------|-----------------------------------|
|`Id`                           |`string`|`RoleId`         | `string`        |
|`Name`                         |`string`|`RoleName`       | `string`        |
|`NormalizedName`               |`string`|`LoweredRoleName`| `string`        |

### <a name="user-roles"></a>User Roles

|Identity<br>( `dbo.AspNetUserRoles` ) sloupec|Typ|Členství<br>( `dbo.aspnet_UsersInRoles` ) sloupec|Typ|
|-------------------------|----------|--------------|---------------------------|
|`RoleId`                 |`string`  |`RoleId`      |`string`                   |
|`UserId`                 |`string`  |`UserId`      |`string`                   |

Při vytváření migračního skriptu pro *uživatele* a *role*odkázat na předchozí tabulky mapování. Následující příklad předpokládá, že máte na databázovém serveru dvě databáze. Jedna databáze obsahuje existující schéma členství ASP.NET a data. Druhá *základní Identity ukázková* databáze byla vytvořena pomocí kroků popsaných výše. K dispozici jsou vložené komentáře pro další podrobnosti.

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

Po dokončení předchozího skriptu ASP.NET Core Identity se dříve vytvořená aplikace vyplní uživateli členství. Uživatelé musí před přihlášením změnit svoje heslo.

> [!NOTE]
> Pokud má systém členství uživatele s uživatelskými jmény, které neodpovídaly jejich e-mailové adrese, je nutné aplikaci vytvořenou dříve změnit. Výchozí šablona očekává `UserName` a `Email` bude stejná. V situacích, kdy se liší, je třeba změnit proces přihlášení, aby používal `UserName` místo `Email` .

Na `PageModel` stránce pro přihlášení, která se nachází na adrese *Pages\Account\Login.cshtml.cs*, odeberte `[EmailAddress]` atribut z vlastnosti *e-mail* . Přejmenujte ho na *uživatelské jméno*. To vyžaduje změnu, kdykoli `EmailAddress` je uvedeno v *zobrazení* a *PageModel*. Výsledek bude vypadat následovně:

 ![Pevné přihlášení](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak přenést uživatele z členství SQL do ASP.NET Core 2,0 Identity . Další informace o ASP.NET Core Identity najdete v tématu [Úvod do Identity ](xref:security/authentication/identity).
