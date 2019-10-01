---
title: Identita uživatelského rozhraní v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak generovat identitu uživatelského rozhraní v projektu ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703763"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="b7988-103">Identita uživatelského rozhraní v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="b7988-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="b7988-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7988-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b7988-105">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core identitu](xref:security/authentication/identity) jako [knihovnu tříd Razor](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="b7988-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b7988-106">Aplikace, které obsahují identitu, můžou použít generátory, aby selektivně přidaly zdrojový kód obsažený v knihovně tříd identity Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="b7988-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b7988-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="b7988-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b7988-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="b7988-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b7988-109">Generovaný kód má přednost před stejným kódem v RCL identity.</span><span class="sxs-lookup"><span data-stu-id="b7988-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b7988-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="b7988-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b7988-111">Aplikace, které **neobsahují** ověřování, můžou použít generátor pro přidání balíčku identity RCL.</span><span class="sxs-lookup"><span data-stu-id="b7988-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b7988-112">Máte možnost vybrat kód identity, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="b7988-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b7988-113">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="b7988-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b7988-114">Tento dokument popisuje kroky potřebné k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="b7988-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b7988-115">Když se spustí generování identit identity, vytvoří se v adresáři projektu soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="b7988-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b7988-116">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení aktualizace generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="b7988-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="b7988-117">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="b7988-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b7988-118">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="b7988-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b7988-119">Zkontrolujte změny po spuštění generování uživatelského rozhraní identity.</span><span class="sxs-lookup"><span data-stu-id="b7988-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b7988-120">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s identitou.</span><span class="sxs-lookup"><span data-stu-id="b7988-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b7988-121">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7988-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b7988-122">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="b7988-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b7988-123">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b7988-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="b7988-124">Identita uživatelského rozhraní do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="b7988-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b7988-125">Do třídy `Startup` přidejte následující zvýrazněná volání:</span><span class="sxs-lookup"><span data-stu-id="b7988-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="b7988-126">Identita uživatelského rozhraní do projektu Razor bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="b7988-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="b7988-127">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="b7988-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b7988-128">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b7988-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b7988-129">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="b7988-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b7988-130">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="b7988-130">Enable authentication</span></span>

<span data-ttu-id="b7988-131">V metodě `Configure` třídy `Startup` volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="b7988-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b7988-132">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="b7988-132">Layout changes</span></span>

<span data-ttu-id="b7988-133">Volitelné: Přidejte k souboru rozložení částečnou přihlášení (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="b7988-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="b7988-134">Identita uživatelského rozhraní do projektu Razor s autorizací</span><span class="sxs-lookup"><span data-stu-id="b7988-134">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="b7988-135">Některé možnosti identity jsou nakonfigurované v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="b7988-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b7988-136">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b7988-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b7988-137">Identita uživatelského rozhraní do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="b7988-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="b7988-138">Volitelné: Do souboru *views/Shared/_Layout. cshtml* přidejte částečná přihlášení (`_LoginPartial`):</span><span class="sxs-lookup"><span data-stu-id="b7988-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b7988-139">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="b7988-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b7988-140">Identita je nakonfigurovaná v *oblasti/identity/IdentityHostingStartup. cs*.</span><span class="sxs-lookup"><span data-stu-id="b7988-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b7988-141">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="b7988-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b7988-142">Volat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="b7988-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b7988-143">Identita uživatelského rozhraní do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="b7988-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b7988-144">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="b7988-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="b7988-145">Vytvořit úplný zdroj uživatelského rozhraní identity</span><span class="sxs-lookup"><span data-stu-id="b7988-145">Create full identity UI source</span></span>

<span data-ttu-id="b7988-146">Chcete-li zachovat úplnou kontrolu nad uživatelským rozhraním identity, spusťte generátor identity a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="b7988-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b7988-147">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí uživatelské rozhraní identity identitou ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="b7988-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b7988-148">To může být vhodné, pokud chcete mít úplnou kontrolu nad uživatelským rozhraním identity.</span><span class="sxs-lookup"><span data-stu-id="b7988-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b7988-149">Výchozí identita je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="b7988-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b7988-150">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="b7988-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b7988-151">Zaregistrujte implementaci `IEmailSender`, například:</span><span class="sxs-lookup"><span data-stu-id="b7988-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="b7988-152">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7988-152">Additional resources</span></span>

* [<span data-ttu-id="b7988-153">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="b7988-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
