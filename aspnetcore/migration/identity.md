---
title: Ověřování a identitu migrovat do ASP.NET Core
author: ardalis
description: Zjistěte, jak migrovat ověřování a identita z projektu aplikace ASP.NET MVC do projektu aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: 72e62e78e37325ec47d54abbc11a875ae87fb63a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "41755510"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>Ověřování a identitu migrovat do ASP.NET Core

Podle [Steve Smith](https://ardalis.com/)

V předchozím článku jsme [migrovat konfigurace z projektu aplikace ASP.NET MVC do ASP.NET Core MVC](xref:migration/configuration). V tomto článku budeme migrovat funkcím pro správu registrace, přihlášení a uživatele.

## <a name="configure-identity-and-membership"></a>Konfigurace Identity a členství

V architektuře ASP.NET MVC, ověřování a identita funkcí je nakonfigurována pomocí technologie ASP.NET Identity v *Startup.Auth.cs* a *IdentityConfig.cs*, který je umístěn v *App_Start* složka. V ASP.NET Core MVC, tyto funkce nakonfigurovány v *Startup.cs*.

Nainstalujte `Microsoft.AspNetCore.Identity.EntityFrameworkCore` a `Microsoft.AspNetCore.Authentication.Cookies` balíčky NuGet.

Pak otevřete *Startup.cs* a aktualizovat `Startup.ConfigureServices` způsob použití rozhraní Entity Framework a služby identit:

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

V tomto okamžiku existují dva typy odkazovaný ve výše uvedeném kódu, který jsme ještě nebyli migrovaní z projektu ASP.NET MVC: `ApplicationDbContext` a `ApplicationUser`. Vytvořte nový *modely* složky v ASP.NET Core projekt a přidejte dvě třídy do ní odpovídající typy. ASP.NET MVC se najít verze těchto tříd v */Models/IdentityModels.cs*, ale budeme používat jeden soubor třídy migrovaného projektu, protože to je trochu objasnit.

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
using Microsoft.AspNetCore.Identity.EntityFramework;
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

Projekt webu ASP.NET Core MVC neobsahuje množství vlastního nastavení uživatelů, nebo `ApplicationDbContext`. Při migraci skutečné aplikace, je také potřeba migrovat všechny vlastní vlastnosti a metody uživatele vaší aplikace a `DbContext` třídy, jakož i jiné třídy modelů, vaše aplikace využívá. Například pokud vaše `DbContext` má `DbSet<Album>`, je potřeba migrovat `Album` třídy.

Pomocí těchto souborů na místě *Startup.cs* souboru provádět kompilaci aktualizací jeho `using` příkazy:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Naše aplikace je teď připravený na podporu ověřování a služby identit. Právě musí mít tyto funkce zveřejněné uživatelům.

## <a name="migrate-registration-and-login-logic"></a>Migrace registrace a přihlášení logiky

Nakonfigurované pro aplikaci služby identit a přístupu k datům pomocí Entity Framework a systému SQL Server nakonfigurovat je připraven k přidání podpory pro registrace a přihlášení do aplikace. Vzpomeňte si, že [výše v procesu migrace](xref:migration/mvc#migrate-the-layout-file) jsme odkaz na komentář *_LoginPartial* v *_Layout.cshtml*. Nyní je čas vrátit kód, Odkomentujte ho a přidat nezbytné kontrolerů a zobrazení pro podporu funkcí přihlášení.

Zrušením komentáře u `@Html.Partial` řádku v *_Layout.cshtml*:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Teď přidejte nové zobrazení Razor s názvem *_LoginPartial* k *zobrazení/Shared* složky:

Aktualizace *_LoginPartial.cshtml* následujícím kódem (nahradit všechny jeho obsah):

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

V tomto okamžiku byste měli nelze aktualizovat stránku ve svém prohlížeči.

## <a name="summary"></a>Souhrn

ASP.NET Core zavádí změn funkce ASP.NET Identity. V tomto článku jste viděli, jak migrovat ověřování a uživatelská funkce správy identity technologie ASP.NET do ASP.NET Core.
