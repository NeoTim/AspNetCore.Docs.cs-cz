---
title: Přidání, stažení a odstranění uživatelských dat Identity v ASP.NET Core projektu
author: rick-anderson
description: Naučte se, jak do projektu ASP.NET Core Identity přidat vlastní uživatelská data. Odstranit data na GDPR
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777329"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Přidání, stažení a odstranění vlastních uživatelských dat do identity v ASP.NET Core projektu

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto článku se dozvíte, jak:

* Přidejte vlastní uživatelská data do ASP.NET Core webové aplikace.
* Označte vlastní uživatelský datový model s <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atributem tak, aby byl automaticky dostupný ke stažení a odstranění. Zpřístupnění dat, které je možné stáhnout a odstranit, pomůže splnit [GDPR](xref:security/gdpr) požadavky.

Ukázka projektu je vytvořena z Razor Pages webové aplikace, ale pokyny jsou podobné pro webovou aplikaci ASP.NET Core MVC.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Jak stáhnout](xref:index#how-to-download-a-sample))

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

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**. Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Vyberte **ASP.NET Core webové aplikace** > **OK** .
* V rozevíracím seznamu vyberte **ASP.NET Core 3,0** .
* Vybrat **webovou aplikaci** > v **pořádku**
* Sestavte a spusťte projekt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**. Pojmenujte projekt **WebApp1** , pokud chcete, aby odpovídal oboru názvů [ukázkového kódu ke stažení](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Vyberte **ASP.NET Core webové aplikace** > **OK** .
* V rozevíracím seznamu vyberte **ASP.NET Core 2,2** .
* Vybrat **webovou aplikaci** > v **pořádku**
* Sestavte a spusťte projekt.

::: moniker-end


# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Spustit generování uživatelského rozhraní identity

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Z **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt > **Přidat** > **novou vygenerované položky**.
* V levém podokně dialogového okna **Přidat generování uživatelského rozhraní** vyberte **Identita** > **Přidat**.
* V dialogovém okně **Přidat identitu** tyto možnosti:
  * Vyberte existující soubor rozložení *~/Pages/Shared/_Layout. cshtml.*
  * Vyberte následující soubory, které chcete přepsat:
    * **Účet/registr**
    * **Účet/Správa/index**
  * Vyberte **+** tlačítko pro vytvoření nové **třídy datového kontextu**. Přijměte typ (**WebApp1. Models. WebApp1Context** , pokud se projekt jmenuje **WebApp1**).
  * Výběrem **+** tlačítka vytvoříte novou **třídu uživatelů**. Přijměte typ (**WebApp1User** , pokud se projekt jmenuje **WebApp1**) > **Přidat**.
* Vyberte **Přidat**.

# <a name="net-core-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Pokud jste předtím ASP.NET Core lešení nenainstalovali, nainstalujte ho hned takto:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Přidejte odkaz na balíček do [Microsoft. VisualStudio. Web. strategii. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do souboru projektu (. csproj). V adresáři projektu spusťte následující příkaz:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Spuštěním následujícího příkazu zobrazíte seznam možností generování identit:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Ve složce projektu spusťte generování uživatelského rozhraní identity:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postupujte podle pokynů v části [migrace, UseAuthentication a rozložení a](xref:security/authentication/scaffold-identity#efm) proveďte následující kroky:

* Vytvořte migraci a aktualizujte databázi.
* Add `UseAuthentication` to `Startup.Configure`.
* Přidejte `<partial name="_LoginPartial" />` do souboru rozložení.
* Otestujte aplikaci:
  * Registrace uživatele
  * Vyberte nové uživatelské jméno (vedle odkazu pro **odhlášení** ). Možná budete muset rozbalit okno nebo vybrat ikonu navigačního panelu a zobrazit uživatelské jméno a další odkazy.
  * Vyberte kartu **osobní data** .
  * Vyberte tlačítko **Download (stáhnout** ) a prověřit soubor *personaldata. JSON* .
  * Otestujte tlačítko **Odstranit** , které odstraní přihlášeného uživatele.

## <a name="add-custom-user-data-to-the-identity-db"></a>Přidat vlastní uživatelská data do databáze identity

Aktualizujte `IdentityUser` odvozenou třídu vlastními vlastnostmi. Pokud jste jmenovali projekt WebApp1, soubor má název *areas/identity/data/WebApp1User. cs*. Aktualizujte soubor pomocí následujícího kódu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Vlastnosti s atributem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) jsou:

* Odstraní se při volání `UserManager.Delete`stránky *oblasti/identita, stránky/účet/Správa/DeletePersonalData. cshtml* Razor.
* Obsahuje stažená data na stránce *oblasti/identita/stránky/účet/Správa/DownloadPersonalData. cshtml* Razor.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizuje stránku účet/Správa/index. cshtml.

Aktualizujte `InputModel` v části *oblasti/identita/stránky/účet/Správa/index. cshtml. cs* následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Aktualizujte *oblasti/identita/stránky/účet/Správa/index. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualizace stránky account/Register. cshtml

Aktualizujte `InputModel` v části *oblasti/identita/stránky/účet/Register. cshtml. cs* následující zvýrazněný kód:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Aktualizujte *oblasti/identita/stránky/účet/Register. cshtml* pomocí následujícího zvýrazněného označení:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Sestavte projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Přidání migrace pro vlastní uživatelská data

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V **konzole správce balíčků**sady Visual Studio:

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

## <a name="test-create-view-download-delete-custom-user-data"></a>Test vytvoření, zobrazení, stažení, odstranění vlastních uživatelských dat

Otestujte aplikaci:

* Zaregistrujte nového uživatele.
* Zobrazte si vlastní uživatelská data na `/Identity/Account/Manage` stránce.
* Stáhněte a zobrazte si osobní údaje uživatelů ze `/Identity/Account/Manage/PersonalData` stránky.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Přidání deklarací identity Identity pro použití IUserClaimsPrincipalFactory<ApplicationUser>

Další deklarace identity je možné přidat do Identity ASP.NET Core pomocí `IUserClaimsPrincipalFactory<T>` rozhraní. Tato třída se dá do aplikace přidat v `Startup.ConfigureServices` metodě. Přidejte vlastní implementaci třídy následujícím způsobem:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Ukázkový kód používá `ApplicationUser` třídu. Tato třída přidá `IsAdmin` vlastnost, která se používá k přidání dodatečné deklarace identity.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` Implementuje `UserClaimsPrincipalFactory` rozhraní. Do se `ClaimsPrincipal`přidá nová deklarace identity role.

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

Další deklaraci identity pak můžete v aplikaci použít. Na Razor stránce lze `IAuthorizationService` instanci použít pro přístup k hodnotě deklarace identity.

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
