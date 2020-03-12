---
title: Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí účtů Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6883af3486256e7c6905626d8da09e8ae0c4a896
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083823"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="fef43-102">Zabezpečení Blazor samostatné aplikace ASP.NET Coreového sestavení pomocí účtů Microsoft</span><span class="sxs-lookup"><span data-stu-id="fef43-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="fef43-103">Od [Javier Calvarro Nelson](https://github.com/javiercn) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fef43-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="fef43-104">Vytvoření samostatné aplikace Blazorového sestavení, která používá [účty Microsoft s Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="fef43-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="fef43-105">Vytvoření tenanta a webové aplikace AAD</span><span class="sxs-lookup"><span data-stu-id="fef43-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="fef43-106">Zaregistrujte aplikaci AAD v **Azure Active Directory** > **Registrace aplikací** oblasti Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="fef43-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="fef43-107">1\.</span><span class="sxs-lookup"><span data-stu-id="fef43-107">1\.</span></span> <span data-ttu-id="fef43-108">Zadejte **název** aplikace (například **Blazor AAD klienta**).</span><span class="sxs-lookup"><span data-stu-id="fef43-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="fef43-109">2\.</span><span class="sxs-lookup"><span data-stu-id="fef43-109">2\.</span></span> <span data-ttu-id="fef43-110">V **podporovaných typech účtů**vyberte **účty v libovolném organizačním adresáři**.</span><span class="sxs-lookup"><span data-stu-id="fef43-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="fef43-111">3\.</span><span class="sxs-lookup"><span data-stu-id="fef43-111">3\.</span></span> <span data-ttu-id="fef43-112">Vynechejte rozevírací seznam **identifikátor URI přesměrování** nastavený na **Web**a zadejte identifikátor URI pro přesměrování `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="fef43-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="fef43-113">4\.</span><span class="sxs-lookup"><span data-stu-id="fef43-113">4\.</span></span> <span data-ttu-id="fef43-114">Zakažte **oprávnění** > oprávnění **správce udělit OpenID a offline_access oprávnění** .</span><span class="sxs-lookup"><span data-stu-id="fef43-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="fef43-115">5\.</span><span class="sxs-lookup"><span data-stu-id="fef43-115">5\.</span></span> <span data-ttu-id="fef43-116">Vyberte **Zaregistrovat**.</span><span class="sxs-lookup"><span data-stu-id="fef43-116">Select **Register**.</span></span>

   <span data-ttu-id="fef43-117">V > ověřování **Konfigurace platforem** > **webu**:</span><span class="sxs-lookup"><span data-stu-id="fef43-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="fef43-118">1\.</span><span class="sxs-lookup"><span data-stu-id="fef43-118">1\.</span></span> <span data-ttu-id="fef43-119">Potvrďte, že je k dispozici **identifikátor URI pro přesměrování** `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="fef43-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="fef43-120">2\.</span><span class="sxs-lookup"><span data-stu-id="fef43-120">2\.</span></span> <span data-ttu-id="fef43-121">V případě **implicitního udělení**zaškrtněte políčka pro **přístupové tokeny** a **tokeny ID**.</span><span class="sxs-lookup"><span data-stu-id="fef43-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="fef43-122">3\.</span><span class="sxs-lookup"><span data-stu-id="fef43-122">3\.</span></span> <span data-ttu-id="fef43-123">Zbývající výchozí hodnoty pro aplikaci jsou pro toto prostředí přijatelné.</span><span class="sxs-lookup"><span data-stu-id="fef43-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="fef43-124">4\.</span><span class="sxs-lookup"><span data-stu-id="fef43-124">4\.</span></span> <span data-ttu-id="fef43-125">Vyberte tlačítko **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="fef43-125">Select the **Save** button.</span></span>

   <span data-ttu-id="fef43-126">Poznamenejte si ID aplikace (ID klienta) (například `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="fef43-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="fef43-127">Zástupné symboly v následujícím příkazu nahraďte dříve zaznamenanými informacemi a spusťte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="fef43-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="fef43-128">Chcete-li určit umístění výstupu, které vytvoří složku projektu, pokud neexistuje, zahrňte možnost výstup do příkazu s cestou (například `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fef43-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fef43-129">Název složky se také stal součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="fef43-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="fef43-130">Po vytvoření aplikace byste měli mít tyto možnosti:</span><span class="sxs-lookup"><span data-stu-id="fef43-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="fef43-131">Přihlaste se k aplikaci pomocí účtu Microsoft.</span><span class="sxs-lookup"><span data-stu-id="fef43-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="fef43-132">Vyžádejte si přístupové tokeny pro rozhraní Microsoft API stejným způsobem jako u samostatných Blazorch aplikací, pokud jste aplikaci správně nakonfigurovali.</span><span class="sxs-lookup"><span data-stu-id="fef43-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="fef43-133">Další informace najdete v tématu [rychlý Start: Konfigurace aplikace k vystavování webových rozhraní API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="fef43-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="fef43-134">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="fef43-134">Authentication package</span></span>

<span data-ttu-id="fef43-135">Když je aplikace vytvořená tak, aby používala pracovní nebo školní účty (`SingleOrg`), aplikace automaticky obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span><span class="sxs-lookup"><span data-stu-id="fef43-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fef43-136">Balíček poskytuje sadu primitivních elementů, které aplikaci pomůžou ověřit uživatele a získat tokeny pro volání chráněných rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fef43-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fef43-137">Pokud se do aplikace přidává ověřování, přidejte balíček do souboru projektu aplikace ručně:</span><span class="sxs-lookup"><span data-stu-id="fef43-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fef43-138">V odkazu na předchozí balíček nahraďte `{VERSION}` verzí balíčku `Microsoft.AspNetCore.Blazor.Templates` zobrazeného v <xref:blazor/get-started> článku.</span><span class="sxs-lookup"><span data-stu-id="fef43-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fef43-139">`Microsoft.Authentication.WebAssembly.Msal` balíček do aplikace přidá balíček `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="fef43-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="fef43-140">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="fef43-140">Authentication service support</span></span>

<span data-ttu-id="fef43-141">Podpora ověřování uživatelů je zaregistrovaná v kontejneru služby s metodou rozšíření `AddMsalAuthentication` poskytovanou balíčkem `Microsoft.Authentication.WebAssembly.Msal`.</span><span class="sxs-lookup"><span data-stu-id="fef43-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fef43-142">Tato metoda nastavuje všechny služby, které aplikace potřebuje k interakci s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="fef43-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fef43-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fef43-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="fef43-144">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů požadovaných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="fef43-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fef43-145">Hodnoty požadované pro konfiguraci aplikace lze získat z konfigurace účtů Microsoft při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="fef43-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="index-page"></a><span data-ttu-id="fef43-146">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="fef43-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="fef43-147">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="fef43-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="fef43-148">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="fef43-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="fef43-149">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="fef43-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="fef43-150">Součást ověřování</span><span class="sxs-lookup"><span data-stu-id="fef43-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fef43-151">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fef43-151">Additional resources</span></span>

* [<span data-ttu-id="fef43-152">Rychlý Start: registrace aplikace s platformou Microsoft identity</span><span class="sxs-lookup"><span data-stu-id="fef43-152">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="fef43-153">Rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API</span><span class="sxs-lookup"><span data-stu-id="fef43-153">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
