---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 44c79b3279db7946b6d89a726bd3f5acfb5f51af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64902061"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="50712-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50712-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="50712-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50712-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="50712-105">[Starší verze rozhraní Google + API byla ukončena od 7. března 2019](https://developers.google.com/+/api-shutdown).</span><span class="sxs-lookup"><span data-stu-id="50712-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="50712-106">Google +. Přihlaste se a vývojáři musí přesunout na znaménko Google nová v systému.</span><span class="sxs-lookup"><span data-stu-id="50712-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="50712-107">ASP.NET Core 2.1, tak i 2.2 balíčky pro ověřování Google byly aktualizovány s ohledem změny.</span><span class="sxs-lookup"><span data-stu-id="50712-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="50712-108">Další informace a dočasné opatření pro ASP.NET Core najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="50712-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="50712-109">V tomto kurzu má aktualizované a přinášejí nové procesu instalace.</span><span class="sxs-lookup"><span data-stu-id="50712-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="50712-110">V tomto kurzu se dozvíte, jak povolit uživatelům přihlásit se přes jejich účet Google pomocí ASP.NET Core 2.2 projektu vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="50712-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="50712-111">Vytvořit ID projektu a klientské konzole rozhraní API Google</span><span class="sxs-lookup"><span data-stu-id="50712-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="50712-112">Přejděte do [integraci Google přihlásit do své webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **konfigurace projektu A**.</span><span class="sxs-lookup"><span data-stu-id="50712-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="50712-113">V **konfigurace klienta OAuth** dialogového okna, vyberte **webový server**.</span><span class="sxs-lookup"><span data-stu-id="50712-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="50712-114">V **identifikátory URI pro přesměrování autorizovaní** pole pro zadání textu, nastavte identifikátor URI pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="50712-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="50712-115">Třeba `https://localhost:5001/signin-google`.</span><span class="sxs-lookup"><span data-stu-id="50712-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="50712-116">Uložit **ID klienta** a **tajný kód klienta**.</span><span class="sxs-lookup"><span data-stu-id="50712-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="50712-117">Při nasazování webu, registrovat novou veřejnou adresu url z **konzoly Google**.</span><span class="sxs-lookup"><span data-stu-id="50712-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="50712-118">Store Google ClientID a ClientSecret</span><span class="sxs-lookup"><span data-stu-id="50712-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="50712-119">Citlivá nastavení, například ke službě Google Store `Client ID` a `Client Secret` s [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="50712-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="50712-120">Pro účely tohoto kurzu se název tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="50712-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="50712-121">Můžete spravovat vaše přihlašovací údaje rozhraní API a jeho použití v [rozhraní API konzoly](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="50712-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="50712-122">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="50712-122">Configure Google authentication</span></span>

<span data-ttu-id="50712-123">Přidat služby Google `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="50712-123">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="50712-124">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="50712-124">Sign in with Google</span></span>

* <span data-ttu-id="50712-125">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="50712-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="50712-126">Zobrazí se možnost přihlásit se přes Google.</span><span class="sxs-lookup"><span data-stu-id="50712-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="50712-127">Klikněte na tlačítko **Google** tlačítka, který přesměruje do Googlu pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="50712-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="50712-128">Jakmile zadáte svoje přihlašovací údaje Google, budete přesměrováni zpět na web.</span><span class="sxs-lookup"><span data-stu-id="50712-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="50712-129">Zobrazit [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="50712-129">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="50712-130">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="50712-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="50712-131">Změnit výchozí identifikátor URI zpětného volání</span><span class="sxs-lookup"><span data-stu-id="50712-131">Change the default callback URI</span></span>

<span data-ttu-id="50712-132">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="50712-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="50712-133">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="50712-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="50712-134">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="50712-134">Troubleshooting</span></span>

* <span data-ttu-id="50712-135">Pokud se nezobrazují žádné chyby přihlášení nebude fungovat, přepněte do režimu vývoj snazší ladit problém.</span><span class="sxs-lookup"><span data-stu-id="50712-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="50712-136">Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, při pokusu o ověření za následek *ArgumentException: Musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="50712-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="50712-137">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="50712-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="50712-138">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="50712-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="50712-139">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="50712-139">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="50712-140">Další kroky</span><span class="sxs-lookup"><span data-stu-id="50712-140">Next steps</span></span>

* <span data-ttu-id="50712-141">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="50712-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="50712-142">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="50712-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="50712-143">Po publikování aplikace do Azure, resetovat `ClientSecret` v konzole rozhraní API Google.</span><span class="sxs-lookup"><span data-stu-id="50712-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="50712-144">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="50712-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="50712-145">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="50712-145">The configuration system is set up to read keys from environment variables.</span></span>
