---
title: Generování Identity uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, Identity jak získat v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768387"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Generování Identity uživatelského rozhraní v ASP.NET Corech projektech

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class). Aplikace, které Identity zahrnují, mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v Identity RCL. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL Identity použít generování uživatelského rozhraní. Máte možnost vybrat Identity kód, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit. Tento dokument popisuje kroky potřebné k dokončení aktualizace Identity generování uživatelského rozhraní.

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.

Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity. Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

Při generování uživatelského Identity rozhraní s novým kontextem dat do projektu se stávajícími jednotlivými účty:

* V `Startup.ConfigureServices`nástroji odeberte volání na:
  * `AddDbContext`
  * `AddDefaultIdentity`

Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Předchozí kód odhlásí kód, který je duplikován v *oblasti/Identity/IdentityHostingStartup.cs*

Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.

## <a name="scaffold-identity-into-an-empty-project"></a>Identita uživatelského rozhraní do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identita uživatelského rozhraní do Razor projektu bez existující autorizace

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

Identityje nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identita uživatelského rozhraní do Razor projektu s autorizací

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
Některé Identity možnosti jsou nakonfigurovány v *oblastiIdentity//IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

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

Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identityje nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu IHostingStartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:

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

Chcete-li zachovat úplnou kontrolu Identity nad uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.

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
## <a name="disable-register-page"></a>Zakázat stránku registrace

Zakázání registrace uživatele:

* Generování Identityuživatelského rozhraní. Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Příklad:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblastíIdentity//Pages/Account/Login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualizujte stránku *oblastiIdentity//Pages/Account/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odstraňte potvrzovací kód z `PageModel`:

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

## <a name="prevent-publish-of-static-identity-assets"></a>Zabránit publikování statických Identity prostředků

Chcete-li zabránit Identity publikování statických prostředků do kořenového <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>adresáře webu, přečtěte si téma.

## <a name="additional-resources"></a>Další zdroje

* [Změny ověřovacího kódu na ASP.NET Core 2,1 a novější](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class). Aplikace, které Identity zahrnují, mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL). Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování. Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci. Generovaný kód má přednost před stejným kódem v Identity RCL. Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).

Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL Identity použít generování uživatelského rozhraní. Máte možnost vybrat Identity kód, který se má vygenerovat.

I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil. Tento dokument popisuje kroky potřebné k dokončení aktualizace Identity generování uživatelského rozhraní.

Identity Když se spustí generátor, vytvoří se soubor *ScaffoldingReadme. txt* v adresáři projektu. Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní. Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .

Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn. Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.

> [!NOTE]
> Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity. Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity. Služby, které umožňují tyto funkce povolit, je nutné přidat ručně. Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Identita uživatelského rozhraní do prázdného projektu

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Do `Startup` třídy přidejte následující zvýrazněná volání:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Identita uživatelského rozhraní do Razor projektu bez existující autorizace

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

Identityje nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Migrace, UseAuthentication a rozložení

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Povolit ověřování

V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Změny rozložení

Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Identita uživatelského rozhraní do Razor projektu s autorizací

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
Některé Identity možnosti jsou nakonfigurovány v *oblastiIdentity//IdentityHostingStartup.cs*. Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).

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

Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*

Identityje nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*. Další informace najdete v tématu IHostingStartup.

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

Chcete-li zachovat úplnou kontrolu Identity nad uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.

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
## <a name="disable-register-page"></a>Zakázat stránku registrace

Zakázání registrace uživatele:

* Generování Identityuživatelského rozhraní. Přidejte účet. registr, Account. Login a account. RegisterConfirmation. Příklad:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* Odkomentujte nebo odeberte odkaz na registraci z *oblastíIdentity//Pages/Account/Login.cshtml*

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* Aktualizujte stránku *oblastiIdentity//Pages/Account/RegisterConfirmation* .

  * Odeberte kód a odkazy ze souboru CSHTML.
  * Odstraňte potvrzovací kód z `PageModel`:

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