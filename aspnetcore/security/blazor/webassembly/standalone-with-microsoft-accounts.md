---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly s účty Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977077"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="5a515-102">Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly s účty Microsoft</span><span class="sxs-lookup"><span data-stu-id="5a515-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="5a515-103">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5a515-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="5a515-104">Vytvoření samostatné Blazor aplikace WebAssembly, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="5a515-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="5a515-105">Vytvoření klienta AAD a webové aplikace</span><span class="sxs-lookup"><span data-stu-id="5a515-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="5a515-106">Zaregistrujte aplikaci AAD v oblasti**registrace aplikací** Azure **Active Directory** > na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="5a515-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="5a515-107">1\.</span><span class="sxs-lookup"><span data-stu-id="5a515-107">1\.</span></span> <span data-ttu-id="5a515-108">Zadejte **název** aplikace (například \*\* Blazor AAD klienta).\*\*</span><span class="sxs-lookup"><span data-stu-id="5a515-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="5a515-109">2\.</span><span class="sxs-lookup"><span data-stu-id="5a515-109">2\.</span></span> <span data-ttu-id="5a515-110">V **části Podporované typy účtů**vyberte možnost Účty v **libovolném organizačním adresáři**.</span><span class="sxs-lookup"><span data-stu-id="5a515-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="5a515-111">3\.</span><span class="sxs-lookup"><span data-stu-id="5a515-111">3\.</span></span> <span data-ttu-id="5a515-112">Ponechejte rozevírací soubor **URI přesměrování** nastavený `https://localhost:5001/authentication/login-callback`na **web**a poskytněte identifikátor URI přesměrování aplikace .</span><span class="sxs-lookup"><span data-stu-id="5a515-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="5a515-113">4\.</span><span class="sxs-lookup"><span data-stu-id="5a515-113">4\.</span></span> <span data-ttu-id="5a515-114">Zamítejte**zaškrtávací** políčko **Oprávnění** > Udělit oprávnění k otevření a offline_access oprávnění.</span><span class="sxs-lookup"><span data-stu-id="5a515-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="5a515-115">5\.</span><span class="sxs-lookup"><span data-stu-id="5a515-115">5\.</span></span> <span data-ttu-id="5a515-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="5a515-116">Select **Register**.</span></span>

   <span data-ttu-id="5a515-117">V**konfiguracích platformy** >  **ověřování** > **web**:</span><span class="sxs-lookup"><span data-stu-id="5a515-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="5a515-118">1\.</span><span class="sxs-lookup"><span data-stu-id="5a515-118">1\.</span></span> <span data-ttu-id="5a515-119">Potvrďte, že `https://localhost:5001/authentication/login-callback` je přítomen **identifikátor URI přesměrování.**</span><span class="sxs-lookup"><span data-stu-id="5a515-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="5a515-120">2\.</span><span class="sxs-lookup"><span data-stu-id="5a515-120">2\.</span></span> <span data-ttu-id="5a515-121">V **případě implicitního udělení**zaškrtněte políčka u **tokenů přístupu** a **tokenů ID**.</span><span class="sxs-lookup"><span data-stu-id="5a515-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="5a515-122">3\.</span><span class="sxs-lookup"><span data-stu-id="5a515-122">3\.</span></span> <span data-ttu-id="5a515-123">Zbývající výchozí hodnoty pro aplikaci jsou přijatelné pro toto prostředí.</span><span class="sxs-lookup"><span data-stu-id="5a515-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="5a515-124">4\.</span><span class="sxs-lookup"><span data-stu-id="5a515-124">4\.</span></span> <span data-ttu-id="5a515-125">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="5a515-125">Select the **Save** button.</span></span>

   <span data-ttu-id="5a515-126">Zaznamenejte ID aplikace (ID `11111111-1111-1111-1111-111111111111`klienta) (například).</span><span class="sxs-lookup"><span data-stu-id="5a515-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="5a515-127">Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="5a515-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="5a515-128">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="5a515-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5a515-129">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="5a515-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="5a515-130">Po vytvoření aplikace byste měli být schopni:</span><span class="sxs-lookup"><span data-stu-id="5a515-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="5a515-131">Přihlaste se do aplikace pomocí účtu Microsoft.</span><span class="sxs-lookup"><span data-stu-id="5a515-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="5a515-132">Požádejte o přístupové tokeny pro rozhraní API Blazor Microsoft u stejný chod jako u samostatných aplikací za předpokladu, že jste aplikaci nakonfigurovali správně.</span><span class="sxs-lookup"><span data-stu-id="5a515-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="5a515-133">Další informace naleznete v [tématu Úvodní příručka: Konfigurace aplikace pro vystavení webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="5a515-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="5a515-134">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="5a515-134">Authentication package</span></span>

<span data-ttu-id="5a515-135">Když je aplikace vytvořena pro použití`SingleOrg`pracovních nebo školních účtů ( ), aplikace`Microsoft.Authentication.WebAssembly.Msal`automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="5a515-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="5a515-136">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="5a515-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5a515-137">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="5a515-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="5a515-138">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="5a515-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="5a515-139">Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a515-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="5a515-140">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="5a515-140">Authentication service support</span></span>

<span data-ttu-id="5a515-141">Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček.</span><span class="sxs-lookup"><span data-stu-id="5a515-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="5a515-142">Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="5a515-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="5a515-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5a515-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="5a515-144">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a515-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="5a515-145">Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="5a515-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="5a515-146">Obory přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="5a515-146">Access token scopes</span></span>

<span data-ttu-id="5a515-147">Šablona Blazor WebAssembly automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5a515-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="5a515-148">Chcete-li zřídit token jako součást toku přihlášení, přidejte obor do `MsalProviderOptions`výchozího oboru přístupového tokenu :</span><span class="sxs-lookup"><span data-stu-id="5a515-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="5a515-149">Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="5a515-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="5a515-150">Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="5a515-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="5a515-151">Zadej oblast URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="5a515-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="5a515-152">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="5a515-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="5a515-153">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="5a515-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="5a515-154">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="5a515-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="5a515-155">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="5a515-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="5a515-156">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="5a515-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="5a515-157">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="5a515-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="5a515-158">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="5a515-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5a515-159">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5a515-159">Additional resources</span></span>

* [<span data-ttu-id="5a515-160">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="5a515-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="5a515-161">Úvodní příručka: Registrace aplikace s platformou microsoftu pro identity</span><span class="sxs-lookup"><span data-stu-id="5a515-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="5a515-162">Rychlý start: Konfigurace aplikace pro vystavení webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="5a515-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
