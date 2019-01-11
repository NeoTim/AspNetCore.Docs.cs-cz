---
title: Migrace z ověřování členství technologie ASP.NET do ASP.NET Core 2.0 Identity
author: isaac2004
description: Zjistěte, jak migrovat existující aplikace v ASP.NET pomocí členství ověřování ASP.NET Core 2.0 Identity.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 0b7001a311eeaaa78e3d52e2ec66d33ad057c381
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207405"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="7b782-103">Migrace z ověřování členství technologie ASP.NET do ASP.NET Core 2.0 Identity</span><span class="sxs-lookup"><span data-stu-id="7b782-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="7b782-104">Podle [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="7b782-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="7b782-105">Tento článek popisuje migraci schématu databáze pro aplikace v ASP.NET pomocí členství ověřování ASP.NET Core 2.0 Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="7b782-106">Tento dokument obsahuje kroky potřebné k migraci schématu databáze pro aplikace založené na členství technologie ASP.NET na schéma databáze používané pro ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="7b782-107">Další informace o migraci z ověřování na základě členství technologie ASP.NET na ASP.NET Identity najdete v tématu [migrovat existující aplikace z členství SQL na ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="7b782-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="7b782-108">Další informace o ASP.NET Core Identity najdete v tématu [Úvod do Identity v ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7b782-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="7b782-109">Přehled schématu členství</span><span class="sxs-lookup"><span data-stu-id="7b782-109">Review of Membership schema</span></span>

<span data-ttu-id="7b782-110">Před ASP.NET 2.0 byly vývojáři úkol s vytvářením celý proces ověřování a autorizace pro své aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b782-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="7b782-111">S prostředím ASP.NET 2.0 byla zavedena členství, poskytuje řešení často používaný k řešení zabezpečení v rámci aplikace ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="7b782-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="7b782-112">Vývojáři byli schopni bootstrap schéma do databáze SQL serveru se teď [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) příkazu.</span><span class="sxs-lookup"><span data-stu-id="7b782-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="7b782-113">Po spuštění tohoto příkazu, byly vytvořeny následující tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="7b782-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabulky členství](identity/_static/membership-tables.png)

<span data-ttu-id="7b782-115">K migraci stávajících aplikací pro ASP.NET Core 2.0 Identity, je potřeba migrovat do tabulky používají nové schéma Identity data v těchto tabulkách.</span><span class="sxs-lookup"><span data-stu-id="7b782-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="7b782-116">ASP.NET Core Identity 2.0 schématu</span><span class="sxs-lookup"><span data-stu-id="7b782-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="7b782-117">Následuje ASP.NET Core 2.0 [Identity](/aspnet/identity/index) Princip zavedené v ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="7b782-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="7b782-118">Když se sdílí se zásadou, implementace mezi rozhraní se liší, dokonce i mezi verzemi ASP.NET Core (viz [migrace ověřování a identita na ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="7b782-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="7b782-119">Nejrychlejší způsob, jak zobrazit schéma pro ASP.NET Core 2.0 Identity je vytvoření nové aplikace ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="7b782-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="7b782-120">Postupujte podle těchto kroků v sadě Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="7b782-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="7b782-121">Vyberte **Soubor** > **Nový** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="7b782-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="7b782-122">Vytvořte nový **webové aplikace ASP.NET Core** projekt s názvem *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="7b782-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="7b782-123">Vyberte **ASP.NET Core 2.0** v rozevíracím seznamu a pak vyberte **webovou aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="7b782-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="7b782-124">Tato šablona vytvoří [Razor Pages](xref:razor-pages/index) aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b782-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7b782-125">Před kliknutím na tlačítko **OK**, klikněte na tlačítko **změna ověřování**.</span><span class="sxs-lookup"><span data-stu-id="7b782-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="7b782-126">Zvolte **jednotlivé uživatelské účty** pro šablony Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="7b782-127">Nakonec klikněte na tlačítko **OK**, pak **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b782-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="7b782-128">Visual Studio vytvoří projekt pomocí šablony ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="7b782-129">Vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků** otevřít **Konzola správce balíčků** Okno (PMC).</span><span class="sxs-lookup"><span data-stu-id="7b782-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="7b782-130">Přejděte do kořenového adresáře projektu v konzole PMC a spusťte [Entity Framework (EF) Core](/ef/core) `Update-Database` příkazu.</span><span class="sxs-lookup"><span data-stu-id="7b782-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="7b782-131">ASP.NET Core 2.0 Identity používá EF Core interakci s databází ukládání ověřovací data.</span><span class="sxs-lookup"><span data-stu-id="7b782-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="7b782-132">Aby nově vytvořené aplikace pro práci existuje musí být databázi pro ukládání těchto dat.</span><span class="sxs-lookup"><span data-stu-id="7b782-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="7b782-133">Po vytvoření nové aplikace, je nejrychlejší způsob, jak zkontrolovat schéma v prostředí s databází k vytvoření databáze pomocí [migrace EF Core](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="7b782-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="7b782-134">Tento proces vytvoří databázi, buď místně nebo někde jinde, která napodobuje tohoto schématu.</span><span class="sxs-lookup"><span data-stu-id="7b782-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="7b782-135">Předchozí dokumentaci pro další informace.</span><span class="sxs-lookup"><span data-stu-id="7b782-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="7b782-136">EF Core příkazů použít připojovací řetězec k databázi zadané v *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7b782-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="7b782-137">Následující připojovací řetězec je zaměřen na databázi na *localhost* s názvem *asp net core identity*.</span><span class="sxs-lookup"><span data-stu-id="7b782-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="7b782-138">V tomto nastavení se EF Core je nakonfigurován pro použití `DefaultConnection` připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="7b782-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```
1. <span data-ttu-id="7b782-139">Vyberte **zobrazení** > **Průzkumník objektů systému SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="7b782-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="7b782-140">Rozbalte uzel odpovídající zadaný v názvu databáze `ConnectionStrings:DefaultConnection` vlastnost *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="7b782-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="7b782-141">`Update-Database` Příkaz vytvořil databázi se schématem a jakékoli data potřebná pro inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b782-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="7b782-142">Následující obrázek znázorňuje strukturu tabulky, který je vytvořen v předchozích krocích.</span><span class="sxs-lookup"><span data-stu-id="7b782-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Identity tabulky](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="7b782-144">Migrace schématu</span><span class="sxs-lookup"><span data-stu-id="7b782-144">Migrate the schema</span></span>

<span data-ttu-id="7b782-145">Jsou drobné rozdíly ve strukturách tabulky a pole pro členství a ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="7b782-146">Vzor se změnilo podstatně ověřování/autorizace pomocí aplikací pro ASP.NET a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7b782-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="7b782-147">Jsou klíčové objekty, které se stále používají s identitou *uživatelé* a *role*.</span><span class="sxs-lookup"><span data-stu-id="7b782-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="7b782-148">Tady jsou mapování tabulky pro *uživatelé*, *role*, a *UserRoles*.</span><span class="sxs-lookup"><span data-stu-id="7b782-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="7b782-149">Uživatelé</span><span class="sxs-lookup"><span data-stu-id="7b782-149">Users</span></span>

|<span data-ttu-id="7b782-150">*Identita<br>(dbo. AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="7b782-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="7b782-151">*Členství<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="7b782-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="7b782-152">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-152">**Field Name**</span></span>                 |<span data-ttu-id="7b782-153">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-153">**Type**</span></span>|<span data-ttu-id="7b782-154">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-154">**Field Name**</span></span>                                    |<span data-ttu-id="7b782-155">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="7b782-156">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="7b782-157">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="7b782-158">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="7b782-159">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="7b782-160">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="7b782-161">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="7b782-162">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="7b782-163">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="7b782-164">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="7b782-165">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="7b782-166">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="7b782-167">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="7b782-168">bitové</span><span class="sxs-lookup"><span data-stu-id="7b782-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="7b782-169">bitové</span><span class="sxs-lookup"><span data-stu-id="7b782-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="7b782-170">Ne všechny mapování polí vypadat podobně jako relace 1: 1 z členství pro ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="7b782-171">V předchozí tabulce přebírá výchozí schéma uživatele se členstvím a mapuje na schéma ASP.NET Core Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="7b782-172">Další vlastní pole, které byly použity pro členství je potřeba namapovat ručně.</span><span class="sxs-lookup"><span data-stu-id="7b782-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="7b782-173">V toto mapování není žádné mapování pro hesla, jako kritéria hesla a heslo soli není migrace mezi dvěma.</span><span class="sxs-lookup"><span data-stu-id="7b782-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="7b782-174">**Doporučujeme ponechat heslo jako hodnota null a pokládat uživatelům resetovat svá hesla.**</span><span class="sxs-lookup"><span data-stu-id="7b782-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="7b782-175">V ASP.NET Core Identity `LockoutEnd` musí být nastavená na datum v budoucnosti, pokud je uživatel uzamčen. To je ukázáno v skript migrace.</span><span class="sxs-lookup"><span data-stu-id="7b782-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="7b782-176">Role</span><span class="sxs-lookup"><span data-stu-id="7b782-176">Roles</span></span>

|<span data-ttu-id="7b782-177">*Identita<br>(dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="7b782-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="7b782-178">*Členství<br>(dbo.aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="7b782-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="7b782-179">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-179">**Field Name**</span></span>                 |<span data-ttu-id="7b782-180">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-180">**Type**</span></span>|<span data-ttu-id="7b782-181">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-181">**Field Name**</span></span>   |<span data-ttu-id="7b782-182">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="7b782-183">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-183">string</span></span>  |`RoleId`         | <span data-ttu-id="7b782-184">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="7b782-185">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-185">string</span></span>  |`RoleName`       | <span data-ttu-id="7b782-186">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="7b782-187">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="7b782-188">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="7b782-189">Role uživatele</span><span class="sxs-lookup"><span data-stu-id="7b782-189">User Roles</span></span>

|<span data-ttu-id="7b782-190">*Identita<br>(dbo. AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="7b782-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="7b782-191">*Členství<br>(dbo.aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="7b782-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="7b782-192">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-192">**Field Name**</span></span>           |<span data-ttu-id="7b782-193">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-193">**Type**</span></span>  |<span data-ttu-id="7b782-194">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="7b782-194">**Field Name**</span></span>|<span data-ttu-id="7b782-195">**Typ**</span><span class="sxs-lookup"><span data-stu-id="7b782-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="7b782-196">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-196">string</span></span>    |`RoleId`      |<span data-ttu-id="7b782-197">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="7b782-198">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-198">string</span></span>    |`UserId`      |<span data-ttu-id="7b782-199">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="7b782-199">string</span></span>                     |

<span data-ttu-id="7b782-200">Při vytváření skript migrace pro odkazují předchozí tabulky mapování *uživatelé* a *role*.</span><span class="sxs-lookup"><span data-stu-id="7b782-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="7b782-201">V následujícím příkladu se předpokládá, že máte dvě databáze na serveru databáze.</span><span class="sxs-lookup"><span data-stu-id="7b782-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="7b782-202">Jedna databáze obsahuje existující členství technologie ASP.NET schéma a data.</span><span class="sxs-lookup"><span data-stu-id="7b782-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="7b782-203">Druhý *CoreIdentitySample* databáze byla vytvořena pomocí kroků popsaných dříve.</span><span class="sxs-lookup"><span data-stu-id="7b782-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="7b782-204">Komentáře jsou zahrnuty vložené další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="7b782-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="7b782-205">Po dokončení předchozího skriptu aplikace ASP.NET Core Identity dříve vytvořenou se vyplní uživatelů se členstvím.</span><span class="sxs-lookup"><span data-stu-id="7b782-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="7b782-206">Uživatelé potřebují ke změně hesla před přihlášením.</span><span class="sxs-lookup"><span data-stu-id="7b782-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="7b782-207">Pokud systém členství uživatele s uživatelská jména, které se neshoduje jejich e-mailovou adresu, se musí aplikaci vytvořili dříve, aby tuto skutečnost zohlednit změny.</span><span class="sxs-lookup"><span data-stu-id="7b782-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="7b782-208">Výchozí šablony očekává, že `UserName` a `Email` být stejné.</span><span class="sxs-lookup"><span data-stu-id="7b782-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="7b782-209">V situacích, ve kterých charakteristik, je potřeba proces přihlášení by vedla k použití `UserName` místo `Email`.</span><span class="sxs-lookup"><span data-stu-id="7b782-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="7b782-210">V `PageModel` přihlašovací stránky v *Pages\Account\Login.cshtml.cs*, odeberte `[EmailAddress]` atribut z *e-mailu* vlastnost.</span><span class="sxs-lookup"><span data-stu-id="7b782-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="7b782-211">Přejmenujte ho na *uživatelské jméno*.</span><span class="sxs-lookup"><span data-stu-id="7b782-211">Rename it to *UserName*.</span></span> <span data-ttu-id="7b782-212">To vyžaduje, aby změnu bez ohledu na to `EmailAddress` je uvedený v *zobrazení* a *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="7b782-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="7b782-213">Výsledek bude vypadat nějak takto:</span><span class="sxs-lookup"><span data-stu-id="7b782-213">The result looks like the following:</span></span>

 ![Oprava přihlášení](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="7b782-215">Další kroky</span><span class="sxs-lookup"><span data-stu-id="7b782-215">Next steps</span></span>

<span data-ttu-id="7b782-216">V tomto kurzu jste zjistili, jak přenést uživatele z členství SQL na ASP.NET Core 2.0 Identity.</span><span class="sxs-lookup"><span data-stu-id="7b782-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="7b782-217">Další informace o ASP.NET Core Identity najdete v tématu [Úvod do Identity](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="7b782-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
