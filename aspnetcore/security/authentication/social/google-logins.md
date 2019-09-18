---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082476"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="7b960-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b960-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="7b960-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7b960-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7b960-105">[Starší verze Google + API se ukončily od 7. března 2019](https://developers.google.com/+/api-shutdown).</span><span class="sxs-lookup"><span data-stu-id="7b960-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="7b960-106">Google + Signing a vývojáři musí přejít na nový systém přihlašování Google.</span><span class="sxs-lookup"><span data-stu-id="7b960-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="7b960-107">Balíčky ASP.NET Core 2,1 a 2,2 pro ověřování Google se aktualizují, aby se vešly změny.</span><span class="sxs-lookup"><span data-stu-id="7b960-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="7b960-108">Další informace a dočasné omezení pro ASP.NET Core najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore/issues/6486).</span><span class="sxs-lookup"><span data-stu-id="7b960-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="7b960-109">Tento kurz byl aktualizován pomocí nového procesu instalace.</span><span class="sxs-lookup"><span data-stu-id="7b960-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="7b960-110">V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 2,2 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7b960-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="7b960-111">Vytvoření projektu konzoly rozhraní Google API a ID klienta</span><span class="sxs-lookup"><span data-stu-id="7b960-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="7b960-112">Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **Konfigurovat projekt**.</span><span class="sxs-lookup"><span data-stu-id="7b960-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="7b960-113">V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.</span><span class="sxs-lookup"><span data-stu-id="7b960-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="7b960-114">Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="7b960-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="7b960-115">Třeba `https://localhost:5001/signin-google`.</span><span class="sxs-lookup"><span data-stu-id="7b960-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="7b960-116">Uložte **ID klienta** a **tajný klíč klienta**.</span><span class="sxs-lookup"><span data-stu-id="7b960-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="7b960-117">Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.</span><span class="sxs-lookup"><span data-stu-id="7b960-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="7b960-118">Store Google ClientID a ClientSecret</span><span class="sxs-lookup"><span data-stu-id="7b960-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="7b960-119">Uložte citlivá nastavení, jako je `Client ID` Google `Client Secret` a pomocí [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7b960-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="7b960-120">Pro účely tohoto kurzu pojmenujte tokeny `Authentication:Google:ClientId` a: `Authentication:Google:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="7b960-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7b960-121">Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="7b960-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="7b960-122">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="7b960-122">Configure Google authentication</span></span>

<span data-ttu-id="7b960-123">Přidat službu Google do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7b960-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="7b960-124">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="7b960-124">Sign in with Google</span></span>

* <span data-ttu-id="7b960-125">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="7b960-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="7b960-126">Zobrazí se možnost přihlásit se přes Google.</span><span class="sxs-lookup"><span data-stu-id="7b960-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="7b960-127">Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="7b960-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="7b960-128">Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.</span><span class="sxs-lookup"><span data-stu-id="7b960-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="7b960-129">Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraníAPI.<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions></span><span class="sxs-lookup"><span data-stu-id="7b960-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="7b960-130">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="7b960-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="7b960-131">Změna výchozího identifikátoru URI zpětného volání</span><span class="sxs-lookup"><span data-stu-id="7b960-131">Change the default callback URI</span></span>

<span data-ttu-id="7b960-132">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="7b960-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="7b960-133">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="7b960-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7b960-134">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="7b960-134">Troubleshooting</span></span>

* <span data-ttu-id="7b960-135">Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.</span><span class="sxs-lookup"><span data-stu-id="7b960-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="7b960-136">Pokud není identita nakonfigurovaná `services.AddIdentity` voláním `ConfigureServices`v, zkuste ověřit výsledky v *ArgumentException: Je nutné zadat*možnost SignInScheme.</span><span class="sxs-lookup"><span data-stu-id="7b960-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7b960-137">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="7b960-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="7b960-138">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="7b960-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7b960-139">Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.</span><span class="sxs-lookup"><span data-stu-id="7b960-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7b960-140">Další postup</span><span class="sxs-lookup"><span data-stu-id="7b960-140">Next steps</span></span>

* <span data-ttu-id="7b960-141">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="7b960-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="7b960-142">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7b960-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="7b960-143">Jakmile aplikaci publikujete do Azure, resetujte ji `ClientSecret` v konzole rozhraní Google API.</span><span class="sxs-lookup"><span data-stu-id="7b960-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="7b960-144">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="7b960-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="7b960-145">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="7b960-145">The configuration system is set up to read keys from environment variables.</span></span>
