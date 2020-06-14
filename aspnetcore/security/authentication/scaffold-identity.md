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
ms.openlocfilehash: 36afa8ece58843b434ebfba6305bffdb9eb9bca0
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724286"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="cb1f7-103">Generování uživatelského rozhraní Identity v ASP.NET Corech projektech</span><span class="sxs-lookup"><span data-stu-id="cb1f7-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="cb1f7-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb1f7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb1f7-105">ASP.NET Core poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb1f7-106">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="cb1f7-107">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cb1f7-108">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cb1f7-109">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="cb1f7-110">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného Identity zdroje uživatelského rozhraní](#full).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="cb1f7-111">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="cb1f7-112">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="cb1f7-113">I když generátor generuje většinu potřebného kódu, je nutné aktualizovat projekt, aby bylo možné proces dokončit.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="cb1f7-114">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="cb1f7-115">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="cb1f7-116">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="cb1f7-117">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="cb1f7-118">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="cb1f7-119">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="cb1f7-120">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="cb1f7-121">Při generování uživatelského rozhraní Identity s novým kontextem dat do projektu se stávajícími jednotlivými účty:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="cb1f7-122">V nástroji `Startup.ConfigureServices` odeberte volání na:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="cb1f7-123">Například `AddDbContext` a `AddDefaultIdentity` jsou zakomentovány v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="cb1f7-124">Předchozí kód odhlásí kód, který je duplikován v *oblasti/ Identity /IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="cb1f7-125">Aplikace, které byly vytvořeny pomocí jednotlivých účtů ***, by obvykle neměly vytvářet*** nový kontext dat.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="cb1f7-126">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="cb1f7-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cb1f7-127">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="cb1f7-128">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="cb1f7-129">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-130">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="cb1f7-131">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="cb1f7-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="cb1f7-132">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="cb1f7-132">Enable authentication</span></span>

<span data-ttu-id="cb1f7-133">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="cb1f7-134">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="cb1f7-134">Layout changes</span></span>

<span data-ttu-id="cb1f7-135">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="cb1f7-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="cb1f7-136">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="cb1f7-137">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-138">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="cb1f7-139">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="cb1f7-140">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="cb1f7-141">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="cb1f7-142">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-143">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="cb1f7-144">Aktualizujte `Startup` třídu podobným kódem jako v následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="cb1f7-145">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="cb1f7-146">Generování uživatelského rozhraní Identity do Blazor projektu serveru bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="cb1f7-147">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-148">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="cb1f7-149">Migrace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="cb1f7-150">Předání tokenu XSRF do aplikace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="cb1f7-151">Tokeny lze předat součástem:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="cb1f7-152">Když jsou ověřovací tokeny zřízené a uložené do ověřovacího souboru cookie, dají se předat součástem.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="cb1f7-153">komponenty nemůžou používat `HttpContext` přímo, takže neexistuje žádný způsob, jak získat [token pro padělání žádostí](xref:security/anti-request-forgery) o odeslání na Identity koncový bod odhlašovacího objektu na adrese `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="cb1f7-154">Token XSRF lze předat komponentám.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="cb1f7-155">Další informace naleznete v tématu <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-155">For more information, see <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="cb1f7-156">V souboru *Pages/_Host. cshtml* vytvořte token po jeho přidání do `InitialApplicationState` `TokenProvider` tříd a:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="cb1f7-157">Aktualizujte `App` komponentu (*App. Razor*), abyste přiřadili `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="cb1f7-158">Služba, která `TokenProvider` je znázorněna v tématu, se používá v `LoginDisplay` části v následujícím oddílu [rozložení a změny toku ověřování](#layout-and-authentication-flow-changes) .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="cb1f7-159">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="cb1f7-159">Enable authentication</span></span>

<span data-ttu-id="cb1f7-160">Ve `Startup` třídě:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-160">In the `Startup` class:</span></span>

* <span data-ttu-id="cb1f7-161">Potvrďte, že Razor jsou do služby přidány stránky `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="cb1f7-162">Pokud používáte [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), zaregistrujte službu.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-162">If using the [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="cb1f7-163">Zavolejte `UseDatabaseErrorPage` na tvůrce aplikací v nástroji `Startup.Configure` pro vývojové prostředí.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="cb1f7-164">Volání `UseAuthentication` a `UseAuthorization` After `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="cb1f7-165">Přidejte koncový bod pro Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="cb1f7-166">Změny toku rozložení a ověřování</span><span class="sxs-lookup"><span data-stu-id="cb1f7-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="cb1f7-167">Přidejte `RedirectToLogin` komponentu (*RedirectToLogin. Razor*) do *sdílené* složky aplikace v kořenu projektu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

Přidejte `LoginDisplay` součást (*LoginDisplay. Razor*) do *sdílené* složky aplikace. <span data-ttu-id="cb1f7-169">[Služba TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) poskytuje token XSRF pro formulář HTML, který odesílá do Identity koncového bodu pro odhlášení:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-169">The [TokenProvider service](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="cb1f7-170">Do `MainLayout` komponenty (*Shared/MainLayout. Razor*) přidejte `LoginDisplay` komponentu do obsahu elementu horního řádku `<div>` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="cb1f7-171">Koncové body ověřování stylu</span><span class="sxs-lookup"><span data-stu-id="cb1f7-171">Style authentication endpoints</span></span>

<span data-ttu-id="cb1f7-172">Vzhledem k tomu Blazor , že server používá stránky Razor stránky Identity , styl uživatelského rozhraní se změní, když návštěvník prochází mezi Identity stránkami a komponentami.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="cb1f7-173">Máte dvě možnosti, jak adresovat incongruous styly:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="cb1f7-174">IdentityKomponenty sestavení</span><span class="sxs-lookup"><span data-stu-id="cb1f7-174">Build Identity components</span></span>

<span data-ttu-id="cb1f7-175">Přístup k používání komponent Identity místo stránek je vytvoření Identity komponent.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="cb1f7-176">Vzhledem `SignInManager` `UserManager` k tomu, že součásti nejsou podporované v Razor součástech, použijte koncové body rozhraní API v Blazor serverové aplikaci ke zpracování akcí uživatelských účtů.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="cb1f7-177">Použití vlastního rozložení pomocí Blazor stylů aplikací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="cb1f7-178">IdentityRozložení a styly stránek lze upravit tak, aby vytvořily stránky, které používají výchozí Blazor motiv.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="cb1f7-179">Příklad v této části je pouze výchozím bodem pro přizpůsobení.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="cb1f7-180">Pro nejlepší uživatelské prostředí se nejspíš vyžaduje další práce.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="cb1f7-181">Vytvoří novou `NavMenu_IdentityLayout` součást (*Shared/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="cb1f7-182">Pro značky a kód součásti použijte stejný obsah `NavMenu` součásti aplikace (*Shared/NavMenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="cb1f7-183">Vydejte všechny `NavLink` součásti, které se nedají získat anonymně, protože automatické přesměrování v `RedirectToLogin` součásti selžou pro součásti, které vyžadují ověřování nebo autorizaci.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="cb1f7-184">V souboru *Pages/Shared/layout. cshtml* proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="cb1f7-185">Přidejte Razor do horní části souboru direktivy pro použití pomocníků značek a součástí aplikace ve *sdílené* složce:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="cb1f7-186">Nahraďte `{APPLICATION ASSEMBLY}` názvem sestavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="cb1f7-187">Přidat `<base>` značku a Blazor šablonu stylů `<link>` k `<head>` obsahu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="cb1f7-188">Změňte obsah `<body>` značky na následující:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="cb1f7-189">Uživatelské rozhraní Identity Blazor pro serverový projekt s autorizací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="cb1f7-190">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-191">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="cb1f7-192">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cb1f7-192">Create full Identity UI source</span></span>

<span data-ttu-id="cb1f7-193">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="cb1f7-194">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="cb1f7-195">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="cb1f7-196">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="cb1f7-197">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="cb1f7-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="cb1f7-198">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="cb1f7-199">Zakázat stránku</span><span class="sxs-lookup"><span data-stu-id="cb1f7-199">Disable a page</span></span>

<span data-ttu-id="cb1f7-200">V této části se dozvíte, jak zakázat stránku registrace, ale přístup můžete použít k zakázání jakékoli stránky.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="cb1f7-201">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-201">To disable user registration:</span></span>

* <span data-ttu-id="cb1f7-202">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-202">Scaffold Identity.</span></span> <span data-ttu-id="cb1f7-203">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="cb1f7-204">Například:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="cb1f7-205">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="cb1f7-206">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="cb1f7-207">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="cb1f7-208">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="cb1f7-209">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="cb1f7-210">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="cb1f7-211">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-211">Use another app to add users</span></span>

<span data-ttu-id="cb1f7-212">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="cb1f7-213">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-213">Options to add users include:</span></span>

* <span data-ttu-id="cb1f7-214">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="cb1f7-215">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-215">A console app.</span></span>

<span data-ttu-id="cb1f7-216">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="cb1f7-217">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="cb1f7-218">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="cb1f7-219">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="cb1f7-220">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="cb1f7-221">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="cb1f7-222">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="cb1f7-223">Zabránit publikování statických Identity prostředků</span><span class="sxs-lookup"><span data-stu-id="cb1f7-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cb1f7-224">Chcete-li zabránit publikování statických Identity prostředků do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb1f7-225">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cb1f7-225">Additional resources</span></span>

* [<span data-ttu-id="cb1f7-226">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="cb1f7-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb1f7-227">ASP.NET Core 2,1 a novější poskytuje [ASP.NET Core Identity ](xref:security/authentication/identity) jako [ Razor knihovnu tříd](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb1f7-228">Aplikace, které zahrnují, Identity mohou použít generátory k selektivnímu Přidání zdrojového kódu obsaženého v Identity Razor knihovně tříd (RCL).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="cb1f7-229">Můžete chtít generovat zdrojový kód, abyste mohli kód upravit a změnit chování.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="cb1f7-230">Můžete například dát generátoru pokyn, aby vygeneroval kód používaný při registraci.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="cb1f7-231">Generovaný kód má přednost před stejným kódem v Identity RCL.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="cb1f7-232">Pokud chcete získat úplnou kontrolu nad uživatelským rozhraním a nepoužívat výchozí RCL, přečtěte si část [Vytvoření úplného zdroje uživatelského rozhraní identity](#full).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="cb1f7-233">Aplikace, které **neobsahují** ověřování, můžou pro přidání balíčku RCL použít generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="cb1f7-234">Máte možnost vybrat Identity kód, který se má vygenerovat.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="cb1f7-235">I když generátor generuje většinu potřebného kódu, budete muset aktualizovat projekt, aby se tento proces dokončil.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="cb1f7-236">Tento dokument popisuje kroky potřebné k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="cb1f7-237">Když se Identity spustí generátor, vytvoří se soubor *ScaffoldingReadme.txt* v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="cb1f7-238">Soubor *ScaffoldingReadme.txt* obsahuje obecné pokyny k tomu, co je potřeba k dokončení Identity aktualizace generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="cb1f7-239">Tento dokument obsahuje ucelené pokyny, než *ScaffoldingReadme.txt* soubor.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="cb1f7-240">Doporučujeme používat systém správy zdrojového kódu, který zobrazuje rozdíly mezi soubory a umožňuje zálohování změn.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="cb1f7-241">Zkontrolujte změny po spuštění nástroje pro Identity generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="cb1f7-242">Služby jsou vyžadovány při použití [dvou ověření](xref:security/authentication/identity-enable-qrcodes), [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm)a dalších funkcí zabezpečení s nástrojem Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="cb1f7-243">Služby nebo zástupné procedury služby nejsou generovány při generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="cb1f7-244">Služby, které umožňují tyto funkce povolit, je nutné přidat ručně.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="cb1f7-245">Podívejte se například na příkaz [vyžadovat potvrzení e-mailu](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="cb1f7-246">Uživatelské rozhraní Identity do prázdného projektu</span><span class="sxs-lookup"><span data-stu-id="cb1f7-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="cb1f7-247">Do třídy přidejte následující zvýrazněná volání `Startup` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="cb1f7-248">Generování uživatelského rozhraní Identity do Razor projektu bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="cb1f7-249">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-250">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="cb1f7-251">Migrace, UseAuthentication a rozložení</span><span class="sxs-lookup"><span data-stu-id="cb1f7-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="cb1f7-252">Povolit ověřování</span><span class="sxs-lookup"><span data-stu-id="cb1f7-252">Enable authentication</span></span>

<span data-ttu-id="cb1f7-253">V `Configure` metodě `Startup` třídy volejte [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="cb1f7-254">Změny rozložení</span><span class="sxs-lookup"><span data-stu-id="cb1f7-254">Layout changes</span></span>

<span data-ttu-id="cb1f7-255">Volitelné: přidejte do souboru rozložení částečnou přihlašování ( `_LoginPartial` ):</span><span class="sxs-lookup"><span data-stu-id="cb1f7-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="cb1f7-256">Generování uživatelského rozhraní Identity do Razor projektu s autorizací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="cb1f7-257">Některé Identity Možnosti jsou nakonfigurovány v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-258">Další informace najdete v tématu [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="cb1f7-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="cb1f7-259">Generování uživatelského rozhraní Identity do projektu MVC bez existující autorizace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="cb1f7-260">Volitelné: přidejte částečné přihlášení ( `_LoginPartial` ) do souboru *views/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="cb1f7-261">Přesuňte soubor *Pages/Shared/_LoginPartial. cshtml* do *views/shared/_LoginPartial. cshtml*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="cb1f7-262">je nakonfigurovaný v *oblasti/ Identity /IdentityHostingStartup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="cb1f7-263">Další informace najdete v tématu IHostingStartup.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="cb1f7-264">Zavolat [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) po `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="cb1f7-265">Generování uživatelského rozhraní Identity do projektu MVC s autorizací</span><span class="sxs-lookup"><span data-stu-id="cb1f7-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="cb1f7-266">Odstraňte *stránky nebo sdílenou* složku a soubory v této složce.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="cb1f7-267">Vytvořit úplný Identity zdroj uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="cb1f7-267">Create full Identity UI source</span></span>

<span data-ttu-id="cb1f7-268">Chcete-li zachovat úplnou kontrolu nad Identity uživatelským rozhraním, spusťte modul pro Identity generování uživatelského rozhraní a vyberte možnost **přepsat všechny soubory**.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="cb1f7-269">Následující zvýrazněný kód ukazuje změny, které nahradí výchozí Identity uživatelské rozhraní Identity ve webové aplikaci ASP.NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="cb1f7-270">To může být vhodné, pokud chcete mít úplnou kontrolu nad Identity uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="cb1f7-271">Výchozí hodnota Identity je nahrazena následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="cb1f7-272">Následující kód nastaví [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)a [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="cb1f7-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="cb1f7-273">Zaregistrujte `IEmailSender` implementaci, například:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="cb1f7-274">Zakázat stránku registrace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-274">Disable register page</span></span>

<span data-ttu-id="cb1f7-275">Zakázání registrace uživatele:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-275">To disable user registration:</span></span>

* <span data-ttu-id="cb1f7-276">Generování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-276">Scaffold Identity.</span></span> <span data-ttu-id="cb1f7-277">Přidejte účet. registr, Account. Login a account. RegisterConfirmation.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="cb1f7-278">Například:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="cb1f7-279">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml.cs* , aby se uživatelé nemohli registrovat z tohoto koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="cb1f7-280">Aktualizovat *oblasti/ Identity /Pages/Account/Register.cshtml* tak, aby byly v souladu s předchozími změnami:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="cb1f7-281">Odkomentujte nebo odeberte odkaz na registraci z *oblastí/ Identity /Pages/Account/Login.cshtml*</span><span class="sxs-lookup"><span data-stu-id="cb1f7-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="cb1f7-282">Aktualizujte stránku *oblasti/ Identity /Pages/Account/RegisterConfirmation* .</span><span class="sxs-lookup"><span data-stu-id="cb1f7-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="cb1f7-283">Odeberte kód a odkazy ze souboru CSHTML.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="cb1f7-284">Odstraňte potvrzovací kód z `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="cb1f7-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="cb1f7-285">Přidání uživatelů pomocí jiné aplikace</span><span class="sxs-lookup"><span data-stu-id="cb1f7-285">Use another app to add users</span></span>

<span data-ttu-id="cb1f7-286">Poskytněte mechanismus pro přidání uživatelů mimo webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="cb1f7-287">Mezi možnosti přidávání uživatelů patří:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-287">Options to add users include:</span></span>

* <span data-ttu-id="cb1f7-288">Vyhrazená webová aplikace pro správu.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="cb1f7-289">Konzolová aplikace.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-289">A console app.</span></span>

<span data-ttu-id="cb1f7-290">Následující kód popisuje jeden přístup k přidávání uživatelů:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="cb1f7-291">Seznam uživatelů je čten do paměti.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="cb1f7-292">Pro každého uživatele je vygenerováno silné jedinečné heslo.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="cb1f7-293">Uživatel se přidá do Identity databáze.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="cb1f7-294">Uživatel obdrží oznámení a sdělí mu změnu hesla.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="cb1f7-295">Následující osnovy kódu přidávají uživatele:</span><span class="sxs-lookup"><span data-stu-id="cb1f7-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="cb1f7-296">Podobný přístup je možné vyřídit v produkčních scénářích.</span><span class="sxs-lookup"><span data-stu-id="cb1f7-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb1f7-297">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cb1f7-297">Additional resources</span></span>

* [<span data-ttu-id="cb1f7-298">Změny ověřovacího kódu na ASP.NET Core 2,1 a novější</span><span class="sxs-lookup"><span data-stu-id="cb1f7-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
