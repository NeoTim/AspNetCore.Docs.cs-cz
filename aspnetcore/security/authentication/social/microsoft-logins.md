---
title: Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka předvádí, integrace ověřování uživatele účtu Microsoft do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815578"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="9f21d-103">Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f21d-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="9f21d-104">Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f21d-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9f21d-105">Tento příklad ukazuje, jak povolit uživatelům přihlašovat se pomocí svého účtu Microsoft pomocí ASP.NET Core 2.2 projektu vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9f21d-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="9f21d-106">Vytvoření aplikace portálu společnosti Microsoft pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="9f21d-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="9f21d-107">Přejděte [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky a vytvoření nebo přihlášení účtem Microsoft:</span><span class="sxs-lookup"><span data-stu-id="9f21d-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="9f21d-108">Pokud nemáte účet Microsoft, vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="9f21d-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="9f21d-109">Po přihlášení budete přesměrováni **registrace aplikací** stránky:</span><span class="sxs-lookup"><span data-stu-id="9f21d-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="9f21d-110">Vyberte **nové registrace**</span><span class="sxs-lookup"><span data-stu-id="9f21d-110">Select **New registration**</span></span>
* <span data-ttu-id="9f21d-111">Zadejte **název**.</span><span class="sxs-lookup"><span data-stu-id="9f21d-111">Enter a **Name**.</span></span>
* <span data-ttu-id="9f21d-112">Vyberte možnost pro **podporovaných typů účtu**.</span><span class="sxs-lookup"><span data-stu-id="9f21d-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="9f21d-113">V části **identifikátor URI pro přesměrování**, zadejte adresu URL svého vývoj s `/signin-microsoft` připojí.</span><span class="sxs-lookup"><span data-stu-id="9f21d-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="9f21d-114">Například, `https://localhost:44389/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="9f21d-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="9f21d-115">Schéma ověřování Microsoft nakonfigurovat později v tomto příkladu bude automaticky zpracovávat požadavky na `/signin-microsoft` trasy, která má implementovat tok OAuth.</span><span class="sxs-lookup"><span data-stu-id="9f21d-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="9f21d-116">Vyberte **zaregistrovat**</span><span class="sxs-lookup"><span data-stu-id="9f21d-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="9f21d-117">Vytvořit tajný kód klienta</span><span class="sxs-lookup"><span data-stu-id="9f21d-117">Create client secret</span></span>

* <span data-ttu-id="9f21d-118">V levém podokně vyberte **certifikáty a tajné kódy**.</span><span class="sxs-lookup"><span data-stu-id="9f21d-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="9f21d-119">V části **tajné klíče klienta**vyberte **nový tajný kód klienta**</span><span class="sxs-lookup"><span data-stu-id="9f21d-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="9f21d-120">Přidáte popis tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="9f21d-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="9f21d-121">Vyberte **přidat** tlačítko.</span><span class="sxs-lookup"><span data-stu-id="9f21d-121">Select the **Add** button.</span></span>

* <span data-ttu-id="9f21d-122">V části **tajné klíče klienta**, zkopírujte hodnotu tajný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="9f21d-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="9f21d-123">Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9f21d-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="9f21d-124">Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="9f21d-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="9f21d-125">Store Microsoft klienta ID a tajný klíč klienta</span><span class="sxs-lookup"><span data-stu-id="9f21d-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="9f21d-126">Spusťte následující příkazy zabezpečeně ukládat `ClientId` a `ClientSecret` pomocí [manažera tajných](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="9f21d-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="9f21d-127">Propojit citlivá nastavení, jako je Microsoft `ClientId` a `ClientSecret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9f21d-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9f21d-128">Pro účely tohoto příkladu název tokeny `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret`.</span><span class="sxs-lookup"><span data-stu-id="9f21d-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="9f21d-129">Konfigurace ověřování pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="9f21d-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="9f21d-130">Přidat službu Microsoft Account `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f21d-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="9f21d-131">Zobrazit [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem Account Microsoft ověřování.</span><span class="sxs-lookup"><span data-stu-id="9f21d-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="9f21d-132">To umožňuje požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="9f21d-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="9f21d-133">Přihlaste se pomocí účtu Microsoft</span><span class="sxs-lookup"><span data-stu-id="9f21d-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="9f21d-134">Spustit a klikněte na tlačítko **přihlášení**.</span><span class="sxs-lookup"><span data-stu-id="9f21d-134">Run the and click **Log in**.</span></span> <span data-ttu-id="9f21d-135">Zobrazí se možnost přihlásit se účtem Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9f21d-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="9f21d-136">Po kliknutí na Microsoft, budete přesměrováni do společnosti Microsoft pro ověřování.</span><span class="sxs-lookup"><span data-stu-id="9f21d-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="9f21d-137">Po přihlášení pomocí Account Microsoftu (pokud ještě nejste přihlášení) se výzva k povolení přístup k informacím:</span><span class="sxs-lookup"><span data-stu-id="9f21d-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="9f21d-138">Klepněte na **Ano** a budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9f21d-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="9f21d-139">Nyní jste přihlášeni pomocí svých přihlašovacích údajů společnosti Microsoft:</span><span class="sxs-lookup"><span data-stu-id="9f21d-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="9f21d-140">Poradce při potížích</span><span class="sxs-lookup"><span data-stu-id="9f21d-140">Troubleshooting</span></span>

* <span data-ttu-id="9f21d-141">Pokud poskytovatel Account Microsoft vás přesměruje na přihlašovací stránce chyby, vezměte na vědomí chyba nadpis a popis parametrů řetězce dotazu přímo po `#` (hashtag) v identifikátoru Uri.</span><span class="sxs-lookup"><span data-stu-id="9f21d-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="9f21d-142">I když chybová zpráva vypadá to, že indikovat problém s ověřováním Microsoft, Nejběžnější příčinou je identifikátor Uri neodpovídá žádné z vaší aplikace **identifikátory URI přesměrování** zadaný pro **webové** platformy .</span><span class="sxs-lookup"><span data-stu-id="9f21d-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="9f21d-143">Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"* .</span><span class="sxs-lookup"><span data-stu-id="9f21d-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="9f21d-144">Šablona projektu používané v tomto příkladu zajistí, že se to.</span><span class="sxs-lookup"><span data-stu-id="9f21d-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="9f21d-145">Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby.</span><span class="sxs-lookup"><span data-stu-id="9f21d-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="9f21d-146">Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.</span><span class="sxs-lookup"><span data-stu-id="9f21d-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9f21d-147">Další postup</span><span class="sxs-lookup"><span data-stu-id="9f21d-147">Next steps</span></span>

* <span data-ttu-id="9f21d-148">V tomto článku jsme si ukázali, jak můžete ověřit s Microsoftem.</span><span class="sxs-lookup"><span data-stu-id="9f21d-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="9f21d-149">Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9f21d-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="9f21d-150">Po publikování webu do webové aplikace Azure, vytvořte nový klient tajných kódů na portálu pro vývojáře společnosti Microsoft.</span><span class="sxs-lookup"><span data-stu-id="9f21d-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="9f21d-151">Nastavte `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="9f21d-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="9f21d-152">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9f21d-152">The configuration system is set up to read keys from environment variables.</span></span>