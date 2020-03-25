---
title: Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ea50d94835b044f9c3d6a0561868f081d32cb62a
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218999"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="ad1ff-102">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí knihovny ověřování</span><span class="sxs-lookup"><span data-stu-id="ad1ff-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="ad1ff-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ad1ff-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="ad1ff-104">*V případě Azure Active Directory (AAD) a Azure Active Directory B2C (AAD B2C) nepostupujte podle pokynů v tomto tématu. Podívejte se na témata AAD a AAD B2C v tomto uzlu obsah.*</span><span class="sxs-lookup"><span data-stu-id="ad1ff-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="ad1ff-105">Chcete-li vytvořit samostatnou aplikaci Blazor WebAssembly, která používá knihovnu `Microsoft.AspNetCore.Components.WebAssembly.Authentication`, spusťte v příkazovém prostředí následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ad1ff-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="ad1ff-106">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="ad1ff-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="ad1ff-107">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="ad1ff-108">V aplikaci Visual Studio [vytvořte Blazor aplikaci WebAssembly](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="ad1ff-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="ad1ff-109">Nastavte **ověřování** na **jednotlivé uživatelské účty** s možností **Uložit uživatelské účty do aplikace** .</span><span class="sxs-lookup"><span data-stu-id="ad1ff-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="ad1ff-110">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="ad1ff-110">Authentication package</span></span>

<span data-ttu-id="ad1ff-111">Když je aplikace vytvořená tak, aby používala jednotlivé uživatelské účty, aplikace automaticky obdrží odkaz na balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication` balíčku v souboru projektu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="ad1ff-112">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ad1ff-113">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="ad1ff-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="ad1ff-114">V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ad1ff-115">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="ad1ff-115">Authentication service support</span></span>

<span data-ttu-id="ad1ff-116">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddOidcAuthentication` poskytovanou balíčkem `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="ad1ff-117">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="ad1ff-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ad1ff-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ad1ff-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="ad1ff-119">Podpora ověřování pro samostatné aplikace se nabízí pomocí Open ID Connect (OIDC).</span><span class="sxs-lookup"><span data-stu-id="ad1ff-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="ad1ff-120">Metoda `AddOidcAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace pomocí OIDC.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="ad1ff-121">Hodnoty požadované pro konfiguraci aplikace lze získat z IP adresy kompatibilní s OIDC.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="ad1ff-122">Získejte hodnoty při registraci aplikace, ke kterým obvykle dochází na online portálu.</span><span class="sxs-lookup"><span data-stu-id="ad1ff-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="ad1ff-123">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="ad1ff-123">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="ad1ff-124">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="ad1ff-124">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ad1ff-125">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="ad1ff-125">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ad1ff-126">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="ad1ff-126">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ad1ff-127">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="ad1ff-127">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
