---
title: Migrace ověřování a identity na ASP.NET Core
author: ardalis
description: Naučte se migrovat ověřování a identitu z projektu ASP.NET MVC do projektu ASP.NET Core MVC.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661852"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="82118-103">Migrace ověřování a identity na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82118-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="82118-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="82118-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="82118-105">V předchozím článku jsme [migrovali konfiguraci z projektu ASP.NET MVC na ASP.NET Core MVC](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="82118-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="82118-106">V tomto článku migrujeme funkce registrace, přihlašování a správy uživatelů.</span><span class="sxs-lookup"><span data-stu-id="82118-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="82118-107">Konfigurace identity a členství</span><span class="sxs-lookup"><span data-stu-id="82118-107">Configure Identity and Membership</span></span>

<span data-ttu-id="82118-108">V ASP.NET MVC jsou funkce ověřování a identity nakonfigurované pomocí ASP.NET Identity v *Startup.auth.cs* a *IdentityConfig.cs*, které jsou umístěné ve složce *app_start* .</span><span class="sxs-lookup"><span data-stu-id="82118-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="82118-109">Ve ASP.NET Core MVC jsou tyto funkce nakonfigurované v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="82118-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="82118-110">Nainstalujte `Microsoft.AspNetCore.Identity.EntityFrameworkCore` a `Microsoft.AspNetCore.Authentication.Cookies` balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="82118-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="82118-111">Pak otevřete *Startup.cs* a aktualizujte metodu `Startup.ConfigureServices`, aby používala Entity Framework a služby identit:</span><span class="sxs-lookup"><span data-stu-id="82118-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="82118-112">V tomto okamžiku existují dva typy odkazované ve výše uvedeném kódu, které jsme ještě nemigrovali z projektu ASP.NET MVC: `ApplicationDbContext` a `ApplicationUser`.</span><span class="sxs-lookup"><span data-stu-id="82118-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="82118-113">Vytvořte novou složku *modelů* v projektu ASP.NET Core a přidejte do ní dvě třídy odpovídající těmto typům.</span><span class="sxs-lookup"><span data-stu-id="82118-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="82118-114">Verze ASP.NET MVC těchto tříd najdete v */Models/IdentityModels.cs*, ale v migrovaném projektu budeme používat jeden soubor na třídu, protože to je mnohem jasné.</span><span class="sxs-lookup"><span data-stu-id="82118-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="82118-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="82118-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="82118-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="82118-116">*ApplicationDbContext.cs*:</span></span>

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

<span data-ttu-id="82118-117">ASP.NET Core webový projekt MVC Starter neobsahuje mnoho přizpůsobení uživatelů nebo `ApplicationDbContext`.</span><span class="sxs-lookup"><span data-stu-id="82118-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="82118-118">Při migraci reálné aplikace je také potřeba migrovat všechny vlastní vlastnosti a metody pro uživatele a `DbContext` třídy vaší aplikace, stejně jako všechny jiné třídy modelu, které vaše aplikace využívá.</span><span class="sxs-lookup"><span data-stu-id="82118-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="82118-119">Například pokud má vaše `DbContext` `DbSet<Album>`, je nutné migrovat třídu `Album`.</span><span class="sxs-lookup"><span data-stu-id="82118-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="82118-120">Když jsou tyto soubory na místě, soubor *Startup.cs* se dá zkompilovat tak, že aktualizuje jeho příkazy `using`:</span><span class="sxs-lookup"><span data-stu-id="82118-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="82118-121">Naše aplikace je teď připravená podporovat ověřování a služby identit.</span><span class="sxs-lookup"><span data-stu-id="82118-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="82118-122">Stačí tyto funkce zpřístupnit uživatelům.</span><span class="sxs-lookup"><span data-stu-id="82118-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="82118-123">Migrace logiky registrace a přihlášení</span><span class="sxs-lookup"><span data-stu-id="82118-123">Migrate registration and login logic</span></span>

<span data-ttu-id="82118-124">Služba identit nakonfigurovaná pro přístup k aplikacím a datům nakonfigurovaným pomocí Entity Framework a SQL Server je připravená přidat podporu pro registraci a přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="82118-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="82118-125">Odhlaste se [dříve v procesu migrace](xref:migration/mvc#migrate-the-layout-file) a odkomentujme odkaz na *_LoginPartial* v *_Layout. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="82118-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="82118-126">Nyní je čas vrátit se k tomuto kódu, odkomentovat ho a přidat v nezbytných řadičích a zobrazeních pro podporu funkcí přihlášení.</span><span class="sxs-lookup"><span data-stu-id="82118-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="82118-127">Odkomentuje `@Html.Partial` řádku v *_Layout. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="82118-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="82118-128">Nyní přidejte nové zobrazení Razor s názvem *_LoginPartial* do *zobrazení/sdílená* složka:</span><span class="sxs-lookup"><span data-stu-id="82118-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="82118-129">Aktualizujte *_LoginPartial. cshtml* pomocí následujícího kódu (nahraďte celý jeho obsah):</span><span class="sxs-lookup"><span data-stu-id="82118-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="82118-130">V tuto chvíli byste měli být schopni aktualizovat web v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="82118-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="82118-131">Souhrn</span><span class="sxs-lookup"><span data-stu-id="82118-131">Summary</span></span>

<span data-ttu-id="82118-132">ASP.NET Core zavádí změny v ASP.NET Identitych funkcích.</span><span class="sxs-lookup"><span data-stu-id="82118-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="82118-133">V tomto článku jste viděli, jak migrovat funkce pro správu ověřování a správy uživatelů ASP.NET Identity ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="82118-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
