---
title: Generování uživatelského rozhraní Identity v ASP.NET Corech projektech
author: rick-anderson
description: Naučte se, jak získat Identity v projektu ASP.NET Core.
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
ms.openlocfilehash: 116e5d27e7585e9168db433480c3a5e9d08379f3
ms.sourcegitcommit: 67eadd7bf28eae0b8786d85e90a7df811ffe5904
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84454658"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="edaef-103">Generování uživatelského rozhraní Identity v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="edaef-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="edaef-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="edaef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="edaef-105">ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="edaef-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="edaef-106">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="edaef-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="edaef-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="edaef-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="edaef-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="edaef-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="edaef-109">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="edaef-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="edaef-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného Identity zdroje uživatelského rozhraní](#full).</span><span class="sxs-lookup"><span data-stu-id="edaef-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="edaef-111">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="edaef-112">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="edaef-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="edaef-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="edaef-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="edaef-114">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edaef-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="edaef-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="edaef-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="edaef-116">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edaef-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="edaef-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="edaef-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="edaef-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="edaef-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="edaef-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="edaef-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="edaef-121">Při generování uživatelského rozhraní Identity s novým kontextem dat do projektu se stávajícími jednotlivými účty:</span><span class="sxs-lookup"><span data-stu-id="edaef-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="edaef-122">V nástroji `Startup.ConfigureServices` odeberte volání na:</span><span class="sxs-lookup"><span data-stu-id="edaef-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="edaef-123">Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="edaef-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="edaef-124">Předchozí kód odhlásí kód, který je duplikován v *oblasti/ Identity /IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="edaef-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="edaef-125">Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.</span><span class="sxs-lookup"><span data-stu-id="edaef-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="edaef-126">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="edaef-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="edaef-127">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edaef-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="edaef-128">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="edaef-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="edaef-129">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-130">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="edaef-131">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="edaef-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="edaef-132">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="edaef-132">Enable authentication</span></span>

<span data-ttu-id="edaef-133">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edaef-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="edaef-134">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="edaef-134">Layout changes</span></span>

<span data-ttu-id="edaef-135">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="edaef-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="edaef-136">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="edaef-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="edaef-137">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-138">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="edaef-139">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="edaef-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="edaef-140">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="edaef-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="edaef-141">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="edaef-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="edaef-142">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-143">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="edaef-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="edaef-144">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="edaef-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="edaef-145">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="edaef-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="edaef-146">Generování uživatelského rozhraní Identity do Blazor projektu serveru bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="edaef-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="edaef-147">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-148">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="edaef-149">Migrace</span><span class="sxs-lookup"><span data-stu-id="edaef-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="edaef-150">Předání tokenu XSRF do aplikace</span><span class="sxs-lookup"><span data-stu-id="edaef-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="edaef-151">Tokeny lze předat součástem:</span><span class="sxs-lookup"><span data-stu-id="edaef-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="edaef-152">Když jsou ověřovací tokeny zřízené a uložené do ověřovacího souboru cookie, dají se předat součástem.</span><span class="sxs-lookup"><span data-stu-id="edaef-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="edaef-153">komponenty nemůžou používat `HttpContext` přímo, takže neexistuje žádný způsob, jak získat [token pro padělání žádostí](xref:security/anti-request-forgery) o odeslání na Identity koncový bod odhlašovacího objektu na adrese `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="edaef-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="edaef-154">Token XSRF lze předat komponentám.</span><span class="sxs-lookup"><span data-stu-id="edaef-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="edaef-155">Další informace naleznete v tématu <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="edaef-155">For more information, see <xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="edaef-156">V souboru *Pages/_Host. cshtml* vytvořte token po jeho přidání do `InitialApplicationState` `TokenProvider` tříd a:</span><span class="sxs-lookup"><span data-stu-id="edaef-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="edaef-157">Aktualizujte `App` komponentu (*App. Razor*), abyste přiřadili `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="edaef-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="edaef-158">Služba, která `TokenProvider` je znázorněna v tématu, se používá v `LoginDisplay` části v následujícím oddílu [rozložení a změny toku ověřování](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="edaef-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="edaef-159">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="edaef-159">Enable authentication</span></span>

<span data-ttu-id="edaef-160">Ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="edaef-160">In the `Startup` class:</span></span>

* <span data-ttu-id="edaef-161">Potvrďte, že Razor jsou do služby přidány stránky `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="edaef-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="edaef-162">Pokud používáte [TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app), zaregistrujte službu.</span><span class="sxs-lookup"><span data-stu-id="edaef-162">If using the [TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="edaef-163">Zavolejte `UseDatabaseErrorPage` na tvůrce aplikací v nástroji `Startup.Configure` pro vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="edaef-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="edaef-164">Volání `UseAuthentication` a `UseAuthorization` After `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="edaef-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="edaef-165">Přidejte koncový bod pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="edaef-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="edaef-166">Změny toku rozložení a ověřování</span><span class="sxs-lookup"><span data-stu-id="edaef-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="edaef-167">Přidejte `RedirectToLogin` komponentu (*RedirectToLogin. Razor*) do *sdílené* složky aplikace v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="edaef-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Přidejte `LoginDisplay` součást (*LoginDisplay. Razor*) do *sdílené* složky aplikace. <span data-ttu-id="edaef-169">[Služba TokenProvider](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app) poskytuje token XSRF pro formulář HTML, který odesílá do Identity koncového bodu pro odhlášení:</span><span class="sxs-lookup"><span data-stu-id="edaef-169">The [TokenProvider service](xref:security/blazor/server/index#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="edaef-170">Do `MainLayout` komponenty (*Shared/MainLayout. Razor*) přidejte `LoginDisplay` komponentu do obsahu elementu horního řádku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="edaef-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="edaef-171">Koncové body ověřování stylu</span><span class="sxs-lookup"><span data-stu-id="edaef-171">Style authentication endpoints</span></span>

<span data-ttu-id="edaef-172">Vzhledem k tomu Blazor , že server používá stránky Razor stránky Identity , styl uživatelského rozhraní se změní, když návštěvník prochází mezi Identity stránkami a komponentami.</span><span class="sxs-lookup"><span data-stu-id="edaef-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="edaef-173">Máte dvě možnosti, jak adresovat incongruous styly:</span><span class="sxs-lookup"><span data-stu-id="edaef-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="edaef-174">IdentityKomponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="edaef-174">Build Identity components</span></span>

<span data-ttu-id="edaef-175">Přístup k používání komponent Identity místo stránek je vytvoření Identity komponent.</span><span class="sxs-lookup"><span data-stu-id="edaef-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="edaef-176">Vzhledem `SignInManager` `UserManager` k tomu, že součásti nejsou podporované v Razor součástech, použijte koncové body rozhraní API v Blazor serverové aplikaci ke zpracování akcí uživatelských účtů.</span><span class="sxs-lookup"><span data-stu-id="edaef-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="edaef-177">Použití vlastního rozložení pomocí Blazor stylů aplikací</span><span class="sxs-lookup"><span data-stu-id="edaef-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="edaef-178">IdentityRozložení a styly stránek lze upravit tak, aby vytvořily stránky, které používají výchozí Blazor motiv.</span><span class="sxs-lookup"><span data-stu-id="edaef-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="edaef-179">Příklad v této části je pouze výchozím bodem pro přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="edaef-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="edaef-180">Pro nejlepší uživatelské prostředí se nejspíš vyžaduje další práce.</span><span class="sxs-lookup"><span data-stu-id="edaef-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="edaef-181">Vytvoří novou `NavMenu_IdentityLayout` součást (*Shared/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="edaef-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="edaef-182">Pro značky a kód součásti použijte stejný obsah `NavMenu` součásti aplikace (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="edaef-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="edaef-183">Vydejte všechny `NavLink` součásti, které se nedají získat anonymně, protože automatické přesměrování v `RedirectToLogin` součásti selžou pro součásti, které vyžadují ověřování nebo autorizaci.</span><span class="sxs-lookup"><span data-stu-id="edaef-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="edaef-184">V souboru *Pages/Shared/layout. cshtml* proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="edaef-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="edaef-185">Přidejte Razor do horní části souboru direktivy pro použití pomocníků značek a součástí aplikace ve *sdílené* složce:</span><span class="sxs-lookup"><span data-stu-id="edaef-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="edaef-186">Nahraďte `{APPLICATION ASSEMBLY}` názvem sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="edaef-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="edaef-187">Přidat `<base>` značku a Blazor šablonu stylů `<link>` k `<head>` obsahu:</span><span class="sxs-lookup"><span data-stu-id="edaef-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="edaef-188">Změňte obsah `<body>` značky na následující:</span><span class="sxs-lookup"><span data-stu-id="edaef-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="edaef-189">Uživatelské rozhraní Identity Blazor pro serverový projekt s autorizací</span><span class="sxs-lookup"><span data-stu-id="edaef-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="edaef-190">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-191">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="edaef-192">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="edaef-192">Create full Identity UI source</span></span>

<span data-ttu-id="edaef-193">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="edaef-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="edaef-194">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="edaef-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="edaef-195">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="edaef-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="edaef-196">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="edaef-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="edaef-197">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="edaef-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="edaef-198">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="edaef-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="edaef-199">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="edaef-199">Disable register page</span></span>

<span data-ttu-id="edaef-200">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="edaef-200">To disable user registration:</span></span>

* <span data-ttu-id="edaef-201">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-201">Scaffold Identity.</span></span> <span data-ttu-id="edaef-202">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="edaef-202">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="edaef-203">Příklad:</span><span class="sxs-lookup"><span data-stu-id="edaef-203">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="edaef-204">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="edaef-204">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="edaef-205">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="edaef-205">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="edaef-206">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="edaef-206">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="edaef-207">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="edaef-207">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="edaef-208">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="edaef-208">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="edaef-209">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="edaef-209">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="edaef-210">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="edaef-210">Use another app to add users</span></span>

<span data-ttu-id="edaef-211">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edaef-211">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="edaef-212">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="edaef-212">Options to add users include:</span></span>

* <span data-ttu-id="edaef-213">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="edaef-213">A dedicated admin web app.</span></span>
* <span data-ttu-id="edaef-214">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="edaef-214">A console app.</span></span>

<span data-ttu-id="edaef-215">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="edaef-215">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="edaef-216">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="edaef-216">A list of users is read into memory.</span></span>
* <span data-ttu-id="edaef-217">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="edaef-217">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="edaef-218">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="edaef-218">The user is added to the Identity database.</span></span>
* <span data-ttu-id="edaef-219">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="edaef-219">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="edaef-220">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="edaef-220">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="edaef-221">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="edaef-221">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="edaef-222">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="edaef-222">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="edaef-223">Chcete-li zabránit publikování statických Identity prostředků do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="edaef-223">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="edaef-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edaef-224">Additional resources</span></span>

* [<span data-ttu-id="edaef-225">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="edaef-225">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="edaef-226">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="edaef-226">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="edaef-227">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="edaef-227">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="edaef-228">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="edaef-228">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="edaef-229">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="edaef-229">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="edaef-230">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="edaef-230">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="edaef-231">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="edaef-231">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="edaef-232">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-232">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="edaef-233">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="edaef-233">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="edaef-234">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="edaef-234">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="edaef-235">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edaef-235">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="edaef-236">Když se Identity spustí generátor, vytvoří se soubor *ScaffoldingReadme. txt* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="edaef-236">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="edaef-237">Soubor *ScaffoldingReadme. txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edaef-237">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="edaef-238">Tento dokument obsahuje ucelené pokyny, než soubor *ScaffoldingReadme. txt* .</span><span class="sxs-lookup"><span data-stu-id="edaef-238">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="edaef-239">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="edaef-239">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="edaef-240">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="edaef-240">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="edaef-241">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-241">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="edaef-242">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-242">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="edaef-243">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="edaef-243">Services to enable these features must be added manually.</span></span> <span data-ttu-id="edaef-244">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="edaef-244">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="edaef-245">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="edaef-245">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="edaef-246">Do třídy přidejte následující zvýrazněná volání `Startup` :</span><span class="sxs-lookup"><span data-stu-id="edaef-246">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="edaef-247">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="edaef-247">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="edaef-248">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-248"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-249">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-249">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="edaef-250">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="edaef-250">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="edaef-251">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="edaef-251">Enable authentication</span></span>

<span data-ttu-id="edaef-252">V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="edaef-252">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="edaef-253">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="edaef-253">Layout changes</span></span>

<span data-ttu-id="edaef-254">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="edaef-254">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="edaef-255">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="edaef-255">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="edaef-256">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-256">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-257">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="edaef-257">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="edaef-258">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="edaef-258">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="edaef-259">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="edaef-259">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="edaef-260">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="edaef-260">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="edaef-261">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="edaef-261"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="edaef-262">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="edaef-262">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="edaef-263">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="edaef-263">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="edaef-264">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="edaef-264">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="edaef-265">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="edaef-265">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="edaef-266">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="edaef-266">Create full Identity UI source</span></span>

<span data-ttu-id="edaef-267">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="edaef-267">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="edaef-268">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="edaef-268">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="edaef-269">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="edaef-269">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="edaef-270">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="edaef-270">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="edaef-271">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="edaef-271">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="edaef-272">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="edaef-272">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="edaef-273">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="edaef-273">Disable register page</span></span>

<span data-ttu-id="edaef-274">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="edaef-274">To disable user registration:</span></span>

* <span data-ttu-id="edaef-275">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="edaef-275">Scaffold Identity.</span></span> <span data-ttu-id="edaef-276">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="edaef-276">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="edaef-277">Příklad:</span><span class="sxs-lookup"><span data-stu-id="edaef-277">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="edaef-278">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="edaef-278">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="edaef-279">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="edaef-279">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="edaef-280">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="edaef-280">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="edaef-281">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="edaef-281">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="edaef-282">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="edaef-282">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="edaef-283">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="edaef-283">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="edaef-284">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="edaef-284">Use another app to add users</span></span>

<span data-ttu-id="edaef-285">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="edaef-285">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="edaef-286">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="edaef-286">Options to add users include:</span></span>

* <span data-ttu-id="edaef-287">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="edaef-287">A dedicated admin web app.</span></span>
* <span data-ttu-id="edaef-288">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="edaef-288">A console app.</span></span>

<span data-ttu-id="edaef-289">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="edaef-289">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="edaef-290">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="edaef-290">A list of users is read into memory.</span></span>
* <span data-ttu-id="edaef-291">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="edaef-291">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="edaef-292">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="edaef-292">The user is added to the Identity database.</span></span>
* <span data-ttu-id="edaef-293">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="edaef-293">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="edaef-294">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="edaef-294">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="edaef-295">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="edaef-295">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="edaef-296">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="edaef-296">Additional resources</span></span>

* [<span data-ttu-id="edaef-297">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="edaef-297">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
