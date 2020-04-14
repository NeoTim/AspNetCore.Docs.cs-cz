---
title: Nastavení externího přihlášení na Twitteru s ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitter do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277285"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="e907b-103">Nastavení externího přihlášení na Twitteru s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e907b-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="e907b-104">[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e907b-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e907b-105">Tato ukázka ukazuje, jak umožnit uživatelům [přihlásit](https://dev.twitter.com/web/sign-in/desktop-browser) se pomocí svého účtu Twitter pomocí ukázkové ASP.NET projektu Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e907b-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="e907b-106">Vytvoření aplikace na Twitteru</span><span class="sxs-lookup"><span data-stu-id="e907b-106">Create the app in Twitter</span></span>

* <span data-ttu-id="e907b-107">Přidejte balíček [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="e907b-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="e907b-108">Přejděte [https://apps.twitter.com/](https://apps.twitter.com/) na a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="e907b-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="e907b-109">Pokud ještě nemáte účet na Twitteru, vytvořte ho pomocí odkazu **[Zaregistrovat nyní.](https://twitter.com/signup)**</span><span class="sxs-lookup"><span data-stu-id="e907b-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="e907b-110">Vyberte **Vytvořit aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="e907b-110">Select **Create an app**.</span></span> <span data-ttu-id="e907b-111">Vyplňte **název aplikace**, **popis aplikace** a veřejný identifikátor **URI webu** (může to být dočasné, dokud název domény nezaregistrujete):</span><span class="sxs-lookup"><span data-stu-id="e907b-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="e907b-112">Zaškrtněte políčko vedle **možnosti Povolit přihlášení na Twitteru.**</span><span class="sxs-lookup"><span data-stu-id="e907b-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="e907b-113">Microsoft.AspNetCore.Identity vyžaduje, aby uživatelé měli ve výchozím nastavení e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="e907b-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="e907b-114">Přejděte na kartu **Oprávnění,** klikněte na tlačítko **Upravit** a zaškrtněte políčko **vedle položky Požádat o e-mailovou adresu od uživatelů**.</span><span class="sxs-lookup"><span data-stu-id="e907b-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="e907b-115">Zadejte identifikátor `/signin-twitter` URI vývoje s připojeným do pole **Adresy URL zpětného volání** (například: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="e907b-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="e907b-116">Schéma ověřování Twitter nakonfigurované dále v `/signin-twitter` této ukázce bude automaticky zpracovávat požadavky na cestě k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="e907b-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="e907b-117">Segment `/signin-twitter` URI je nastaven jako výchozí zpětné volání poskytovatele ověřování na Twitteru.</span><span class="sxs-lookup"><span data-stu-id="e907b-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="e907b-118">Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování na Twitteru prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions.](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)</span><span class="sxs-lookup"><span data-stu-id="e907b-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="e907b-119">Vyplňte zbytek formuláře a vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="e907b-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="e907b-120">Zobrazí se nové podrobnosti o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="e907b-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="e907b-121">Uložení klíče a tajného klíče a tajného klíče rozhraní API pro spotřebitele twitteru</span><span class="sxs-lookup"><span data-stu-id="e907b-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="e907b-122">Uklápěte citlivá nastavení, jako je například klíč twitterového spotřebitelského rozhraní API a tajný klíč, pomocí [správce tajných služeb](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="e907b-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="e907b-123">Pro tuto ukázku použijte následující kroky:</span><span class="sxs-lookup"><span data-stu-id="e907b-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="e907b-124">Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="e907b-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="e907b-125">Uložte citlivá nastavení v místním tajném úložišti s klíči `Authentication:Twitter:ConsumerKey` tajných klíčů a `Authentication:Twitter:ConsumerSecret`:</span><span class="sxs-lookup"><span data-stu-id="e907b-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="e907b-126">Tyto tokeny lze nalézt na kartě **Klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:</span><span class="sxs-lookup"><span data-stu-id="e907b-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="e907b-127">Konfigurace ověřování na Twitteru</span><span class="sxs-lookup"><span data-stu-id="e907b-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="e907b-128">Přidejte službu `ConfigureServices` Twitter do metody v *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="e907b-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="e907b-129">Další informace o možnostech konfigurace podporovaných ověřováním na Twitteru naleznete v referenční příručce [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API.</span><span class="sxs-lookup"><span data-stu-id="e907b-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="e907b-130">To lze použít k vyžádání různých informací o uživateli.</span><span class="sxs-lookup"><span data-stu-id="e907b-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="e907b-131">Přihlaste se pomocí Twitteru</span><span class="sxs-lookup"><span data-stu-id="e907b-131">Sign in with Twitter</span></span>

<span data-ttu-id="e907b-132">Spusťte aplikaci a **vyberte Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="e907b-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="e907b-133">Zobrazí se možnost přihlásit se pomocí Twitteru:</span><span class="sxs-lookup"><span data-stu-id="e907b-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="e907b-134">Kliknutím na **Twitter** přesměruje na Twitter pro ověření:</span><span class="sxs-lookup"><span data-stu-id="e907b-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="e907b-135">Po zadání pověření twitteru budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="e907b-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="e907b-136">Nyní jste přihlášeni pomocí pověření Twitter:</span><span class="sxs-lookup"><span data-stu-id="e907b-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="e907b-137">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="e907b-137">Troubleshooting</span></span>

* <span data-ttu-id="e907b-138">**pouze ASP.NET Core 2.x:** Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*.</span><span class="sxs-lookup"><span data-stu-id="e907b-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="e907b-139">Šablona projektu použitá v této ukázce zajišťuje, že se tak děje.</span><span class="sxs-lookup"><span data-stu-id="e907b-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="e907b-140">Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.*</span><span class="sxs-lookup"><span data-stu-id="e907b-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="e907b-141">Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.</span><span class="sxs-lookup"><span data-stu-id="e907b-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="e907b-142">Další kroky</span><span class="sxs-lookup"><span data-stu-id="e907b-142">Next steps</span></span>

* <span data-ttu-id="e907b-143">Tento článek ukázal, jak se můžete ověřit pomocí Twitteru.</span><span class="sxs-lookup"><span data-stu-id="e907b-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="e907b-144">Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e907b-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="e907b-145">Po publikování webu do webové aplikace Azure byste `ConsumerSecret` měli obnovit na twitterovém vývojářském portálu.</span><span class="sxs-lookup"><span data-stu-id="e907b-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="e907b-146">Nastavte `Authentication:Twitter:ConsumerKey` nastavení `Authentication:Twitter:ConsumerSecret` aplikace a jako na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e907b-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="e907b-147">Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="e907b-147">The configuration system is set up to read keys from environment variables.</span></span>
