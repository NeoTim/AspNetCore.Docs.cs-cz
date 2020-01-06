---
title: Identita uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak generovat identitu uživatelského rozhraní v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 2432d346d9678157848a38fa01d9057cdd7503ff
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75356266"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="f8367-103">Identita uživatelského rozhraní v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="f8367-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="f8367-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f8367-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8367-105">ASP.NET Core poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f8367-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f8367-106">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="f8367-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="f8367-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="f8367-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="f8367-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="f8367-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="f8367-109">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="f8367-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="f8367-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="f8367-111">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="f8367-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="f8367-112">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="f8367-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="f8367-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="f8367-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="f8367-114">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="f8367-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="f8367-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="f8367-116">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="f8367-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="f8367-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="f8367-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8367-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="f8367-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="f8367-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="f8367-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="f8367-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="f8367-121">Tento dokument obsahuje podrobnější pokyny, než je soubor *ScaffoldingReadme. txt* , který je generován při spuštění nástroje pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8367-121">This document contains more complete instructions than the *ScaffoldingReadme.txt* file which is generated when running the the scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="f8367-122">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="f8367-122">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="f8367-123">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="f8367-123">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="f8367-124">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="f8367-124">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="f8367-125">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-125">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-126">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8367-126">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="f8367-127">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="f8367-127">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="f8367-128">Povolení ověřování</span><span class="sxs-lookup"><span data-stu-id="f8367-128">Enable authentication</span></span>

<span data-ttu-id="f8367-129">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="f8367-129">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="f8367-130">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="f8367-130">Layout changes</span></span>

<span data-ttu-id="f8367-131">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="f8367-131">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="f8367-132">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="f8367-132">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="f8367-133">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-133">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-134">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8367-134">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="f8367-135">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="f8367-135">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="f8367-136">Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="f8367-136">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="f8367-137">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8367-137">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="f8367-138">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-138">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-139">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="f8367-139">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="f8367-140">Aktualizujte třídu `Startup` podobným kódem jako následující:</span><span class="sxs-lookup"><span data-stu-id="f8367-140">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="f8367-141">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="f8367-141">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="f8367-142">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="f8367-142">Create full identity UI source</span></span>

<span data-ttu-id="f8367-143">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="f8367-143">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="f8367-144">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f8367-144">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="f8367-145">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-145">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="f8367-146">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f8367-146">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="f8367-147">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="f8367-147">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="f8367-148">Zaregistrujte implementaci `IEmailSender`, například:</span><span class="sxs-lookup"><span data-stu-id="f8367-148">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="f8367-149">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="f8367-149">Disable register page</span></span>

<span data-ttu-id="f8367-150">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="f8367-150">To disable user registration:</span></span>

* <span data-ttu-id="f8367-151">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8367-151">Scaffold Identity.</span></span> <span data-ttu-id="f8367-152">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="f8367-152">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="f8367-153">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f8367-153">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="f8367-154">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="f8367-154">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="f8367-155">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="f8367-155">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="f8367-156">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="f8367-156">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="f8367-157">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="f8367-157">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="f8367-158">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="f8367-158">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="f8367-159">Odeberte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="f8367-159">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="f8367-160">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="f8367-160">Use another app to add users</span></span>

<span data-ttu-id="f8367-161">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8367-161">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="f8367-162">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="f8367-162">Options to add users include:</span></span>

* <span data-ttu-id="f8367-163">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="f8367-163">A dedicated admin web app.</span></span>
* <span data-ttu-id="f8367-164">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8367-164">A console app.</span></span>

<span data-ttu-id="f8367-165">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="f8367-165">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="f8367-166">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="f8367-166">A list of users is read into memory.</span></span>
* <span data-ttu-id="f8367-167">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="f8367-167">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="f8367-168">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-168">The user is added to the Identity database.</span></span>
* <span data-ttu-id="f8367-169">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="f8367-169">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="f8367-170">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="f8367-170">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="f8367-171">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="f8367-171">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8367-172">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f8367-172">Additional resources</span></span>

* [<span data-ttu-id="f8367-173">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="f8367-173">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8367-174">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="f8367-174">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f8367-175">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="f8367-175">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="f8367-176">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="f8367-176">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="f8367-177">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="f8367-177">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="f8367-178">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-178">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="f8367-179">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="f8367-179">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="f8367-180">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="f8367-180">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="f8367-181">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="f8367-181">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="f8367-182">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="f8367-182">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="f8367-183">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-183">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="f8367-184">Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="f8367-184">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="f8367-185">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-185">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="f8367-186">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="f8367-186">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="f8367-187">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="f8367-187">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="f8367-188">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-188">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="f8367-189">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="f8367-189">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="f8367-190">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8367-190">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="f8367-191">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="f8367-191">Services to enable these features must be added manually.</span></span> <span data-ttu-id="f8367-192">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="f8367-192">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="f8367-193">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="f8367-193">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="f8367-194">Přidejte následující zvýrazněná volání do třídy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f8367-194">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="f8367-195">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="f8367-195">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="f8367-196">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-196">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-197">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8367-197">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="f8367-198">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="f8367-198">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="f8367-199">Povolení ověřování</span><span class="sxs-lookup"><span data-stu-id="f8367-199">Enable authentication</span></span>

<span data-ttu-id="f8367-200">V metodě `Configure` třídy `Startup` volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="f8367-200">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="f8367-201">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="f8367-201">Layout changes</span></span>

<span data-ttu-id="f8367-202">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="f8367-202">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="f8367-203">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="f8367-203">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="f8367-204">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-204">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-205">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f8367-205">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="f8367-206">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="f8367-206">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="f8367-207">Volitelné: do souboru *views/Shared/_Layout. cshtml* Přidejte částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="f8367-207">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="f8367-208">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8367-208">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="f8367-209">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="f8367-209">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f8367-210">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="f8367-210">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="f8367-211">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="f8367-211">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="f8367-212">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="f8367-212">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="f8367-213">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="f8367-213">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="f8367-214">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="f8367-214">Create full identity UI source</span></span>

<span data-ttu-id="f8367-215">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="f8367-215">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="f8367-216">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="f8367-216">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="f8367-217">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-217">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="f8367-218">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="f8367-218">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="f8367-219">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="f8367-219">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="f8367-220">Zaregistrujte implementaci `IEmailSender`, například:</span><span class="sxs-lookup"><span data-stu-id="f8367-220">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="f8367-221">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="f8367-221">Disable register page</span></span>

<span data-ttu-id="f8367-222">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="f8367-222">To disable user registration:</span></span>

* <span data-ttu-id="f8367-223">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f8367-223">Scaffold Identity.</span></span> <span data-ttu-id="f8367-224">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="f8367-224">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="f8367-225">Příklad:</span><span class="sxs-lookup"><span data-stu-id="f8367-225">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="f8367-226">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="f8367-226">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="f8367-227">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="f8367-227">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="f8367-228">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="f8367-228">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="f8367-229">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="f8367-229">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="f8367-230">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="f8367-230">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="f8367-231">Odeberte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="f8367-231">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="f8367-232">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="f8367-232">Use another app to add users</span></span>

<span data-ttu-id="f8367-233">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f8367-233">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="f8367-234">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="f8367-234">Options to add users include:</span></span>

* <span data-ttu-id="f8367-235">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="f8367-235">A dedicated admin web app.</span></span>
* <span data-ttu-id="f8367-236">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="f8367-236">A console app.</span></span>

<span data-ttu-id="f8367-237">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="f8367-237">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="f8367-238">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="f8367-238">A list of users is read into memory.</span></span>
* <span data-ttu-id="f8367-239">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="f8367-239">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="f8367-240">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="f8367-240">The user is added to the Identity database.</span></span>
* <span data-ttu-id="f8367-241">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="f8367-241">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="f8367-242">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="f8367-242">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="f8367-243">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="f8367-243">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f8367-244">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f8367-244">Additional resources</span></span>

* [<span data-ttu-id="f8367-245">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="f8367-245">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end