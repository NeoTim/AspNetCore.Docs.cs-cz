---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 1328bcbce3e6e4786f9d410d1f28f309dc9d2722
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750548"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="8e402-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e402-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="8e402-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8e402-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8e402-105">V lednu 2019 Google začali [vypnout](https://developers.google.com/+/api-shutdown) Google +. Přihlaste se a vývojáři musí přesunout na znaménko Google nová v systému podle dne.</span><span class="sxs-lookup"><span data-stu-id="8e402-105">In January 2019 Google started to [shut down](https://developers.google.com/+/api-shutdown) Google+ sign in and developers must move to a new Google sign in system by March.</span></span> <span data-ttu-id="8e402-106">ASP.NET Core 2.1 a 2,2 balíčky pro ověřování Google se aktualizují v únoru s ohledem změny.</span><span class="sxs-lookup"><span data-stu-id="8e402-106">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication will be updated in February to accommodate the changes.</span></span> <span data-ttu-id="8e402-107">Další informace a dočasné opatření pro ASP.NET Core najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="8e402-107">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="8e402-108">V tomto kurzu má aktualizované a přinášejí nové procesu instalace.</span><span class="sxs-lookup"><span data-stu-id="8e402-108">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="8e402-109">V tomto kurzu se dozvíte, jak povolit uživatelům přihlásit se přes jejich účet Google pomocí ASP.NET Core 2.2 projektu vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8e402-109">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="8e402-110">Vytvořit ID projektu a klientské konzole rozhraní API Google</span><span class="sxs-lookup"><span data-stu-id="8e402-110">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="8e402-111">Přejděte do [integraci Google přihlásit do své webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **konfigurace projektu A**.</span><span class="sxs-lookup"><span data-stu-id="8e402-111">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="8e402-112">V **konfigurace klienta OAuth** dialogového okna, vyberte **webový server**.</span><span class="sxs-lookup"><span data-stu-id="8e402-112">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="8e402-113">V **identifikátory URI pro přesměrování autorizovaní** pole pro zadání textu, nastavte identifikátor URI pro přesměrování.</span><span class="sxs-lookup"><span data-stu-id="8e402-113">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="8e402-114">Třeba `https://localhost:5001/signin-google`.</span><span class="sxs-lookup"><span data-stu-id="8e402-114">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="8e402-115">Uložit **ID klienta** a **tajný kód klienta**.</span><span class="sxs-lookup"><span data-stu-id="8e402-115">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="8e402-116">Při nasazování webu, registrovat novou veřejnou adresu url z **konzoly Google**.</span><span class="sxs-lookup"><span data-stu-id="8e402-116">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="8e402-117">Store Google ClientID a ClientSecret</span><span class="sxs-lookup"><span data-stu-id="8e402-117">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="8e402-118">Citlivá nastavení, například ke službě Google Store `Client ID` a `Client Secret` s [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8e402-118">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="8e402-119">Pro účely tohoto kurzu se název tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="8e402-119">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8e402-120">Můžete spravovat vaše přihlašovací údaje rozhraní API a jeho použití v [rozhraní API konzoly](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="8e402-120">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="8e402-121">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="8e402-121">Configure Google authentication</span></span>

<span data-ttu-id="8e402-122">Přidat služby Google `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8e402-122">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="8e402-123">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="8e402-123">Sign in with Google</span></span>

* <span data-ttu-id="8e402-124">Spusťte aplikaci a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="8e402-124">Run the app and click **Log in**.</span></span> <span data-ttu-id="8e402-125">Zobrazí se možnost přihlásit se přes Google.</span><span class="sxs-lookup"><span data-stu-id="8e402-125">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="8e402-126">Klikněte na tlačítko **Google** tlačítka, který přesměruje do Googlu pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="8e402-126">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="8e402-127">Jakmile zadáte svoje přihlašovací údaje Google, budete přesměrováni zpět na web.</span><span class="sxs-lookup"><span data-stu-id="8e402-127">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="8e402-128">Zobrazit [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="8e402-128">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="8e402-129">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="8e402-129">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="8e402-130">Změnit výchozí identifikátor URI zpětného volání</span><span class="sxs-lookup"><span data-stu-id="8e402-130">Change the default callback URI</span></span>

<span data-ttu-id="8e402-131">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="8e402-131">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="8e402-132">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="8e402-132">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="8e402-133">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="8e402-133">Troubleshooting</span></span>

* <span data-ttu-id="8e402-134">Pokud se nezobrazují žádné chyby přihlášení nebude fungovat, přepněte do režimu vývoj snazší ladit problém.</span><span class="sxs-lookup"><span data-stu-id="8e402-134">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="8e402-135">Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, při pokusu o ověření za následek *ArgumentException: Musí být Zadaná možnost "SignInScheme"*.</span><span class="sxs-lookup"><span data-stu-id="8e402-135">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="8e402-136">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="8e402-136">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="8e402-137">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="8e402-137">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="8e402-138">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="8e402-138">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8e402-139">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8e402-139">Next steps</span></span>

* <span data-ttu-id="8e402-140">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="8e402-140">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="8e402-141">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8e402-141">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="8e402-142">Po publikování aplikace do Azure, resetovat `ClientSecret` v konzole rozhraní API Google.</span><span class="sxs-lookup"><span data-stu-id="8e402-142">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="8e402-143">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="8e402-143">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="8e402-144">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8e402-144">The configuration system is set up to read keys from environment variables.</span></span>
