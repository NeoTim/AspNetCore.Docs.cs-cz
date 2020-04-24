---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111198"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="193ce-102">Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="193ce-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="193ce-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="193ce-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="193ce-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="193ce-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="193ce-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="193ce-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="193ce-106">Vytvoření Blazor samostatné aplikace WebAssembly, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :</span><span class="sxs-lookup"><span data-stu-id="193ce-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="193ce-107">[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="193ce-107">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="193ce-108">Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="193ce-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="193ce-109">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="193ce-109">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="193ce-110">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="193ce-110">Choose a **Supported account types**.</span></span> <span data-ttu-id="193ce-111">**Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="193ce-111">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="193ce-112">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="193ce-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="193ce-113">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="193ce-113">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="193ce-114">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="193ce-114">Select **Register**.</span></span>

<span data-ttu-id="193ce-115">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="193ce-115">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="193ce-116">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="193ce-116">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="193ce-117">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="193ce-117">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="193ce-118">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="193ce-118">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="193ce-119">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="193ce-119">Select the **Save** button.</span></span>

<span data-ttu-id="193ce-120">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="193ce-120">Record the following information:</span></span>

* <span data-ttu-id="193ce-121">ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="193ce-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="193ce-122">ID adresáře (ID klienta) (například `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="193ce-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="193ce-123">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="193ce-123">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="193ce-124">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="193ce-124">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="193ce-125">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="193ce-125">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="193ce-126">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="193ce-126">Authentication package</span></span>

<span data-ttu-id="193ce-127">Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="193ce-127">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="193ce-128">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="193ce-128">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="193ce-129">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="193ce-129">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="193ce-130">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="193ce-130">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="193ce-131">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="193ce-131">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="193ce-132">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="193ce-132">Authentication service support</span></span>

<span data-ttu-id="193ce-133">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="193ce-133">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="193ce-134">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="193ce-134">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="193ce-135">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="193ce-135">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="193ce-136">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="193ce-136">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="193ce-137">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="193ce-137">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="193ce-138">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="193ce-138">Access token scopes</span></span>

<span data-ttu-id="193ce-139">Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="193ce-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="193ce-140">Pokud chcete vytvořit token jako součást toku přihlašování, přidejte obor do výchozích oborů přístupových tokenů `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="193ce-140">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="193ce-141">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="193ce-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="193ce-142">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="193ce-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="193ce-143">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="193ce-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="193ce-144">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="193ce-144">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="193ce-145">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="193ce-145">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="193ce-146">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="193ce-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="193ce-147">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="193ce-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="193ce-148">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="193ce-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="193ce-149">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="193ce-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="193ce-150">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="193ce-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="193ce-151">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="193ce-151">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="193ce-152">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="193ce-152">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
