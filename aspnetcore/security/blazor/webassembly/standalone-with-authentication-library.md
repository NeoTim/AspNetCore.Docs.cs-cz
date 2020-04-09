---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí knihovny Ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977038"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="12e7e-102">Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí knihovny Ověřování</span><span class="sxs-lookup"><span data-stu-id="12e7e-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="12e7e-103">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="12e7e-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="12e7e-104">*Pro Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Viz témata AAD a AAD B2C v tomto uzlu obsahu.*</span><span class="sxs-lookup"><span data-stu-id="12e7e-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="12e7e-105">Chcete-li Blazor vytvořit samostatnou aplikaci WebAssembly, která používá `Microsoft.AspNetCore.Components.WebAssembly.Authentication` knihovnu, spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="12e7e-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="12e7e-106">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="12e7e-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="12e7e-107">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="12e7e-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="12e7e-108">V Sadě Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="12e7e-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="12e7e-109">Nastavte **ověřování** na **jednotlivé uživatelské účty** pomocí možnosti uživatelské účty Store v **aplikaci.**</span><span class="sxs-lookup"><span data-stu-id="12e7e-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="12e7e-110">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="12e7e-110">Authentication package</span></span>

<span data-ttu-id="12e7e-111">Když je aplikace vytvořena pro použití individuálních uživatelských účtů, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` aplikace automaticky obdrží odkaz na balíček pro balíček v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="12e7e-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="12e7e-112">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="12e7e-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="12e7e-113">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="12e7e-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="12e7e-114">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="12e7e-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="12e7e-115">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="12e7e-115">Authentication service support</span></span>

<span data-ttu-id="12e7e-116">Podpora pro ověřování uživatelů je registrována v `AddOidcAuthentication` kontejneru služby `Microsoft.AspNetCore.Components.WebAssembly.Authentication` s metodou rozšíření poskytované balíček.</span><span class="sxs-lookup"><span data-stu-id="12e7e-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="12e7e-117">Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="12e7e-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="12e7e-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="12e7e-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="12e7e-119">Podpora ověřování pro samostatné aplikace je nabízena pomocí open id connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="12e7e-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="12e7e-120">Metoda `AddOidcAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="12e7e-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="12e7e-121">Hodnoty potřebné pro konfiguraci aplikace lze získat z IP kompatibilní s OIDC.</span><span class="sxs-lookup"><span data-stu-id="12e7e-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="12e7e-122">Získat hodnoty při registraci aplikace, která se obvykle vyskytuje v jejich online portálu.</span><span class="sxs-lookup"><span data-stu-id="12e7e-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="12e7e-123">Obory přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="12e7e-123">Access token scopes</span></span>

<span data-ttu-id="12e7e-124">Šablona Blazor WebAssembly automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="12e7e-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="12e7e-125">Chcete-li zřídit token jako součást toku přihlášení, přidejte obor `OidcProviderOptions`do výchozích oborů tokenu :</span><span class="sxs-lookup"><span data-stu-id="12e7e-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="12e7e-126">Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="12e7e-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="12e7e-127">Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="12e7e-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="12e7e-128">Zadej oblast URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="12e7e-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="12e7e-129">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="12e7e-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="12e7e-130">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="12e7e-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="12e7e-131">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="12e7e-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="12e7e-132">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="12e7e-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="12e7e-133">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="12e7e-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="12e7e-134">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="12e7e-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="12e7e-135">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="12e7e-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="12e7e-136">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="12e7e-136">Additional resources</span></span>

* [<span data-ttu-id="12e7e-137">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="12e7e-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
