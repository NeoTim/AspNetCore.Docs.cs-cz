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
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="04587-103">Generování Identity uživatelského rozhraní v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="04587-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="04587-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="04587-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04587-105">ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="04587-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="04587-106">Aplikace, které Identity zahrnují, mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="04587-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="04587-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="04587-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="04587-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="04587-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="04587-109">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="04587-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="04587-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="04587-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="04587-111">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL Identity použít generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="04587-112">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="04587-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="04587-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="04587-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="04587-114">Tento dokument popisuje kroky potřebné k dokončení aktualizace Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="04587-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="04587-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="04587-116">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="04587-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity.</span><span class="sxs-lookup"><span data-stu-id="04587-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="04587-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity.</span><span class="sxs-lookup"><span data-stu-id="04587-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="04587-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="04587-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="04587-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="04587-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="04587-121">Při generování uživatelského Identity rozhraní s novým kontextem dat do projektu se stávajícími jednotlivými účty:</span><span class="sxs-lookup"><span data-stu-id="04587-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="04587-122">V `Startup.ConfigureServices`nástroji odeberte volání na:</span><span class="sxs-lookup"><span data-stu-id="04587-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="04587-123">Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="04587-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="04587-124">Předchozí kód odhlásí kód, který je duplikován v *oblasti/Identity/IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="04587-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="04587-125">Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.</span><span class="sxs-lookup"><span data-stu-id="04587-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="04587-126">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="04587-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="04587-127">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="04587-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="04587-128">Identita uživatelského rozhraní do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="04587-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="04587-129">je nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-130">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="04587-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="04587-131">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="04587-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="04587-132">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="04587-132">Enable authentication</span></span>

<span data-ttu-id="04587-133">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="04587-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="04587-134">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="04587-134">Layout changes</span></span>

<span data-ttu-id="04587-135">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="04587-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="04587-136">Identita uživatelského rozhraní do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="04587-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="04587-137">Některé Identity možnosti jsou nakonfigurovány v *oblastiIdentity//IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-138">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="04587-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="04587-139">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="04587-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="04587-140">Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="04587-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="04587-141">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="04587-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="04587-142">je nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-143">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="04587-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="04587-144">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="04587-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="04587-145">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="04587-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="04587-146">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="04587-146">Create full identity UI source</span></span>

<span data-ttu-id="04587-147">Chcete-li zachovat úplnou kontrolu Identity nad uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="04587-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="04587-148">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="04587-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="04587-149">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="04587-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="04587-150">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="04587-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="04587-151">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="04587-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="04587-152">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="04587-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="04587-153">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="04587-153">Disable register page</span></span>

<span data-ttu-id="04587-154">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="04587-154">To disable user registration:</span></span>

* <span data-ttu-id="04587-155">Generování Identityuživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-155">Scaffold Identity.</span></span> <span data-ttu-id="04587-156">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="04587-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="04587-157">Příklad:</span><span class="sxs-lookup"><span data-stu-id="04587-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="04587-158">Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="04587-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="04587-159">Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="04587-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="04587-160">Odkomentujte nebo odeberte odkaz na registraci z *oblastíIdentity//Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="04587-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="04587-161">Aktualizujte stránku *oblastiIdentity//Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="04587-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="04587-162">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="04587-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="04587-163">Odstraňte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="04587-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="04587-164">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="04587-164">Use another app to add users</span></span>

<span data-ttu-id="04587-165">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="04587-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="04587-166">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="04587-166">Options to add users include:</span></span>

* <span data-ttu-id="04587-167">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="04587-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="04587-168">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="04587-168">A console app.</span></span>

<span data-ttu-id="04587-169">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="04587-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="04587-170">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="04587-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="04587-171">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="04587-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="04587-172">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="04587-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="04587-173">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="04587-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="04587-174">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="04587-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="04587-175">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="04587-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="04587-176">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="04587-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="04587-177">Chcete-li zabránit Identity publikování statických prostředků do kořenového <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>adresáře webu, přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="04587-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04587-178">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="04587-178">Additional resources</span></span>

* [<span data-ttu-id="04587-179">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="04587-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04587-180">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="04587-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="04587-181">Aplikace, které Identity zahrnují, mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="04587-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="04587-182">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="04587-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="04587-183">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="04587-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="04587-184">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="04587-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="04587-185">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="04587-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="04587-186">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL Identity použít generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="04587-187">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="04587-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="04587-188">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="04587-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="04587-189">Tento dokument popisuje kroky potřebné k dokončení aktualizace Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="04587-190">Identity Když se spustí generátor, vytvoří se soubor *ScaffoldingReadme. txt* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="04587-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="04587-191">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="04587-192">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="04587-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="04587-193">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="04587-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="04587-194">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="04587-195">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity.</span><span class="sxs-lookup"><span data-stu-id="04587-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="04587-196">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity.</span><span class="sxs-lookup"><span data-stu-id="04587-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="04587-197">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="04587-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="04587-198">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="04587-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="04587-199">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="04587-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="04587-200">Do `Startup` třídy přidejte následující zvýrazněná volání:</span><span class="sxs-lookup"><span data-stu-id="04587-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="04587-201">Identita uživatelského rozhraní do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="04587-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="04587-202">je nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-203">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="04587-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="04587-204">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="04587-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="04587-205">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="04587-205">Enable authentication</span></span>

<span data-ttu-id="04587-206">V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="04587-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="04587-207">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="04587-207">Layout changes</span></span>

<span data-ttu-id="04587-208">Volitelné: přidejte do souboru rozložení částečnou přihlašování (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="04587-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="04587-209">Identita uživatelského rozhraní do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="04587-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="04587-210">Některé Identity možnosti jsou nakonfigurovány v *oblastiIdentity//IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-211">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="04587-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="04587-212">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="04587-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="04587-213">Volitelné: přidejte částečné přihlášení (`_LoginPartial`) do souboru *views/shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="04587-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="04587-214">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="04587-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="04587-215">je nakonfigurovaný v *oblasti/Identity/IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="04587-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="04587-216">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="04587-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="04587-217">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="04587-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="04587-218">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="04587-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="04587-219">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="04587-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="04587-220">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="04587-220">Create full identity UI source</span></span>

<span data-ttu-id="04587-221">Chcete-li zachovat úplnou kontrolu Identity nad uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="04587-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="04587-222">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="04587-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="04587-223">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="04587-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="04587-224">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="04587-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="04587-225">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="04587-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="04587-226">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="04587-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="04587-227">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="04587-227">Disable register page</span></span>

<span data-ttu-id="04587-228">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="04587-228">To disable user registration:</span></span>

* <span data-ttu-id="04587-229">Generování Identityuživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="04587-229">Scaffold Identity.</span></span> <span data-ttu-id="04587-230">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="04587-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="04587-231">Příklad:</span><span class="sxs-lookup"><span data-stu-id="04587-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="04587-232">Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="04587-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="04587-233">Aktualizovat *oblasti/Identity/Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="04587-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="04587-234">Odkomentujte nebo odeberte odkaz na registraci z *oblastíIdentity//Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="04587-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="04587-235">Aktualizujte stránku *oblastiIdentity//Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="04587-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="04587-236">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="04587-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="04587-237">Odstraňte potvrzovací kód z `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="04587-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="04587-238">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="04587-238">Use another app to add users</span></span>

<span data-ttu-id="04587-239">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="04587-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="04587-240">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="04587-240">Options to add users include:</span></span>

* <span data-ttu-id="04587-241">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="04587-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="04587-242">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="04587-242">A console app.</span></span>

<span data-ttu-id="04587-243">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="04587-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="04587-244">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="04587-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="04587-245">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="04587-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="04587-246">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="04587-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="04587-247">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="04587-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="04587-248">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="04587-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="04587-249">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="04587-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04587-250">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="04587-250">Additional resources</span></span>

* [<span data-ttu-id="04587-251">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="04587-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end