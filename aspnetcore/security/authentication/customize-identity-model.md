---
title: IdentityPřizpůsobení modelu v ASP.NET Core
author: ajcvickers
description: Tento článek popisuje, jak přizpůsobit základní datový model Entity Framework Core pro ASP.NET Core Identity .
ms.author: avickers
ms.date: 07/01/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 3a5bac0e3e34602b1f8a85a7bcde1ba92b372607
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399163"
---
# <a name="identity-model-customization-in-aspnet-core"></a>IdentityPřizpůsobení modelu v ASP.NET Core

Od [Arthur Vickers](https://github.com/ajcvickers)

ASP.NET Core Identity poskytuje rozhraní pro správu a ukládání uživatelských účtů v aplikacích ASP.NET Core. Identityse přidá do projektu, když jsou **jednotlivé uživatelské účty** vybrané jako ověřovací mechanismus. Ve výchozím nastavení Identity využívá základní datový model Entity Framework (EF). Tento článek popisuje, jak model přizpůsobit Identity .

## <a name="identity-and-ef-core-migrations"></a>Identitya EF Core migrace

Před prozkoumáním modelu je užitečné pochopit, jak Identity funguje s [EF Core migrací](/ef/core/managing-schemas/migrations/) pro vytvoření a aktualizaci databáze. Na nejvyšší úrovni je tento proces:

1. Definice nebo aktualizace [datového modelu v kódu](/ef/core/modeling/).
1. Přidáním migrace můžete tento model přeložit do změn, které se dají použít pro databázi.
1. Ověřte, že migrace správně reprezentuje vaše záměry.
1. Použijte migraci k aktualizaci databáze, aby byla synchronizovaná s modelem.
1. Opakujte kroky 1 až 4 pro další upřesnění modelu a udržujte databázi v synchronizaci.

Pomocí jednoho z následujících přístupů přidejte a použijte migrace:

* Okno **konzoly Správce balíčků** (PMC), pokud používáte Visual Studio. Další informace najdete v tématu [EF Core nástrojů PMC](/ef/core/miscellaneous/cli/powershell).
* .NET Core CLI při použití příkazového řádku Další informace najdete v tématu [EF Core nástroje příkazového řádku .NET](/ef/core/miscellaneous/cli/dotnet).
* Po spuštění aplikace klikněte na tlačítko **použít migrace** na chybové stránce.

ASP.NET Core má obslužnou rutinu chybové stránky v době vývoje. Obslužná rutina může při spuštění aplikace použít migrace. Produkční aplikace obvykle generují skripty SQL z migrace a nasazují změny databáze jako součást řízené aplikace a nasazení databáze.

Když se vytvoří nová aplikace Identity , kroky 1 a 2 se už dokončí. To znamená, že počáteční datový model již existuje a byla do projektu přidána počáteční migrace. Počáteční migrace se pořád musí použít pro databázi. Prvotní migraci můžete provést pomocí jednoho z následujících přístupů:

* Spusťte `Update-Database` v PMC.
* Spusťte `dotnet ef database update` příkaz v příkazovém prostředí.
* Po spuštění aplikace klikněte na tlačítko **použít migrace** na chybové stránce.

Předchozí kroky opakujte, protože se v modelu provedou změny.

## <a name="the-identity-model"></a>IdentityModel

### <a name="entity-types"></a>Typy entit

IdentityModel se skládá z následujících typů entit.

|Typ entity|Description                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Představuje uživatele.                                         |
|`Role`     |Představuje roli.                                           |
|`UserClaim`|Představuje deklaraci identity, kterou uživatel má.                    |
|`UserToken`|Představuje ověřovací token pro uživatele.               |
|`UserLogin`|Přidruží uživatele k přihlášení.                              |
|`RoleClaim`|Představuje deklaraci identity, která je udělená všem uživatelům v rámci role.|
|`UserRole` |Entita JOIN, která přidruží uživatele a role.               |

### <a name="entity-type-relationships"></a>Vztahy typů entit

[Typy entit](#entity-types) spolu vzájemně souvisí následujícími způsoby:

* Každý z nich `User` může mít mnoho `UserClaims` .
* Každý z nich `User` může mít mnoho `UserLogins` .
* Každý z nich `User` může mít mnoho `UserTokens` .
* Každý z nich `Role` může mít přiřazeno mnoho `RoleClaims` .
* Každý z nich `User` může mít přiřazeno mnoho `Roles` a každý z nich `Role` může být přidružen k mnoha `Users` . Jedná se o vztah m:n, který vyžaduje tabulku JOIN v databázi. Tabulka JOIN je reprezentována `UserRole` entitou.

### <a name="default-model-configuration"></a>Výchozí konfigurace modelu

Identitydefinuje mnoho *kontextových tříd* , které dědí z [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) ke konfiguraci a použití modelu. Tato konfigurace se provádí pomocí [rozhraní EF Core Code First Fluent API](/ef/core/modeling/) v metodě [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) třídy Context. Výchozí konfigurace:

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

### <a name="model-generic-types"></a>Obecné typy modelů

Identitydefinuje výchozí typy modulu CLR ( [Common Language Runtime](/dotnet/standard/glossary#clr) ) pro každý z typů entit uvedených výše. Všechny tyto typy mají předponu *Identity* :

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Místo toho, aby tyto typy používaly přímo, lze typy použít jako základní třídy pro vlastní typy aplikace. `DbContext`Třídy definované pomocí Identity jsou obecné, takže různé typy CLR lze použít pro jeden nebo více typů entit v modelu. Tyto obecné typy také umožňují `User` změnit datový typ primární klíč (PK).

Při použití Identity s podporou rolí <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> by měla být použita třída. Například:

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

Je také možné použít Identity bez rolí (pouze deklarace identity), v takovém případě <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> by měla být použita třída:

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

## <a name="customize-the-model"></a>Přizpůsobení modelu

Výchozí bod pro přizpůsobení modelu je odvozen od příslušného typu kontextu. Viz oddíl [Obecné typy modelů](#model-generic-types) . Tento typ kontextu je obvykle volán `ApplicationDbContext` a je vytvořen šablonami ASP.NET Core.

Kontext se používá ke konfiguraci modelu dvěma způsoby:

* Zadání entit a typů klíčů pro parametry obecného typu.
* Přepsáním `OnModelCreating` upravíte mapování těchto typů.

Při přepsání `OnModelCreating` `base.OnModelCreating` by se mělo volat jako první. přepsání konfigurace by se mělo volat jako další. EF Core obvykle má pro konfiguraci poslední zásadu služby WINS. Například pokud `ToTable` je metoda pro typ entity volána jako první v jednom názvu tabulky a poté později s jiným názvem tabulky, je použita název tabulky ve druhém volání.

### <a name="custom-user-data"></a>Vlastní uživatelská data

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

[Vlastní uživatelská data](xref:security/authentication/add-user-data) jsou podporovaná děděním z `IdentityUser` . Vlastní pojmenování tohoto typu `ApplicationUser` :

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Použijte `ApplicationUser` typ jako obecný argument pro kontext:

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

Ve třídě není nutné přepisovat `OnModelCreating` `ApplicationDbContext` . EF Core mapuje `CustomTag` vlastnost podle konvence. Databázi je ale potřeba aktualizovat, aby se vytvořil nový `CustomTag` sloupec. Pokud chcete vytvořit sloupec, přidejte migraci a pak aktualizujte databázi, jak je popsáno v tématu [ Identity a EF Core migrace](#identity-and-ef-core-migrations).

Update *Pages/Shared/_LoginPartial. cshtml* a nahraďte `IdentityUser` `ApplicationUser` :

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

Aktualizujte *oblasti/ Identity /IdentityHostingStartup.cs* nebo `Startup.ConfigureServices` nahraďte `IdentityUser` `ApplicationUser` .

```csharp
services.AddIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` . Další informace naleznete v tématu:

* [GenerováníIdentity](xref:security/authentication/scaffold-identity)
* [Přidání, stažení a odstranění vlastních uživatelských datIdentity](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a>Změnit typ primárního klíče

Změna datového typu sloupce PK poté, co byla databáze vytvořena, je problematická v mnoha databázových systémech. Změna PK obvykle zahrnuje vyřazení a opětovné vytvoření tabulky. Proto by měly být při vytvoření databáze zadané typy klíčů při počáteční migraci.

Chcete-li změnit typ PK, postupujte podle těchto kroků:

1. Pokud byla databáze vytvořena před změnou PK, spusťte `Drop-Database` (PMC) nebo `dotnet ef database drop` (.NET Core CLI) a odstraňte ji.
2. Po potvrzení odstranění databáze odeberte počáteční migraci pomocí `Remove-Migration` (PMC) nebo `dotnet ef migrations remove` (.NET Core CLI).
3. Aktualizuje `ApplicationDbContext` třídu, ze které se má odvodit <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603> . Zadejte nový typ klíče pro `TKey` . Pokud například chcete použít `Guid` typ klíče:

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

    V předchozím kódu je nutné zadat obecné třídy <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> použít nový typ klíče.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    V předchozím kódu je nutné zadat obecné třídy <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> použít nový typ klíče.

    ::: moniker-end

    `Startup.ConfigureServices`je nutné aktualizovat, aby používala obecného uživatele:

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

4. Pokud `ApplicationUser` je použita vlastní třída, aktualizujte třídu tak, aby dědila z `IdentityUser` . Například:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Aktualizace `ApplicationDbContext` , aby odkazovala na vlastní `ApplicationUser` třídu:

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

    Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda přijímá typ, který `TKey` označuje datový typ primárního klíče.

    ::: moniker-end

5. Pokud `ApplicationRole` je použita vlastní třída, aktualizujte třídu tak, aby dědila z `IdentityRole<TKey>` . Například:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Aktualizujte `ApplicationDbContext` tak, aby odkazovala na vlastní `ApplicationRole` třídu. Například následující třída odkazuje na vlastní `ApplicationUser` a vlastní `ApplicationRole` :

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    V ASP.NET Core 2,1 nebo novější, Identity je k dispozici jako Razor Knihovna tříd. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předchozí kód vyžaduje volání metody <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*> . Pokud se generátor Identity použil k přidání Identity souborů do projektu, odeberte volání `AddDefaultUI` .

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Datový typ primárního klíče je odvozený analýzou objektu [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) .

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu vlastního kontextu databáze při přidávání Identity služby v nástroji `Startup.ConfigureServices` :

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>Metoda přijímá typ, který `TKey` označuje datový typ primárního klíče.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Přidat vlastnosti navigace

Změna konfigurace modelu pro relace může být obtížnější než provedení jiných změn. Je nutné vzít v potaz stávající relace, aniž byste museli vytvářet nové, další vztahy. Konkrétně změna vztahu musí určovat stejnou vlastnost cizího klíče (FK) jako existující relace. Například vztah mezi `Users` a `UserClaims` je ve výchozím nastavení zadán následujícím způsobem:

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

FK pro tento vztah je zadáno jako `UserClaim.UserId` vlastnost. `HasMany`a `WithOne` jsou volány bez argumentů pro vytvoření vztahu bez vlastností navigace.

Přidat navigační vlastnost k `ApplicationUser` , která umožňuje `UserClaims` odkazování na uživatele:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey`Pro `IdentityUserClaim<TKey>` je typ určený pro PK pro uživatele. V tomto případě je to způsobeno tím, že `TKey` `string` jsou použity výchozí hodnoty. **Není** to typ PK pro `UserClaim` typ entity.

Teď, když existuje vlastnost navigace, musí být nakonfigurovaná v `OnModelCreating` :

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

Všimněte si, že vztah je nakonfigurován přesně stejně jako dříve, pouze s navigační vlastností zadanou v volání `HasMany` .

Navigační vlastnosti existují pouze v modelu EF, nikoli v databázi. Vzhledem k tomu, že se FK pro relaci nezměnilo, tento druh změny modelu nevyžaduje aktualizaci databáze. Tuto možnost lze zkontrolovat přidáním migrace po provedení změny. `Up`Metody a `Down` jsou prázdné.

### <a name="add-all-user-navigation-properties"></a>Přidat všechny vlastnosti navigace na uživateli

V následujícím příkladu se pomocí výše uvedeného příkladu nakonfiguruje jednosměrné navigační vlastnosti pro všechny relace na uživateli:

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

### <a name="add-user-and-role-navigation-properties"></a>Přidat vlastnosti navigace uživatele a role

Pomocí výše uvedeného oddílu můžete v následujícím příkladu nakonfigurovat navigační vlastnosti pro všechny relace pro uživatele a roli:

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

Poznámky:

* Tento příklad obsahuje také `UserRole` entitu JOIN, která je nutná k procházení relace m:n od uživatelů po role.
* Nezapomeňte změnit typy vlastností navigace tak, aby odrážely, že `ApplicationXxx` se nyní používají typy namísto `IdentityXxx` typů.
* Nezapomeňte použít `ApplicationXxx` v obecné `ApplicationContext` definici.

### <a name="add-all-navigation-properties"></a>Přidat všechny navigační vlastnosti

Pomocí výše uvedeného oddílu můžete v následujícím příkladu nakonfigurovat navigační vlastnosti pro všechny relace na všech typech entit:

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

### <a name="use-composite-keys"></a>Použití složených klíčů

Předchozí části ukázaly změnu typu klíče použitého v Identity modelu. Změna Identity modelu klíče na použití složených klíčů není podporována nebo není doporučena. Použití složeného klíče s Identity zahrnuje změnu způsobu Identity interakce kódu vedoucího s modelem. Toto přizpůsobení překračuje rozsah tohoto dokumentu.

### <a name="change-tablecolumn-names-and-facets"></a>Změna názvů a vlastností tabulky nebo sloupce

Chcete-li změnit názvy tabulek a sloupců, zavolejte `base.OnModelCreating` . Pak přidejte konfiguraci pro přepsání všech výchozích hodnot. Chcete-li například změnit název všech Identity tabulek:

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

V těchto příkladech jsou použity výchozí Identity typy. Pokud používáte typ aplikace `ApplicationUser` , jako je, nakonfigurujte tento typ místo výchozího typu.

Následující příklad změní některé názvy sloupců:

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

U některých typů databázových sloupců se dají nakonfigurovat určité *omezující vlastnosti* (například maximální `string` povolená délka). Následující příklad nastaví maximální délku sloupce pro několik `string` vlastností v modelu:

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

### <a name="map-to-a-different-schema"></a>Mapování na jiné schéma

Schémata se můžou v různých poskytovatelích databáze chovat různě. V případě SQL Server se ve výchozím nastavení vytvoří všechny tabulky ve schématu *dbo* . Tabulky lze vytvořit v jiném schématu. Například:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Opožděné načítání

V této části je přidána podpora pro opožděné načítání proxy serverů v Identity modelu. Opožděné načítání je užitečné, protože umožňuje použití navigačních vlastností bez prvotního zajištění jejich načtení.

Typy entit mohou být vhodné pro opožděné načtení několika způsobů, jak je popsáno v [dokumentaci EF Core](/ef/core/querying/related-data#lazy-loading). Pro jednoduchost používejte proxy servery s opožděným načtením, které vyžadují:

* Instalace balíčku [Microsoft. EntityFrameworkCore. proxy](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) .
* Volání <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> uvnitř [ \<TContext> AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).
* Typy veřejných entit s `public virtual` navigačními vlastnostmi.

Následující příklad ukazuje volání `UseLazyLoadingProxies` v `Startup.ConfigureServices` :

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Pokyny k přidávání navigačních vlastností do typů entit najdete v předchozích příkladech.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/scaffold-identity>

::: moniker-end
