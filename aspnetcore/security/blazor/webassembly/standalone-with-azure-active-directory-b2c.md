---
title: Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí služby Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977051"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="6244b-102">Zabezpečení samostatné Blazor aplikace ASP.NET Core WebAssembly pomocí služby Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6244b-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="6244b-103">[Javier Calvarro Nelson](https://github.com/javiercn) a Luke [Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6244b-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6244b-104">Vytvoření samostatné Blazor aplikace WebAssembly, která používá pro ověřování [B2C služby Azure Active Directory (AAD):](/azure/active-directory-b2c/overview)</span><span class="sxs-lookup"><span data-stu-id="6244b-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="6244b-105">Podle pokynů v následujících tématech vytvořte klienta a zaregistrujte webovou aplikaci na webu Azure Portal:</span><span class="sxs-lookup"><span data-stu-id="6244b-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="6244b-106">&ndash; [Vytvořte klienta AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Zaznamenejte následující informace:</span><span class="sxs-lookup"><span data-stu-id="6244b-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="6244b-107">1\.</span><span class="sxs-lookup"><span data-stu-id="6244b-107">1\.</span></span> <span data-ttu-id="6244b-108">Instance AAD B2C (například `https://contoso.b2clogin.com/`, která zahrnuje koncové lomítko)</span><span class="sxs-lookup"><span data-stu-id="6244b-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="6244b-109">2\.</span><span class="sxs-lookup"><span data-stu-id="6244b-109">2\.</span></span> <span data-ttu-id="6244b-110">Doména klienta AAD B2C (například `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="6244b-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="6244b-111">[Zaregistrujte webovou aplikaci](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Při registraci aplikace proveďte následující výběry:</span><span class="sxs-lookup"><span data-stu-id="6244b-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="6244b-112">1\.</span><span class="sxs-lookup"><span data-stu-id="6244b-112">1\.</span></span> <span data-ttu-id="6244b-113">Nastavte **webové aplikace / webové rozhraní API** na **ano**.</span><span class="sxs-lookup"><span data-stu-id="6244b-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="6244b-114">2\.</span><span class="sxs-lookup"><span data-stu-id="6244b-114">2\.</span></span> <span data-ttu-id="6244b-115">Nastavit **povolit implicitní tok** na **Ano**.</span><span class="sxs-lookup"><span data-stu-id="6244b-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="6244b-116">3\.</span><span class="sxs-lookup"><span data-stu-id="6244b-116">3\.</span></span> <span data-ttu-id="6244b-117">Přidejte adresu `https://localhost:5001/authentication/login-callback`URL **odpovědi** společnosti .</span><span class="sxs-lookup"><span data-stu-id="6244b-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="6244b-118">Zaznamenejte ID aplikace (ID `11111111-1111-1111-1111-111111111111`klienta) (například).</span><span class="sxs-lookup"><span data-stu-id="6244b-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="6244b-119">[Vytvoření toků](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; uživatelů: Vytvoření toku registrace a přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="6244b-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="6244b-120">Vyberte minimálně uživatelský atribut**Zobrazení názvu** **aplikace,** >  `LoginDisplay` chcete-li `context.User.Identity.Name` naplnit v komponentě (*Shared/LoginDisplay.razor).*</span><span class="sxs-lookup"><span data-stu-id="6244b-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="6244b-121">Zaznamenejte název uživatelského toku registrace a přihlášení `B2C_1_signupsignin`vytvořený pro aplikaci (například).</span><span class="sxs-lookup"><span data-stu-id="6244b-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="6244b-122">Nahraďte zástupné symboly v následujícím příkazu informacemi zaznamenanými dříve a proveďte příkaz v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="6244b-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="6244b-123">Chcete-li určit výstupní umístění, které vytvoří složku projektu, pokud neexistuje, zahrňte do `-o BlazorSample`příkazu možnost výstupu s cestou (například ).</span><span class="sxs-lookup"><span data-stu-id="6244b-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6244b-124">Název složky se také stane součástí názvu projektu.</span><span class="sxs-lookup"><span data-stu-id="6244b-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6244b-125">Ověřovací balíček</span><span class="sxs-lookup"><span data-stu-id="6244b-125">Authentication package</span></span>

<span data-ttu-id="6244b-126">Když je aplikace vytvořena pro použití individuálního`IndividualB2C`účtu B2C ( ), aplikace automaticky`Microsoft.Authentication.WebAssembly.Msal`obdrží odkaz na balíček pro [knihovnu Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ( ).</span><span class="sxs-lookup"><span data-stu-id="6244b-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6244b-127">Balíček obsahuje sadu primitiv, které pomáhají aplikaci ověřovat uživatele a získat tokeny pro volání chráněných api.</span><span class="sxs-lookup"><span data-stu-id="6244b-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6244b-128">Pokud přidáváte ověřování do aplikace, přidejte balíček ručně do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="6244b-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="6244b-129">Nahraďte `{VERSION}` v předchozím odkazu na `Microsoft.AspNetCore.Blazor.Templates` balíček verzí <xref:blazor/get-started> balíčku uvedenou v článku.</span><span class="sxs-lookup"><span data-stu-id="6244b-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="6244b-130">Balíček `Microsoft.Authentication.WebAssembly.Msal` transitively `Microsoft.AspNetCore.Components.WebAssembly.Authentication` přidá balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="6244b-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6244b-131">Podpora ověřovací služby</span><span class="sxs-lookup"><span data-stu-id="6244b-131">Authentication service support</span></span>

<span data-ttu-id="6244b-132">Podpora pro ověřování uživatelů je registrována v `AddMsalAuthentication` kontejneru služby `Microsoft.Authentication.WebAssembly.Msal` s metodou rozšíření poskytované balíček.</span><span class="sxs-lookup"><span data-stu-id="6244b-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6244b-133">Tato metoda nastaví všechny služby potřebné pro interakci aplikace s poskytovatelem identity (IP).</span><span class="sxs-lookup"><span data-stu-id="6244b-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6244b-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6244b-134">*Program.cs*:</span></span>

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

<span data-ttu-id="6244b-135">Metoda `AddMsalAuthentication` přijímá zpětné volání ke konfiguraci parametrů potřebných k ověření aplikace.</span><span class="sxs-lookup"><span data-stu-id="6244b-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6244b-136">Hodnoty potřebné pro konfiguraci aplikace lze získat z konfigurace Azure Portal AAD při registraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="6244b-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="6244b-137">Obory přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="6244b-137">Access token scopes</span></span>

<span data-ttu-id="6244b-138">Šablona Blazor WebAssembly automaticky nekonfiguruje aplikaci tak, aby požadovala přístupový token pro zabezpečené rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6244b-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6244b-139">Chcete-li zřídit token jako součást toku přihlášení, přidejte obor do `MsalProviderOptions`výchozího oboru přístupového tokenu :</span><span class="sxs-lookup"><span data-stu-id="6244b-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6244b-140">Pokud portál Azure poskytuje identifikátor URI oboru a **aplikace vyvolá neošetřenou výjimku,** když obdrží *neoprávněnou odpověď 401* z rozhraní API, zkuste použít identifikátor URI oboru, který neobsahuje schéma a hostitele.</span><span class="sxs-lookup"><span data-stu-id="6244b-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6244b-141">Portál Azure může například poskytovat jeden z následujících formátů identifikátoru URI oboru:</span><span class="sxs-lookup"><span data-stu-id="6244b-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6244b-142">Zadej oblast URI oboru bez schématu a hostitele:</span><span class="sxs-lookup"><span data-stu-id="6244b-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6244b-143">Další informace naleznete v tématu <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="6244b-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="6244b-144">Importuje soubor</span><span class="sxs-lookup"><span data-stu-id="6244b-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6244b-145">Indexová stránka</span><span class="sxs-lookup"><span data-stu-id="6244b-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6244b-146">Součást aplikace</span><span class="sxs-lookup"><span data-stu-id="6244b-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6244b-147">Komponenta RedirectToLogin</span><span class="sxs-lookup"><span data-stu-id="6244b-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6244b-148">Komponenta LoginDisplay</span><span class="sxs-lookup"><span data-stu-id="6244b-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6244b-149">Ověřovací komponenta</span><span class="sxs-lookup"><span data-stu-id="6244b-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6244b-150">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6244b-150">Additional resources</span></span>

* [<span data-ttu-id="6244b-151">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="6244b-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="6244b-152">Kurz: Vytvoření tenanta Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="6244b-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="6244b-153">Dokumentace k platformě Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="6244b-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
