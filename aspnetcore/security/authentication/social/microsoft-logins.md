---
title: Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka demonstruje integraci účet Microsoft ověřování uživatelů do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: bd75efb1d7ce08538d1a67be74d2f40f3964614f
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989751"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="c3a0e-103">Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3a0e-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="c3a0e-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c3a0e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c3a0e-105">V této ukázce se dozvíte, jak uživatelům povolit, aby se k účet Microsoft přihlásili pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c3a0e-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="c3a0e-106">Vytvoření aplikace na portálu Microsoftu pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="c3a0e-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="c3a0e-107">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) .</span><span class="sxs-lookup"><span data-stu-id="c3a0e-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="c3a0e-108">Přejděte na stránku [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a vytvořte nebo Přihlaste se ke účet Microsoft:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="c3a0e-109">Pokud nemáte účet Microsoft, vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="c3a0e-110">Po přihlášení budete přesměrováni na stránku **Registrace aplikací** :</span><span class="sxs-lookup"><span data-stu-id="c3a0e-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="c3a0e-111">Vybrat **novou registraci**</span><span class="sxs-lookup"><span data-stu-id="c3a0e-111">Select **New registration**</span></span>
* <span data-ttu-id="c3a0e-112">Zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-112">Enter a **Name**.</span></span>
* <span data-ttu-id="c3a0e-113">Vyberte možnost pro **podporované typy účtů**.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="c3a0e-114">V části **identifikátor URI pro přesměrování**zadejte adresu URL pro vývoj s `/signin-microsoft` připojena.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="c3a0e-115">například `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="c3a0e-116">Schéma ověřování společnosti Microsoft nakonfigurované později v této ukázce bude automaticky zpracovávat požadavky v cestě `/signin-microsoft` k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="c3a0e-117">Vybrat **registraci**</span><span class="sxs-lookup"><span data-stu-id="c3a0e-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="c3a0e-118">Vytvořit tajný klíč klienta</span><span class="sxs-lookup"><span data-stu-id="c3a0e-118">Create client secret</span></span>

* <span data-ttu-id="c3a0e-119">V levém podokně vyberte **certifikáty & tajných**kódů.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="c3a0e-120">V části **tajné klíče klienta**vyberte **nový tajný klíč klienta** .</span><span class="sxs-lookup"><span data-stu-id="c3a0e-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="c3a0e-121">Přidejte popis pro tajný klíč klienta.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="c3a0e-122">Vyberte tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="c3a0e-122">Select the **Add** button.</span></span>

* <span data-ttu-id="c3a0e-123">V části **tajné klíče klienta**Zkopírujte hodnotu tajného klíče klienta.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="c3a0e-124">Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětné volání poskytovatele ověřování společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="c3a0e-125">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware ověřování od společnosti Microsoft prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .</span><span class="sxs-lookup"><span data-stu-id="c3a0e-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="c3a0e-126">Uložte si ID a tajný kód klienta Microsoft.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="c3a0e-127">Uložte citlivá nastavení, jako je ID klienta Microsoftu a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="c3a0e-128">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="c3a0e-129">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="c3a0e-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="c3a0e-130">Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="c3a0e-131">Konfigurace ověřování účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="c3a0e-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="c3a0e-132">Přidejte službu účtu Microsoft do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="c3a0e-133">Další informace o možnostech Konfigurace podporovaných ověřováním účtů Microsoft najdete v referenčních informacích k rozhraní [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="c3a0e-134">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="c3a0e-135">Účet Přihlásit se účtem Microsoft</span><span class="sxs-lookup"><span data-stu-id="c3a0e-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="c3a0e-136">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="c3a0e-137">Zobrazí se možnost přihlásit se s Microsoftem.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="c3a0e-138">Když kliknete na Microsoft, budete přesměrováni na Microsoft pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="c3a0e-139">Po přihlášení pomocí účtu Microsoft se zobrazí výzva, abyste aplikaci dali přístup k vašim informacím:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="c3a0e-140">Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="c3a0e-141">Nyní jste přihlášeni pomocí přihlašovacích údajů Microsoftu:</span><span class="sxs-lookup"><span data-stu-id="c3a0e-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="c3a0e-142">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="c3a0e-142">Troubleshooting</span></span>

* <span data-ttu-id="c3a0e-143">Pokud vám poskytovatel účtu Microsoft přesměruje na chybovou stránku pro přihlášení, poznamenejte si parametry řetězce chyby a popis v parametrech řetězce dotazu přímo za `#` (hashtag) v identifikátoru URI.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="c3a0e-144">I když se chybová zpráva jeví jako problém s ověřováním Microsoftu, Nejběžnější příčinou je, že identifikátor URI vaší aplikace neodpovídá žádnému identifikátoru **URI přesměrování** , který je zadaný pro **webovou** platformu.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="c3a0e-145">Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="c3a0e-146">Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="c3a0e-147">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při zpracování chyby žádosti se zobrazí *operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="c3a0e-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="c3a0e-148">Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c3a0e-149">Další kroky</span><span class="sxs-lookup"><span data-stu-id="c3a0e-149">Next steps</span></span>

* <span data-ttu-id="c3a0e-150">Tento článek ukazuje, jak se dá ověřit u Microsoftu.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="c3a0e-151">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="c3a0e-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="c3a0e-152">Po publikování webu do webové aplikace Azure vytvořte nové tajné klíče klienta na portálu Microsoftu pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="c3a0e-153">Nastavte `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="c3a0e-154">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="c3a0e-154">The configuration system is set up to read keys from environment variables.</span></span>
