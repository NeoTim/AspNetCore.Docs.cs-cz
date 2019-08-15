---
title: Migrace ověřování a identity na ASP.NET Core
author: ardalis
description: Naučte se migrovat ověřování a identitu z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022273"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Migrace ověřování a identity na ASP.NET Core

[Steve Smith](https://ardalis.com/)

V předchozím článku jsme [migrovali konfiguraci z projektu ASP.NET MVC na ASP.NET Core MVC](xref:migration/configuration). V tomto článku migrujeme funkce registrace, přihlašování a správy uživatelů.

## <a name="configure-identity-and-membership"></a>Konfigurace identity a členství

V ASP.NET MVC jsou funkce ověřování a identity nakonfigurované pomocí ASP.NET Identity v *Startup.auth.cs* a *IdentityConfig.cs*, které jsou umístěné ve složce *app_start* . Ve ASP.NET Core MVC jsou tyto funkce nakonfigurované v *Startup.cs*.

Nainstalujte balíčky NuGet `Microsoft.AspNetCore.Authentication.Cookies`a. `Microsoft.AspNetCore.Identity.EntityFrameworkCore`

Pak otevřete *Startup.cs* a aktualizujte `Startup.ConfigureServices` metodu tak, aby používala Entity Framework a služby identit:

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

V tomto okamžiku existují dva typy, na které se odkazuje ve výše uvedeném kódu, který jsme ještě nemigrovali z projektu ASP.NET `ApplicationDbContext` MVC `ApplicationUser`: a. Vytvořte novou složku *modelů* v projektu ASP.NET Core a přidejte do ní dvě třídy odpovídající těmto typům. Verze ASP.NET MVC těchto tříd najdete v */Models/IdentityModels.cs*, ale v migrovaném projektu budeme používat jeden soubor na třídu, protože to je mnohem jasné.

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

Webový projekt ASP.NET Core MVC Starter nezahrnuje mnoho přizpůsobení uživatelů nebo `ApplicationDbContext`. Při migraci reálné aplikace je také potřeba migrovat všechny vlastní vlastnosti a metody uživatele a `DbContext` třídy vaší aplikace a také všechny jiné třídy modelu, které vaše aplikace využívá. Například pokud máte `DbContext` `DbSet<Album>`, potřebujete migrovat `Album` třídu.

Když jsou tyto soubory na místě, soubor *Startup.cs* se dá zkompilovat tak, že aktualizuje jeho `using` příkazy:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Naše aplikace je teď připravená podporovat ověřování a služby identit. Stačí tyto funkce zpřístupnit uživatelům.

## <a name="migrate-registration-and-login-logic"></a>Migrace logiky registrace a přihlášení

Služba identit nakonfigurovaná pro přístup k aplikacím a datům nakonfigurovaným pomocí Entity Framework a SQL Server je připravená přidat podporu pro registraci a přihlášení do aplikace. Odhlaste se [dříve v procesu migrace](xref:migration/mvc#migrate-the-layout-file) a zakomentujte odkaz na *_LoginPartial* v *_Layout. cshtml*. Nyní je čas vrátit se k tomuto kódu, odkomentovat ho a přidat v nezbytných řadičích a zobrazeních pro podporu funkcí přihlášení.

Odkomentujte `@Html.Partial` řádek v *_Layout. cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Nyní přidejte nové zobrazení Razor s názvem *_LoginPartial* do *zobrazení/sdílená* složka:

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

## <a name="summary"></a>Souhrn

ASP.NET Core zavádí změny v ASP.NET Identitych funkcích. V tomto článku jste viděli, jak migrovat funkce pro správu ověřování a správy uživatelů ASP.NET Identity ASP.NET Core.
