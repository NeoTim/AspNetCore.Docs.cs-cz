---
title: Nastavení externího přihlášení účtu Microsoft s ASP.NET Core
author: rick-anderson
description: Tato ukázka ukazuje integraci ověřování uživatelů účtu Microsoft do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277246"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="33a6e-103">Nastavení externího přihlášení účtu Microsoft s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33a6e-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="33a6e-104">[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33a6e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="33a6e-105">V této ukázce se zobrazí, jak uživatelům umožnit přihlášení pomocí účtu Microsoft pomocí projektu ASP.NET Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="33a6e-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="33a6e-106">Vytvoření aplikace na portálu Microsoft Developer Portal</span><span class="sxs-lookup"><span data-stu-id="33a6e-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="33a6e-107">Přidejte do projektu balíček [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="33a6e-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="33a6e-108">Přejděte na stránku [Registrace aplikací Azure –](https://go.microsoft.com/fwlink/?linkid=2083908) registrace aplikací a vytvořte nebo přihlaste účet Microsoft:</span><span class="sxs-lookup"><span data-stu-id="33a6e-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="33a6e-109">Pokud nemáte účet Microsoft, vyberte **Vytvořit jeden**.</span><span class="sxs-lookup"><span data-stu-id="33a6e-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="33a6e-110">Po přihlášení budete přesměrováni na stránku **Registrace aplikací:**</span><span class="sxs-lookup"><span data-stu-id="33a6e-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="33a6e-111">Vybrat **novou registraci**</span><span class="sxs-lookup"><span data-stu-id="33a6e-111">Select **New registration**</span></span>
* <span data-ttu-id="33a6e-112">Zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="33a6e-112">Enter a **Name**.</span></span>
* <span data-ttu-id="33a6e-113">Vyberte možnost pro **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="33a6e-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="33a6e-114">V části **Přesměrování identifikátoru**URI `/signin-microsoft` zadejte adresu URL vývoje s připojeným.</span><span class="sxs-lookup"><span data-stu-id="33a6e-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="33a6e-115">Například, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="33a6e-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="33a6e-116">Schéma ověřování společnosti Microsoft nakonfigurované dále `/signin-microsoft` v této ukázce bude automaticky zpracovávat požadavky na cestě k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="33a6e-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="33a6e-117">Vybrat **registr**</span><span class="sxs-lookup"><span data-stu-id="33a6e-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="33a6e-118">Vytvořit tajný klíč klienta</span><span class="sxs-lookup"><span data-stu-id="33a6e-118">Create client secret</span></span>

* <span data-ttu-id="33a6e-119">V levém podokně vyberte **certifikáty & tajných kódů**.</span><span class="sxs-lookup"><span data-stu-id="33a6e-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="33a6e-120">V části **Tajné klíče klienta**vyberte **Nový tajný klíč klienta.**</span><span class="sxs-lookup"><span data-stu-id="33a6e-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="33a6e-121">Přidejte popis tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="33a6e-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="33a6e-122">Vyberte tlačítko **Přidat.**</span><span class="sxs-lookup"><span data-stu-id="33a6e-122">Select the **Add** button.</span></span>

* <span data-ttu-id="33a6e-123">V části **Tajné klíče klienta**zkopírujte hodnotu tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="33a6e-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="33a6e-124">Segment `/signin-microsoft` URI je nastaven jako výchozí zpětné volání zprostředkovatele ověřování společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="33a6e-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="33a6e-125">Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="33a6e-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="33a6e-126">Uložení ID klienta Microsoftu a tajného klíče</span><span class="sxs-lookup"><span data-stu-id="33a6e-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="33a6e-127">Uklápěte citlivá nastavení, jako je ID klienta Microsoft a tajné hodnoty, pomocí [Správce tajných barev](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="33a6e-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="33a6e-128">Pro tuto ukázku použijte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="33a6e-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="33a6e-129">Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="33a6e-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="33a6e-130">Uložte citlivá nastavení v místním tajném úložišti s tajnými klíči `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="33a6e-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="33a6e-131">Konfigurace ověřování účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="33a6e-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="33a6e-132">Přidejte službu Účet `Startup.ConfigureServices`Microsoft do :</span><span class="sxs-lookup"><span data-stu-id="33a6e-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="33a6e-133">Další informace o možnostech konfigurace podporovaných ověřováním účtu Microsoft najdete v tématu Odkaz na rozhraní [API MicrosoftAccountOptions.](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)</span><span class="sxs-lookup"><span data-stu-id="33a6e-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="33a6e-134">To lze použít k vyžádání různých informací o uživateli.</span><span class="sxs-lookup"><span data-stu-id="33a6e-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="33a6e-135">Přihlášení pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="33a6e-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="33a6e-136">Spusťte aplikaci a klepněte na tlačítko **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="33a6e-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="33a6e-137">Zobrazí se možnost přihlásit se pomocí společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="33a6e-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="33a6e-138">Po kliknutí na společnost Microsoft budete přesměrováni na společnost Microsoft k ověření.</span><span class="sxs-lookup"><span data-stu-id="33a6e-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="33a6e-139">Po přihlášení pomocí účtu Microsoft budete vyzváni k tomu, abyste aplikaci dovolili přístup k vašim údajům:</span><span class="sxs-lookup"><span data-stu-id="33a6e-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="33a6e-140">Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="33a6e-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="33a6e-141">Nyní jste přihlášeni pomocí pověření společnosti Microsoft:</span><span class="sxs-lookup"><span data-stu-id="33a6e-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="33a6e-142">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="33a6e-142">Troubleshooting</span></span>

* <span data-ttu-id="33a6e-143">Pokud vás poskytovatel účtu Microsoft přesměruje na přihlašovací chybovou stránku, poznamenejte `#` si parametry řetězce názvu chyby a popisu dotazu přímo za (hashtag) v uri.</span><span class="sxs-lookup"><span data-stu-id="33a6e-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="33a6e-144">Přestože se zdá, že chybová zpráva označuje problém s ověřováním společnosti Microsoft, nejčastější příčinou je, že aplikace Uri neodpovídá žádné z **identifikátorů URI přesměrování** zadaných pro **webovou** platformu.</span><span class="sxs-lookup"><span data-stu-id="33a6e-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="33a6e-145">Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*.</span><span class="sxs-lookup"><span data-stu-id="33a6e-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="33a6e-146">Šablona projektu použitá v této ukázce zajišťuje, že se tak děje.</span><span class="sxs-lookup"><span data-stu-id="33a6e-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="33a6e-147">Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.*</span><span class="sxs-lookup"><span data-stu-id="33a6e-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="33a6e-148">Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.</span><span class="sxs-lookup"><span data-stu-id="33a6e-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="33a6e-149">Další kroky</span><span class="sxs-lookup"><span data-stu-id="33a6e-149">Next steps</span></span>

* <span data-ttu-id="33a6e-150">Tento článek ukazuje, jak můžete ověřit s Microsoft.</span><span class="sxs-lookup"><span data-stu-id="33a6e-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="33a6e-151">Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="33a6e-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="33a6e-152">Po publikování webu do webové aplikace Azure vytvořte nové tajné kódy klientů na portálu Microsoft Developer Portal.</span><span class="sxs-lookup"><span data-stu-id="33a6e-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="33a6e-153">Nastavte `Authentication:Microsoft:ClientId` nastavení `Authentication:Microsoft:ClientSecret` aplikace a jako na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="33a6e-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="33a6e-154">Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="33a6e-154">The configuration system is set up to read keys from environment variables.</span></span>
