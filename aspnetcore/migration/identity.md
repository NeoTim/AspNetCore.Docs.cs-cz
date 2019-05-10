---
title: Ověřování a identitu migrovat do ASP.NET Core
author: ardalis
description: Zjistěte, jak migrovat ověřování a identita z projektu aplikace ASP.NET MVC do projektu aplikace ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: 72e62e78e37325ec47d54abbc11a875ae87fb63a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898461"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="8d7a0-103">Ověřování a identitu migrovat do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d7a0-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="8d7a0-104">Podle [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8d7a0-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8d7a0-105">V předchozím článku jsme [migrovat konfigurace z projektu aplikace ASP.NET MVC do ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="8d7a0-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="8d7a0-106">V tomto článku budeme migrovat funkcím pro správu registrace, přihlášení a uživatele.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="8d7a0-107">Konfigurace Identity a členství</span><span class="sxs-lookup"><span data-stu-id="8d7a0-107">Configure Identity and Membership</span></span>

<span data-ttu-id="8d7a0-108">V architektuře ASP.NET MVC, ověřování a identita funkcí je nakonfigurována pomocí technologie ASP.NET Identity v *Startup.Auth.cs* a *IdentityConfig.cs*, který je umístěn v *App_Start* složka.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="8d7a0-109">V ASP.NET Core MVC, tyto funkce nakonfigurovány v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="8d7a0-110">Nainstalujte `Microsoft.AspNetCore.Identity.EntityFrameworkCore` a `Microsoft.AspNetCore.Authentication.Cookies` balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="8d7a0-111">Pak otevřete *Startup.cs* a aktualizovat `Startup.ConfigureServices` způsob použití rozhraní Entity Framework a služby identit:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="8d7a0-112">V tomto okamžiku existují dva typy odkazovaný ve výše uvedeném kódu, který jsme ještě nebyli migrovaní z projektu ASP.NET MVC: `ApplicationDbContext` a `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="8d7a0-113">Vytvořte nový *modely* složky v ASP.NET Core projekt a přidejte dvě třídy do ní odpovídající typy.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="8d7a0-114">ASP.NET MVC se najít verze těchto tříd v */Models/IdentityModels.cs*, ale budeme používat jeden soubor třídy migrovaného projektu, protože to je trochu objasnit.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="8d7a0-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="8d7a0-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-116">*ApplicationDbContext.cs*:</span></span>

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

<span data-ttu-id="8d7a0-117">Projekt webu ASP.NET Core MVC neobsahuje množství vlastního nastavení uživatelů, nebo `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="8d7a0-118">Při migraci skutečné aplikace, je také potřeba migrovat všechny vlastní vlastnosti a metody uživatele vaší aplikace a `DbContext` třídy, jakož i jiné třídy modelů, vaše aplikace využívá.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="8d7a0-119">Například pokud vaše `DbContext` má `DbSet<Album>`, je potřeba migrovat `Album` třídy.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="8d7a0-120">Pomocí těchto souborů na místě *Startup.cs* souboru provádět kompilaci aktualizací jeho `using` příkazy:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="8d7a0-121">Naše aplikace je teď připravený na podporu ověřování a služby identit.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="8d7a0-122">Právě musí mít tyto funkce zveřejněné uživatelům.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="8d7a0-123">Migrace registrace a přihlášení logiky</span><span class="sxs-lookup"><span data-stu-id="8d7a0-123">Migrate registration and login logic</span></span>

<span data-ttu-id="8d7a0-124">Nakonfigurované pro aplikaci služby identit a přístupu k datům pomocí Entity Framework a systému SQL Server nakonfigurovat je připraven k přidání podpory pro registrace a přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="8d7a0-125">Vzpomeňte si, že [výše v procesu migrace](xref:migration/mvc#migrate-the-layout-file) jsme odkaz na komentář *_LoginPartial* v *_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="8d7a0-126">Nyní je čas vrátit kód, Odkomentujte ho a přidat nezbytné kontrolerů a zobrazení pro podporu funkcí přihlášení.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="8d7a0-127">Zrušením komentáře u `@Html.Partial` řádku v *_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="8d7a0-128">Teď přidejte nové zobrazení Razor s názvem *_LoginPartial* k *zobrazení/Shared* složky:</span><span class="sxs-lookup"><span data-stu-id="8d7a0-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="8d7a0-129">Aktualizace *_LoginPartial.cshtml* následujícím kódem (nahradit všechny jeho obsah):</span><span class="sxs-lookup"><span data-stu-id="8d7a0-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="8d7a0-130">V tomto okamžiku byste měli nelze aktualizovat stránku ve svém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="8d7a0-131">Souhrn</span><span class="sxs-lookup"><span data-stu-id="8d7a0-131">Summary</span></span>

<span data-ttu-id="8d7a0-132">ASP.NET Core zavádí změn funkce ASP.NET Identity.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="8d7a0-133">V tomto článku jste viděli, jak migrovat ověřování a uživatelská funkce správy identity technologie ASP.NET do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8d7a0-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
