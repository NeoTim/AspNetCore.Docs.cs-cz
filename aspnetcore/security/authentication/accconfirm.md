---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: d6ea37ceb83ffbaa94187e0c541c79428594e4b4
ms.sourcegitcommit: 2039e60eb7b482da8298f82dcd5eda27cf747f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88906446"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="5f3aa-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f3aa-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="5f3aa-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="5f3aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="5f3aa-105">V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="5f3aa-106">Tento **kurz není** úvodním tématem.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="5f3aa-107">Měli byste být obeznámeni s:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-107">You should be familiar with:</span></span>

* [<span data-ttu-id="5f3aa-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f3aa-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="5f3aa-109">Authentication</span><span class="sxs-lookup"><span data-stu-id="5f3aa-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5f3aa-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5f3aa-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5f3aa-111">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="5f3aa-111">Prerequisites</span></span>

[<span data-ttu-id="5f3aa-112">.NET Core 3,0 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="5f3aa-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="5f3aa-113">Vytvoření a otestování webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="5f3aa-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="5f3aa-114">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="5f3aa-115">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="5f3aa-116">Po zaregistrování budete přesměrováni na `/Identity/Account/RegisterConfirmation` stránku, která obsahuje odkaz pro simulaci potvrzení e-mailu:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="5f3aa-117">Vyberte odkaz `Click here to confirm your account`.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="5f3aa-118">Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="5f3aa-119">Vyberte `Hello YourEmail@provider.com!` odkaz, který vás přesměruje na `/Identity/Account/Manage/PersonalData` stránku.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="5f3aa-120">Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="5f3aa-121">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-121">Configure an email provider</span></span>

<span data-ttu-id="5f3aa-122">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="5f3aa-123">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="5f3aa-124">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-124">You can use other email providers.</span></span> <span data-ttu-id="5f3aa-125">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="5f3aa-126">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="5f3aa-127">Účet SendGrid může vyžadovat [Přidání odesílatele](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="5f3aa-128">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="5f3aa-129">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="5f3aa-130">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="5f3aa-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="5f3aa-131">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5f3aa-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="5f3aa-133">Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="5f3aa-134">Obsah *secrets.jsv* souboru není šifrovaný.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="5f3aa-135">Následující kód ukazuje *secrets.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="5f3aa-136">`SendGridKey`Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="5f3aa-137">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="5f3aa-138">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="5f3aa-138">Install SendGrid</span></span>

<span data-ttu-id="5f3aa-139">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="5f3aa-140">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3aa-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3aa-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3aa-142">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5f3aa-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5f3aa-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f3aa-144">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="5f3aa-145">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="5f3aa-146">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="5f3aa-146">Implement IEmailSender</span></span>

<span data-ttu-id="5f3aa-147">K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="5f3aa-148">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-148">Configure startup to support email</span></span>

<span data-ttu-id="5f3aa-149">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="5f3aa-150">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="5f3aa-151">Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="5f3aa-152">RegisterConfirmation uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="5f3aa-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="5f3aa-153">Postupujte podle pokynů pro [generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity) a uživatelského rozhraní `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="5f3aa-154">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="5f3aa-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="5f3aa-155">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="5f3aa-156">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="5f3aa-156">Run the app and register a new user</span></span>
* <span data-ttu-id="5f3aa-157">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="5f3aa-158">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="5f3aa-159">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="5f3aa-160">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="5f3aa-161">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="5f3aa-162">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="5f3aa-162">Test password reset</span></span>

* <span data-ttu-id="5f3aa-163">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="5f3aa-164">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="5f3aa-165">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="5f3aa-166">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="5f3aa-167">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="5f3aa-168">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="5f3aa-169">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="5f3aa-169">Resend email confirmation</span></span>

<span data-ttu-id="5f3aa-170">V ASP.NET Core 5,0 a novějším vyberte odkaz znovu **Odeslat e-mail s potvrzením** na **přihlašovací** stránce.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="5f3aa-171">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="5f3aa-171">Change email and activity timeout</span></span>

<span data-ttu-id="5f3aa-172">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="5f3aa-173">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="5f3aa-174">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="5f3aa-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="5f3aa-175">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="5f3aa-176">Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="5f3aa-177">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-177">Change the email token lifespan</span></span>

<span data-ttu-id="5f3aa-178">Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="5f3aa-179">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="5f3aa-180">Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="5f3aa-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="5f3aa-181">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="5f3aa-182">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="5f3aa-182">Debug email</span></span>

<span data-ttu-id="5f3aa-183">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-183">If you can't get email working:</span></span>

* <span data-ttu-id="5f3aa-184">Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="5f3aa-185">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="5f3aa-186">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="5f3aa-187">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-187">Check your spam folder.</span></span>
* <span data-ttu-id="5f3aa-188">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="5f3aa-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="5f3aa-189">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="5f3aa-190">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="5f3aa-191">Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="5f3aa-192">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="5f3aa-193">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="5f3aa-193">Combine social and local login accounts</span></span>

<span data-ttu-id="5f3aa-194">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="5f3aa-195">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5f3aa-196">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="5f3aa-197">V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="5f3aa-199">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-199">Click on the **Manage** link.</span></span> <span data-ttu-id="5f3aa-200">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-200">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="5f3aa-202">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="5f3aa-203">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-203">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="5f3aa-205">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-205">The two accounts have been combined.</span></span> <span data-ttu-id="5f3aa-206">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-206">You are able to sign in with either account.</span></span> <span data-ttu-id="5f3aa-207">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="5f3aa-208">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="5f3aa-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="5f3aa-209">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="5f3aa-210">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="5f3aa-211">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="5f3aa-212">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="5f3aa-213">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-213">Confirm existing users.</span></span> <span data-ttu-id="5f3aa-214">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="5f3aa-215">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="5f3aa-215">Prerequisites</span></span>

[<span data-ttu-id="5f3aa-216">.NET Core 2,2 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="5f3aa-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="5f3aa-217">Vytvoření webové aplikace a uživatelského rozhraní Identity</span><span class="sxs-lookup"><span data-stu-id="5f3aa-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="5f3aa-218">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="5f3aa-219">Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="5f3aa-220">`InputModel` `Password` Vlastnost se nachází v `Areas/Identity/Pages/Account/Register.cshtml.cs` souboru po vygenerování uživatelského rozhraní Identity .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="5f3aa-221">Otestovat registraci nových uživatelů</span><span class="sxs-lookup"><span data-stu-id="5f3aa-221">Test new user registration</span></span>

<span data-ttu-id="5f3aa-222">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="5f3aa-223">V tomto okamžiku je jediným ověřením e-mailu [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="5f3aa-224">Po odeslání registrace jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="5f3aa-225">Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="5f3aa-226">Všimněte si, že `EmailConfirmed` pole tabulky je `False` .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="5f3aa-227">Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="5f3aa-228">Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="5f3aa-229">Odstranění aliasu e-mailu usnadňuje následující kroky.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="5f3aa-230">Vyžadovat potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-230">Require email confirmation</span></span>

<span data-ttu-id="5f3aa-231">Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="5f3aa-232">Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="5f3aa-233">Předpokládejme, že máte diskuzní fórum a chtěli jste zabránit yli@example.com v registraci jako nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="5f3aa-234">Bez potvrzení e-mailu nolivetto@contoso.com může zpráva z vaší aplikace obdržet nevyžádaný e-mail.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="5f3aa-235">Předpokládejme, že uživatel omylem registrovaný jako " ylo@example.com " a jste zaznamenal chybu "Yli".</span><span class="sxs-lookup"><span data-stu-id="5f3aa-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="5f3aa-236">Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="5f3aa-237">Potvrzení e-mailu poskytuje omezené ochrany z roboty.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="5f3aa-238">Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty</span><span class="sxs-lookup"><span data-stu-id="5f3aa-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="5f3aa-239">Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="5f3aa-240">Aktualizace `Startup.ConfigureServices`  pro vyžadování potvrzeného e-mailové adresy:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="5f3aa-241">`config.SignIn.RequireConfirmedEmail = true;` zabrání registrovaným uživatelům v přihlášení, dokud se nepotvrdí jejich e-maily.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="5f3aa-242">Konfigurovat poskytovatele e-mailů</span><span class="sxs-lookup"><span data-stu-id="5f3aa-242">Configure email provider</span></span>

<span data-ttu-id="5f3aa-243">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="5f3aa-244">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="5f3aa-245">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-245">You can use other email providers.</span></span> <span data-ttu-id="5f3aa-246">ASP.NET Core 2. x zahrnuje `System.Net.Mail` , což umožňuje odesílat e-maily z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="5f3aa-247">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="5f3aa-248">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="5f3aa-249">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="5f3aa-250">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="5f3aa-251">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="5f3aa-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="5f3aa-252">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5f3aa-253">Příklad:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="5f3aa-254">Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="5f3aa-255">Obsah *secrets.jsv* souboru není šifrovaný.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="5f3aa-256">Následující kód ukazuje *secrets.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="5f3aa-257">`SendGridKey`Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="5f3aa-258">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="5f3aa-259">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="5f3aa-259">Install SendGrid</span></span>

<span data-ttu-id="5f3aa-260">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="5f3aa-261">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5f3aa-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5f3aa-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5f3aa-263">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5f3aa-264">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="5f3aa-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5f3aa-265">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="5f3aa-266">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="5f3aa-267">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="5f3aa-267">Implement IEmailSender</span></span>

<span data-ttu-id="5f3aa-268">K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="5f3aa-269">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-269">Configure startup to support email</span></span>

<span data-ttu-id="5f3aa-270">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="5f3aa-271">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="5f3aa-272">Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="5f3aa-273">Povolení potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="5f3aa-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="5f3aa-274">Šablona obsahuje kód pro potvrzení účtu a obnovení hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="5f3aa-275">Vyhledejte `OnPostAsync` metodu v *oblasti/ Identity /Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="5f3aa-276">Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="5f3aa-277">Metoda Complete se zobrazí se zvýrazněným změněným řádkem:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="5f3aa-278">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="5f3aa-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="5f3aa-279">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="5f3aa-280">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="5f3aa-280">Run the app and register a new user</span></span>
* <span data-ttu-id="5f3aa-281">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="5f3aa-282">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="5f3aa-283">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="5f3aa-284">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="5f3aa-285">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="5f3aa-286">Zobrazit stránku pro správu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-286">View the manage page</span></span>

<span data-ttu-id="5f3aa-287">V prohlížeči vyberte své uživatelské jméno: ![ okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="5f3aa-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="5f3aa-288">Stránka Správa se zobrazí s vybranou kartou **profil** .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="5f3aa-289">**E-mail** zobrazuje zaškrtávací políčko označující, že byl e-mail potvrzen.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="5f3aa-290">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="5f3aa-290">Test password reset</span></span>

* <span data-ttu-id="5f3aa-291">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="5f3aa-292">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="5f3aa-293">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="5f3aa-294">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="5f3aa-295">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="5f3aa-296">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="5f3aa-297">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="5f3aa-297">Change email and activity timeout</span></span>

<span data-ttu-id="5f3aa-298">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="5f3aa-299">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="5f3aa-300">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="5f3aa-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="5f3aa-301">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="5f3aa-302">Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="5f3aa-303">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="5f3aa-303">Change the email token lifespan</span></span>

<span data-ttu-id="5f3aa-304">Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="5f3aa-305">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="5f3aa-306">Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="5f3aa-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="5f3aa-307">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="5f3aa-308">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="5f3aa-308">Resend email confirmation</span></span>

<span data-ttu-id="5f3aa-309">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="5f3aa-310">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="5f3aa-310">Debug email</span></span>

<span data-ttu-id="5f3aa-311">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-311">If you can't get email working:</span></span>

* <span data-ttu-id="5f3aa-312">Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="5f3aa-313">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="5f3aa-314">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="5f3aa-315">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-315">Check your spam folder.</span></span>
* <span data-ttu-id="5f3aa-316">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="5f3aa-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="5f3aa-317">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="5f3aa-318">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="5f3aa-319">Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="5f3aa-320">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="5f3aa-321">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="5f3aa-321">Combine social and local login accounts</span></span>

<span data-ttu-id="5f3aa-322">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="5f3aa-323">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5f3aa-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5f3aa-324">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="5f3aa-325">V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="5f3aa-327">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="5f3aa-327">Click on the **Manage** link.</span></span> <span data-ttu-id="5f3aa-328">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-328">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="5f3aa-330">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="5f3aa-331">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-331">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="5f3aa-333">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-333">The two accounts have been combined.</span></span> <span data-ttu-id="5f3aa-334">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-334">You are able to sign in with either account.</span></span> <span data-ttu-id="5f3aa-335">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="5f3aa-336">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="5f3aa-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="5f3aa-337">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="5f3aa-338">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="5f3aa-339">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="5f3aa-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="5f3aa-340">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="5f3aa-341">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-341">Confirm existing users.</span></span> <span data-ttu-id="5f3aa-342">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="5f3aa-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
