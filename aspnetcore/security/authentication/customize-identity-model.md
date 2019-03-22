---
title: Přizpůsobení modelu identity v ASP.NET Core
author: ajcvickers
description: Tento článek popisuje, jak přizpůsobit základní datový model Entity Framework Core pro ASP.NET Core Identity.
ms.author: avickers
ms.date: 09/24/2018
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 0aa7448ac37a97a4d09a04caf365f641f22f5997
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327298"
---
# <a name="identity-model-customization-in-aspnet-core"></a>Přizpůsobení modelu identity v ASP.NET Core

Podle [podle Arthur Vickerse](https://github.com/ajcvickers)

ASP.NET Core Identity poskytuje rozhraní pro správu a ukládání uživatelských účtů v aplikacích ASP.NET Core. Identita je přidána do projektu při **jednotlivé uživatelské účty** je zvolen jako mechanismus ověřování. Ve výchozím nastavení, Identity využívá sady Entity Framework (EF) základní datový model. Tento článek popisuje, jak přizpůsobit modelem Identity.

## <a name="identity-and-ef-core-migrations"></a>Identity a migrace EF Core

Před prozkoumání modelu, je užitečné k pochopení fungování Identity s [migrace EF Core](/ef/core/managing-schemas/migrations/) k vytvoření a aktualizaci databáze. Proces je na nejvyšší úrovni:

1. Definovat nebo aktualizovat [datového modelu v kódu](/ef/core/modeling/).
1. Přidejte migraci na tomto modelu se převedou změny, které mohou být použity k databázi.
1. Zkontrolujte, že migrace správně představuje vaše záměry.
1. Použití migrace k aktualizaci databáze byly synchronizované s modelem.
1. Opakujte kroky 1 až 4 dále upřesnit modelu a zachovat databázi synchronizované.

Přidat a použití migrace, použijte jednu z následujících postupů:

* **Konzola správce balíčků** okno (PMC) Pokud pomocí sady Visual Studio. Další informace najdete v tématu [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).
* .NET Core CLI Pokud pomocí příkazového řádku. Další informace najdete v tématu [nástroje příkazového řádku EF Core .NET](/ef/core/miscellaneous/cli/dotnet).
* Kliknutím **migrace použít** tlačítko na chybovou stránku při spuštění aplikace.

ASP.NET Core má dobu vývoje chybová stránka. Obslužná rutina provést migrace při spuštění aplikace. Pro produkční aplikace, často je vhodné generovat SQL skripty z migrace a nasazení změn databází jako součást řízené nasazení aplikace a databáze.

Když se vytvoří nová aplikace využívající identitu, kroky 1 a 2 výše již dokončena. To znamená že původního datového modelu již existuje, a počáteční migraci se přidal do projektu. Počáteční migraci stále musí být použita pro databázi. Počáteční migraci můžete použít některou z následujících postupů:

* Spustit `Update-Database` v konzole PMC.
* Spustit `dotnet ef database update` v příkazovém řádku.
* Klikněte na tlačítko **migrace použít** tlačítko na chybovou stránku při spuštění aplikace.

Zopakujte předchozí kroky, jakmile jsou provedeny změny modelu.

## <a name="the-identity-model"></a>Modelem Identity

### <a name="entity-types"></a>Typy entit

Identity model se skládá z následujících typů entit.

|Typ entity|Popis                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |Představuje uživatele.                                         |
|`Role`     |Představuje roli.                                           |
|`UserClaim`|Reprezentuje deklaraci identity, který má uživatel.                    |
|`UserToken`|Představuje ověřovací token pro uživatele.               |
|`UserLogin`|Přidruží přihlášení uživatele.                              |
|`RoleClaim`|Reprezentuje deklaraci identity, které je udělen pro všechny uživatele v rámci role.|
|`UserRole` |Spojení entit, které přidružuje uživatelů a rolí.               |

### <a name="entity-type-relationships"></a>Entitu typu vztahy

[Typy entit](#entity-types) se vztahují k sobě navzájem následujícími způsoby:

* Každý `User` může mít mnoho `UserClaims`.
* Každý `User` může mít mnoho `UserLogins`.
* Každý `User` může mít mnoho `UserTokens`.
* Každý `Role` může mít mnoho přidružené `RoleClaims`.
* Každý `User` může mít mnoho přidružené `Roles`a každý `Role` můžou být spojené s mnoha `Users`. Toto je vztah many-to-many, který vyžaduje připojení k tabulku v databázi. Tabulky spojení reprezentována `UserRole` entity.

### <a name="default-model-configuration"></a>Výchozí konfigurace modelu

Identity definuje mnoho *třídy kontextu* , která dědí z <xref:Microsoft.EntityFrameworkCore.DbContext> ke konfiguraci a použití modelu. Tato konfigurace se provádí pomocí [EF Core kód první Fluent API](/ef/core/modeling/) v <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> metody třídy kontextu. Výchozí konfigurace je:

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

### <a name="model-generic-types"></a>Obecné typy modelu

Identity definuje výchozí [Common Language Runtime](/dotnet/standard/glossary#clr) výše uvedených typů (CLR) pro každý typ entity. Tyto typy jsou předponu *Identity*:

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

Místo použití těchto typů přímo, typy slouží jako základní třídy pro aplikace pro vlastní typy. `DbContext` Tříd definovaných výčtem Identity jsou obecné, tak, že různé typy CLR lze použít pro jeden nebo více typů entit v modelu. Také umožní tyto obecné typy `User` primární klíč (PK) datový typ změnit.

Při použití identit s podporou pro role, <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> třída by měla být použita. Příklad:

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

Je také možné použít Identity bez role (jenom deklarace identity), v takovém případě <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> třída by měla být použita:

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

Výchozí bod pro přizpůsobení modelu je odvozen od typu odpovídající kontext. Najdete v článku [Model obecných typů](#model-generic-types) oddílu. Tento typ kontextu se běžně označuje `ApplicationDbContext` a je vytvořen pomocí šablony ASP.NET Core.

Kontext se používá ke konfiguraci modelu dvěma způsoby:

* Zadání entity a typy klíčů pro parametry obecného typu.
* Přepsání `OnModelCreating` upravit mapování z těchto typů.

Při přepisování `OnModelCreating`, `base.OnModelCreating` by měla být volána nejprve; dále by měla být volána přepsání konfigurace. EF Core má obvykle služby wins poslední jednu zásadu konfigurace. Například pokud `ToTable` nejdříve volána metoda pro určitý typ entity s názvem jedné tabulky a pak znovu později s názvem jinou tabulku, název tabulky do druhé volání se používá.

### <a name="custom-user-data"></a>Vlastní uživatelská data

[Vlastní uživatelská data](xref:security/authentication/add-user-data) podporuje dědění z `IdentityUser`. Je to obvyklé název tohoto typu `ApplicationUser`:

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

Použití `ApplicationUser` typem jako argumentem obecného kontextu:

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

Není nutné přepsat `OnModelCreating` v `ApplicationDbContext` třídy. EF Core mapuje `CustomTag` vlastnost konvencí. Ale potřeba aktualizovat k vytvoření nové databáze `CustomTag` sloupce. Chcete-li vytvořit sloupec, přidejte migraci a pak aktualizujte databázi, jak je popsáno v [Identity a migrace EF Core](#identity-and-ef-core-migrations).

Aktualizace `Startup.ConfigureServices` použití nového `ApplicationUser` třídy:

::: moniker range=">= aspnetcore-2.1"

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

V ASP.NET Core 2.1 nebo novější je identita ve formě knihovny tříd Razor. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předcházející kód vyžaduje volání <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Pokud generátor Identity se použil k přidání Identity soubory do projektu, odeberte volání `AddDefaultUI`. Další informace naleznete v tématu:

* [Vygenerování identity](xref:security/authentication/scaffold-identity)
* [Přidat, stáhněte si a odstranit vlastní uživatelská data na identitu](xref:security/authentication/add-user-data)

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
        .AddDefaultTokenProviders();
```

::: moniker-end

### <a name="change-the-primary-key-type"></a>Změnit typ primárního klíče

Změnit na datový typ sloupce PK po vytvoření databáze je problematické u řada databázových systémů. Změna primárnímu Klíči obvykle zahrnuje vyřadit a znovu vytvořit v tabulce. Proto typy klíčů musí být zadán v počáteční migraci při vytvoření databáze.

Použijte následující postup změna typu PK:

1. Pokud byla vytvořena databáze před změnou PK spustit `Drop-Database` (PMC) nebo `dotnet ef database drop` (.NET Core CLI) k jeho odstranění.
2. Po potvrzení databáze, odebrat úvodní migrace s `Remove-Migration` (PMC) nebo `dotnet ef migrations remove` (.NET Core CLI).
3. Aktualizace `ApplicationDbContext` třídy odvozovat z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>. Zadejte nový typ klíče pro `TKey`. Například pro použití `Guid` typ klíče:

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

    V předchozím kódu, obecné třídy <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> použít nový typ klíče musí být zadán.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    V předchozím kódu, obecné třídy <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> a <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> použít nový typ klíče musí být zadán.

    ::: moniker-end

    `Startup.ConfigureServices` musí být aktualizován na použití obecných uživatele:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
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

4. Pokud vlastní `ApplicationUser` používá třídy, aktualizaci třídy, která se dědí z `IdentityUser`. Příklad:

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    Aktualizace `ApplicationDbContext` tak, aby odkazovaly vlastní `ApplicationUser` třídy:

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

    Registrovat třídu kontext vlastní databázi, při přidávání služba identit v `Startup.ConfigureServices`:

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    Primární klíč datový typ je odvozen díky analýze <xref:Microsoft.EntityFrameworkCore.DbContext> objektu.

    V ASP.NET Core 2.1 nebo novější je identita ve formě knihovny tříd Razor. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předcházející kód vyžaduje volání <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Pokud generátor Identity se použil k přidání Identity soubory do projektu, odeberte volání `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    Primární klíč datový typ je odvozen díky analýze <xref:Microsoft.EntityFrameworkCore.DbContext> objektu.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> Metoda přijímá `TKey` typ určující primární klíč datového typu.

    ::: moniker-end

5. Pokud vlastní `ApplicationRole` používá třídy, aktualizaci třídy, která se dědí z `IdentityRole<TKey>`. Příklad:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    Aktualizace `ApplicationDbContext` tak, aby odkazovaly vlastní `ApplicationRole` třídy. Například následující třídy odkazuje na vlastní `ApplicationUser` a vlastní `ApplicationRole`:

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu kontext vlastní databázi, při přidávání služba identit v `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    Primární klíč datový typ je odvozen díky analýze <xref:Microsoft.EntityFrameworkCore.DbContext> objektu.

    V ASP.NET Core 2.1 nebo novější je identita ve formě knihovny tříd Razor. Další informace naleznete v tématu <xref:security/authentication/scaffold-identity>. V důsledku toho předcházející kód vyžaduje volání <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>. Pokud generátor Identity se použil k přidání Identity soubory do projektu, odeberte volání `AddDefaultUI`.

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu kontext vlastní databázi, při přidávání služba identit v `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    Primární klíč datový typ je odvozen díky analýze <xref:Microsoft.EntityFrameworkCore.DbContext> objektu.

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    Registrovat třídu kontext vlastní databázi, při přidávání služba identit v `Startup.ConfigureServices`:

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> Metoda přijímá `TKey` typ určující primární klíč datového typu.

    ::: moniker-end

### <a name="add-navigation-properties"></a>Přidání navigační vlastnosti

Změna konfigurace modelu pro relace může být obtížnější než dělat jiné změny. Nahraďte existující relace, spíše než nový, vytvořit další relace musí věnovat pozornost. Zejména změněné relaci je třeba určit stejné vlastnost cizího klíče (Cizíklíč) jako existující relaci. Například vztah mezi `Users` a `UserClaims` je ve výchozím nastavení zadané následujícím způsobem:

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

Cizího klíče pro tento vztah je stanoveno, `UserClaim.UserId` vlastnost. `HasMany` a `WithOne` jsou volat bez argumentů a vytvořit tak relaci bez vlastnosti navigace.

Přidání navigační vlastnost pro `ApplicationUser` , která umožňuje přidružené `UserClaims` odkazovat od uživatele:

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

`TKey` Pro `IdentityUserClaim<TKey>` je typ zadaný pro PK uživatelů. V takovém případě `TKey` je `string` vzhledem k tomu, že se používají výchozí hodnoty. Má **není** PK typ `UserClaim` typu entity.

Teď, když existuje navigační vlastnost, musí se nakonfigurovat v `OnModelCreating`:

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

Všimněte si, že je přesně stejné jako dříve, jenom s navigační vlastnost zadanou ve volání do nakonfigurovaný vztah `HasMany`.

Vlastnosti navigace existují pouze v modelu EF, ne databáze. Vzhledem k tomu, že nedošlo ke změně cizího klíče pro relaci, nevyžaduje, aby databáze, kterou chcete aktualizovat tento druh změny modelu. To lze ověřit tak, že přidáte migrace po provedení změny. `Up` a `Down` metody jsou prázdné.

### <a name="add-all-user-navigation-properties"></a>Přidat všechny uživatele navigační vlastnosti

Pomocí výše uvedené části jako vodítko, v následujícím příkladu nakonfigurujeme jednosměrnou navigační vlastnosti pro všechny relace pro uživatele:

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

### <a name="add-user-and-role-navigation-properties"></a>Přidat uživatele a roli navigační vlastnosti

Pomocí výše uvedené části jako vodítko, v následujícím příkladu nakonfigurujeme navigačních vlastností u všech relací na uživatele a roli:

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

* Tento příklad zahrnuje také `UserRole` připojte se k entitě, která je nutná pro navigaci vztah many-to-many od uživatelů k rolím.
* Nezapomeňte změnit typy navigačních vlastností, aby to odrážel `ApplicationXxx` typy jsou nyní používá místo `IdentityXxx` typy.
* Nezapomeňte použít `ApplicationXxx` v Obecné `ApplicationContext` definice.

### <a name="add-all-navigation-properties"></a>Přidat všechny vlastnosti navigace

Pomocí výše uvedené části jako vodítko, v následujícím příkladu nakonfigurujeme navigační vlastnosti pro všechny relace na všechny typy entit:

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

### <a name="use-composite-keys"></a>Pomocí složených klíčů

V předchozích částech jsme vám ukázali, změna typu klíče, použít v modelu Identity. Změna klíčů model Identity, který se má použít složené klíče není podporován nebo doporučené. Složený klíč pomocí Identity postup zahrnuje změnu, jak kód Identity Manageru komunikuje s modelem. Toto přizpůsobení je nad rámec tohoto dokumentu.

### <a name="change-tablecolumn-names-and-facets"></a>Změňte názvy tabulek nebo sloupců a omezující vlastnosti

Chcete-li změnit názvy tabulek a sloupců, zavolejte `base.OnModelCreating`. Pak přidejte konfiguraci přepsat všechny výchozí hodnoty. Chcete-li například změnit název všech tabulek Identity:

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

Tyto příklady používají výchozí typy Identity. Pokud se používá jako typ aplikace. `ApplicationUser`, nakonfigurujte tento typ namísto výchozího typu.

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

Některé typy sloupců databáze může mít nakonfigurovanou určité *omezující vlastnosti* (například maximální `string` povolená délka). Následující příklad nastaví maximální délka sloupce pro několik `string` vlastnosti v modelu:

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

Schémata může chovat jinak napříč poskytovatelé databází. Pro SQL Server, ve výchozím nastavení je vytvořit všechny tabulky v *dbo* schématu. Tabulky lze vytvořit v jiné schéma. Příklad:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a>Opožděné načtení

V této části se přidá podporu pro proxy opožděné načtení do modelu identit. Opožděné načtení je užitečné, protože umožňuje navigační vlastnosti bez první zajištění, která jste načetli.

Typy entit provádět vhodný pro opožděné načtení několika způsoby, jak je popsáno v [EF Core dokumentaci](/ef/core/querying/related-data#lazy-loading). Pro jednoduchost použijte opožděné načtení proxy, což vyžaduje:

* Instalace [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) balíčku.
* Volání <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> uvnitř <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.
* Typy subjekt `public virtual` navigační vlastnosti.

Následující příklad ukazuje volání `UseLazyLoadingProxies` v `Startup.ConfigureServices`:

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

Naleznete v předchozích ukázkách pro doprovodné materiály k přidávání navigačních vlastností pro typy entit.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/authentication/scaffold-identity>

::: moniker-end
