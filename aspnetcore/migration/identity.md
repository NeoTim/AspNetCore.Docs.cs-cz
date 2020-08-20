---
title: Migrace ověřování a Identity ASP.NET Core
author: ardalis
description: Naučte se migrovat ověřování a identitu z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 85674d7ed02f189f78da92bffdf927a3ca21357d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629506"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>Migrace ověřování a Identity ASP.NET Core

[Steve Smith](https://ardalis.com/)

V předchozím článku jsme [migrovali konfiguraci z projektu ASP.NET MVC na ASP.NET Core MVC](xref:migration/configuration). V tomto článku migrujeme funkce registrace, přihlašování a správy uživatelů.

## <a name="configure-no-locidentity-and-membership"></a>Konfigurace Identity a členství

V ASP.NET MVC jsou funkce ověřování a identity nakonfigurované pomocí ASP.NET Identity v *Startup.Auth.cs* a * Identity config.cs*, které jsou umístěné ve složce *app_start* . Ve ASP.NET Core MVC jsou tyto funkce nakonfigurované v *Startup.cs*.

Nainstalujte následující balíčky NuGet:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

V *Startup.cs*aktualizujte `Startup.ConfigureServices` metodu tak, aby používala Entity Framework a Identity služby:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

V tomto okamžiku existují dva typy, na které se odkazuje ve výše uvedeném kódu, který jsme ještě nemigrovali z projektu ASP.NET MVC: `ApplicationDbContext` a `ApplicationUser` . Vytvořte novou složku *modelů* v projektu ASP.NET Core a přidejte do ní dvě třídy odpovídající těmto typům. Verze ASP.NET MVC těchto tříd najdete ve */Models/ Identity Models.cs*, ale v migrovaném projektu budeme používat jeden soubor na třídu, protože to je mnohem jasné.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

Webový projekt ASP.NET Core MVC Starter nezahrnuje mnoho přizpůsobení uživatelů nebo `ApplicationDbContext` . Při migraci reálné aplikace je také potřeba migrovat všechny vlastní vlastnosti a metody uživatele a třídy vaší aplikace a také `DbContext` všechny jiné třídy modelu, které vaše aplikace využívá. Například pokud máte `DbContext` `DbSet<Album>` , potřebujete migrovat `Album` třídu.

Když jsou tyto soubory na místě, soubor *Startup.cs* se dá zkompilovat tak, že aktualizuje jeho `using` příkazy:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Naše aplikace je teď připravená na podporu ověřování a Identity služeb. Stačí tyto funkce zpřístupnit uživatelům.

## <a name="migrate-registration-and-login-logic"></a>Migrace logiky registrace a přihlášení

Se Identity službami nakonfigurovanými pro přístup k aplikacím a datům nakonfigurovaným pomocí Entity Framework a SQL Server jsme připraveni přidat podporu pro registraci a přihlášení do aplikace. Odhlaste se [dříve v procesu migrace](xref:migration/mvc#migrate-the-layout-file) a odkomentujme odkaz na *_LoginPartial* v *_Layout. cshtml*. Nyní je čas vrátit se k tomuto kódu, odkomentovat ho a přidat v nezbytných řadičích a zobrazeních pro podporu funkcí přihlášení.

Odkomentujte `@Html.Partial` řádek v *_Layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Nyní přidejte nové zobrazení s Razor názvem *_LoginPartial* do *zobrazení/sdílená* složka:

Aktualizujte *_LoginPartial. cshtml* pomocí následujícího kódu (nahraďte celý jeho obsah):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

V tuto chvíli byste měli být schopni aktualizovat web v prohlížeči.

## <a name="summary"></a>Shrnutí

ASP.NET Core přináší změny Identity funkcí ASP.NET. V tomto článku jste se seznámili s postupem migrace funkcí pro správu ověřování a správy uživatelů ASP.NET Identity na ASP.NET Core.
