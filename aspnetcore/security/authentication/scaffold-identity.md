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
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="19b07-103">Identita uživatelského rozhraní v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="19b07-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="19b07-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="19b07-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="19b07-105">ASP.NET Core poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="19b07-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="19b07-106">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="19b07-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="19b07-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="19b07-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="19b07-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="19b07-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="19b07-109">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="19b07-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="19b07-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="19b07-111">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="19b07-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="19b07-112">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="19b07-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="19b07-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="19b07-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="19b07-114">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="19b07-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="19b07-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="19b07-116">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="19b07-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="19b07-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="19b07-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="19b07-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="19b07-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="19b07-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="19b07-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="19b07-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="19b07-121">Tento dokument obsahuje podrobnější pokyny než soubor *ScaffoldingReadme. txt* , který je generován při spuštění lešení.</span><span class="sxs-lookup"><span data-stu-id="19b07-121">This document contains more complete instructions than the *ScaffoldingReadme.txt* file which is generated when running the scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="19b07-122">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="19b07-122">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="19b07-123">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="19b07-123">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="19b07-124">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="19b07-124">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="19b07-125">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-125">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-126">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="19b07-126">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="19b07-127">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="19b07-127">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="19b07-128">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="19b07-128">Enable authentication</span></span>

<span data-ttu-id="19b07-129">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="19b07-129">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="19b07-130">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="19b07-130">Layout changes</span></span>

<span data-ttu-id="19b07-131">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="19b07-131">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="19b07-132">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="19b07-132">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="19b07-133">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-133">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-134">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="19b07-134">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="19b07-135">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="19b07-135">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="19b07-136">Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="19b07-136">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="19b07-137">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="19b07-137">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="19b07-138">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-138">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-139">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="19b07-139">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="19b07-140">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="19b07-140">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="19b07-141">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="19b07-141">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="19b07-142">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="19b07-142">Create full identity UI source</span></span>

<span data-ttu-id="19b07-143">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="19b07-143">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="19b07-144">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="19b07-144">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="19b07-145">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-145">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="19b07-146">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="19b07-146">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="19b07-147">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="19b07-147">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="19b07-148">Zaregistrujte implementaci `IEmailSender`, například:</span><span class="sxs-lookup"><span data-stu-id="19b07-148">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="19b07-149">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="19b07-149">Disable register page</span></span>

<span data-ttu-id="19b07-150">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="19b07-150">To disable user registration:</span></span>

* <span data-ttu-id="19b07-151">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="19b07-151">Scaffold Identity.</span></span> <span data-ttu-id="19b07-152">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="19b07-152">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="19b07-153">Příklad:</span><span class="sxs-lookup"><span data-stu-id="19b07-153">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="19b07-154">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="19b07-154">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="19b07-155">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="19b07-155">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="19b07-156">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="19b07-156">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="19b07-157">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="19b07-157">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="19b07-158">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="19b07-158">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="19b07-159">Odeberte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="19b07-159">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="19b07-160">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="19b07-160">Use another app to add users</span></span>

<span data-ttu-id="19b07-161">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19b07-161">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="19b07-162">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="19b07-162">Options to add users include:</span></span>

* <span data-ttu-id="19b07-163">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="19b07-163">A dedicated admin web app.</span></span>
* <span data-ttu-id="19b07-164">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="19b07-164">A console app.</span></span>

<span data-ttu-id="19b07-165">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="19b07-165">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="19b07-166">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="19b07-166">A list of users is read into memory.</span></span>
* <span data-ttu-id="19b07-167">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="19b07-167">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="19b07-168">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-168">The user is added to the Identity database.</span></span>
* <span data-ttu-id="19b07-169">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="19b07-169">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="19b07-170">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="19b07-170">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="19b07-171">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="19b07-171">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="19b07-172">Zabránit publikování prostředků statické identity</span><span class="sxs-lookup"><span data-stu-id="19b07-172">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="19b07-173">Pokud chcete zabránit publikování prostředků statických identit do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="19b07-173">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19b07-174">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="19b07-174">Additional resources</span></span>

* [<span data-ttu-id="19b07-175">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="19b07-175">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="19b07-176">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="19b07-176">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="19b07-177">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="19b07-177">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="19b07-178">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="19b07-178">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="19b07-179">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="19b07-179">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="19b07-180">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-180">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="19b07-181">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="19b07-181">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="19b07-182">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="19b07-182">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="19b07-183">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="19b07-183">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="19b07-184">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="19b07-184">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="19b07-185">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-185">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="19b07-186">Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="19b07-186">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="19b07-187">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-187">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="19b07-188">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="19b07-188">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="19b07-189">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="19b07-189">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="19b07-190">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-190">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="19b07-191">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="19b07-191">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="19b07-192">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="19b07-192">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="19b07-193">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="19b07-193">Services to enable these features must be added manually.</span></span> <span data-ttu-id="19b07-194">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="19b07-194">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="19b07-195">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="19b07-195">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="19b07-196">Přidejte následující zvýrazněná volání do třídy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="19b07-196">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="19b07-197">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="19b07-197">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="19b07-198">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-198">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-199">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="19b07-199">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="19b07-200">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="19b07-200">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="19b07-201">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="19b07-201">Enable authentication</span></span>

<span data-ttu-id="19b07-202">V metodě `Configure` třídy `Startup` volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="19b07-202">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="19b07-203">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="19b07-203">Layout changes</span></span>

<span data-ttu-id="19b07-204">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="19b07-204">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="19b07-205">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="19b07-205">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="19b07-206">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-206">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-207">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="19b07-207">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="19b07-208">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="19b07-208">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="19b07-209">Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="19b07-209">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="19b07-210">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="19b07-210">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="19b07-211">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="19b07-211">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="19b07-212">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="19b07-212">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="19b07-213">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="19b07-213">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="19b07-214">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="19b07-214">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="19b07-215">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="19b07-215">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="19b07-216">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="19b07-216">Create full identity UI source</span></span>

<span data-ttu-id="19b07-217">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="19b07-217">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="19b07-218">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="19b07-218">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="19b07-219">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-219">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="19b07-220">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="19b07-220">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="19b07-221">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="19b07-221">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="19b07-222">Zaregistrujte implementaci `IEmailSender`, například:</span><span class="sxs-lookup"><span data-stu-id="19b07-222">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="19b07-223">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="19b07-223">Disable register page</span></span>

<span data-ttu-id="19b07-224">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="19b07-224">To disable user registration:</span></span>

* <span data-ttu-id="19b07-225">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="19b07-225">Scaffold Identity.</span></span> <span data-ttu-id="19b07-226">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="19b07-226">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="19b07-227">Příklad:</span><span class="sxs-lookup"><span data-stu-id="19b07-227">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="19b07-228">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="19b07-228">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="19b07-229">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="19b07-229">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="19b07-230">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="19b07-230">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="19b07-231">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="19b07-231">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="19b07-232">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="19b07-232">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="19b07-233">Odeberte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="19b07-233">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="19b07-234">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="19b07-234">Use another app to add users</span></span>

<span data-ttu-id="19b07-235">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="19b07-235">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="19b07-236">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="19b07-236">Options to add users include:</span></span>

* <span data-ttu-id="19b07-237">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="19b07-237">A dedicated admin web app.</span></span>
* <span data-ttu-id="19b07-238">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="19b07-238">A console app.</span></span>

<span data-ttu-id="19b07-239">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="19b07-239">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="19b07-240">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="19b07-240">A list of users is read into memory.</span></span>
* <span data-ttu-id="19b07-241">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="19b07-241">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="19b07-242">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="19b07-242">The user is added to the Identity database.</span></span>
* <span data-ttu-id="19b07-243">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="19b07-243">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="19b07-244">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="19b07-244">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="19b07-245">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="19b07-245">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19b07-246">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="19b07-246">Additional resources</span></span>

* [<span data-ttu-id="19b07-247">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="19b07-247">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end