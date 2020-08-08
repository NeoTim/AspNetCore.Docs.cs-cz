---
title: Zabezpečení Blazor WebAssembly samostatné aplikace v ASP.NET Core s využitím Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: cf8b224d417bf70b4053f22209a67bb8eb56257a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013733"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="9c30a-102">Zabezpečení Blazor WebAssembly samostatné aplikace v ASP.NET Core s využitím Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="9c30a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="9c30a-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9c30a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9c30a-104">Chcete-li vytvořit Blazor WebAssembly samostatnou aplikaci, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :</span><span class="sxs-lookup"><span data-stu-id="9c30a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="9c30a-105">[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="9c30a-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="9c30a-106">Zaregistrujte aplikaci AAD v **Azure Active Directory**  >  **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="9c30a-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9c30a-107">Zadejte **název** aplikace (například \*\* Blazor samostatná AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9c30a-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="9c30a-108">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="9c30a-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="9c30a-109">**Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="9c30a-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="9c30a-110">Ponechte rozevírací seznam **URI přesměrování** nastavenou na **Web** a zadejte následující identifikátor URI pro přesměrování: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9c30a-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9c30a-111">Výchozí port pro aplikaci běžící na Kestrel je 5001.</span><span class="sxs-lookup"><span data-stu-id="9c30a-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9c30a-112">Pokud je aplikace spuštěná na jiném Kestrel portu, použijte port aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c30a-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9c30a-113">Pro IIS Express se náhodně generovaný port pro aplikaci dá najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="9c30a-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9c30a-114">Vzhledem k tomu, že aplikace v tomto okamžiku neexistuje a port IIS Express není znám, vraťte se k tomuto kroku po vytvoření aplikace a aktualizaci identifikátoru URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9c30a-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9c30a-115">Po přeznačení se objeví dál v tomto tématu, které připomínat IIS Express uživatelům aktualizovat identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9c30a-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9c30a-116">Zakažte **oprávnění**  >  **udělení souhlasu správce OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="9c30a-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9c30a-117">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="9c30a-117">Select **Register**.</span></span>

<span data-ttu-id="9c30a-118">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="9c30a-118">Record the following information:</span></span>

* <span data-ttu-id="9c30a-119">ID aplikace (klienta) (například `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="9c30a-119">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="9c30a-120">ID adresáře (tenanta) (například `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="9c30a-120">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="9c30a-121">Na webu konfigurace **ověřovacích**  >  **platforem**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="9c30a-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9c30a-122">Ověřte, zda je **identifikátor URI přesměrování** k `https://localhost:{PORT}/authentication/login-callback` dispozici.</span><span class="sxs-lookup"><span data-stu-id="9c30a-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9c30a-123">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="9c30a-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9c30a-124">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="9c30a-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9c30a-125">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="9c30a-125">Select the **Save** button.</span></span>

<span data-ttu-id="9c30a-126">Vytvořte aplikaci v prázdné složce.</span><span class="sxs-lookup"><span data-stu-id="9c30a-126">Create the app in an empty folder.</span></span> <span data-ttu-id="9c30a-127">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="9c30a-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="9c30a-128">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="9c30a-128">Placeholder</span></span>   | <span data-ttu-id="9c30a-129">Název Azure Portal</span><span class="sxs-lookup"><span data-stu-id="9c30a-129">Azure portal name</span></span>       | <span data-ttu-id="9c30a-130">Příklad</span><span class="sxs-lookup"><span data-stu-id="9c30a-130">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="9c30a-131">ID aplikace (klienta)</span><span class="sxs-lookup"><span data-stu-id="9c30a-131">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="9c30a-132">ID adresáře (tenanta)</span><span class="sxs-lookup"><span data-stu-id="9c30a-132">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="9c30a-133">Umístění výstupu zadané s `-o|--output` možností vytvoří složku projektu, pokud neexistuje a bude součástí názvu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c30a-133">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9c30a-134">V Azure Portal **Authentication**  >  **Platform configurations**  >  **Web**  >  pro aplikace, které běží na serveru Kestrel s výchozími nastaveními, je pro port 5001 nakonfigurován**identifikátor URI webového přesměrování** konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c30a-134">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9c30a-135">Pokud je aplikace spuštěná na náhodném IIS Expressm portu, můžete port pro aplikaci najít ve vlastnostech aplikace na panelu **ladění** .</span><span class="sxs-lookup"><span data-stu-id="9c30a-135">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9c30a-136">Pokud port nebyl dříve nakonfigurovaný se známým portem aplikace, vraťte se k registraci aplikace v Azure Portal a aktualizujte identifikátor URI přesměrování pomocí správného portu.</span><span class="sxs-lookup"><span data-stu-id="9c30a-136">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="9c30a-137">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="9c30a-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="9c30a-138">Přihlaste se k aplikaci pomocí uživatelského účtu AAD.</span><span class="sxs-lookup"><span data-stu-id="9c30a-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="9c30a-139">Vyžádá přístupové tokeny pro rozhraní API Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="9c30a-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="9c30a-140">Další informace:</span><span class="sxs-lookup"><span data-stu-id="9c30a-140">For more information, see:</span></span>
  * [<span data-ttu-id="9c30a-141">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="9c30a-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="9c30a-142">[Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="9c30a-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="9c30a-143">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="9c30a-143">Authentication package</span></span>

<span data-ttu-id="9c30a-144">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty ( `SingleOrg` ), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) ).</span><span class="sxs-lookup"><span data-stu-id="9c30a-144">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="9c30a-145">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c30a-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9c30a-146">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="9c30a-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9c30a-147">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)Balíček [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) do této aplikace přidá balíček.</span><span class="sxs-lookup"><span data-stu-id="9c30a-147">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="9c30a-148">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="9c30a-148">Authentication service support</span></span>

<span data-ttu-id="9c30a-149">Podpora ověřování uživatelů je registrovaná v kontejneru služby s <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metodou rozšíření poskytovanou [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) balíčkem.</span><span class="sxs-lookup"><span data-stu-id="9c30a-149">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="9c30a-150">Tato metoda nastavuje služby, které aplikace potřebuje k interakci se Identity zprostředkovatelem (IP).</span><span class="sxs-lookup"><span data-stu-id="9c30a-150">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9c30a-151">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="9c30a-151">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="9c30a-152"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c30a-152">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9c30a-153">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9c30a-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="9c30a-154">Soubor zadal konfiguraci `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="9c30a-154">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9c30a-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="9c30a-155">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="9c30a-156">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="9c30a-156">Access token scopes</span></span>

<span data-ttu-id="9c30a-157">Tato Blazor WebAssembly Šablona automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="9c30a-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="9c30a-158">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9c30a-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9c30a-159">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="9c30a-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9c30a-160">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="9c30a-160">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9c30a-161">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="9c30a-161">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="9c30a-162">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="9c30a-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="9c30a-163">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="9c30a-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="9c30a-164">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="9c30a-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="9c30a-165">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="9c30a-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="9c30a-166">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="9c30a-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="9c30a-167">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="9c30a-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9c30a-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9c30a-168">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="9c30a-169">Neověřené nebo neautorizované požadavky webového rozhraní API v aplikaci s zabezpečeným výchozím klientem</span><span class="sxs-lookup"><span data-stu-id="9c30a-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9c30a-170">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="9c30a-170">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
