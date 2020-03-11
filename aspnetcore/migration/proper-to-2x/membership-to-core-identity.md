---
title: Migrace z ověřování členství ASP.NET do identity ASP.NET Core 2,0
author: isaac2004
description: Naučte se migrovat existující aplikace ASP.NET pomocí ověřování členství ASP.NET Core identitě 2,0.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659241"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrace z ověřování členství ASP.NET do identity ASP.NET Core 2,0

Od [Petr Levin](https://isaaclevin.com)

Tento článek popisuje migraci schématu databáze pro aplikace ASP.NET, které používají ověřování členství, k ASP.NET Core 2,0 identit.

> [!NOTE]
> Tento dokument popisuje kroky potřebné k migraci schématu databáze pro aplikace založené na členství ASP.NET do schématu databáze používaného pro ASP.NET Coreou identitu. Další informace o migraci z ASP.NET ověřování na základě členství na ASP.NET Identity najdete v tématu [migrace stávající aplikace z členství SQL do ASP.NET identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Další informace o ASP.NET Core identitě najdete v tématu [Úvod do identity v ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Kontrola schématu členství

Před ASP.NET 2,0 bylo vývojářům uděleno vytváření celého procesu ověřování a autorizace pro své aplikace. S ASP.NET 2,0 se zavedlo členství, které poskytuje často používané řešení pro zpracování zabezpečení v aplikacích ASP.NET. Vývojáři teď dokázali spustit schéma do databáze SQL Server pomocí příkazu [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) . Po spuštění tohoto příkazu byly v databázi vytvořeny následující tabulky.

  ![Tabulky členství](identity/_static/membership-tables.png)

Chcete-li migrovat existující aplikace na identitu ASP.NET Core 2,0, musí být data v těchto tabulkách migrována do tabulek používaných novým schématem identity.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core schéma identity 2,0

ASP.NET Core 2,0 se řídí principem [identity](/aspnet/identity/index) představeným v ASP.NET 4,5. I když je zásada sdílená, implementace mezi architekturami se liší i mezi verzemi ASP.NET Core (viz [migrace ověřování a identita do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).

Nejrychlejší způsob, jak zobrazit schéma pro ASP.NET Core 2,0 identity, je vytvoření nové aplikace ASP.NET Core 2,0. Postupujte podle těchto kroků v aplikaci Visual Studio 2017:

1. Vyberte **Soubor** > **Nový** > **Projekt**.
1. Vytvořte nový projekt **ASP.NET Core webové aplikace** s názvem *CoreIdentitySample*.
1. V rozevíracím seznamu vyberte **ASP.NET Core 2,0** a potom vyberte **Webová aplikace**. Tato šablona vytvoří aplikaci [Razor Pages](xref:razor-pages/index) . Než kliknete na **OK**, klikněte na **změnit ověřování**.
1. Vyberte **jednotlivé uživatelské účty** pro šablony identity. Nakonec klikněte na **OK**a pak na **OK**. Visual Studio vytvoří projekt pomocí šablony ASP.NET Core identity.
1. Výběrem **nástrojů** > **správce balíčků NuGet** > **konzole správce balíčků** otevřete okno **konzoly Správce balíčků** (PMC).
1. Přejděte do kořenového adresáře projektu v PMC a spusťte příkaz [Entity Framework (EF) Core](/ef/core) `Update-Database`.

    Identita ASP.NET Core 2,0 používá EF Core k interakci s databází, která ukládá ověřovací data. Aby nově vytvořená aplikace fungovala, musí být databáze pro uložení těchto dat. Po vytvoření nové aplikace je nejrychlejší způsob, jak zkontrolovat schéma v databázovém prostředí, vytvořit databázi pomocí [EF Core migrace](/ef/core/managing-schemas/migrations/). Tento proces vytvoří databázi buď místně, nebo jinde, což napodobuje toto schéma. Další informace najdete v předchozí dokumentaci.

    Příkazy EF Core používají připojovací řetězec pro databázi určenou v souboru *appSettings. JSON*. Následující připojovací řetězec cílí na databázi na *localhost* s názvem *ASP-NET-Core-identity*. V tomto nastavení je EF Core nakonfigurovaná tak, aby používala připojovací řetězec `DefaultConnection`.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Vyberte **zobrazení** > **Průzkumník objektů systému SQL Server**. Rozbalte uzel odpovídající názvu databáze zadané ve vlastnosti `ConnectionStrings:DefaultConnection` *appSettings. JSON*.

    Příkaz `Update-Database` vytvořil databázi zadanou pomocí schématu a všechna data potřebná pro inicializaci aplikace. Následující obrázek znázorňuje strukturu tabulky, která je vytvořená pomocí předchozích kroků.

    ![Tabulky identity](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrace schématu

Existují drobné rozdíly v strukturách tabulek a polích pro členství i ASP.NET Core identitu. Vzor se podstatně změnil pro ověřování nebo autorizaci pomocí aplikací ASP.NET a ASP.NET Core. Klíčové objekty, které jsou pořád používané s identitou, jsou *Uživatelé* a *role*. Tady jsou mapování tabulek pro *uživatele*, *role*a *položka userroles může*.

### <a name="users"></a>Uživatelé

|*<br>identity (dbo. AspNetUsers)*        ||*<br>členství (dbo. aspnet_Users/dbo. aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Název pole**                 |**Typ**|**Název pole**                                    |**Typ**|
|`Id`                           |řetězec  |`aspnet_Users.UserId`                             |řetězec  |
|`UserName`                     |řetězec  |`aspnet_Users.UserName`                           |řetězec  |
|`Email`                        |řetězec  |`aspnet_Membership.Email`                         |řetězec  |
|`NormalizedUserName`           |řetězec  |`aspnet_Users.LoweredUserName`                    |řetězec  |
|`NormalizedEmail`              |řetězec  |`aspnet_Membership.LoweredEmail`                  |řetězec  |
|`PhoneNumber`                  |řetězec  |`aspnet_Users.MobileAlias`                        |řetězec  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> Ne všechna mapování polí se podobají relacím 1:1 z členství ASP.NET Core identitou. Předchozí tabulka přijímá výchozí schéma uživatele členství a mapuje je na ASP.NET Core schématu identity. Všechna další vlastní pole, která byla použita pro členství, je nutné namapovat ručně. V tomto mapování není k dispozici žádná mapa hesel, protože mezi těmito dvěma kritérii hesla a resůl hesla nedojde k migraci. **Doporučuje se ponechat heslo jako null a požádat uživatele, aby obnovili hesla.** V ASP.NET Core identit by `LockoutEnd` měla být nastavená na nějaké datum v budoucnu, pokud je uživatel uzamčený. Tato ukázka se zobrazí ve skriptu migrace.

### <a name="roles"></a>Role

|*<br>identity (dbo. AspNetRoles)*        ||*<br>členství (dbo. aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Název pole**                 |**Typ**|**Název pole**   |**Typ**         |
|`Id`                           |řetězec  |`RoleId`         | řetězec          |
|`Name`                         |řetězec  |`RoleName`       | řetězec          |
|`NormalizedName`               |řetězec  |`LoweredRoleName`| řetězec          |

### <a name="user-roles"></a>Uživatelské role

|*<br>identity (dbo. AspNetUserRoles)*||*<br>členství (dbo. aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Název pole**           |**Typ**  |**Název pole**|**Typ**                   |
|`RoleId`                 |řetězec    |`RoleId`      |řetězec                     |
|`UserId`                 |řetězec    |`UserId`      |řetězec                     |

Při vytváření migračního skriptu pro *uživatele* a *role*odkázat na předchozí tabulky mapování. Následující příklad předpokládá, že máte na databázovém serveru dvě databáze. Jedna databáze obsahuje existující schéma členství ASP.NET a data. Druhá databáze *CoreIdentitySample* byla vytvořena pomocí kroků popsaných výše. K dispozici jsou vložené komentáře pro další podrobnosti.

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

Po dokončení předchozího skriptu se dříve vytvořená aplikace ASP.NET Core identity vyplní uživateli členství. Uživatelé musí před přihlášením změnit svoje heslo.

> [!NOTE]
> Pokud má systém členství uživatele s uživatelskými jmény, které neodpovídaly jejich e-mailové adrese, je nutné aplikaci vytvořenou dříve změnit. Výchozí šablona očekává `UserName` a `Email` stejné. V situacích, kdy se liší, je třeba změnit proces přihlášení, aby používal `UserName` místo `Email`.

V `PageModel` přihlašovací stránky, která se nachází na adrese *Pages\Account\Login.cshtml.cs*, odeberte atribut `[EmailAddress]` z vlastnosti *e-mail* . Přejmenujte ho na *uživatelské jméno*. To vyžaduje změnu všude, kde je uvedeno `EmailAddress`, a to v *zobrazení* a *PageModel*. Výsledek bude vypadat následovně:

 ![Pevné přihlášení](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak připravovat uživatele z členství SQL k ASP.NET Core 2,0 identit. Další informace týkající se ASP.NET Core identity najdete v tématu [Úvod do identity](xref:security/authentication/identity).
