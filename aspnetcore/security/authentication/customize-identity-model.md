---
title: IdentityPřizpůsobení modelu v ASP.NET Core
author: ajcvickers
description: Tento článek popisuje, jak přizpůsobit základní datový model Entity Framework Core pro ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 96ee703da4ced69c5d9c703139e33b76b5dcdff1
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074139"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Identity<span data-ttu-id="f934c-103">Přizpůsobení modelu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f934c-103"> model customization in ASP.NET Core</span></span>

<span data-ttu-id="f934c-104">Od [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="f934c-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="f934c-105">ASP.NET Core Identity poskytuje rozhraní pro správu a ukládání uživatelských účtů v aplikacích ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f934c-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> Identity<span data-ttu-id="f934c-106">se přidá do projektu, když jsou **jednotlivé uživatelské účty** vybrané jako ověřovací mechanismus.</span><span class="sxs-lookup"><span data-stu-id="f934c-106"> is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="f934c-107">Ve výchozím nastavení Identity využívá základní datový model Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="f934c-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="f934c-108">Tento článek popisuje, jak model přizpůsobit Identity .</span><span class="sxs-lookup"><span data-stu-id="f934c-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a>Identity<span data-ttu-id="f934c-109">a EF Core migrace</span><span class="sxs-lookup"><span data-stu-id="f934c-109"> and EF Core Migrations</span></span>

<span data-ttu-id="f934c-110">Před prozkoumáním modelu je užitečné pochopit, jak Identity funguje s [EF Core migrací](/ef/core/managing-schemas/migrations/) pro vytvoření a aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="f934c-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="f934c-111">Na nejvyšší úrovni je tento proces:</span><span class="sxs-lookup"><span data-stu-id="f934c-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="f934c-112">Definice nebo aktualizace [datového modelu v kódu](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="f934c-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="f934c-113">Přidáním migrace můžete tento model přeložit do změn, které se dají použít pro databázi.</span><span class="sxs-lookup"><span data-stu-id="f934c-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="f934c-114">Ověřte, že migrace správně reprezentuje vaše záměry.</span><span class="sxs-lookup"><span data-stu-id="f934c-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="f934c-115">Použijte migraci k aktualizaci databáze, aby byla synchronizovaná s modelem.</span><span class="sxs-lookup"><span data-stu-id="f934c-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="f934c-116">Opakujte kroky 1 až 4 pro další upřesnění modelu a udržujte databázi v synchronizaci.</span><span class="sxs-lookup"><span data-stu-id="f934c-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="f934c-117">Pomocí jednoho z následujících přístupů přidejte a použijte migrace:</span><span class="sxs-lookup"><span data-stu-id="f934c-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="f934c-118">Okno **konzoly Správce balíčků** (PMC), pokud používáte Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f934c-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="f934c-119">Další informace najdete v tématu [EF Core nástrojů PMC](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="f934c-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="f934c-120">.NET Core CLI při použití příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="f934c-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="f934c-121">Další informace najdete v tématu [EF Core nástroje příkazového řádku .NET](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="f934c-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="f934c-122">Po spuštění aplikace klikněte na tlačítko **použít migrace** na chybové stránce.</span><span class="sxs-lookup"><span data-stu-id="f934c-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="f934c-123">ASP.NET Core má obslužnou rutinu chybové stránky v době vývoje.</span><span class="sxs-lookup"><span data-stu-id="f934c-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="f934c-124">Obslužná rutina může při spuštění aplikace použít migrace.</span><span class="sxs-lookup"><span data-stu-id="f934c-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="f934c-125">Produkční aplikace obvykle generují skripty SQL z migrace a nasazují změny databáze jako součást řízené aplikace a nasazení databáze.</span><span class="sxs-lookup"><span data-stu-id="f934c-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="f934c-126">Když se vytvoří nová aplikace Identity , kroky 1 a 2 se už dokončí.</span><span class="sxs-lookup"><span data-stu-id="f934c-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="f934c-127">To znamená, že počáteční datový model již existuje a byla do projektu přidána počáteční migrace.</span><span class="sxs-lookup"><span data-stu-id="f934c-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="f934c-128">Počáteční migrace se pořád musí použít pro databázi.</span><span class="sxs-lookup"><span data-stu-id="f934c-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="f934c-129">Prvotní migraci můžete provést pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="f934c-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="f934c-130">Spusťte `Update-Database` v PMC.</span><span class="sxs-lookup"><span data-stu-id="f934c-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="f934c-131">Spusťte `dotnet ef database update` příkaz v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="f934c-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="f934c-132">Po spuštění aplikace klikněte na tlačítko **použít migrace** na chybové stránce.</span><span class="sxs-lookup"><span data-stu-id="f934c-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="f934c-133">Předchozí kroky opakujte, protože se v modelu provedou změny.</span><span class="sxs-lookup"><span data-stu-id="f934c-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="f934c-134">IdentityModel</span><span class="sxs-lookup"><span data-stu-id="f934c-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="f934c-135">Typy entit</span><span class="sxs-lookup"><span data-stu-id="f934c-135">Entity types</span></span>

<span data-ttu-id="f934c-136">IdentityModel se skládá z následujících typů entit.</span><span class="sxs-lookup"><span data-stu-id="f934c-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="f934c-137">Typ entity</span><span class="sxs-lookup"><span data-stu-id="f934c-137">Entity type</span></span>|<span data-ttu-id="f934c-138">Popis</span><span class="sxs-lookup"><span data-stu-id="f934c-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="f934c-139">Představuje uživatele.</span><span class="sxs-lookup"><span data-stu-id="f934c-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="f934c-140">Představuje roli.</span><span class="sxs-lookup"><span data-stu-id="f934c-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="f934c-141">Představuje deklaraci identity, kterou uživatel má.</span><span class="sxs-lookup"><span data-stu-id="f934c-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="f934c-142">Představuje ověřovací token pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="f934c-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="f934c-143">Přidruží uživatele k přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f934c-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="f934c-144">Představuje deklaraci identity, která je udělená všem uživatelům v rámci role.</span><span class="sxs-lookup"><span data-stu-id="f934c-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="f934c-145">Entita JOIN, která přidruží uživatele a role.</span><span class="sxs-lookup"><span data-stu-id="f934c-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="f934c-146">Vztahy typů entit</span><span class="sxs-lookup"><span data-stu-id="f934c-146">Entity type relationships</span></span>

<span data-ttu-id="f934c-147">[Typy entit](#entity-types) spolu vzájemně souvisí následujícími způsoby:</span><span class="sxs-lookup"><span data-stu-id="f934c-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="f934c-148">Každý z nich `User` může mít mnoho `UserClaims` .</span><span class="sxs-lookup"><span data-stu-id="f934c-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="f934c-149">Každý z nich `User` může mít mnoho `UserLogins` .</span><span class="sxs-lookup"><span data-stu-id="f934c-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="f934c-150">Každý z nich `User` může mít mnoho `UserTokens` .</span><span class="sxs-lookup"><span data-stu-id="f934c-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="f934c-151">Každý z nich `Role` může mít přiřazeno mnoho `RoleClaims` .</span><span class="sxs-lookup"><span data-stu-id="f934c-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="f934c-152">Každý z nich `User` může mít přiřazeno mnoho `Roles` a každý z nich `Role` může být přidružen k mnoha `Users` .</span><span class="sxs-lookup"><span data-stu-id="f934c-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="f934c-153">Jedná se o vztah m:n, který vyžaduje tabulku JOIN v databázi.</span><span class="sxs-lookup"><span data-stu-id="f934c-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="f934c-154">Tabulka JOIN je reprezentována `UserRole` entitou.</span><span class="sxs-lookup"><span data-stu-id="f934c-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="f934c-155">Výchozí konfigurace modelu</span><span class="sxs-lookup"><span data-stu-id="f934c-155">Default model configuration</span></span>

Identity<span data-ttu-id="f934c-156">definuje mnoho *kontextových tříd* , které dědí z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) ke konfiguraci a použití modelu.</span><span class="sxs-lookup"><span data-stu-id="f934c-156"> defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="f934c-157">Tato konfigurace se provádí pomocí [rozhraní EF Core Code First Fluent API](/ef/core/modeling/) v metodě [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) třídy Context.</span><span class="sxs-lookup"><span data-stu-id="f934c-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="f934c-158">Výchozí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="f934c-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="f934c-159">Obecné typy modelů</span><span class="sxs-lookup"><span data-stu-id="f934c-159">Model generic types</span></span>

Identity<span data-ttu-id="f934c-160">definuje výchozí typy modulu CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) pro každý z typů entit uvedených výše.</span><span class="sxs-lookup"><span data-stu-id="f934c-160"> defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="f934c-161">Všechny tyto typy mají předponu *Identity* :</span><span class="sxs-lookup"><span data-stu-id="f934c-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="f934c-162">Místo toho, aby tyto typy používaly přímo, lze typy použít jako základní třídy pro vlastní typy aplikace.</span><span class="sxs-lookup"><span data-stu-id="f934c-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="f934c-163">`DbContext`Třídy definované pomocí Identity jsou obecné, takže různé typy CLR lze použít pro jeden nebo více typů entit v modelu.</span><span class="sxs-lookup"><span data-stu-id="f934c-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="f934c-164">Tyto obecné typy také umožňují `User` změnit datový typ primární klíč (PK).</span><span class="sxs-lookup"><span data-stu-id="f934c-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="f934c-165">Při použití Identity s podporou rolí <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> by měla být použita třída.</span><span class="sxs-lookup"><span data-stu-id="f934c-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="f934c-166">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f934c-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="f934c-167">Je také možné použít Identity bez rolí (pouze deklarace identity), v takovém případě <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> by měla být použita třída:</span><span class="sxs-lookup"><span data-stu-id="f934c-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="f934c-168">Přizpůsobení modelu</span><span class="sxs-lookup"><span data-stu-id="f934c-168">Customize the model</span></span>

<span data-ttu-id="f934c-169">Výchozí bod pro přizpůsobení modelu je odvozen od příslušného typu kontextu.</span><span class="sxs-lookup"><span data-stu-id="f934c-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="f934c-170">Viz oddíl [Obecné typy modelů](#model-generic-types) .</span><span class="sxs-lookup"><span data-stu-id="f934c-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="f934c-171">Tento typ kontextu je obvykle volán `ApplicationDbContext` a je vytvořen šablonami ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f934c-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="f934c-172">Kontext se používá ke konfiguraci modelu dvěma způsoby:</span><span class="sxs-lookup"><span data-stu-id="f934c-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="f934c-173">Zadání entit a typů klíčů pro parametry obecného typu.</span><span class="sxs-lookup"><span data-stu-id="f934c-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="f934c-174">Přepsáním `OnModelCreating` upravíte mapování těchto typů.</span><span class="sxs-lookup"><span data-stu-id="f934c-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="f934c-175">Při přepsání `OnModelCreating` `base.OnModelCreating` by se mělo volat jako první. přepsání konfigurace by se mělo volat jako další.</span><span class="sxs-lookup"><span data-stu-id="f934c-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="f934c-176">EF Core obvykle má pro konfiguraci poslední zásadu služby WINS.</span><span class="sxs-lookup"><span data-stu-id="f934c-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="f934c-177">Například pokud `ToTable` je metoda pro typ entity volána jako první v jednom názvu tabulky a poté později s jiným názvem tabulky, je použita název tabulky ve druhém volání.</span><span class="sxs-lookup"><span data-stu-id="f934c-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="f934c-178">Vlastní uživatelská data</span><span class="sxs-lookup"><span data-stu-id="f934c-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="f934c-179">[Vlastní uživatelská data](xref:security/authentication/add-user-data) jsou podporovaná děděním z `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="f934c-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="f934c-180">Vlastní pojmenování tohoto typu `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="f934c-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="f934c-181">Použijte `ApplicationUser` typ jako obecný argument pro kontext:</span><span class="sxs-lookup"><span data-stu-id="f934c-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="f934c-182">Ve třídě není nutné přepisovat `OnModelCreating` `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="f934c-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="f934c-183">EF Core mapuje `CustomTag` vlastnost podle konvence.</span><span class="sxs-lookup"><span data-stu-id="f934c-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="f934c-184">Databázi je ale potřeba aktualizovat, aby se vytvořil nový `CustomTag` sloupec.</span><span class="sxs-lookup"><span data-stu-id="f934c-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="f934c-185">Pokud chcete vytvořit sloupec, přidejte migraci a pak aktualizujte databázi, jak je popsáno v tématu [ Identity a EF Core migrace](#identity-and-ef-core-migrations).</span><span class="sxs-lookup"><span data-stu-id="f934c-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="f934c-186">Update *Pages/Shared/_LoginPartial. cshtml* a nahraďte `IdentityUser` `ApplicationUser` :</span><span class="sxs-lookup"><span data-stu-id="f934c-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="f934c-187">Aktualizujte *oblasti/ Identity /IdentityHostingStartup.cs* nebo `Startup.ConfigureServices` nahraďte `IdentityUser` `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="f934c-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="f934c-188">V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd.</span><span class="sxs-lookup"><span data-stu-id="f934c-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="f934c-189">Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="f934c-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="f934c-190">V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="f934c-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="f934c-191">Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="f934c-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="f934c-192">Další informace naleznete v tématu:</span><span class="sxs-lookup"><span data-stu-id="f934c-192">For more information, see:</span></span>

* <span data-ttu-id="f934c-193">[GenerováníIdentity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="f934c-193">[Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
* <span data-ttu-id="f934c-194">[Přidání, stažení a odstranění vlastních uživatelských datIdentity](xref:security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="f934c-194">[Add, download, and delete custom user data to Identity](xref:security/authentication/add-user-data)</span></span>

### <a name="change-the-primary-key-type"></a><span data-ttu-id="f934c-195">Změnit typ primárního klíče</span><span class="sxs-lookup"><span data-stu-id="f934c-195">Change the primary key type</span></span>

<span data-ttu-id="f934c-196">Změna datového typu sloupce PK poté, co byla databáze vytvořena, je problematická v mnoha databázových systémech.</span><span class="sxs-lookup"><span data-stu-id="f934c-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="f934c-197">Změna PK obvykle zahrnuje vyřazení a opětovné vytvoření tabulky.</span><span class="sxs-lookup"><span data-stu-id="f934c-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="f934c-198">Proto by měly být při vytvoření databáze zadané typy klíčů při počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="f934c-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="f934c-199">Chcete-li změnit typ PK, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="f934c-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="f934c-200">Pokud byla databáze vytvořena před změnou PK, spusťte `Drop-Database` (PMC) nebo `dotnet ef database drop` (.NET Core CLI) a odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="f934c-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="f934c-201">Po potvrzení odstranění databáze odeberte počáteční migraci pomocí `Remove-Migration` (PMC) nebo `dotnet ef migrations remove` (.NET Core CLI).</span><span class="sxs-lookup"><span data-stu-id="f934c-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="f934c-202">Aktualizuje `ApplicationDbContext` třídu, ze které se má odvodit <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> .</span><span class="sxs-lookup"><span data-stu-id="f934c-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="f934c-203">Zadejte nový typ klíče pro `TKey` .</span><span class="sxs-lookup"><span data-stu-id="f934c-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="f934c-204">Pokud například chcete použít `Guid` typ klíče:</span><span class="sxs-lookup"><span data-stu-id="f934c-204">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="f934c-205">V předchozím kódu je nutné zadat obecné třídy <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> použít nový typ klíče.</span><span class="sxs-lookup"><span data-stu-id="f934c-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="f934c-206">V předchozím kódu je nutné zadat obecné třídy <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> použít nový typ klíče.</span><span class="sxs-lookup"><span data-stu-id="f934c-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="f934c-207">`Startup.ConfigureServices`je nutné aktualizovat, aby používala obecného uživatele:</span><span class="sxs-lookup"><span data-stu-id="f934c-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="f934c-208">Pokud `ApplicationUser` je použita vlastní třída, aktualizujte třídu tak, aby dědila z `IdentityUser` .</span><span class="sxs-lookup"><span data-stu-id="f934c-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="f934c-209">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f934c-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="f934c-210">Aktualizace `ApplicationDbContext` , aby odkazovala na vlastní `ApplicationUser` třídu:</span><span class="sxs-lookup"><span data-stu-id="f934c-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="f934c-211">Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f934c-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="f934c-212">Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="f934c-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="f934c-213">V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd.</span><span class="sxs-lookup"><span data-stu-id="f934c-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="f934c-214">Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="f934c-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="f934c-215">V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="f934c-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="f934c-216">Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="f934c-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="f934c-217">Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="f934c-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="f934c-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda přijímá typ, který `TKey` označuje datový typ primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="f934c-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="f934c-219">Pokud `ApplicationRole` je použita vlastní třída, aktualizujte třídu tak, aby dědila z `IdentityRole<TKey>` .</span><span class="sxs-lookup"><span data-stu-id="f934c-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="f934c-220">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f934c-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="f934c-221">Aktualizujte `ApplicationDbContext` tak, aby odkazovala na vlastní `ApplicationRole` třídu.</span><span class="sxs-lookup"><span data-stu-id="f934c-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="f934c-222">Například následující třída odkazuje na vlastní `ApplicationUser` a vlastní `ApplicationRole` :</span><span class="sxs-lookup"><span data-stu-id="f934c-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="f934c-223">Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f934c-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="f934c-224">Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="f934c-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="f934c-225">V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd.</span><span class="sxs-lookup"><span data-stu-id="f934c-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="f934c-226">Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="f934c-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="f934c-227">V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> .</span><span class="sxs-lookup"><span data-stu-id="f934c-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="f934c-228">Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` .</span><span class="sxs-lookup"><span data-stu-id="f934c-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="f934c-229">Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f934c-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="f934c-230">Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .</span><span class="sxs-lookup"><span data-stu-id="f934c-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="f934c-231">Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f934c-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="f934c-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda přijímá typ, který `TKey` označuje datový typ primárního klíče.</span><span class="sxs-lookup"><span data-stu-id="f934c-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="f934c-233">Přidat vlastnosti navigace</span><span class="sxs-lookup"><span data-stu-id="f934c-233">Add navigation properties</span></span>

<span data-ttu-id="f934c-234">Změna konfigurace modelu pro relace může být obtížnější než provedení jiných změn.</span><span class="sxs-lookup"><span data-stu-id="f934c-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="f934c-235">Je nutné vzít v potaz stávající relace, aniž byste museli vytvářet nové, další vztahy.</span><span class="sxs-lookup"><span data-stu-id="f934c-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="f934c-236">Konkrétně změna vztahu musí určovat stejnou vlastnost cizího klíče (FK) jako existující relace.</span><span class="sxs-lookup"><span data-stu-id="f934c-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="f934c-237">Například vztah mezi `Users` a `UserClaims` je ve výchozím nastavení zadán následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="f934c-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="f934c-238">FK pro tento vztah je zadáno jako `UserClaim.UserId` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f934c-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="f934c-239">`HasMany`a `WithOne` jsou volány bez argumentů pro vytvoření vztahu bez vlastností navigace.</span><span class="sxs-lookup"><span data-stu-id="f934c-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="f934c-240">Přidat navigační vlastnost k `ApplicationUser` , která umožňuje `UserClaims` odkazování na uživatele:</span><span class="sxs-lookup"><span data-stu-id="f934c-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="f934c-241">`TKey`Pro `IdentityUserClaim<TKey>` je typ určený pro PK pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="f934c-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="f934c-242">V tomto případě je to způsobeno tím, že `TKey` `string` jsou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="f934c-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="f934c-243">**Není** to typ PK pro `UserClaim` typ entity.</span><span class="sxs-lookup"><span data-stu-id="f934c-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="f934c-244">Teď, když existuje vlastnost navigace, musí být nakonfigurovaná v `OnModelCreating` :</span><span class="sxs-lookup"><span data-stu-id="f934c-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="f934c-245">Všimněte si, že vztah je nakonfigurován přesně stejně jako dříve, pouze s navigační vlastností zadanou v volání `HasMany` .</span><span class="sxs-lookup"><span data-stu-id="f934c-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="f934c-246">Navigační vlastnosti existují pouze v modelu EF, nikoli v databázi.</span><span class="sxs-lookup"><span data-stu-id="f934c-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="f934c-247">Vzhledem k tomu, že se FK pro relaci nezměnilo, tento druh změny modelu nevyžaduje aktualizaci databáze.</span><span class="sxs-lookup"><span data-stu-id="f934c-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="f934c-248">Tuto možnost lze zkontrolovat přidáním migrace po provedení změny.</span><span class="sxs-lookup"><span data-stu-id="f934c-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="f934c-249">`Up`Metody a `Down` jsou prázdné.</span><span class="sxs-lookup"><span data-stu-id="f934c-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="f934c-250">Přidat všechny vlastnosti navigace na uživateli</span><span class="sxs-lookup"><span data-stu-id="f934c-250">Add all User navigation properties</span></span>

<span data-ttu-id="f934c-251">V následujícím příkladu se pomocí výše uvedeného příkladu nakonfiguruje jednosměrné navigační vlastnosti pro všechny relace na uživateli:</span><span class="sxs-lookup"><span data-stu-id="f934c-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="f934c-252">Přidat vlastnosti navigace uživatele a role</span><span class="sxs-lookup"><span data-stu-id="f934c-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="f934c-253">Pomocí výše uvedeného oddílu můžete v následujícím příkladu nakonfigurovat navigační vlastnosti pro všechny relace pro uživatele a roli:</span><span class="sxs-lookup"><span data-stu-id="f934c-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="f934c-254">Poznámky:</span><span class="sxs-lookup"><span data-stu-id="f934c-254">Notes:</span></span>

* <span data-ttu-id="f934c-255">Tento příklad obsahuje také `UserRole` entitu JOIN, která je nutná k procházení relace m:n od uživatelů po role.</span><span class="sxs-lookup"><span data-stu-id="f934c-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="f934c-256">Nezapomeňte změnit typy vlastností navigace tak, aby odrážely, že `ApplicationXxx` se nyní používají typy namísto `IdentityXxx` typů.</span><span class="sxs-lookup"><span data-stu-id="f934c-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="f934c-257">Nezapomeňte použít `ApplicationXxx` v obecné `ApplicationContext` definici.</span><span class="sxs-lookup"><span data-stu-id="f934c-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="f934c-258">Přidat všechny navigační vlastnosti</span><span class="sxs-lookup"><span data-stu-id="f934c-258">Add all navigation properties</span></span>

<span data-ttu-id="f934c-259">Pomocí výše uvedeného oddílu můžete v následujícím příkladu nakonfigurovat navigační vlastnosti pro všechny relace na všech typech entit:</span><span class="sxs-lookup"><span data-stu-id="f934c-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="f934c-260">Použití složených klíčů</span><span class="sxs-lookup"><span data-stu-id="f934c-260">Use composite keys</span></span>

<span data-ttu-id="f934c-261">Předchozí části ukázaly změnu typu klíče použitého v Identity modelu.</span><span class="sxs-lookup"><span data-stu-id="f934c-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="f934c-262">Změna Identity modelu klíče na použití složených klíčů není podporována nebo není doporučena.</span><span class="sxs-lookup"><span data-stu-id="f934c-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="f934c-263">Použití složeného klíče s Identity zahrnuje změnu způsobu Identity interakce kódu vedoucího s modelem.</span><span class="sxs-lookup"><span data-stu-id="f934c-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="f934c-264">Toto přizpůsobení překračuje rozsah tohoto dokumentu.</span><span class="sxs-lookup"><span data-stu-id="f934c-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="f934c-265">Změna názvů a vlastností tabulky nebo sloupce</span><span class="sxs-lookup"><span data-stu-id="f934c-265">Change table/column names and facets</span></span>

<span data-ttu-id="f934c-266">Chcete-li změnit názvy tabulek a sloupců, zavolejte `base.OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="f934c-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="f934c-267">Pak přidejte konfiguraci pro přepsání všech výchozích hodnot.</span><span class="sxs-lookup"><span data-stu-id="f934c-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="f934c-268">Chcete-li například změnit název všech Identity tabulek:</span><span class="sxs-lookup"><span data-stu-id="f934c-268">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="f934c-269">V těchto příkladech jsou použity výchozí Identity typy.</span><span class="sxs-lookup"><span data-stu-id="f934c-269">These examples use the default Identity types.</span></span> <span data-ttu-id="f934c-270">Pokud používáte typ aplikace `ApplicationUser` , jako je, nakonfigurujte tento typ místo výchozího typu.</span><span class="sxs-lookup"><span data-stu-id="f934c-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="f934c-271">Následující příklad změní některé názvy sloupců:</span><span class="sxs-lookup"><span data-stu-id="f934c-271">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="f934c-272">U některých typů databázových sloupců se dají nakonfigurovat určité *omezující vlastnosti* (například maximální `string` povolená délka).</span><span class="sxs-lookup"><span data-stu-id="f934c-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="f934c-273">Následující příklad nastaví maximální délku sloupce pro několik `string` vlastností v modelu:</span><span class="sxs-lookup"><span data-stu-id="f934c-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="f934c-274">Mapování na jiné schéma</span><span class="sxs-lookup"><span data-stu-id="f934c-274">Map to a different schema</span></span>

<span data-ttu-id="f934c-275">Schémata se můžou v různých poskytovatelích databáze chovat různě.</span><span class="sxs-lookup"><span data-stu-id="f934c-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="f934c-276">V případě SQL Server se ve výchozím nastavení vytvoří všechny tabulky ve schématu *dbo* .</span><span class="sxs-lookup"><span data-stu-id="f934c-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="f934c-277">Tabulky lze vytvořit v jiném schématu.</span><span class="sxs-lookup"><span data-stu-id="f934c-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="f934c-278">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f934c-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="f934c-279">Opožděné načítání</span><span class="sxs-lookup"><span data-stu-id="f934c-279">Lazy loading</span></span>

<span data-ttu-id="f934c-280">V této části je přidána podpora pro opožděné načítání proxy serverů v Identity modelu.</span><span class="sxs-lookup"><span data-stu-id="f934c-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="f934c-281">Opožděné načítání je užitečné, protože umožňuje použití navigačních vlastností bez prvotního zajištění jejich načtení.</span><span class="sxs-lookup"><span data-stu-id="f934c-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="f934c-282">Typy entit mohou být vhodné pro opožděné načtení několika způsobů, jak je popsáno v [dokumentaci EF Core](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="f934c-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="f934c-283">Pro jednoduchost používejte proxy servery s opožděným načtením, které vyžadují:</span><span class="sxs-lookup"><span data-stu-id="f934c-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="f934c-284">Instalace balíčku [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .</span><span class="sxs-lookup"><span data-stu-id="f934c-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="f934c-285">Volání <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> uvnitř [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span><span class="sxs-lookup"><span data-stu-id="f934c-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="f934c-286">Typy veřejných entit s `public virtual` navigačními vlastnostmi.</span><span class="sxs-lookup"><span data-stu-id="f934c-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="f934c-287">Následující příklad ukazuje volání `UseLazyLoadingProxies` v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f934c-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="f934c-288">Pokyny k přidávání navigačních vlastností do typů entit najdete v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="f934c-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f934c-289">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f934c-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
