---
title: Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitteru do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 6b6fa3e50f602a92fec9112ac3ba43583de33a70
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994285"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="11b3c-103">Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="11b3c-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="11b3c-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="11b3c-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="11b3c-105">V této ukázce se dozvíte, jak uživatelům povolit, aby se k [účtu Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) přihlásili pomocí ukázkového projektu ASP.NET Core 2,2 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="11b3c-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="11b3c-106">Vytvoření aplikace na Twitteru</span><span class="sxs-lookup"><span data-stu-id="11b3c-106">Create the app in Twitter</span></span>

* <span data-ttu-id="11b3c-107">Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="11b3c-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="11b3c-108">Pokud ještě nemáte účet na Twitteru, vytvořte si ho pomocí odkazu **[zaregistrovat nyní](https://twitter.com/signup)** .</span><span class="sxs-lookup"><span data-stu-id="11b3c-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="11b3c-109">Klepněte na **vytvořit novou aplikaci** a vyplňte **název**aplikace, **Popis** a identifikátor URI veřejného **webu** (může to být dočasné, dokud nezaregistrujete název domény):</span><span class="sxs-lookup"><span data-stu-id="11b3c-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="11b3c-110">Zadejte identifikátor URI pro vývoj `/signin-twitter` s připojením k platným poli s **platnými identifikátory URI pro přesměrování OAuth** (například: `https://webapp128.azurewebsites.net/signin-twitter`).</span><span class="sxs-lookup"><span data-stu-id="11b3c-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="11b3c-111">Schéma ověřování Twitteru, které je nakonfigurované později v této ukázce, `/signin-twitter` automaticky zpracuje požadavky na trase k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="11b3c-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="11b3c-112">Segment `/signin-twitter` identifikátoru URI je nastaven jako výchozí zpětné volání poskytovatele ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="11b3c-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="11b3c-113">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování Twitteru prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="11b3c-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="11b3c-114">Vyplňte zbytek formuláře a klepněte na **vytvořit aplikaci Twitter**.</span><span class="sxs-lookup"><span data-stu-id="11b3c-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="11b3c-115">Zobrazí se nové podrobnosti o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="11b3c-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="11b3c-116">Ukládá se klíč rozhraní API pro příjemce Twitteru a tajný kód.</span><span class="sxs-lookup"><span data-stu-id="11b3c-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="11b3c-117">Spuštěním následujících příkazů bezpečně uložte `ClientId` a `ClientSecret` použijte [správce tajných klíčů](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="11b3c-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="11b3c-118">Pomocí [správce tajného](xref:security/app-secrets)kódu `Consumer Key` můžete `Consumer Secret` propojit citlivá nastavení, jako je Twitter a konfigurace vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="11b3c-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="11b3c-119">Pro účely této ukázky pojmenujte tokeny `Authentication:Twitter:ConsumerKey` a. `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="11b3c-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="11b3c-120">Tyto tokeny najdete na kartě **klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:</span><span class="sxs-lookup"><span data-stu-id="11b3c-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="11b3c-121">Konfigurace ověřování na Twitteru</span><span class="sxs-lookup"><span data-stu-id="11b3c-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="11b3c-122">Přidejte službu Twitter do `ConfigureServices` metody v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="11b3c-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="11b3c-123">Další informace o možnostech Konfigurace podporovaných ověřováním pomocí Twitteru najdete v referenčních informacích k rozhraní [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API.</span><span class="sxs-lookup"><span data-stu-id="11b3c-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="11b3c-124">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="11b3c-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="11b3c-125">Přihlášení pomocí Twitteru</span><span class="sxs-lookup"><span data-stu-id="11b3c-125">Sign in with Twitter</span></span>

<span data-ttu-id="11b3c-126">Spusťte aplikaci a vyberte **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="11b3c-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="11b3c-127">Zobrazí se možnost přihlásit se pomocí Twitteru:</span><span class="sxs-lookup"><span data-stu-id="11b3c-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="11b3c-128">Kliknutí na **Twitter** přesměruje na Twitter pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="11b3c-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="11b3c-129">Po zadání přihlašovacích údajů pro Twitter budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="11b3c-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="11b3c-130">Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Twitteru:</span><span class="sxs-lookup"><span data-stu-id="11b3c-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="11b3c-131">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="11b3c-131">Troubleshooting</span></span>

* <span data-ttu-id="11b3c-132">**Pouze ASP.NET Core 2. x:** Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, výsledkem *pokusu o ověření bude ArgumentException: Je nutné zadat*možnost SignInScheme.</span><span class="sxs-lookup"><span data-stu-id="11b3c-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="11b3c-133">Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.</span><span class="sxs-lookup"><span data-stu-id="11b3c-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="11b3c-134">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="11b3c-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="11b3c-135">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="11b3c-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="11b3c-136">Další postup</span><span class="sxs-lookup"><span data-stu-id="11b3c-136">Next steps</span></span>

* <span data-ttu-id="11b3c-137">Tento článek ukazuje, jak se dá ověřit pomocí Twitteru.</span><span class="sxs-lookup"><span data-stu-id="11b3c-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="11b3c-138">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="11b3c-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="11b3c-139">Po publikování webu do webové aplikace Azure byste měli resetovat na `ConsumerSecret` portálu pro vývojáře na Twitteru.</span><span class="sxs-lookup"><span data-stu-id="11b3c-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="11b3c-140">Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="11b3c-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="11b3c-141">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="11b3c-141">The configuration system is set up to read keys from environment variables.</span></span>
