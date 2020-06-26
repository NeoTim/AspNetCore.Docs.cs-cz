---
title: Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitteru do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: 61c983de33b91a16ad207d8a350daf4859c89eaf
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406092"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="6afb2-103">Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6afb2-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="6afb2-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6afb2-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6afb2-105">V této ukázce se dozvíte, jak uživatelům povolit, aby se k [účtu Twitter přihlásili](https://dev.twitter.com/web/sign-in/desktop-browser) pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6afb2-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="6afb2-106">Vytvoření aplikace na Twitteru</span><span class="sxs-lookup"><span data-stu-id="6afb2-106">Create the app in Twitter</span></span>

* <span data-ttu-id="6afb2-107">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) .</span><span class="sxs-lookup"><span data-stu-id="6afb2-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="6afb2-108">Přejděte na [https://apps.twitter.com/](https://apps.twitter.com/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="6afb2-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="6afb2-109">Pokud ještě nemáte účet na Twitteru, vytvořte si ho pomocí odkazu **[zaregistrovat nyní](https://twitter.com/signup)** .</span><span class="sxs-lookup"><span data-stu-id="6afb2-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="6afb2-110">Vyberte **vytvořit aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="6afb2-110">Select **Create an app**.</span></span> <span data-ttu-id="6afb2-111">Vyplňte **název aplikace**, **Popis aplikace** a identifikátor URI veřejného **webu** (může to být dočasné, dokud nezaregistrujete název domény):</span><span class="sxs-lookup"><span data-stu-id="6afb2-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="6afb2-112">Zaškrtněte políčko vedle **Povolit možnost přihlásit se pomocí Twitteru** .</span><span class="sxs-lookup"><span data-stu-id="6afb2-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="6afb2-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="6afb2-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="6afb2-114">vyžaduje, aby uživatelé měli ve výchozím nastavení e-mailovou adresu.</span><span class="sxs-lookup"><span data-stu-id="6afb2-114">requires users to have an email address by default.</span></span> <span data-ttu-id="6afb2-115">Přejděte na kartu **oprávnění** , klikněte na tlačítko **Upravit** a zaškrtněte políčko u možnosti **požádat uživatele o e-mailovou adresu**.</span><span class="sxs-lookup"><span data-stu-id="6afb2-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="6afb2-116">Zadejte identifikátor URI pro vývoj s `/signin-twitter` připojením k poli **adresy URL zpětného volání** (například: `https://webapp128.azurewebsites.net/signin-twitter` ).</span><span class="sxs-lookup"><span data-stu-id="6afb2-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="6afb2-117">Schéma ověřování Twitteru, které je nakonfigurované později v této ukázce, automaticky zpracuje požadavky na `/signin-twitter` trase k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="6afb2-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6afb2-118">Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětné volání poskytovatele ověřování Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6afb2-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="6afb2-119">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování Twitteru prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .</span><span class="sxs-lookup"><span data-stu-id="6afb2-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="6afb2-120">Vyplňte zbytek formuláře a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="6afb2-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="6afb2-121">Zobrazí se nové podrobnosti o aplikaci:</span><span class="sxs-lookup"><span data-stu-id="6afb2-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="6afb2-122">Uložit klíč rozhraní API pro příjemce Twitteru a tajný klíč</span><span class="sxs-lookup"><span data-stu-id="6afb2-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="6afb2-123">Uložte citlivá nastavení, jako je klíč rozhraní API pro Twitter Consumer a tajný klíč pomocí [správce tajných](xref:security/app-secrets)klíčů.</span><span class="sxs-lookup"><span data-stu-id="6afb2-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6afb2-124">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="6afb2-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="6afb2-125">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="6afb2-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="6afb2-126">Uložte citlivá nastavení do místního úložiště tajného klíče s klíči tajných klíčů `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` :</span><span class="sxs-lookup"><span data-stu-id="6afb2-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="6afb2-127">Tyto tokeny najdete na kartě **klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:</span><span class="sxs-lookup"><span data-stu-id="6afb2-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="6afb2-128">Konfigurace ověřování na Twitteru</span><span class="sxs-lookup"><span data-stu-id="6afb2-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="6afb2-129">Přidejte službu Twitter do `ConfigureServices` metody v souboru *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="6afb2-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="6afb2-130">Další informace o možnostech Konfigurace podporovaných ověřováním pomocí Twitteru najdete v referenčních informacích k rozhraní [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API.</span><span class="sxs-lookup"><span data-stu-id="6afb2-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="6afb2-131">Dá se použít k vyžádání různých informací o uživateli.</span><span class="sxs-lookup"><span data-stu-id="6afb2-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="6afb2-132">Přihlášení pomocí Twitteru</span><span class="sxs-lookup"><span data-stu-id="6afb2-132">Sign in with Twitter</span></span>

<span data-ttu-id="6afb2-133">Spusťte aplikaci a vyberte **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="6afb2-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="6afb2-134">Zobrazí se možnost přihlásit se pomocí Twitteru:</span><span class="sxs-lookup"><span data-stu-id="6afb2-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="6afb2-135">Kliknutí na **Twitter** přesměruje na Twitter pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="6afb2-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="6afb2-136">Po zadání přihlašovacích údajů pro Twitter budete přesměrováni zpět na web, kde můžete nastavit e-mail.</span><span class="sxs-lookup"><span data-stu-id="6afb2-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="6afb2-137">Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Twitteru:</span><span class="sxs-lookup"><span data-stu-id="6afb2-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="6afb2-138">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="6afb2-138">Troubleshooting</span></span>

* <span data-ttu-id="6afb2-139">**Pouze ASP.NET Core 2. x:** Pokud Identity není nakonfigurováno voláním `services.AddIdentity` v `ConfigureServices` , pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="6afb2-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6afb2-140">Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.</span><span class="sxs-lookup"><span data-stu-id="6afb2-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="6afb2-141">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při zpracování chyby žádosti se zobrazí *operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="6afb2-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6afb2-142">Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.</span><span class="sxs-lookup"><span data-stu-id="6afb2-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6afb2-143">Další kroky</span><span class="sxs-lookup"><span data-stu-id="6afb2-143">Next steps</span></span>

* <span data-ttu-id="6afb2-144">Tento článek ukazuje, jak se dá ověřit pomocí Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6afb2-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="6afb2-145">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6afb2-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6afb2-146">Po publikování webu do webové aplikace Azure byste měli resetovat na `ConsumerSecret` portálu pro vývojáře na Twitteru.</span><span class="sxs-lookup"><span data-stu-id="6afb2-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="6afb2-147">Nastavte `Authentication:Twitter:ConsumerKey` nastavení a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="6afb2-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6afb2-148">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="6afb2-148">The configuration system is set up to read keys from environment variables.</span></span>
