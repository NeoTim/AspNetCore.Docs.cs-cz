---
title: Přidání, stažení a odstranění uživatelských dat do identity v projektu ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak přidat vlastní uživatelská data do identity v projektu ASP.NET Core. Smazat data podle GDPR.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501233"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Přidání, stažení a odstranění vlastních uživatelských dat do identity v projektu ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento článek ukazuje, jak:

* Přidejte vlastní uživatelská data do webové aplikace ASP.NET Core.
* Označte vlastní uživatelský <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> datový model atributem tak, aby byl automaticky k dispozici ke stažení a odstranění. Zpřístupnění dat ke stažení a smazání pomáhá splnit požadavky [GDPR.](xref:security/gdpr)

Ukázka projektu se vytvoří z webové aplikace Razor Pages, ale pokyny jsou podobné pro ASP.NET webové aplikace Core MVC.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Vytvoření webové aplikace Razor

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**. Název projektu **WebApp1,** pokud chcete, aby odpovídaly oboru názvů [ke stažení ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kód.
* Vybrat **ASP.NET základní webovou aplikaci** > **OK**
* V rozevíracím souboru vyberte **ASP.NET jádrem 3.0.**
* Vybrat **webovou aplikaci** > **ok**
* Sestavení a spuštění projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**. Název projektu **WebApp1,** pokud chcete, aby odpovídaly oboru názvů [ke stažení ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kód.
* Vybrat **ASP.NET základní webovou aplikaci** > **OK**
* V rozevíracím souboru vyberte **ASP.NET Core 2.2.**
* Vybrat **webovou aplikaci** > **ok**
* Sestavení a spuštění projektu.

::: moniker-end


# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Spuštění složky scaffolder identity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* V **Průzkumníku řešení**klepněte pravým tlačítkem myši na projekt > **Přidat** > **novou položku scaffolded item**.
* V levém podokně dialogového okna **Přidat poslíšovací systém** vyberte **Přidat identitu** > **.**
* V dialogovém okně Přidat identitu následující **volby:**
  * Vyberte existující soubor rozložení *~/Pages/Shared/_Layout.cshtml*
  * Vyberte následující soubory, které chcete přepsat:
    * **Účet/registr**
    * **Účet/Správa/Index**
  * Vyberte **+** tlačítko pro vytvoření nové **třídy kontextu Dat**. Přijměte typ (**WebApp1.Models.WebApp1Context,** pokud projekt s názvem **WebApp1**).
  * Vyberte **+** tlačítko pro vytvoření nové **třídy User**. Přijměte typ (**WebApp1User,** pokud projekt s názvem **WebApp1**) > **Přidat**.
* Vyberte **Přidat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste dříve nenainstalovali ASP.NET složky core, nainstalujte jej nyní:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček [microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (.csproj). V adresáři projektu spusťte následující příkaz:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spuštěním následujícího příkazu zobrazíte seznam možností složky identity:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spusťte složku scaffolder identity:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postupujte podle pokynů v [migraci, UseAuthentication a rozložení](xref:security/authentication/scaffold-identity#efm) provést následující kroky:

* Vytvořte migraci a aktualizujte databázi.
* Add `UseAuthentication` to `Startup.Configure`.
* Přidat `<partial name="_LoginPartial" />` do souboru rozložení.
* Otestujte aplikaci:
  * Registrace uživatele
  * Vyberte nové uživatelské jméno (vedle odkazu **Odhlásit** se). Možná budete muset rozbalit okno nebo vybrat ikonu navigačního panelu, aby se zobrazilo uživatelské jméno a další odkazy.
  * Vyberte kartu **Osobní údaje.**
  * Vyberte tlačítko **Stáhnout** a prohlédněte si soubor *PersonalData.json.*
  * Otestujte tlačítko **Odstranit,** které odstraní přihlášeného uživatele.

## <a name="add-custom-user-data-to-the-identity-db"></a>Přidání vlastních uživatelských dat do databáze identity

Aktualizujte `IdentityUser` odvozenou třídu vlastními vlastnostmi. Pokud jste projekt pojmenovali WebApp1, soubor se jmenuje *Oblasti/Identita/Data/WebApp1User.cs*. Aktualizujte soubor pomocí následujícího kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Vlastnosti s atributem [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:

* Odstraněno při volání *stránek/identity/stránek/účtu/správy/odstranění osobního data.cshtml* `UserManager.Delete`.
* Zahrnuto do stažených dat *na stránce Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizace stránky Účet/Správa/Index.cshtml

Aktualizujte `InputModel` v *oblastech/Identita/Stránky/Účet/Spravovat/Index.cshtml.cs* s následujícím zvýrazněným kódem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aktualizujte *oblasti/Identity/Stránky/Účet/Spravovat/Index.cshtml* pomocí následujících zvýrazněných značek:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualizujte *oblasti/Identity/Stránky/Účet/Spravovat/Index.cshtml* pomocí následujících zvýrazněných značek:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualizace stránky Účet/Register.cshtml

Aktualizujte `InputModel` v *oblastech/Identity/Pages/Account/Register.cshtml.cs* následujícím zvýrazněným kódem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aktualizujte *oblasti/Identity/Stránky/Účet/Register.cshtml* pomocí následujících zvýrazněných značek:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualizujte *oblasti/Identity/Stránky/Účet/Register.cshtml* pomocí následujících zvýrazněných značek:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Sestavte projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Přidání migrace pro vlastní uživatelská data

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V konzole **Správce balíčků**sady Visual Studio :

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testování vytváření, zobrazení, stahování, odstraňování vlastních uživatelských dat

Otestujte aplikaci:

* Zaregistrujte nového uživatele.
* Zobrazení vlastních uživatelských `/Identity/Account/Manage` dat na stránce.
* Stáhněte si a prohlédněte `/Identity/Account/Manage/PersonalData` si osobní údaje uživatelů ze stránky.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Přidání deklarací identity pomocí aplikace IUserClaimsPrincipalFactory<ApplicationUser>

Další deklarace identity lze přidat do `IUserClaimsPrincipalFactory<T>` ASP.NET základní identity pomocí rozhraní. Tuto třídu lze přidat do `Startup.ConfigureServices` aplikace v metodě. Přidejte vlastní implementaci třídy takto:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Demo kód používá `ApplicationUser` třídu. Tato třída `IsAdmin` přidá vlastnost, která se používá k přidání další deklarace.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

Implementuje `AdditionalUserClaimsPrincipalFactory` `UserClaimsPrincipalFactory` rozhraní. Do souboru . je `ClaimsPrincipal`přidána nová deklarace role.

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Další deklarace pak může být použita v aplikaci. Na stránce Razor `IAuthorizationService` lze instanci použít pro přístup k hodnotě deklarace.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
