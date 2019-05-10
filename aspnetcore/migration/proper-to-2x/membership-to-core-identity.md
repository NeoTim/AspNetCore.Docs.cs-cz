---
title: Migrace z ověřování členství technologie ASP.NET do ASP.NET Core 2.0 Identity
author: isaac2004
description: Zjistěte, jak migrovat existující aplikace v ASP.NET pomocí členství ověřování ASP.NET Core 2.0 Identity.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084880"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>Migrace z ověřování členství technologie ASP.NET do ASP.NET Core 2.0 Identity

Podle [Petr Levin](https://isaaclevin.com)

Tento článek popisuje migraci schématu databáze pro aplikace v ASP.NET pomocí členství ověřování ASP.NET Core 2.0 Identity.

> [!NOTE]
> Tento dokument obsahuje kroky potřebné k migraci schématu databáze pro aplikace založené na členství technologie ASP.NET na schéma databáze používané pro ASP.NET Core Identity. Další informace o migraci z ověřování na základě členství technologie ASP.NET na ASP.NET Identity najdete v tématu [migrovat existující aplikace z členství SQL na ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity). Další informace o ASP.NET Core Identity najdete v tématu [Úvod do Identity v ASP.NET Core](xref:security/authentication/identity).

## <a name="review-of-membership-schema"></a>Přehled schématu členství

Před ASP.NET 2.0 byly vývojáři úkol s vytvářením celý proces ověřování a autorizace pro své aplikace. S prostředím ASP.NET 2.0 byla zavedena členství, poskytuje řešení často používaný k řešení zabezpečení v rámci aplikace ASP.NET. Vývojáři byli schopni bootstrap schéma do databáze SQL serveru se teď [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) příkazu. Po spuštění tohoto příkazu, byly vytvořeny následující tabulky v databázi.

  ![Tabulky členství](identity/_static/membership-tables.png)

K migraci stávajících aplikací pro ASP.NET Core 2.0 Identity, je potřeba migrovat do tabulky používají nové schéma Identity data v těchto tabulkách.

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core Identity 2.0 schématu

Následuje ASP.NET Core 2.0 [Identity](/aspnet/identity/index) Princip zavedené v ASP.NET 4.5. Když se sdílí se zásadou, implementace mezi rozhraní se liší, dokonce i mezi verzemi ASP.NET Core (viz [migrace ověřování a identita na ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).

Nejrychlejší způsob, jak zobrazit schéma pro ASP.NET Core 2.0 Identity je vytvoření nové aplikace ASP.NET Core 2.0. Postupujte podle těchto kroků v sadě Visual Studio 2017:

1. Vyberte **Soubor** > **Nový** > **Projekt**.
1. Vytvořte nový **webové aplikace ASP.NET Core** projekt s názvem *CoreIdentitySample*.
1. Vyberte **ASP.NET Core 2.0** v rozevíracím seznamu a pak vyberte **webovou aplikaci**. Tato šablona vytvoří [Razor Pages](xref:razor-pages/index) aplikace. Před kliknutím na tlačítko **OK**, klikněte na tlačítko **změna ověřování**.
1. Zvolte **jednotlivé uživatelské účty** pro šablony Identity. Nakonec klikněte na tlačítko **OK**, pak **OK**. Visual Studio vytvoří projekt pomocí šablony ASP.NET Core Identity.
1. Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků** otevřít **Konzola správce balíčků** Okno (PMC).
1. Přejděte do kořenového adresáře projektu v konzole PMC a spusťte [Entity Framework (EF) Core](/ef/core) `Update-Database` příkazu.

    ASP.NET Core 2.0 Identity používá EF Core interakci s databází ukládání ověřovací data. Aby nově vytvořené aplikace pro práci existuje musí být databázi pro ukládání těchto dat. Po vytvoření nové aplikace, je nejrychlejší způsob, jak zkontrolovat schéma v prostředí s databází k vytvoření databáze pomocí [migrace EF Core](/ef/core/managing-schemas/migrations/). Tento proces vytvoří databázi, buď místně nebo někde jinde, která napodobuje tohoto schématu. Předchozí dokumentaci pro další informace.

    EF Core příkazů použít připojovací řetězec k databázi zadané v *appsettings.json*. Následující připojovací řetězec je zaměřen na databázi na *localhost* s názvem *asp net core identity*. V tomto nastavení se EF Core je nakonfigurován pro použití `DefaultConnection` připojovací řetězec.

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. Vyberte **zobrazení** > **Průzkumník objektů systému SQL Server**. Rozbalte uzel odpovídající zadaný v názvu databáze `ConnectionStrings:DefaultConnection` vlastnost *appsettings.json*.

    `Update-Database` Příkaz vytvořil databázi se schématem a jakékoli data potřebná pro inicializaci aplikace. Následující obrázek znázorňuje strukturu tabulky, který je vytvořen v předchozích krocích.

    ![Identity tabulky](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>Migrace schématu

Jsou drobné rozdíly ve strukturách tabulky a pole pro členství a ASP.NET Core Identity. Vzor se změnilo podstatně ověřování/autorizace pomocí aplikací pro ASP.NET a ASP.NET Core. Jsou klíčové objekty, které se stále používají s identitou *uživatelé* a *role*. Tady jsou mapování tabulky pro *uživatelé*, *role*, a *UserRoles*.

### <a name="users"></a>Uživatelé

|*Identity<br>(dbo.AspNetUsers)*        ||*Členství<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**Název pole**                 |**Typ**|**Název pole**                                    |**Typ**|
|`Id`                           |odkazy řetězců  |`aspnet_Users.UserId`                             |odkazy řetězců  |
|`UserName`                     |odkazy řetězců  |`aspnet_Users.UserName`                           |odkazy řetězců  |
|`Email`                        |odkazy řetězců  |`aspnet_Membership.Email`                         |odkazy řetězců  |
|`NormalizedUserName`           |odkazy řetězců  |`aspnet_Users.LoweredUserName`                    |odkazy řetězců  |
|`NormalizedEmail`              |odkazy řetězců  |`aspnet_Membership.LoweredEmail`                  |odkazy řetězců  |
|`PhoneNumber`                  |odkazy řetězců  |`aspnet_Users.MobileAlias`                        |odkazy řetězců  |
|`LockoutEnabled`               |bitové     |`aspnet_Membership.IsLockedOut`                   |bitové     |

> [!NOTE]
> Ne všechny mapování polí vypadat podobně jako relace 1: 1 z členství pro ASP.NET Core Identity. V předchozí tabulce přebírá výchozí schéma uživatele se členstvím a mapuje na schéma ASP.NET Core Identity. Další vlastní pole, které byly použity pro členství je potřeba namapovat ručně. V toto mapování není žádné mapování pro hesla, jako kritéria hesla a heslo soli není migrace mezi dvěma. **Doporučujeme ponechat heslo jako hodnota null a pokládat uživatelům resetovat svá hesla.** V ASP.NET Core Identity `LockoutEnd` musí být nastavená na datum v budoucnosti, pokud je uživatel uzamčen. To je ukázáno v skript migrace.

### <a name="roles"></a>Role

|*Identita<br>(dbo. AspNetRoles)*        ||*Membership<br>(dbo.aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**Název pole**                 |**Typ**|**Název pole**   |**Typ**         |
|`Id`                           |odkazy řetězců  |`RoleId`         | odkazy řetězců          |
|`Name`                         |odkazy řetězců  |`RoleName`       | odkazy řetězců          |
|`NormalizedName`               |odkazy řetězců  |`LoweredRoleName`| odkazy řetězců          |

### <a name="user-roles"></a>Role uživatele

|*Identity<br>(dbo.AspNetUserRoles)*||*Membership<br>(dbo.aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**Název pole**           |**Typ**  |**Název pole**|**Typ**                   |
|`RoleId`                 |odkazy řetězců    |`RoleId`      |odkazy řetězců                     |
|`UserId`                 |odkazy řetězců    |`UserId`      |odkazy řetězců                     |

Při vytváření skript migrace pro odkazují předchozí tabulky mapování *uživatelé* a *role*. V následujícím příkladu se předpokládá, že máte dvě databáze na serveru databáze. Jedna databáze obsahuje existující členství technologie ASP.NET schéma a data. Druhý *CoreIdentitySample* databáze byla vytvořena pomocí kroků popsaných dříve. Komentáře jsou zahrnuty vložené další podrobnosti.

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

Po dokončení předchozího skriptu aplikace ASP.NET Core Identity dříve vytvořenou se vyplní uživatelů se členstvím. Uživatelé potřebují ke změně hesla před přihlášením.

> [!NOTE]
> Pokud systém členství uživatele s uživatelská jména, které se neshoduje jejich e-mailovou adresu, se musí aplikaci vytvořili dříve, aby tuto skutečnost zohlednit změny. Výchozí šablony očekává, že `UserName` a `Email` být stejné. V situacích, ve kterých charakteristik, je potřeba proces přihlášení by vedla k použití `UserName` místo `Email`.

V `PageModel` přihlašovací stránky v *Pages\Account\Login.cshtml.cs*, odeberte `[EmailAddress]` atribut z *e-mailu* vlastnost. Přejmenujte ho na *uživatelské jméno*. To vyžaduje, aby změnu bez ohledu na to `EmailAddress` je uvedený v *zobrazení* a *PageModel*. Výsledek bude vypadat nějak takto:

 ![Oprava přihlášení](identity/_static/fixed-login.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak přenést uživatele z členství SQL na ASP.NET Core 2.0 Identity. Další informace o ASP.NET Core Identity najdete v tématu [Úvod do Identity](xref:security/authentication/identity).
