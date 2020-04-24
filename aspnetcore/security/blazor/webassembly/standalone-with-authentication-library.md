---
title: Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí knihovny ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110942"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="696d4-102">Zabezpečení samostatné aplikace Blazor ASP.NET Coreového sestavení pomocí knihovny ověřování</span><span class="sxs-lookup"><span data-stu-id="696d4-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="696d4-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="696d4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="696d4-104">Pokyny v tomto článku se týkají ASP.NET Core 3,2 Preview 4.</span><span class="sxs-lookup"><span data-stu-id="696d4-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="696d4-105">Toto téma se bude aktualizovat na verzi Preview 5 v pátek, 24. dubna.</span><span class="sxs-lookup"><span data-stu-id="696d4-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="696d4-106">*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*</span><span class="sxs-lookup"><span data-stu-id="696d4-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="696d4-107">Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly `Microsoft.AspNetCore.Components.WebAssembly.Authentication` , která používá knihovnu, spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="696d4-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="696d4-108">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="696d4-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="696d4-109">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="696d4-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="696d4-110">V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="696d4-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="696d4-111">Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .</span><span class="sxs-lookup"><span data-stu-id="696d4-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="696d4-112">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="696d4-112">Authentication package</span></span>

<span data-ttu-id="696d4-113">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz `Microsoft.AspNetCore.Components.WebAssembly.Authentication` na balíček v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="696d4-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="696d4-114">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="696d4-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="696d4-115">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="696d4-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="696d4-116">Nahraďte `{VERSION}` odkazem na předchozí balíček verzí `Microsoft.AspNetCore.Blazor.Templates` balíčku, který je uvedený v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="696d4-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="696d4-117">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="696d4-117">Authentication service support</span></span>

<span data-ttu-id="696d4-118">Podpora ověřování uživatelů je registrovaná v kontejneru služby s metodou `AddOidcAuthentication` rozšíření poskytovanou `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="696d4-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="696d4-119">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="696d4-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="696d4-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="696d4-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="696d4-121">Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="696d4-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="696d4-122">`AddOidcAuthentication` Metoda přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="696d4-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="696d4-123">Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC.</span><span class="sxs-lookup"><span data-stu-id="696d4-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="696d4-124">Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.</span><span class="sxs-lookup"><span data-stu-id="696d4-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="696d4-125">Obory přístupového tokenu</span><span class="sxs-lookup"><span data-stu-id="696d4-125">Access token scopes</span></span>

<span data-ttu-id="696d4-126">Blazor Šablona protokolu WebAssembly nekonfiguruje aplikaci automaticky pro vyžádání přístupového tokenu pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="696d4-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="696d4-127">Chcete-li zřídit token jako součást toku přihlášení, přidejte obor do výchozího oboru tokenu `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="696d4-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="696d4-128">Pokud Azure Portal poskytne identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku** , když obdrží od rozhraní API *neautorizovanou odpověď 401* , zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="696d4-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="696d4-129">Azure Portal například může poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="696d4-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="696d4-130">Zadejte identifikátor URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="696d4-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="696d4-131">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="696d4-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="696d4-132">Importovat soubor</span><span class="sxs-lookup"><span data-stu-id="696d4-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="696d4-133">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="696d4-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="696d4-134">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="696d4-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="696d4-135">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="696d4-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="696d4-136">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="696d4-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="696d4-137">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="696d4-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="696d4-138">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="696d4-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 