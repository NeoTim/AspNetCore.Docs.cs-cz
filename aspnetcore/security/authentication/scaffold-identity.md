---
title: Identita uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak generovat identitu uživatelského rozhraní v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972035"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identita uživatelského rozhraní v ASP.NET Corech projektech

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v RCL identity. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL. Máte možnost vybrat kód identity, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit. Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.

Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní. Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

Tento dokument obsahuje podrobnější pokyny než soubor *ScaffoldingReadme. txt* , který je generován při spuštění lešení.

## <a name="scaffold-identity-into-an-empty-project"></a>Identita uživatelského rozhraní do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aktualizujte třídu `Startup` podobným kódem jako následující:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu Razor bez existující autorizace

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

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

Aktualizujte třídu `Startup` podobným kódem jako následující:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identita uživatelského rozhraní do projektu Razor s autorizací

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
Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu MVC bez existující autorizace

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

Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aktualizujte třídu `Startup` podobným kódem jako následující:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identita uživatelského rozhraní do projektu MVC s autorizací

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Vytvořit úplný zdroj uživatelského rozhraní identity

Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1. To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Výchozí identita je nahrazena následujícím kódem:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zaregistrujte implementaci `IEmailSender`, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Zakázat stránku registrace

Zakázání registrace uživatele:

* Identita uživatelského rozhraní. Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Příklad:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odeberte potvrzovací kód z `PageModel`:

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
* Uživatel se přidá do databáze identity.
* Uživatel obdrží oznámení a sdělí mu změnu hesla.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Následující osnovy kódu přidávají uživatele:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobný přístup je možné vyřídit v produkčních scénářích.

## <a name="prevent-publish-of-static-identity-assets"></a>Zabránit publikování prostředků statické identity

Pokud chcete zabránit publikování prostředků statických identit do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class). Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v RCL identity. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL. Máte možnost vybrat kód identity, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil. Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.

Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* . Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity. Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.

> [!NOTE]
> Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní. Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identita uživatelského rozhraní do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Přidejte následující zvýrazněná volání do třídy `Startup`:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu Razor bez existující autorizace

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

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

V metodě `Configure` třídy `Startup` volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identita uživatelského rozhraní do projektu Razor s autorizací

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
Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Identita uživatelského rozhraní do projektu MVC bez existující autorizace

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

Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Identita uživatelského rozhraní do projektu MVC s autorizací

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

## <a name="create-full-identity-ui-source"></a>Vytvořit úplný zdroj uživatelského rozhraní identity

Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.

Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1. To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Výchozí identita je nahrazena následujícím kódem:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Zaregistrujte implementaci `IEmailSender`, například:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>Zakázat stránku registrace

Zakázání registrace uživatele:

* Identita uživatelského rozhraní. Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Příklad:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odeberte potvrzovací kód z `PageModel`:

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
* Uživatel se přidá do databáze identity.
* Uživatel obdrží oznámení a sdělí mu změnu hesla.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Následující osnovy kódu přidávají uživatele:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Podobný přístup je možné vyřídit v produkčních scénářích.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end