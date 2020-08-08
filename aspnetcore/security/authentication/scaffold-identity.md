---
title: Generování uživatelského rozhraní Identity v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak získat Identity v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 4404a5513d9dc989e50c904f3e7863de59991939
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022326"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>Generování uživatelského rozhraní Identity v ASP.NET Corech projektech

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class). Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v Identity RCL. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného Identity zdroje uživatelského rozhraní](#full).

Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity . Máte možnost vybrat Identity kód, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit. Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.

Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity . Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity . Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

Při generování uživatelského rozhraní Identity s novým kontextem dat do projektu se stávajícími jednotlivými účty:

* V nástroji `Startup.ConfigureServices` odeberte volání na:
  * `AddDbContext`
  * `AddDefaultIdentity`

Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Předchozí kód odhlásí kód, který je duplikován v *oblasti/ Identity / Identity HostingStartup.cs*

Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Uživatelské rozhraní Identity do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Generování uživatelského rozhraní Identity do Razor projektu s autorizací

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Generování uživatelského rozhraní Identity do projektu MVC s autorizací

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Generování uživatelského rozhraní Identity do Blazor Server projektu bez existující autorizace

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Migrace

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Předání tokenu XSRF do aplikace

Tokeny lze předat součástem:

* Při zřizování ověřovacích tokenů a jejich uložení do ověřování je cookie možné je předat součástem.
* Razorkomponenty nemůžou používat `HttpContext` přímo, takže neexistuje žádný způsob, jak získat [token pro padělání žádostí](xref:security/anti-request-forgery) o odeslání na Identity koncový bod odhlašovacího objektu na adrese `/Identity/Account/Logout` . Token XSRF lze předat komponentám.

Další informace naleznete v tématu <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

V souboru *Pages/_Host. cshtml* vytvořte token po jeho přidání do `InitialApplicationState` `TokenProvider` tříd a:

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Aktualizujte `App` komponentu (*App. Razor*), abyste přiřadili `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

Služba, která `TokenProvider` je znázorněna v tématu, se používá v `LoginDisplay` části v následujícím oddílu [rozložení a změny toku ověřování](#layout-and-authentication-flow-changes) .

### <a name="enable-authentication"></a>Povolit ověřování

Ve `Startup` třídě:

* Potvrďte, že Razor jsou do služby přidány stránky `Startup.ConfigureServices` .
* Pokud používáte [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), zaregistrujte službu.
* Zavolejte `UseDatabaseErrorPage` na tvůrce aplikací v nástroji `Startup.Configure` pro vývojové prostředí.
* Volání `UseAuthentication` a `UseAuthorization` After `UseRouting` .
* Přidejte koncový bod pro Razor stránky.

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Změny toku rozložení a ověřování

Přidejte `RedirectToLogin` komponentu (*RedirectToLogin. Razor*) do *sdílené* složky aplikace v kořenu projektu:

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

Přidejte `LoginDisplay` součást (*LoginDisplay. Razor*) do *sdílené* složky aplikace. [Služba TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) poskytuje token XSRF pro formulář HTML, který odesílá do Identity koncového bodu pro odhlášení:

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

Do `MainLayout` komponenty (*Shared/MainLayout. Razor*) přidejte `LoginDisplay` komponentu do obsahu elementu horního řádku `<div>` :

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Koncové body ověřování stylu

Vzhledem k tomu Blazor Server Razor , že nástroj používá stránky stránky Identity , styl uživatelského rozhraní se změní, když návštěvník prochází mezi Identity stránkami a komponentami. Máte dvě možnosti, jak adresovat incongruous styly:

#### <a name="build-no-locidentity-components"></a>IdentityKomponenty sestavení

Přístup k používání komponent Identity místo stránek je vytvoření Identity komponent. Vzhledem `SignInManager` `UserManager` k tomu, že součásti nejsou podporovány v Razor součástech, použijte koncové body rozhraní API v Blazor Server aplikaci ke zpracování akcí uživatelských účtů.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Použití vlastního rozložení pomocí Blazor stylů aplikací

IdentityRozložení a styly stránek lze upravit tak, aby vytvořily stránky, které používají výchozí Blazor motiv.

> [!NOTE]
> Příklad v této části je pouze výchozím bodem pro přizpůsobení. Pro nejlepší uživatelské prostředí se nejspíš vyžaduje další práce.

Vytvoří novou `NavMenu_IdentityLayout` součást (*Shared/NavMenu_ Identity layout. Razor*). Pro značky a kód součásti použijte stejný obsah `NavMenu` součásti aplikace (*Shared/NavMenu. Razor*). Vydejte všechny `NavLink` součásti, které se nedají získat anonymně, protože automatické přesměrování v `RedirectToLogin` součásti selžou pro součásti, které vyžadují ověřování nebo autorizaci.

V souboru *Pages/Shared/layout. cshtml* proveďte následující změny:

* Přidejte Razor do horní části souboru direktivy pro použití pomocníků značek a součástí aplikace ve *sdílené* složce:

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  Nahraďte `{APPLICATION ASSEMBLY}` názvem sestavení aplikace.

* Přidat `<base>` značku a Blazor šablonu stylů `<link>` k `<head>` obsahu:

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* Změňte obsah `<body>` značky na následující:

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Generování uživatelského rozhraní Identity do Blazor Server projektu s autorizací

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Vytvořit úplný Identity zdroj uživatelského rozhraní

Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1. To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Výchozí hodnota Identity je nahrazena následujícím kódem:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zaregistrujte `IEmailSender` implementaci, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Konfigurace hesla

Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity . `InputModel``Password`vlastnosti se nacházejí v následujících souborech:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Zakázat stránku

V této části se dozvíte, jak zakázat stránku registrace, ale přístup můžete použít k zakázání jakékoli stránky.

Zakázání registrace uživatele:

* Generování uživatelského rozhraní Identity . Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Například:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odstraňte potvrzovací kód z `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Přidání uživatelů pomocí jiné aplikace

Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci. Mezi možnosti přidávání uživatelů patří:

* Vyhrazená webová aplikace pro správu.
* Konzolová aplikace.

Následující kód popisuje jeden přístup k přidávání uživatelů:

* Seznam uživatelů je čten do paměti.
* Pro každého uživatele je vygenerováno silné jedinečné heslo.
* Uživatel se přidá do Identity databáze.
* Uživatel obdrží oznámení a sdělí mu změnu hesla.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Následující osnovy kódu přidávají uživatele:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobný přístup je možné vyřídit v produkčních scénářích.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Zabránit publikování statických Identity prostředků

Chcete-li zabránit publikování statických Identity prostředků do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .

## <a name="additional-resources"></a>Další zdroje

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class). Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v Identity RCL. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity . Máte možnost vybrat Identity kód, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil. Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.

Když se Identity spustí generátor, vytvoří se soubor *ScaffoldingReadme.txt* v adresáři projektu. Soubor *ScaffoldingReadme.txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní. Tento dokument obsahuje ucelené pokyny, než *ScaffoldingReadme.txt* soubor.

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.

> [!NOTE]
> Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity . Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity . Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>Uživatelské rozhraní Identity do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Do třídy přidejte následující zvýrazněná volání `Startup` :

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Generování uživatelského rozhraní Identity do Razor projektu s autorizací

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>Generování uživatelského rozhraní Identity do projektu MVC s autorizací

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Vytvořit úplný Identity zdroj uživatelského rozhraní

Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1. To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Výchozí hodnota Identity je nahrazena následujícím kódem:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zaregistrujte `IEmailSender` implementaci, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Konfigurace hesla

Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity . `InputModel``Password`vlastnosti se nacházejí v následujících souborech:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Zakázat stránku registrace

Zakázání registrace uživatele:

* Generování uživatelského rozhraní Identity . Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Například:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odstraňte potvrzovací kód z `PageModel` :

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>Přidání uživatelů pomocí jiné aplikace

Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci. Mezi možnosti přidávání uživatelů patří:

* Vyhrazená webová aplikace pro správu.
* Konzolová aplikace.

Následující kód popisuje jeden přístup k přidávání uživatelů:

* Seznam uživatelů je čten do paměti.
* Pro každého uživatele je vygenerováno silné jedinečné heslo.
* Uživatel se přidá do Identity databáze.
* Uživatel obdrží oznámení a sdělí mu změnu hesla.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Následující osnovy kódu přidávají uživatele:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobný přístup je možné vyřídit v produkčních scénářích.

## <a name="additional-resources"></a>Další zdroje

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
