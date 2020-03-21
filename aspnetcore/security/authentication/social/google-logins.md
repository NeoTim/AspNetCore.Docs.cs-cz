---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989773"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="9795b-103">Nastavení Google externí přihlášení v technologii ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9795b-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="9795b-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9795b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9795b-105">V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9795b-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="9795b-106">Vytvoření projektu konzoly rozhraní Google API a ID klienta</span><span class="sxs-lookup"><span data-stu-id="9795b-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="9795b-107">Nainstalujte [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="9795b-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="9795b-108">Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **Konfigurovat projekt**.</span><span class="sxs-lookup"><span data-stu-id="9795b-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="9795b-109">V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.</span><span class="sxs-lookup"><span data-stu-id="9795b-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="9795b-110">Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování.</span><span class="sxs-lookup"><span data-stu-id="9795b-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="9795b-111">Například `https://localhost:44312/signin-google`.</span><span class="sxs-lookup"><span data-stu-id="9795b-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="9795b-112">Uložte **ID klienta** a **tajný klíč klienta**.</span><span class="sxs-lookup"><span data-stu-id="9795b-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="9795b-113">Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.</span><span class="sxs-lookup"><span data-stu-id="9795b-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="9795b-114">Uložte si ID a tajný kód klienta Google.</span><span class="sxs-lookup"><span data-stu-id="9795b-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="9795b-115">Uložte citlivá nastavení, jako je třeba ID klienta Google a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů.</span><span class="sxs-lookup"><span data-stu-id="9795b-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9795b-116">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="9795b-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="9795b-117">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="9795b-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="9795b-118">Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="9795b-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="9795b-119">Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).</span><span class="sxs-lookup"><span data-stu-id="9795b-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="9795b-120">Konfigurace ověřování Google</span><span class="sxs-lookup"><span data-stu-id="9795b-120">Configure Google authentication</span></span>

<span data-ttu-id="9795b-121">Přidejte službu Google pro `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9795b-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="9795b-122">Přihlásit se přes Google</span><span class="sxs-lookup"><span data-stu-id="9795b-122">Sign in with Google</span></span>

* <span data-ttu-id="9795b-123">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="9795b-123">Run the app and click **Log in**.</span></span> <span data-ttu-id="9795b-124">Zobrazí se možnost přihlásit se přes Google.</span><span class="sxs-lookup"><span data-stu-id="9795b-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="9795b-125">Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="9795b-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="9795b-126">Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.</span><span class="sxs-lookup"><span data-stu-id="9795b-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="9795b-127">Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraní <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API.</span><span class="sxs-lookup"><span data-stu-id="9795b-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="9795b-128">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="9795b-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="9795b-129">Změna výchozího identifikátoru URI zpětného volání</span><span class="sxs-lookup"><span data-stu-id="9795b-129">Change the default callback URI</span></span>

<span data-ttu-id="9795b-130">Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětné volání poskytovatele ověřování Google.</span><span class="sxs-lookup"><span data-stu-id="9795b-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="9795b-131">Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middleware pro ověřování Google prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .</span><span class="sxs-lookup"><span data-stu-id="9795b-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9795b-132">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9795b-132">Troubleshooting</span></span>

* <span data-ttu-id="9795b-133">Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.</span><span class="sxs-lookup"><span data-stu-id="9795b-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="9795b-134">Pokud identita není konfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření výsledků v *souboru ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="9795b-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="9795b-135">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="9795b-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="9795b-136">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="9795b-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="9795b-137">Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.</span><span class="sxs-lookup"><span data-stu-id="9795b-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9795b-138">Další kroky</span><span class="sxs-lookup"><span data-stu-id="9795b-138">Next steps</span></span>

* <span data-ttu-id="9795b-139">V tomto článku jsme si ukázali, jak můžete ověřit s Google.</span><span class="sxs-lookup"><span data-stu-id="9795b-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="9795b-140">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9795b-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="9795b-141">Jakmile aplikaci publikujete do Azure, resetujte `ClientSecret` v konzole rozhraní Google API.</span><span class="sxs-lookup"><span data-stu-id="9795b-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="9795b-142">Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="9795b-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="9795b-143">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9795b-143">The configuration system is set up to read keys from environment variables.</span></span>
