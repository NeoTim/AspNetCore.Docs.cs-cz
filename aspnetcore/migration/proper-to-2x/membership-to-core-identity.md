---
title: Migrace z ověřování členství ASP.NET do ASP.NET Core 2,0Identity
author: isaac2004
description: Naučte se migrovat existující aplikace ASP.NET pomocí ověřování členství na ASP.NET Core 2,0 Identity.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774506"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="61678-103">Migrace z ověřování členství ASP.NET do identity ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="61678-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="61678-104">Od [Petr Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="61678-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="61678-105">Tento článek popisuje migraci schématu databáze pro aplikace ASP.NET, které používají ověřování členství, k ASP.NET Core 2,0 identit.</span><span class="sxs-lookup"><span data-stu-id="61678-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="61678-106">Tento dokument popisuje kroky potřebné k migraci schématu databáze pro aplikace založené na členství ASP.NET do schématu databáze používaného pro ASP.NET Coreou identitu.</span><span class="sxs-lookup"><span data-stu-id="61678-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="61678-107">Další informace o migraci z ASP.NET ověřování na základě členství na ASP.NET Identity najdete v tématu [migrace stávající aplikace z členství SQL do ASP.NET identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span><span class="sxs-lookup"><span data-stu-id="61678-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="61678-108">Další informace o ASP.NET Core identitě najdete v tématu [Úvod do identity v ASP.NET Core](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="61678-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="61678-109">Kontrola schématu členství</span><span class="sxs-lookup"><span data-stu-id="61678-109">Review of Membership schema</span></span>

<span data-ttu-id="61678-110">Před ASP.NET 2,0 bylo vývojářům uděleno vytváření celého procesu ověřování a autorizace pro své aplikace.</span><span class="sxs-lookup"><span data-stu-id="61678-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="61678-111">S ASP.NET 2,0 se zavedlo členství, které poskytuje často používané řešení pro zpracování zabezpečení v aplikacích ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="61678-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="61678-112">Vývojáři teď dokázali spustit schéma do databáze SQL Server pomocí příkazu [Aspnet_regsql. exe](https://msdn.microsoft.com/library/ms229862.aspx) .</span><span class="sxs-lookup"><span data-stu-id="61678-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="61678-113">Po spuštění tohoto příkazu byly v databázi vytvořeny následující tabulky.</span><span class="sxs-lookup"><span data-stu-id="61678-113">After running this command, the following tables were created in the database.</span></span>

  ![Tabulky členství](identity/_static/membership-tables.png)

<span data-ttu-id="61678-115">Chcete-li migrovat existující aplikace na identitu ASP.NET Core 2,0, musí být data v těchto tabulkách migrována do tabulek používaných novým schématem identity.</span><span class="sxs-lookup"><span data-stu-id="61678-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="61678-116">ASP.NET Core schéma identity 2,0</span><span class="sxs-lookup"><span data-stu-id="61678-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="61678-117">ASP.NET Core 2,0 se řídí principem [identity](/aspnet/identity/index) představeným v ASP.NET 4,5.</span><span class="sxs-lookup"><span data-stu-id="61678-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="61678-118">I když je zásada sdílená, implementace mezi architekturami se liší i mezi verzemi ASP.NET Core (viz [migrace ověřování a identita do ASP.NET Core 2,0](xref:migration/1x-to-2x/index)).</span><span class="sxs-lookup"><span data-stu-id="61678-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="61678-119">Nejrychlejší způsob, jak zobrazit schéma pro ASP.NET Core 2,0 identity, je vytvoření nové aplikace ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="61678-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="61678-120">Postupujte podle těchto kroků v aplikaci Visual Studio 2017:</span><span class="sxs-lookup"><span data-stu-id="61678-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="61678-121">Vyberte **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="61678-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="61678-122">Vytvořte nový projekt **ASP.NET Core webové aplikace** s názvem *CoreIdentitySample*.</span><span class="sxs-lookup"><span data-stu-id="61678-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="61678-123">V rozevíracím seznamu vyberte **ASP.NET Core 2,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="61678-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="61678-124">Tato šablona vytvoří aplikaci [Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="61678-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="61678-125">Než kliknete na **OK**, klikněte na **změnit ověřování**.</span><span class="sxs-lookup"><span data-stu-id="61678-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="61678-126">Vyberte **jednotlivé uživatelské účty** pro šablony identity.</span><span class="sxs-lookup"><span data-stu-id="61678-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="61678-127">Nakonec klikněte na **OK**a pak na **OK**.</span><span class="sxs-lookup"><span data-stu-id="61678-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="61678-128">Visual Studio vytvoří projekt pomocí šablony ASP.NET Core identity.</span><span class="sxs-lookup"><span data-stu-id="61678-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="61678-129">Vyberte **nástroje** > **správce** > balíčků NuGet**Konzola správce balíčků** a otevřete tak okno **konzoly Správce balíčků** (PMC).</span><span class="sxs-lookup"><span data-stu-id="61678-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="61678-130">Přejděte do kořenového adresáře projektu v PMC a spusťte příkaz [Entity Framework (EF) Core](/ef/core) `Update-Database` .</span><span class="sxs-lookup"><span data-stu-id="61678-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="61678-131">Identita ASP.NET Core 2,0 používá EF Core k interakci s databází, která ukládá ověřovací data.</span><span class="sxs-lookup"><span data-stu-id="61678-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="61678-132">Aby nově vytvořená aplikace fungovala, musí být databáze pro uložení těchto dat.</span><span class="sxs-lookup"><span data-stu-id="61678-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="61678-133">Po vytvoření nové aplikace je nejrychlejší způsob, jak zkontrolovat schéma v databázovém prostředí, vytvořit databázi pomocí [EF Core migrace](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="61678-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="61678-134">Tento proces vytvoří databázi buď místně, nebo jinde, což napodobuje toto schéma.</span><span class="sxs-lookup"><span data-stu-id="61678-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="61678-135">Další informace najdete v předchozí dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="61678-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="61678-136">Příkazy EF Core používají připojovací řetězec pro databázi určenou v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61678-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="61678-137">Následující připojovací řetězec cílí na databázi na *localhost* s názvem *ASP-NET-Core-identity*.</span><span class="sxs-lookup"><span data-stu-id="61678-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="61678-138">V tomto nastavení je EF Core nakonfigurován pro použití `DefaultConnection` připojovacího řetězce.</span><span class="sxs-lookup"><span data-stu-id="61678-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="61678-139">Vyberte **zobrazení** > **Průzkumník objektů systému SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="61678-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="61678-140">Rozbalte uzel odpovídající názvu databáze zadané ve `ConnectionStrings:DefaultConnection` vlastnosti *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="61678-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="61678-141">`Update-Database` Příkaz vytvořil databázi zadanou se schématem a všemi daty potřebnými k inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="61678-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="61678-142">Následující obrázek znázorňuje strukturu tabulky, která je vytvořená pomocí předchozích kroků.</span><span class="sxs-lookup"><span data-stu-id="61678-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![Tabulky identity](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="61678-144">Migrace schématu</span><span class="sxs-lookup"><span data-stu-id="61678-144">Migrate the schema</span></span>

<span data-ttu-id="61678-145">Existují drobné rozdíly v strukturách tabulek a polích pro členství i ASP.NET Core identitu.</span><span class="sxs-lookup"><span data-stu-id="61678-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="61678-146">Vzor se podstatně změnil pro ověřování nebo autorizaci pomocí aplikací ASP.NET a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="61678-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="61678-147">Klíčové objekty, které jsou pořád používané s identitou, jsou *Uživatelé* a *role*.</span><span class="sxs-lookup"><span data-stu-id="61678-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="61678-148">Tady jsou mapování tabulek pro *uživatele*, *role*a *položka userroles může*.</span><span class="sxs-lookup"><span data-stu-id="61678-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="61678-149">Uživatelé</span><span class="sxs-lookup"><span data-stu-id="61678-149">Users</span></span>

|<span data-ttu-id="61678-150">*Identita<br>(dbo. AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="61678-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="61678-151">*Členství<br>(dbo. aspnet_Users/dbo. aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="61678-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="61678-152">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-152">**Field Name**</span></span>                 |<span data-ttu-id="61678-153">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-153">**Type**</span></span>|<span data-ttu-id="61678-154">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-154">**Field Name**</span></span>                                    |<span data-ttu-id="61678-155">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="61678-156">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="61678-157">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="61678-158">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="61678-159">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="61678-160">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="61678-161">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="61678-162">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="61678-163">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="61678-164">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="61678-165">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="61678-166">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="61678-167">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="61678-168">bitové</span><span class="sxs-lookup"><span data-stu-id="61678-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="61678-169">bitové</span><span class="sxs-lookup"><span data-stu-id="61678-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="61678-170">Ne všechna mapování polí se podobají relacím 1:1 z členství ASP.NET Core identitou.</span><span class="sxs-lookup"><span data-stu-id="61678-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="61678-171">Předchozí tabulka přijímá výchozí schéma uživatele členství a mapuje je na ASP.NET Core schématu identity.</span><span class="sxs-lookup"><span data-stu-id="61678-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="61678-172">Všechna další vlastní pole, která byla použita pro členství, je nutné namapovat ručně.</span><span class="sxs-lookup"><span data-stu-id="61678-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="61678-173">V tomto mapování není k dispozici žádná mapa hesel, protože mezi těmito dvěma kritérii hesla a resůl hesla nedojde k migraci.</span><span class="sxs-lookup"><span data-stu-id="61678-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="61678-174">**Doporučuje se ponechat heslo jako null a požádat uživatele, aby obnovili hesla.**</span><span class="sxs-lookup"><span data-stu-id="61678-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="61678-175">Pokud je uživatel uzamčený, `LockoutEnd` musí se v ASP.NET Core identita nastavit na nějaké datum v budoucnosti. Tato ukázka se zobrazí ve skriptu migrace.</span><span class="sxs-lookup"><span data-stu-id="61678-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="61678-176">Role</span><span class="sxs-lookup"><span data-stu-id="61678-176">Roles</span></span>

|<span data-ttu-id="61678-177">*Identita<br>(dbo. AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="61678-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="61678-178">*Členství<br>(dbo. aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="61678-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="61678-179">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-179">**Field Name**</span></span>                 |<span data-ttu-id="61678-180">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-180">**Type**</span></span>|<span data-ttu-id="61678-181">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-181">**Field Name**</span></span>   |<span data-ttu-id="61678-182">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="61678-183">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-183">string</span></span>  |`RoleId`         | <span data-ttu-id="61678-184">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="61678-185">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-185">string</span></span>  |`RoleName`       | <span data-ttu-id="61678-186">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="61678-187">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="61678-188">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="61678-189">User Roles</span><span class="sxs-lookup"><span data-stu-id="61678-189">User Roles</span></span>

|<span data-ttu-id="61678-190">*Identita<br>(dbo. AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="61678-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="61678-191">*Členství<br>(dbo. aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="61678-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="61678-192">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-192">**Field Name**</span></span>           |<span data-ttu-id="61678-193">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-193">**Type**</span></span>  |<span data-ttu-id="61678-194">**Název pole**</span><span class="sxs-lookup"><span data-stu-id="61678-194">**Field Name**</span></span>|<span data-ttu-id="61678-195">**Typ**</span><span class="sxs-lookup"><span data-stu-id="61678-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="61678-196">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-196">string</span></span>    |`RoleId`      |<span data-ttu-id="61678-197">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="61678-198">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-198">string</span></span>    |`UserId`      |<span data-ttu-id="61678-199">řetězec</span><span class="sxs-lookup"><span data-stu-id="61678-199">string</span></span>                     |

<span data-ttu-id="61678-200">Při vytváření migračního skriptu pro *uživatele* a *role*odkázat na předchozí tabulky mapování.</span><span class="sxs-lookup"><span data-stu-id="61678-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="61678-201">Následující příklad předpokládá, že máte na databázovém serveru dvě databáze.</span><span class="sxs-lookup"><span data-stu-id="61678-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="61678-202">Jedna databáze obsahuje existující schéma členství ASP.NET a data.</span><span class="sxs-lookup"><span data-stu-id="61678-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="61678-203">Druhá databáze *CoreIdentitySample* byla vytvořena pomocí kroků popsaných výše.</span><span class="sxs-lookup"><span data-stu-id="61678-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="61678-204">K dispozici jsou vložené komentáře pro další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="61678-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="61678-205">Po dokončení předchozího skriptu se dříve vytvořená aplikace Identity ASP.NET Core naplní uživateli členství.</span><span class="sxs-lookup"><span data-stu-id="61678-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="61678-206">Uživatelé musí před přihlášením změnit svoje heslo.</span><span class="sxs-lookup"><span data-stu-id="61678-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="61678-207">Pokud má systém členství uživatele s uživatelskými jmény, které neodpovídaly jejich e-mailové adrese, je nutné aplikaci vytvořenou dříve změnit.</span><span class="sxs-lookup"><span data-stu-id="61678-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="61678-208">Výchozí šablona očekává `UserName` a `Email` bude stejná.</span><span class="sxs-lookup"><span data-stu-id="61678-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="61678-209">V situacích, kdy se liší, je třeba změnit proces přihlášení, aby používal `UserName` místo. `Email`</span><span class="sxs-lookup"><span data-stu-id="61678-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="61678-210">Na stránce `PageModel` pro přihlášení, která se nachází na adrese *Pages\Account\Login.cshtml.cs*, odeberte `[EmailAddress]` atribut z vlastnosti *e-mail* .</span><span class="sxs-lookup"><span data-stu-id="61678-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="61678-211">Přejmenujte ho na *uživatelské jméno*.</span><span class="sxs-lookup"><span data-stu-id="61678-211">Rename it to *UserName*.</span></span> <span data-ttu-id="61678-212">To vyžaduje změnu, kdykoli `EmailAddress` je uvedeno v *zobrazení* a *PageModel*.</span><span class="sxs-lookup"><span data-stu-id="61678-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="61678-213">Výsledek bude vypadat následovně:</span><span class="sxs-lookup"><span data-stu-id="61678-213">The result looks like the following:</span></span>

 ![Pevné přihlášení](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="61678-215">Další kroky</span><span class="sxs-lookup"><span data-stu-id="61678-215">Next steps</span></span>

<span data-ttu-id="61678-216">V tomto kurzu jste se naučili, jak přenést uživatele z členství SQL do ASP.NET Core Identity2,0.</span><span class="sxs-lookup"><span data-stu-id="61678-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="61678-217">Další informace týkající se ASP.NET Core Identitynajdete v tématu [Úvod Identitydo ](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="61678-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
