---
title: Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka demonstruje integraci účet Microsoft ověřování uživatelů do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 731a17085a1fd01852bb3fe2f0fc9f3e7a9ac30f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775658"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="cb709-103">Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb709-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="cb709-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb709-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb709-105">V této ukázce se dozvíte, jak uživatelům povolit, aby se přihlásili pomocí své pracovní, školní nebo osobní účet Microsoft pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb709-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="cb709-106">Vytvoření aplikace na portálu Microsoftu pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="cb709-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="cb709-107">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) .</span><span class="sxs-lookup"><span data-stu-id="cb709-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="cb709-108">Přejděte na stránku [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a vytvořte nebo Přihlaste se ke účet Microsoft:</span><span class="sxs-lookup"><span data-stu-id="cb709-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="cb709-109">Pokud nemáte účet Microsoft, vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="cb709-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="cb709-110">Po přihlášení budete přesměrováni na stránku **Registrace aplikací** :</span><span class="sxs-lookup"><span data-stu-id="cb709-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="cb709-111">Vybrat **novou registraci**</span><span class="sxs-lookup"><span data-stu-id="cb709-111">Select **New registration**</span></span>
* <span data-ttu-id="cb709-112">Zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="cb709-112">Enter a **Name**.</span></span>
* <span data-ttu-id="cb709-113">Vyberte možnost pro **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="cb709-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="cb709-114">V části **identifikátor URI přesměrování**zadejte adresu URL pro `/signin-microsoft` vývoj s připojením.</span><span class="sxs-lookup"><span data-stu-id="cb709-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="cb709-115">Například, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="cb709-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="cb709-116">Schéma ověřování společnosti Microsoft nakonfigurované později v této ukázce bude automaticky zpracovávat požadavky `/signin-microsoft` na trase za účelem implementace toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="cb709-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="cb709-117">Vybrat **registraci**</span><span class="sxs-lookup"><span data-stu-id="cb709-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="cb709-118">Vytvořit tajný klíč klienta</span><span class="sxs-lookup"><span data-stu-id="cb709-118">Create client secret</span></span>

* <span data-ttu-id="cb709-119">V levém podokně vyberte **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="cb709-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="cb709-120">V části **tajné klíče klienta**vyberte **nový tajný klíč klienta** .</span><span class="sxs-lookup"><span data-stu-id="cb709-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="cb709-121">Přidejte popis pro tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="cb709-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="cb709-122">Vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="cb709-122">Select the **Add** button.</span></span>

* <span data-ttu-id="cb709-123">V části **tajné klíče klienta**Zkopírujte hodnotu tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="cb709-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="cb709-124">Segment `/signin-microsoft` identifikátoru URI je nastaven jako výchozí zpětné volání poskytovatele ověřování společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cb709-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="cb709-125">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware ověřování od společnosti Microsoft prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="cb709-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="cb709-126">Uložte si ID a tajný kód klienta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="cb709-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="cb709-127">Uložte citlivá nastavení, jako je ID klienta Microsoftu a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů.</span><span class="sxs-lookup"><span data-stu-id="cb709-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cb709-128">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="cb709-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="cb709-129">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="cb709-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="cb709-130">Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných `Authentication:Microsoft:ClientId` klíčů `Authentication:Microsoft:ClientSecret`a:</span><span class="sxs-lookup"><span data-stu-id="cb709-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="cb709-131">Konfigurace ověřování účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="cb709-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="cb709-132">Přidejte službu účtu Microsoft do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="cb709-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="cb709-133">Další informace o možnostech Konfigurace podporovaných ověřováním účtů Microsoft najdete v referenčních informacích k rozhraní [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API.</span><span class="sxs-lookup"><span data-stu-id="cb709-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="cb709-134">Dá se použít k vyžádání různých informací o uživateli.</span><span class="sxs-lookup"><span data-stu-id="cb709-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="cb709-135">Účet Přihlásit se účtem Microsoft</span><span class="sxs-lookup"><span data-stu-id="cb709-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="cb709-136">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="cb709-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="cb709-137">Zobrazí se možnost přihlásit se s Microsoftem.</span><span class="sxs-lookup"><span data-stu-id="cb709-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="cb709-138">Když kliknete na Microsoft, budete přesměrováni na Microsoft pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="cb709-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="cb709-139">Po přihlášení pomocí účtu Microsoft se zobrazí výzva, abyste aplikaci dali přístup k vašim informacím:</span><span class="sxs-lookup"><span data-stu-id="cb709-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="cb709-140">Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="cb709-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="cb709-141">Nyní jste přihlášeni pomocí přihlašovacích údajů Microsoftu:</span><span class="sxs-lookup"><span data-stu-id="cb709-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="cb709-142">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="cb709-142">Troubleshooting</span></span>

* <span data-ttu-id="cb709-143">Pokud vám poskytovatel účtu Microsoft přesměruje na chybovou stránku pro přihlášení, poznamenejte si parametry řetězce chyby a popis v parametrech řetězce dotazu `#` přímo za (hashtag) v identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="cb709-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="cb709-144">I když se chybová zpráva jeví jako problém s ověřováním Microsoftu, Nejběžnější příčinou je, že identifikátor URI vaší aplikace neodpovídá žádnému identifikátoru **URI přesměrování** , který je zadaný pro **webovou** platformu.</span><span class="sxs-lookup"><span data-stu-id="cb709-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="cb709-145">Pokud Identity není nakonfigurováno `services.AddIdentity` voláním `ConfigureServices`v, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="cb709-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="cb709-146">Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.</span><span class="sxs-lookup"><span data-stu-id="cb709-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="cb709-147">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při zpracování chyby žádosti se zobrazí *operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="cb709-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="cb709-148">Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.</span><span class="sxs-lookup"><span data-stu-id="cb709-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb709-149">Další kroky</span><span class="sxs-lookup"><span data-stu-id="cb709-149">Next steps</span></span>

* <span data-ttu-id="cb709-150">Tento článek ukazuje, jak se dá ověřit u Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="cb709-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="cb709-151">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb709-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="cb709-152">Po publikování webu do webové aplikace Azure vytvořte nové tajné klíče klienta na portálu Microsoftu pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="cb709-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="cb709-153">Nastavte nastavení `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="cb709-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="cb709-154">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="cb709-154">The configuration system is set up to read keys from environment variables.</span></span>
