---
title: Externí přihlášení nastavení sítě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů z účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989782"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="9ecfa-103">Externí přihlášení nastavení sítě Facebook v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ecfa-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="9ecfa-104">Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9ecfa-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9ecfa-105">Tento kurz s příklady kódu ukazuje, jak se uživatelům umožní přihlásit se pomocí účtu na Facebooku pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9ecfa-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="9ecfa-106">Začneme vytvořením ID aplikace Facebook pomocí následujících [oficiálních kroků](https://developers.facebook.com).</span><span class="sxs-lookup"><span data-stu-id="9ecfa-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="9ecfa-107">Vytvoření aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="9ecfa-107">Create the app in Facebook</span></span>

* <span data-ttu-id="9ecfa-108">Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .</span><span class="sxs-lookup"><span data-stu-id="9ecfa-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="9ecfa-109">Přejděte na stránku [aplikace pro vývojáře na Facebooku](https://developers.facebook.com/apps/) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="9ecfa-110">Pokud ještě nemáte účet Facebook, vytvořte ho pomocí odkazu **zaregistrovat pro Facebook** na přihlašovací stránce.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="9ecfa-111">Jakmile budete mít účet Facebook, zaregistrujte se jako vývojář Facebooku podle pokynů.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="9ecfa-112">V nabídce **Moje aplikace** vyberte **vytvořit aplikaci** a vytvořte nové ID aplikace.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook pro portál pro vývojáře otevřít v Microsoft Edge](index/_static/FBMyApps.png)

* <span data-ttu-id="9ecfa-114">Vyplňte formulář a klepněte na tlačítko **vytvořit ID aplikace** .</span><span class="sxs-lookup"><span data-stu-id="9ecfa-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Vytvoření nové aplikace ID formuláře](index/_static/FBNewAppId.png)

* <span data-ttu-id="9ecfa-116">Na nové kartě aplikace vyberte **Přidat produkt**.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="9ecfa-117">Na **přihlašovací kartě Facebooku** klikněte na **nastavit** .</span><span class="sxs-lookup"><span data-stu-id="9ecfa-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Stránka nastavení produktu](index/_static/FBProductSetup.png)

* <span data-ttu-id="9ecfa-119">Průvodce **rychlým startem** se spustí s **volbou platformy** jako první stránky.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="9ecfa-120">Vynechejte Průvodce hned kliknutím na odkaz nastavení **přihlášení na Facebooku** v nabídce na spodní levé straně:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Přeskočit rychlý Start](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="9ecfa-122">Zobrazí se stránka **Nastavení OAuth pro klienta** :</span><span class="sxs-lookup"><span data-stu-id="9ecfa-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Stránka nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="9ecfa-124">Zadejte identifikátor URI pro vývoj s */SignIn-Facebook* připojením k platným poli identifikátorů **URI přesměrování OAuth** (například: `https://localhost:44320/signin-facebook`).</span><span class="sxs-lookup"><span data-stu-id="9ecfa-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="9ecfa-125">Ověřování na Facebooku nakonfigurované později v tomto kurzu automaticky zpracuje žádosti na trase */SignIn-Facebook* k implementaci toku OAuth.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="9ecfa-126">Identifikátor URI */SignIn-Facebook* je nastaven jako výchozí zpětné volání poskytovatele ověřování Facebooku.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="9ecfa-127">Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování na Facebooku prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .</span><span class="sxs-lookup"><span data-stu-id="9ecfa-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="9ecfa-128">Klikněte na **Uložit změny**.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="9ecfa-129">V levém navigačním panelu klikněte na **nastavení** > **základní** odkaz.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="9ecfa-130">Na této stránce si poznamenejte `App ID` a `App Secret`.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="9ecfa-131">Přidá do vaší aplikace ASP.NET Core v následující části:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="9ecfa-132">Když nasadíte lokalitu, musíte znovu navštívit stránku nastavení **přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="9ecfa-133">Uložení ID a tajného klíče aplikace na Facebooku</span><span class="sxs-lookup"><span data-stu-id="9ecfa-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="9ecfa-134">Uložte citlivá nastavení, jako je například ID aplikace Facebook a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="9ecfa-135">V této ukázce použijte následující postup:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="9ecfa-136">Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="9ecfa-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="9ecfa-137">Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="9ecfa-138">Konfigurace ověřování sítě Facebook</span><span class="sxs-lookup"><span data-stu-id="9ecfa-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="9ecfa-139">Do souboru *Startup.cs* přidejte do metody `ConfigureServices` službu Facebook:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="9ecfa-140">Další informace o možnostech Konfigurace podporovaných ověřováním na Facebooku najdete v referenčních informacích k rozhraní [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="9ecfa-141">Možnosti konfigurace umožňuje:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="9ecfa-142">Požádat o jiné informace o uživateli.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-142">Request different information about the user.</span></span>
* <span data-ttu-id="9ecfa-143">Přidáte argumenty řetězce dotazu přizpůsobit přihlašovací prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="9ecfa-144">Přihlášení pomocí Facebooku</span><span class="sxs-lookup"><span data-stu-id="9ecfa-144">Sign in with Facebook</span></span>

<span data-ttu-id="9ecfa-145">Spusťte aplikaci a klikněte na **Přihlásit se**.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="9ecfa-146">Zobrazí se možnost přihlásit se přes Facebook.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-146">You see an option to sign in with Facebook.</span></span>

![Webová aplikace: uživatel nebyl ověřen](index/_static/DoneFacebook.png)

<span data-ttu-id="9ecfa-148">Když kliknete na **Facebook**, budete přesměrováni na Facebook pro ověřování:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Stránka pro ověřování sítě Facebook](index/_static/FBLogin.png)

<span data-ttu-id="9ecfa-150">Požadavky na ověření sítě Facebook veřejný profil a e-mailovou adresu ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-150">Facebook authentication requests public profile and email address by default:</span></span>

![Obrazovka pro vyjádření souhlasu stránky ověřování sítě Facebook](index/_static/FBLoginDone.png)

<span data-ttu-id="9ecfa-152">Po zadání vašich přihlašovacích údajů k Facebooku budete přesměrováni zpět na váš web, kde můžete nastavit e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="9ecfa-153">Nyní jste přihlášeni pomocí vašich přihlašovacích údajů k Facebooku:</span><span class="sxs-lookup"><span data-stu-id="9ecfa-153">You are now logged in using your Facebook credentials:</span></span>

![Webová aplikace: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="9ecfa-155">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="9ecfa-155">Troubleshooting</span></span>

* <span data-ttu-id="9ecfa-156">**Pouze ASP.NET Core 2. x:** Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="9ecfa-157">Šablona projektu použité v tomto kurzu zajistí, že to se provádí.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="9ecfa-158">Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* .</span><span class="sxs-lookup"><span data-stu-id="9ecfa-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="9ecfa-159">Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="9ecfa-160">Další kroky</span><span class="sxs-lookup"><span data-stu-id="9ecfa-160">Next steps</span></span>

* <span data-ttu-id="9ecfa-161">V tomto článku jsme si ukázali, jak ověřování pomocí Facebooku.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-161">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="9ecfa-162">Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9ecfa-162">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="9ecfa-163">Po publikování webu do webové aplikace Azure byste měli resetovat `AppSecret` na portálu pro vývojáře na Facebooku.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-163">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="9ecfa-164">Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace v Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-164">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="9ecfa-165">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9ecfa-165">The configuration system is set up to read keys from environment variables.</span></span>
