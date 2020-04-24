---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí účtů Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111185"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="71d4c-102">Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí účtů Microsoft</span><span class="sxs-lookup"><span data-stu-id="71d4c-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="71d4c-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="71d4c-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="71d4c-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="71d4c-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="71d4c-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="71d4c-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="71d4c-106">Vytvoření Blazor samostatné aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="71d4c-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="71d4c-107">Vytvoření tenanta a webové aplikace AAD</span><span class="sxs-lookup"><span data-stu-id="71d4c-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="71d4c-108">Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="71d4c-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="71d4c-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-109">1\.</span></span> <span data-ttu-id="71d4c-110">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="71d4c-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="71d4c-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-111">2\.</span></span> <span data-ttu-id="71d4c-112">V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři**.</span><span class="sxs-lookup"><span data-stu-id="71d4c-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="71d4c-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-113">3\.</span></span> <span data-ttu-id="71d4c-114">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="71d4c-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="71d4c-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-115">4\.</span></span> <span data-ttu-id="71d4c-116">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="71d4c-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="71d4c-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-117">5\.</span></span> <span data-ttu-id="71d4c-118">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="71d4c-118">Select **Register**.</span></span>

   <span data-ttu-id="71d4c-119">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="71d4c-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="71d4c-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-120">1\.</span></span> <span data-ttu-id="71d4c-121">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="71d4c-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="71d4c-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-122">2\.</span></span> <span data-ttu-id="71d4c-123">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="71d4c-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="71d4c-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-124">3\.</span></span> <span data-ttu-id="71d4c-125">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="71d4c-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="71d4c-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="71d4c-126">4\.</span></span> <span data-ttu-id="71d4c-127">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="71d4c-127">Select the **Save** button.</span></span>

   <span data-ttu-id="71d4c-128">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="71d4c-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="71d4c-129">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="71d4c-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="71d4c-130">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="71d4c-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="71d4c-131">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="71d4c-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="71d4c-132">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="71d4c-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="71d4c-133">Přihlaste se k aplikaci pomocí účtu Microsoft.</span><span class="sxs-lookup"><span data-stu-id="71d4c-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="71d4c-134">Vyžádejte si přístupové tokeny pro rozhraní API Microsoftu stejným způsobem jako Blazor u samostatných aplikací za předpokladu, že jste aplikaci správně nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="71d4c-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="71d4c-135">Další informace najdete v tématu [rychlý Start: Konfigurace aplikace k vystavování webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="71d4c-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="71d4c-136">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="71d4c-136">Authentication package</span></span>

<span data-ttu-id="71d4c-137">Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="71d4c-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="71d4c-138">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="71d4c-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="71d4c-139">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="71d4c-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="71d4c-140">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="71d4c-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="71d4c-141">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="71d4c-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="71d4c-142">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="71d4c-142">Authentication service support</span></span>

<span data-ttu-id="71d4c-143">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="71d4c-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="71d4c-144">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="71d4c-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="71d4c-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="71d4c-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="71d4c-146">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="71d4c-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="71d4c-147">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="71d4c-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="71d4c-148">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="71d4c-148">Access token scopes</span></span>

<span data-ttu-id="71d4c-149">Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="71d4c-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="71d4c-150">Pokud chcete vytvořit token jako součást toku přihlašování, přidejte obor do výchozích oborů přístupových tokenů `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="71d4c-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="71d4c-151">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="71d4c-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="71d4c-152">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="71d4c-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="71d4c-153">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="71d4c-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="71d4c-154">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="71d4c-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="71d4c-155">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="71d4c-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="71d4c-156">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="71d4c-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="71d4c-157">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="71d4c-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="71d4c-158">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="71d4c-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="71d4c-159">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="71d4c-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="71d4c-160">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="71d4c-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="71d4c-161">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="71d4c-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="71d4c-162">Rychlý Start: registrace aplikace s platformou Microsoft identity</span><span class="sxs-lookup"><span data-stu-id="71d4c-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="71d4c-163">Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="71d4c-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
