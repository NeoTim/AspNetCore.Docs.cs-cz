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
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="ab303-103">Generování uživatelského rozhraní Identity v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="ab303-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="ab303-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ab303-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab303-105">ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="ab303-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="ab303-106">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="ab303-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="ab303-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="ab303-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="ab303-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="ab303-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="ab303-109">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="ab303-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="ab303-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného Identity zdroje uživatelského rozhraní](#full).</span><span class="sxs-lookup"><span data-stu-id="ab303-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="ab303-111">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="ab303-112">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="ab303-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="ab303-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="ab303-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="ab303-114">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ab303-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="ab303-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="ab303-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="ab303-116">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ab303-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="ab303-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="ab303-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="ab303-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="ab303-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="ab303-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="ab303-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="ab303-121">Při generování uživatelského rozhraní Identity s novým kontextem dat do projektu se stávajícími jednotlivými účty:</span><span class="sxs-lookup"><span data-stu-id="ab303-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="ab303-122">V nástroji `Startup.ConfigureServices` odeberte volání na:</span><span class="sxs-lookup"><span data-stu-id="ab303-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="ab303-123">Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="ab303-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="ab303-124">Předchozí kód odhlásí kód, který je duplikován v *oblasti/ Identity / Identity HostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="ab303-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="ab303-125">Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.</span><span class="sxs-lookup"><span data-stu-id="ab303-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="ab303-126">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="ab303-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="ab303-127">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ab303-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="ab303-128">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="ab303-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="ab303-129">Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-130">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="ab303-131">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="ab303-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="ab303-132">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="ab303-132">Enable authentication</span></span>

<span data-ttu-id="ab303-133">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ab303-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="ab303-134">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="ab303-134">Layout changes</span></span>

<span data-ttu-id="ab303-135">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="ab303-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="ab303-136">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="ab303-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="ab303-137">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-138">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="ab303-139">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="ab303-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="ab303-140">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ab303-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="ab303-141">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab303-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="ab303-142">Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-143">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="ab303-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="ab303-144">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="ab303-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="ab303-145">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="ab303-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="ab303-146">Generování uživatelského rozhraní Identity do Blazor Server projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="ab303-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="ab303-147">Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-148">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="ab303-149">Migrace</span><span class="sxs-lookup"><span data-stu-id="ab303-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="ab303-150">Předání tokenu XSRF do aplikace</span><span class="sxs-lookup"><span data-stu-id="ab303-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="ab303-151">Tokeny lze předat součástem:</span><span class="sxs-lookup"><span data-stu-id="ab303-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="ab303-152">Při zřizování ověřovacích tokenů a jejich uložení do ověřování je cookie možné je předat součástem.</span><span class="sxs-lookup"><span data-stu-id="ab303-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="ab303-153">Razorkomponenty nemůžou používat `HttpContext` přímo, takže neexistuje žádný způsob, jak získat [token pro padělání žádostí](xref:security/anti-request-forgery) o odeslání na Identity koncový bod odhlašovacího objektu na adrese `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="ab303-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="ab303-154">Token XSRF lze předat komponentám.</span><span class="sxs-lookup"><span data-stu-id="ab303-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="ab303-155">Další informace naleznete v tématu <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="ab303-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="ab303-156">V souboru *Pages/_Host. cshtml* vytvořte token po jeho přidání do `InitialApplicationState` `TokenProvider` tříd a:</span><span class="sxs-lookup"><span data-stu-id="ab303-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="ab303-157">Aktualizujte `App` komponentu (*App. Razor*), abyste přiřadili `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="ab303-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="ab303-158">Služba, která `TokenProvider` je znázorněna v tématu, se používá v `LoginDisplay` části v následujícím oddílu [rozložení a změny toku ověřování](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="ab303-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="ab303-159">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="ab303-159">Enable authentication</span></span>

<span data-ttu-id="ab303-160">Ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="ab303-160">In the `Startup` class:</span></span>

* <span data-ttu-id="ab303-161">Potvrďte, že Razor jsou do služby přidány stránky `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ab303-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="ab303-162">Pokud používáte [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), zaregistrujte službu.</span><span class="sxs-lookup"><span data-stu-id="ab303-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="ab303-163">Zavolejte `UseDatabaseErrorPage` na tvůrce aplikací v nástroji `Startup.Configure` pro vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="ab303-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="ab303-164">Volání `UseAuthentication` a `UseAuthorization` After `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="ab303-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="ab303-165">Přidejte koncový bod pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="ab303-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="ab303-166">Změny toku rozložení a ověřování</span><span class="sxs-lookup"><span data-stu-id="ab303-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="ab303-167">Přidejte `RedirectToLogin` komponentu (*RedirectToLogin. Razor*) do *sdílené* složky aplikace v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="ab303-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Přidejte `LoginDisplay` součást (*LoginDisplay. Razor*) do *sdílené* složky aplikace. <span data-ttu-id="ab303-169">[Služba TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) poskytuje token XSRF pro formulář HTML, který odesílá do Identity koncového bodu pro odhlášení:</span><span class="sxs-lookup"><span data-stu-id="ab303-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="ab303-170">Do `MainLayout` komponenty (*Shared/MainLayout. Razor*) přidejte `LoginDisplay` komponentu do obsahu elementu horního řádku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="ab303-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="ab303-171">Koncové body ověřování stylu</span><span class="sxs-lookup"><span data-stu-id="ab303-171">Style authentication endpoints</span></span>

<span data-ttu-id="ab303-172">Vzhledem k tomu Blazor Server Razor , že nástroj používá stránky stránky Identity , styl uživatelského rozhraní se změní, když návštěvník prochází mezi Identity stránkami a komponentami.</span><span class="sxs-lookup"><span data-stu-id="ab303-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="ab303-173">Máte dvě možnosti, jak adresovat incongruous styly:</span><span class="sxs-lookup"><span data-stu-id="ab303-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="ab303-174">IdentityKomponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="ab303-174">Build Identity components</span></span>

<span data-ttu-id="ab303-175">Přístup k používání komponent Identity místo stránek je vytvoření Identity komponent.</span><span class="sxs-lookup"><span data-stu-id="ab303-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="ab303-176">Vzhledem `SignInManager` `UserManager` k tomu, že součásti nejsou podporovány v Razor součástech, použijte koncové body rozhraní API v Blazor Server aplikaci ke zpracování akcí uživatelských účtů.</span><span class="sxs-lookup"><span data-stu-id="ab303-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="ab303-177">Použití vlastního rozložení pomocí Blazor stylů aplikací</span><span class="sxs-lookup"><span data-stu-id="ab303-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="ab303-178">IdentityRozložení a styly stránek lze upravit tak, aby vytvořily stránky, které používají výchozí Blazor motiv.</span><span class="sxs-lookup"><span data-stu-id="ab303-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="ab303-179">Příklad v této části je pouze výchozím bodem pro přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="ab303-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="ab303-180">Pro nejlepší uživatelské prostředí se nejspíš vyžaduje další práce.</span><span class="sxs-lookup"><span data-stu-id="ab303-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="ab303-181">Vytvoří novou `NavMenu_IdentityLayout` součást (*Shared/NavMenu_ Identity layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ab303-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="ab303-182">Pro značky a kód součásti použijte stejný obsah `NavMenu` součásti aplikace (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="ab303-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="ab303-183">Vydejte všechny `NavLink` součásti, které se nedají získat anonymně, protože automatické přesměrování v `RedirectToLogin` součásti selžou pro součásti, které vyžadují ověřování nebo autorizaci.</span><span class="sxs-lookup"><span data-stu-id="ab303-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="ab303-184">V souboru *Pages/Shared/layout. cshtml* proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="ab303-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="ab303-185">Přidejte Razor do horní části souboru direktivy pro použití pomocníků značek a součástí aplikace ve *sdílené* složce:</span><span class="sxs-lookup"><span data-stu-id="ab303-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="ab303-186">Nahraďte `{APPLICATION ASSEMBLY}` názvem sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="ab303-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="ab303-187">Přidat `<base>` značku a Blazor šablonu stylů `<link>` k `<head>` obsahu:</span><span class="sxs-lookup"><span data-stu-id="ab303-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="ab303-188">Změňte obsah `<body>` značky na následující:</span><span class="sxs-lookup"><span data-stu-id="ab303-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="ab303-189">Generování uživatelského rozhraní Identity do Blazor Server projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="ab303-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="ab303-190">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-191">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="ab303-192">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="ab303-192">Create full Identity UI source</span></span>

<span data-ttu-id="ab303-193">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="ab303-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="ab303-194">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="ab303-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="ab303-195">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="ab303-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="ab303-196">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab303-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="ab303-197">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="ab303-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="ab303-198">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="ab303-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="ab303-199">Konfigurace hesla</span><span class="sxs-lookup"><span data-stu-id="ab303-199">Password configuration</span></span>

<span data-ttu-id="ab303-200">Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="ab303-201">`InputModel``Password`vlastnosti se nacházejí v následujících souborech:</span><span class="sxs-lookup"><span data-stu-id="ab303-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="ab303-202">Zakázat stránku</span><span class="sxs-lookup"><span data-stu-id="ab303-202">Disable a page</span></span>

<span data-ttu-id="ab303-203">V této části se dozvíte, jak zakázat stránku registrace, ale přístup můžete použít k zakázání jakékoli stránky.</span><span class="sxs-lookup"><span data-stu-id="ab303-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="ab303-204">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="ab303-204">To disable user registration:</span></span>

* <span data-ttu-id="ab303-205">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-205">Scaffold Identity.</span></span> <span data-ttu-id="ab303-206">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="ab303-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="ab303-207">Například:</span><span class="sxs-lookup"><span data-stu-id="ab303-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="ab303-208">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="ab303-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="ab303-209">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="ab303-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="ab303-210">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab303-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="ab303-211">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="ab303-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="ab303-212">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="ab303-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="ab303-213">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ab303-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="ab303-214">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="ab303-214">Use another app to add users</span></span>

<span data-ttu-id="ab303-215">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ab303-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="ab303-216">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="ab303-216">Options to add users include:</span></span>

* <span data-ttu-id="ab303-217">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="ab303-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="ab303-218">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="ab303-218">A console app.</span></span>

<span data-ttu-id="ab303-219">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="ab303-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="ab303-220">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="ab303-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="ab303-221">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="ab303-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="ab303-222">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="ab303-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="ab303-223">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="ab303-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="ab303-224">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="ab303-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="ab303-225">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="ab303-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="ab303-226">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="ab303-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="ab303-227">Chcete-li zabránit publikování statických Identity prostředků do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="ab303-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab303-228">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ab303-228">Additional resources</span></span>

* [<span data-ttu-id="ab303-229">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="ab303-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab303-230">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="ab303-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="ab303-231">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="ab303-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="ab303-232">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="ab303-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="ab303-233">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="ab303-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="ab303-234">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="ab303-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="ab303-235">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="ab303-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="ab303-236">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="ab303-237">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="ab303-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="ab303-238">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="ab303-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="ab303-239">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ab303-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="ab303-240">Když se Identity spustí generátor, vytvoří se soubor *ScaffoldingReadme.txt* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="ab303-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="ab303-241">Soubor *ScaffoldingReadme.txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ab303-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="ab303-242">Tento dokument obsahuje ucelené pokyny, než *ScaffoldingReadme.txt* soubor.</span><span class="sxs-lookup"><span data-stu-id="ab303-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="ab303-243">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="ab303-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="ab303-244">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ab303-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="ab303-245">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="ab303-246">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="ab303-247">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="ab303-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="ab303-248">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="ab303-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="ab303-249">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="ab303-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="ab303-250">Do třídy přidejte následující zvýrazněná volání `Startup` :</span><span class="sxs-lookup"><span data-stu-id="ab303-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="ab303-251">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="ab303-251">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="ab303-252">Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-252">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-253">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="ab303-254">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="ab303-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="ab303-255">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="ab303-255">Enable authentication</span></span>

<span data-ttu-id="ab303-256">V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="ab303-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="ab303-257">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="ab303-257">Layout changes</span></span>

<span data-ttu-id="ab303-258">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="ab303-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="ab303-259">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="ab303-259">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="ab303-260">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-261">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab303-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="ab303-262">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="ab303-262">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="ab303-263">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ab303-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="ab303-264">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab303-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="ab303-265">Identityje nakonfigurovaný v *oblasti/ Identity / Identity HostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ab303-265">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="ab303-266">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="ab303-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="ab303-267">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="ab303-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="ab303-268">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="ab303-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="ab303-269">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="ab303-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="ab303-270">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="ab303-270">Create full Identity UI source</span></span>

<span data-ttu-id="ab303-271">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="ab303-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="ab303-272">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="ab303-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="ab303-273">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="ab303-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="ab303-274">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab303-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="ab303-275">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="ab303-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="ab303-276">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="ab303-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="ab303-277">Konfigurace hesla</span><span class="sxs-lookup"><span data-stu-id="ab303-277">Password configuration</span></span>

<span data-ttu-id="ab303-278">Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="ab303-279">`InputModel``Password`vlastnosti se nacházejí v následujících souborech:</span><span class="sxs-lookup"><span data-stu-id="ab303-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="ab303-280">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="ab303-280">Disable register page</span></span>

<span data-ttu-id="ab303-281">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="ab303-281">To disable user registration:</span></span>

* <span data-ttu-id="ab303-282">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="ab303-282">Scaffold Identity.</span></span> <span data-ttu-id="ab303-283">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="ab303-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="ab303-284">Například:</span><span class="sxs-lookup"><span data-stu-id="ab303-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="ab303-285">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="ab303-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="ab303-286">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="ab303-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="ab303-287">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="ab303-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="ab303-288">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="ab303-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="ab303-289">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="ab303-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="ab303-290">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ab303-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="ab303-291">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="ab303-291">Use another app to add users</span></span>

<span data-ttu-id="ab303-292">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ab303-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="ab303-293">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="ab303-293">Options to add users include:</span></span>

* <span data-ttu-id="ab303-294">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="ab303-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="ab303-295">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="ab303-295">A console app.</span></span>

<span data-ttu-id="ab303-296">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="ab303-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="ab303-297">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="ab303-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="ab303-298">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="ab303-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="ab303-299">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="ab303-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="ab303-300">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="ab303-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="ab303-301">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="ab303-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="ab303-302">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="ab303-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ab303-303">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ab303-303">Additional resources</span></span>

* [<span data-ttu-id="ab303-304">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="ab303-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
