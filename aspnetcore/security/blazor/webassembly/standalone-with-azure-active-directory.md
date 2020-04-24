---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4c5fc6a07ce52ee9560cd2aa0c7dbc3032522f00
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138449"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="03bdc-102">Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="03bdc-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="03bdc-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="03bdc-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="03bdc-104">Vytvoření Blazor samostatné aplikace WebAssembly, která pro ověřování používá [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) :</span><span class="sxs-lookup"><span data-stu-id="03bdc-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="03bdc-105">[Vytvoření TENANTA AAD a webové aplikace](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="03bdc-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="03bdc-106">Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="03bdc-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="03bdc-107">Zadejte **název** aplikace (například \*\* Blazor AAD klienta\*\*).</span><span class="sxs-lookup"><span data-stu-id="03bdc-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="03bdc-108">Vyberte **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="03bdc-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="03bdc-109">**Účty v tomto organizačním adresáři** můžete vybrat jenom pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="03bdc-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="03bdc-110">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro `https://localhost:5001/authentication/login-callback`přesměrování.</span><span class="sxs-lookup"><span data-stu-id="03bdc-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="03bdc-111">Zakažte **oprávnění** > **udělit správcům oprávnění k OpenID a offline_access** .</span><span class="sxs-lookup"><span data-stu-id="03bdc-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="03bdc-112">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="03bdc-112">Select **Register**.</span></span>

<span data-ttu-id="03bdc-113">Na webu**Konfigurace** >  **ověřovacích** > platforem**Web**:</span><span class="sxs-lookup"><span data-stu-id="03bdc-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="03bdc-114">Ověřte, zda `https://localhost:5001/authentication/login-callback` je **identifikátor URI přesměrování** k dispozici.</span><span class="sxs-lookup"><span data-stu-id="03bdc-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="03bdc-115">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="03bdc-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="03bdc-116">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="03bdc-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="03bdc-117">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="03bdc-117">Select the **Save** button.</span></span>

<span data-ttu-id="03bdc-118">Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="03bdc-118">Record the following information:</span></span>

* <span data-ttu-id="03bdc-119">ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="03bdc-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="03bdc-120">ID adresáře (ID klienta) (například `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="03bdc-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="03bdc-121">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="03bdc-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="03bdc-122">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="03bdc-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="03bdc-123">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="03bdc-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="03bdc-124">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="03bdc-124">Authentication package</span></span>

<span data-ttu-id="03bdc-125">Když je aplikace vytvořená tak, aby používala pracovní nebo`SingleOrg`školní účty (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="03bdc-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="03bdc-126">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="03bdc-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="03bdc-127">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="03bdc-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="03bdc-128">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="03bdc-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="03bdc-129">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="03bdc-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="03bdc-130">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="03bdc-130">Authentication service support</span></span>

<span data-ttu-id="03bdc-131">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="03bdc-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="03bdc-132">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="03bdc-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="03bdc-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="03bdc-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="03bdc-134">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="03bdc-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="03bdc-135">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="03bdc-135">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

<span data-ttu-id="03bdc-136">Konfigurace je dodána souborem *wwwroot/appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="03bdc-136">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/{TENANT ID}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="03bdc-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="03bdc-137">Example:</span></span>

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
        "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
    }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="03bdc-138">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="03bdc-138">Access token scopes</span></span>

<span data-ttu-id="03bdc-139">Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="03bdc-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="03bdc-140">Pokud chcete jako součást toku přihlášení zřídit přístupový token, přidejte obor do výchozích oborů přístupového tokenu `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="03bdc-140">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="03bdc-141">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="03bdc-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="03bdc-142">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="03bdc-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="03bdc-143">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="03bdc-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="03bdc-144">Další informace najdete v následujících částech článku o *dalších scénářích* :</span><span class="sxs-lookup"><span data-stu-id="03bdc-144">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="03bdc-145">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="03bdc-145">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="03bdc-146">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="03bdc-146">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="03bdc-147">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="03bdc-147">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="03bdc-148">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="03bdc-148">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="03bdc-149">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="03bdc-149">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="03bdc-150">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="03bdc-150">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="03bdc-151">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="03bdc-151">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="03bdc-152">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="03bdc-152">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="03bdc-153">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="03bdc-153">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="03bdc-154">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="03bdc-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
