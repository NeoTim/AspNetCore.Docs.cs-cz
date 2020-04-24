---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111159"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="39ec6-102">Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="39ec6-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="39ec6-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="39ec6-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="39ec6-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="39ec6-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="39ec6-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="39ec6-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="39ec6-106">Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly, která pro ověřování používá [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) :</span><span class="sxs-lookup"><span data-stu-id="39ec6-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="39ec6-107">Při vytváření tenanta a registraci webové aplikace na webu Azure Portal postupujte podle pokynů v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="39ec6-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="39ec6-108">[Vytvořte tenanta](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; AAD B2C zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="39ec6-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="39ec6-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="39ec6-109">1\.</span></span> <span data-ttu-id="39ec6-110">Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="39ec6-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="39ec6-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="39ec6-111">2\.</span></span> <span data-ttu-id="39ec6-112">AAD B2C domény klienta (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="39ec6-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="39ec6-113">[Registrace webové aplikace](/azure/active-directory-b2c/tutorial-register-applications) &ndash; proveďte následující výběry během registrace aplikace:</span><span class="sxs-lookup"><span data-stu-id="39ec6-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="39ec6-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="39ec6-114">1\.</span></span> <span data-ttu-id="39ec6-115">Nastavte **Web App/Web API** na **Ano**.</span><span class="sxs-lookup"><span data-stu-id="39ec6-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="39ec6-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="39ec6-116">2\.</span></span> <span data-ttu-id="39ec6-117">Nastavte možnost **povoluje implicitní tok** na **hodnotu Ano**.</span><span class="sxs-lookup"><span data-stu-id="39ec6-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="39ec6-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="39ec6-118">3\.</span></span> <span data-ttu-id="39ec6-119">Přidejte **adresu URL odpovědi** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="39ec6-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="39ec6-120">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="39ec6-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="39ec6-121">[Vytváření uživatelských toků](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; : vytvoření uživatelského toku pro registraci a přihlašování.</span><span class="sxs-lookup"><span data-stu-id="39ec6-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="39ec6-122">Aby bylo možné naplnit `context.User.Identity.Name` `LoginDisplay` součást (*Shared/LoginDisplay. Razor*), vyberte alespoň atribut uživatele > **Zobrazovaný název** **deklarací identity aplikace**.</span><span class="sxs-lookup"><span data-stu-id="39ec6-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="39ec6-123">Zaznamenejte si název uživatelského toku pro registraci a přihlašování vytvořený pro aplikaci (například `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="39ec6-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="39ec6-124">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="39ec6-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="39ec6-125">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="39ec6-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="39ec6-126">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="39ec6-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="39ec6-127">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="39ec6-127">Authentication package</span></span>

<span data-ttu-id="39ec6-128">Když je aplikace vytvořená tak, aby používala individuální účet`IndividualB2C`B2C (), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="39ec6-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="39ec6-129">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="39ec6-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="39ec6-130">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="39ec6-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="39ec6-131">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="39ec6-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="39ec6-132">`Microsoft.Authentication.WebAssembly.Msal` Balíček do této aplikace přidá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíček.</span><span class="sxs-lookup"><span data-stu-id="39ec6-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="39ec6-133">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="39ec6-133">Authentication service support</span></span>

<span data-ttu-id="39ec6-134">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddMsalAuthentication` rozšíření poskytovanou `Microsoft.Authentication.WebAssembly.Msal` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="39ec6-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="39ec6-135">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="39ec6-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="39ec6-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="39ec6-136">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="39ec6-137">`AddMsalAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="39ec6-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="39ec6-138">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace AAD webu Azure Portal při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="39ec6-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="39ec6-139">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="39ec6-139">Access token scopes</span></span>

<span data-ttu-id="39ec6-140">Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="39ec6-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="39ec6-141">Pokud chcete vytvořit token jako součást toku přihlašování, přidejte obor do výchozích oborů přístupových tokenů `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="39ec6-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="39ec6-142">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="39ec6-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="39ec6-143">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="39ec6-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="39ec6-144">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="39ec6-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="39ec6-145">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="39ec6-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="39ec6-146">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="39ec6-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="39ec6-147">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="39ec6-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="39ec6-148">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="39ec6-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="39ec6-149">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="39ec6-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="39ec6-150">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="39ec6-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="39ec6-151">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="39ec6-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="39ec6-152">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="39ec6-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="39ec6-153">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="39ec6-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="39ec6-154">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="39ec6-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
