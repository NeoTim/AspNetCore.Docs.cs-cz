---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/30/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 83f45143eca1be43410880bfd875a3fce1d2e9c9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667508"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="ecb1c-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecb1c-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="ecb1c-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ecb1c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ecb1c-105">V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ecb1c-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="ecb1c-106">Vytvoření projektu konzoly rozhraní Google API a ID klienta</span><span class="sxs-lookup"><span data-stu-id="ecb1c-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="ecb1c-107">Nainstalujte [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="ecb1c-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="ecb1c-108">Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **Konfigurovat projekt**.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="ecb1c-109">V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="ecb1c-110">Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="ecb1c-111">Například `https://localhost:44312/signin-google`.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="ecb1c-112">Uložte **ID klienta** a **tajný klíč klienta**.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="ecb1c-113">Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="ecb1c-114">Store Google ClientID a ClientSecret</span><span class="sxs-lookup"><span data-stu-id="ecb1c-114">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="ecb1c-115">Uložte citlivá nastavení, jako je Google `Client ID` a `Client Secret`, pomocí [správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ecb1c-115">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="ecb1c-116">Pro účely tohoto kurzu pojmenujte tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="ecb1c-116">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="ecb1c-117">Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="ecb1c-117">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="ecb1c-118">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="ecb1c-118">Configure Google authentication</span></span>

<span data-ttu-id="ecb1c-119">Přidejte službu Google pro `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ecb1c-119">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="ecb1c-120">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="ecb1c-120">Sign in with Google</span></span>

* <span data-ttu-id="ecb1c-121">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-121">Run the app and click **Log in**.</span></span> <span data-ttu-id="ecb1c-122">Zobrazí se možnost přihlásit se přes Google.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-122">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="ecb1c-123">Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-123">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="ecb1c-124">Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-124">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="ecb1c-125">Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraní <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-125">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="ecb1c-126">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-126">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="ecb1c-127">Změna výchozího identifikátoru URI zpětného volání</span><span class="sxs-lookup"><span data-stu-id="ecb1c-127">Change the default callback URI</span></span>

<span data-ttu-id="ecb1c-128">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětné volání poskytovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-128">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="ecb1c-129">Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middleware pro ověřování Google prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="ecb1c-129">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ecb1c-130">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="ecb1c-130">Troubleshooting</span></span>

* <span data-ttu-id="ecb1c-131">Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-131">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="ecb1c-132">Pokud identita není konfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření výsledků v *souboru ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-132">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="ecb1c-133">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-133">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="ecb1c-134">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="ecb1c-134">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="ecb1c-135">Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-135">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ecb1c-136">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ecb1c-136">Next steps</span></span>

* <span data-ttu-id="ecb1c-137">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-137">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="ecb1c-138">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ecb1c-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="ecb1c-139">Jakmile aplikaci publikujete do Azure, resetujte `ClientSecret` v konzole rozhraní Google API.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-139">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="ecb1c-140">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-140">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="ecb1c-141">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ecb1c-141">The configuration system is set up to read keys from environment variables.</span></span>
