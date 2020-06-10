---
title: Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí účtů Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: e4c49f899d5988dc42b16c16eb5114613c7204ba
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "83852335"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="6165e-102">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí účtů Microsoft</span><span class="sxs-lookup"><span data-stu-id="6165e-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="6165e-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6165e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6165e-104">Vytvoření Blazor samostatné aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="6165e-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="6165e-105">Vytvoření tenanta a webové aplikace AAD</span><span class="sxs-lookup"><span data-stu-id="6165e-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="6165e-106">Zaregistrujte aplikaci AAD v **Azure Active Directory**  >  **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="6165e-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="6165e-107">Zadejte **název** aplikace (například \*\* Blazor samostatné účty Microsoft AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6165e-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="6165e-108">V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři**.</span><span class="sxs-lookup"><span data-stu-id="6165e-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="6165e-109">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="6165e-109">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="6165e-110">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="6165e-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="6165e-111">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="6165e-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="6165e-112">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="6165e-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="6165e-113">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="6165e-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="6165e-114">Po přeznačení se objeví dál v tomto tématu, které připomínat IIS Express uživatelům aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="6165e-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="6165e-115">Zakažte **oprávnění**  >  **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="6165e-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="6165e-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="6165e-116">Select **Register**.</span></span>

<span data-ttu-id="6165e-117">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="6165e-117">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="6165e-118">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="6165e-118">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="6165e-119">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="6165e-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="6165e-120">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="6165e-120">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="6165e-121">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="6165e-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="6165e-122">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="6165e-122">Select the **Save** button.</span></span>

<span data-ttu-id="6165e-123">Vytvořte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6165e-123">Create the app.</span></span> <span data-ttu-id="6165e-124">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte následující příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="6165e-124">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="6165e-125">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="6165e-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6165e-126">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="6165e-126">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="6165e-127">V Azure Portal **Authentication**  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je pro port 5001 nakonfigurován**identifikátor URI webového přesměrování** konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="6165e-127">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="6165e-128">Pokud je aplikace spuštěná na náhodném IIS Expressm portu, můžete port pro aplikaci najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="6165e-128">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="6165e-129">Pokud port nebyl dříve nakonfigurovaný se známým portem aplikace, vraťte se k registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="6165e-129">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="6165e-130">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="6165e-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="6165e-131">Přihlaste se k aplikaci pomocí účet Microsoft.</span><span class="sxs-lookup"><span data-stu-id="6165e-131">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="6165e-132">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="6165e-132">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="6165e-133">Další informace:</span><span class="sxs-lookup"><span data-stu-id="6165e-133">For more information, see:</span></span>
  * [<span data-ttu-id="6165e-134">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="6165e-134">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="6165e-135">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="6165e-135">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6165e-136">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="6165e-136">Authentication package</span></span>

<span data-ttu-id="6165e-137">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="6165e-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="6165e-138">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6165e-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6165e-139">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="6165e-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="6165e-140">Balíček [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) po přechodu do aplikace přidá balíček [Microsoft. AspNetCore. Components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="6165e-140">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6165e-141">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="6165e-141">Authentication service support</span></span>

<span data-ttu-id="6165e-142">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou balíčkem [Microsoft. Authentication. WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) .</span><span class="sxs-lookup"><span data-stu-id="6165e-142">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="6165e-143">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s Identity poskytovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="6165e-143">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6165e-144">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6165e-144">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="6165e-145"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="6165e-145">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6165e-146">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="6165e-146">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="6165e-147">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6165e-147">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="6165e-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6165e-148">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="6165e-149">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="6165e-149">Access token scopes</span></span>

<span data-ttu-id="6165e-150">BlazorŠablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6165e-150">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6165e-151">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="6165e-151">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="6165e-152">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="6165e-152">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="6165e-153">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="6165e-153">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="6165e-154">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="6165e-154">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="6165e-155">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="6165e-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6165e-156">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="6165e-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6165e-157">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="6165e-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6165e-158">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6165e-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6165e-159">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6165e-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6165e-160">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="6165e-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6165e-161">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6165e-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="6165e-162">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="6165e-162">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="6165e-163">Rychlý Start: registrace aplikace s platformou Microsoft identity</span><span class="sxs-lookup"><span data-stu-id="6165e-163">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="6165e-164">Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6165e-164">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
