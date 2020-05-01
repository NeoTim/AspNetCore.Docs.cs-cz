---
title: Identita uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak generovat identitu uživatelského rozhraní v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604724"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="33ef4-103">Identita uživatelského rozhraní v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="33ef4-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="33ef4-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33ef4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33ef4-105">ASP.NET Core poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="33ef4-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="33ef4-106">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="33ef4-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="33ef4-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="33ef4-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="33ef4-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="33ef4-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="33ef4-109">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="33ef4-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="33ef4-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="33ef4-111">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="33ef4-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="33ef4-112">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="33ef4-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="33ef4-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="33ef4-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="33ef4-114">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="33ef4-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="33ef4-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="33ef4-116">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="33ef4-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="33ef4-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="33ef4-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="33ef4-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="33ef4-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="33ef4-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="33ef4-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="33ef4-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="33ef4-121">Při generování identity s novým kontextem dat do projektu se stávajícími jednotlivými účty:</span><span class="sxs-lookup"><span data-stu-id="33ef4-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="33ef4-122">V `Startup.ConfigureServices`nástroji odeberte volání na:</span><span class="sxs-lookup"><span data-stu-id="33ef4-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="33ef4-123">Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="33ef4-124">Předchozí kód oddělí kód, který je duplikován v *oblasti/identity/IdentityHostingStartup. cs.*</span><span class="sxs-lookup"><span data-stu-id="33ef4-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="33ef4-125">Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.</span><span class="sxs-lookup"><span data-stu-id="33ef4-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="33ef4-126">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="33ef4-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="33ef4-127">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="33ef4-128">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="33ef4-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="33ef4-129">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-130">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="33ef4-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="33ef4-131">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="33ef4-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="33ef4-132">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="33ef4-132">Enable authentication</span></span>

<span data-ttu-id="33ef4-133">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="33ef4-134">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="33ef4-134">Layout changes</span></span>

<span data-ttu-id="33ef4-135">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="33ef4-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="33ef4-136">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="33ef4-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="33ef4-137">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-138">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="33ef4-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="33ef4-139">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="33ef4-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="33ef4-140">Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="33ef4-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="33ef4-141">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="33ef4-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="33ef4-142">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-143">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="33ef4-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="33ef4-144">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="33ef4-145">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="33ef4-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="33ef4-146">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="33ef4-146">Create full identity UI source</span></span>

<span data-ttu-id="33ef4-147">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="33ef4-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="33ef4-148">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="33ef4-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="33ef4-149">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="33ef4-150">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="33ef4-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="33ef4-151">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="33ef4-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="33ef4-152">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="33ef4-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="33ef4-153">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="33ef4-153">Disable register page</span></span>

<span data-ttu-id="33ef4-154">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="33ef4-154">To disable user registration:</span></span>

* <span data-ttu-id="33ef4-155">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="33ef4-155">Scaffold Identity.</span></span> <span data-ttu-id="33ef4-156">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="33ef4-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="33ef4-157">Příklad:</span><span class="sxs-lookup"><span data-stu-id="33ef4-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="33ef4-158">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="33ef4-159">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="33ef4-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="33ef4-160">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="33ef4-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="33ef4-161">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="33ef4-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="33ef4-162">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="33ef4-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="33ef4-163">Odstraňte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="33ef4-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="33ef4-164">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="33ef4-164">Use another app to add users</span></span>

<span data-ttu-id="33ef4-165">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="33ef4-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="33ef4-166">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="33ef4-166">Options to add users include:</span></span>

* <span data-ttu-id="33ef4-167">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="33ef4-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="33ef4-168">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="33ef4-168">A console app.</span></span>

<span data-ttu-id="33ef4-169">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="33ef4-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="33ef4-170">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="33ef4-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="33ef4-171">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="33ef4-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="33ef4-172">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="33ef4-173">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="33ef4-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="33ef4-174">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="33ef4-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="33ef4-175">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="33ef4-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="33ef4-176">Zabránit publikování prostředků statické identity</span><span class="sxs-lookup"><span data-stu-id="33ef4-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="33ef4-177">Chcete-li zabránit publikování statických prostředků identity do kořenového <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>adresáře webu, přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="33ef4-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33ef4-178">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="33ef4-178">Additional resources</span></span>

* [<span data-ttu-id="33ef4-179">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="33ef4-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="33ef4-180">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="33ef4-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="33ef4-181">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="33ef4-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="33ef4-182">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="33ef4-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="33ef4-183">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="33ef4-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="33ef4-184">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="33ef4-185">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="33ef4-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="33ef4-186">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="33ef4-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="33ef4-187">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="33ef4-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="33ef4-188">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="33ef4-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="33ef4-189">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="33ef4-190">Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="33ef4-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="33ef4-191">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="33ef4-192">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="33ef4-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="33ef4-193">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="33ef4-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="33ef4-194">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="33ef4-195">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="33ef4-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="33ef4-196">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="33ef4-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="33ef4-197">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="33ef4-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="33ef4-198">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="33ef4-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="33ef4-199">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="33ef4-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="33ef4-200">Do `Startup` třídy přidejte následující zvýrazněná volání:</span><span class="sxs-lookup"><span data-stu-id="33ef4-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="33ef4-201">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="33ef4-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="33ef4-202">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-203">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="33ef4-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="33ef4-204">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="33ef4-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="33ef4-205">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="33ef4-205">Enable authentication</span></span>

<span data-ttu-id="33ef4-206">V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="33ef4-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="33ef4-207">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="33ef4-207">Layout changes</span></span>

<span data-ttu-id="33ef4-208">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="33ef4-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="33ef4-209">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="33ef4-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="33ef4-210">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-211">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="33ef4-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="33ef4-212">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="33ef4-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="33ef4-213">Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="33ef4-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="33ef4-214">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="33ef4-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="33ef4-215">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="33ef4-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="33ef4-216">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="33ef4-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="33ef4-217">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="33ef4-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="33ef4-218">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="33ef4-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="33ef4-219">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="33ef4-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="33ef4-220">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="33ef4-220">Create full identity UI source</span></span>

<span data-ttu-id="33ef4-221">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="33ef4-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="33ef4-222">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="33ef4-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="33ef4-223">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="33ef4-224">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="33ef4-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="33ef4-225">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="33ef4-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="33ef4-226">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="33ef4-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="33ef4-227">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="33ef4-227">Disable register page</span></span>

<span data-ttu-id="33ef4-228">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="33ef4-228">To disable user registration:</span></span>

* <span data-ttu-id="33ef4-229">Identita uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="33ef4-229">Scaffold Identity.</span></span> <span data-ttu-id="33ef4-230">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="33ef4-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="33ef4-231">Příklad:</span><span class="sxs-lookup"><span data-stu-id="33ef4-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="33ef4-232">Aktualizovat *oblasti/identitu/stránky/účet/zaregistrovat. cshtml. cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="33ef4-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="33ef4-233">Aktualizovat *oblasti/identitu/stránky/účet/registrovat. cshtml* pro zajištění konzistence s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="33ef4-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="33ef4-234">Odkomentujte nebo odeberte odkaz na registraci z *oblasti/identity/stránky/účet/Login. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="33ef4-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="33ef4-235">Aktualizuje stránku *oblasti/identita/stránky/účet/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="33ef4-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="33ef4-236">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="33ef4-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="33ef4-237">Odstraňte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="33ef4-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="33ef4-238">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="33ef4-238">Use another app to add users</span></span>

<span data-ttu-id="33ef4-239">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="33ef4-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="33ef4-240">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="33ef4-240">Options to add users include:</span></span>

* <span data-ttu-id="33ef4-241">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="33ef4-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="33ef4-242">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="33ef4-242">A console app.</span></span>

<span data-ttu-id="33ef4-243">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="33ef4-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="33ef4-244">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="33ef4-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="33ef4-245">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="33ef4-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="33ef4-246">Uživatel se přidá do databáze identity.</span><span class="sxs-lookup"><span data-stu-id="33ef4-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="33ef4-247">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="33ef4-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="33ef4-248">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="33ef4-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="33ef4-249">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="33ef4-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="33ef4-250">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="33ef4-250">Additional resources</span></span>

* [<span data-ttu-id="33ef4-251">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="33ef4-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end