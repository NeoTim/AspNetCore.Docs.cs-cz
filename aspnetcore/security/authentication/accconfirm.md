---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404649"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="9417f-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9417f-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="9417f-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="9417f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="9417f-105">V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="9417f-106">Tento **kurz není** úvodním tématem.</span><span class="sxs-lookup"><span data-stu-id="9417f-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="9417f-107">Měli byste být obeznámeni s:</span><span class="sxs-lookup"><span data-stu-id="9417f-107">You should be familiar with:</span></span>

* [<span data-ttu-id="9417f-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9417f-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="9417f-109">Ověřování</span><span class="sxs-lookup"><span data-stu-id="9417f-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="9417f-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9417f-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="9417f-111">V [tomto souboru PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) se zobrazí verze ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="9417f-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="9417f-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9417f-112">Prerequisites</span></span>

[<span data-ttu-id="9417f-113">.NET Core 3,0 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="9417f-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="9417f-114">Vytvoření a otestování webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="9417f-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="9417f-115">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="9417f-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="9417f-116">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9417f-117">Po zaregistrování budete přesměrováni na `/Identity/Account/RegisterConfirmation` stránku, která obsahuje odkaz pro simulaci potvrzení e-mailu:</span><span class="sxs-lookup"><span data-stu-id="9417f-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="9417f-118">Vyberte `Click here to confirm your account` odkaz.</span><span class="sxs-lookup"><span data-stu-id="9417f-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="9417f-119">Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="9417f-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="9417f-120">Vyberte `Hello YourEmail@provider.com!` odkaz, který vás přesměruje na `/Identity/Account/Manage/PersonalData` stránku.</span><span class="sxs-lookup"><span data-stu-id="9417f-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="9417f-121">Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="9417f-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="9417f-122">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-122">Configure an email provider</span></span>

<span data-ttu-id="9417f-123">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="9417f-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9417f-124">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="9417f-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9417f-125">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="9417f-125">You can use other email providers.</span></span> <span data-ttu-id="9417f-126">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="9417f-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9417f-127">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="9417f-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9417f-128">Účet SendGrid může vyžadovat [Přidání odesílatele](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="9417f-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="9417f-129">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="9417f-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9417f-130">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9417f-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9417f-131">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="9417f-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9417f-132">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9417f-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9417f-133">Například:</span><span class="sxs-lookup"><span data-stu-id="9417f-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9417f-134">Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="9417f-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9417f-135">Obsah *secrets.jsv* souboru není šifrovaný.</span><span class="sxs-lookup"><span data-stu-id="9417f-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9417f-136">Následující kód ukazuje *secrets.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="9417f-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9417f-137">`SendGridKey`Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="9417f-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9417f-138">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9417f-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9417f-139">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="9417f-139">Install SendGrid</span></span>

<span data-ttu-id="9417f-140">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="9417f-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9417f-141">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="9417f-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9417f-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9417f-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9417f-143">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9417f-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9417f-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9417f-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9417f-145">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9417f-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9417f-146">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="9417f-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9417f-147">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="9417f-147">Implement IEmailSender</span></span>

<span data-ttu-id="9417f-148">K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9417f-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9417f-149">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-149">Configure startup to support email</span></span>

<span data-ttu-id="9417f-150">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9417f-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9417f-151">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="9417f-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9417f-152">Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9417f-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9417f-153">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="9417f-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9417f-154">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9417f-155">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="9417f-155">Run the app and register a new user</span></span>
* <span data-ttu-id="9417f-156">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9417f-157">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="9417f-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9417f-158">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="9417f-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9417f-159">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="9417f-160">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="9417f-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9417f-161">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="9417f-161">Test password reset</span></span>

* <span data-ttu-id="9417f-162">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="9417f-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9417f-163">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="9417f-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9417f-164">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9417f-165">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9417f-166">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="9417f-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9417f-167">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9417f-168">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="9417f-168">Change email and activity timeout</span></span>

<span data-ttu-id="9417f-169">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="9417f-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9417f-170">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="9417f-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9417f-171">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="9417f-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="9417f-172">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="9417f-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="9417f-173">Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9417f-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9417f-174">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-174">Change the email token lifespan</span></span>

<span data-ttu-id="9417f-175">Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9417f-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9417f-176">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9417f-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9417f-177">Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9417f-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9417f-178">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="9417f-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9417f-179">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="9417f-179">Resend email confirmation</span></span>

<span data-ttu-id="9417f-180">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9417f-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9417f-181">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="9417f-181">Debug email</span></span>

<span data-ttu-id="9417f-182">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="9417f-182">If you can't get email working:</span></span>

* <span data-ttu-id="9417f-183">Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="9417f-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9417f-184">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="9417f-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9417f-185">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9417f-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9417f-186">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="9417f-186">Check your spam folder.</span></span>
* <span data-ttu-id="9417f-187">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="9417f-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9417f-188">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="9417f-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="9417f-189">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="9417f-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9417f-190">Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="9417f-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9417f-191">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9417f-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9417f-192">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="9417f-192">Combine social and local login accounts</span></span>

<span data-ttu-id="9417f-193">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="9417f-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9417f-194">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9417f-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9417f-195">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="9417f-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9417f-196">V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="9417f-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="9417f-198">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="9417f-198">Click on the **Manage** link.</span></span> <span data-ttu-id="9417f-199">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-199">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="9417f-201">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9417f-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9417f-202">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="9417f-202">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="9417f-204">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="9417f-204">The two accounts have been combined.</span></span> <span data-ttu-id="9417f-205">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="9417f-205">You are able to sign in with either account.</span></span> <span data-ttu-id="9417f-206">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9417f-207">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="9417f-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9417f-208">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9417f-209">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="9417f-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9417f-210">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9417f-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9417f-211">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="9417f-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9417f-212">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-212">Confirm existing users.</span></span> <span data-ttu-id="9417f-213">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="9417f-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9417f-214">Požadavky</span><span class="sxs-lookup"><span data-stu-id="9417f-214">Prerequisites</span></span>

[<span data-ttu-id="9417f-215">.NET Core 2,2 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="9417f-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="9417f-216">Vytvoření webové aplikace a uživatelského rozhraníIdentity</span><span class="sxs-lookup"><span data-stu-id="9417f-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="9417f-217">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="9417f-217">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="9417f-218">Otestovat registraci nových uživatelů</span><span class="sxs-lookup"><span data-stu-id="9417f-218">Test new user registration</span></span>

<span data-ttu-id="9417f-219">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="9417f-220">V tomto okamžiku je jediným ověřením e-mailu [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="9417f-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="9417f-221">Po odeslání registrace jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="9417f-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="9417f-222">Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="9417f-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="9417f-223">Všimněte si, že `EmailConfirmed` pole tabulky je `False` .</span><span class="sxs-lookup"><span data-stu-id="9417f-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="9417f-224">Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail.</span><span class="sxs-lookup"><span data-stu-id="9417f-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="9417f-225">Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="9417f-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="9417f-226">Odstranění aliasu e-mailu usnadňuje následující kroky.</span><span class="sxs-lookup"><span data-stu-id="9417f-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="9417f-227">Vyžadovat potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-227">Require email confirmation</span></span>

<span data-ttu-id="9417f-228">Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="9417f-229">Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného).</span><span class="sxs-lookup"><span data-stu-id="9417f-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="9417f-230">Předpokládejme, že máte diskuzní fórum a chtěli jste zabránit yli@example.com v registraci jako nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="9417f-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="9417f-231">Bez potvrzení e-mailu nolivetto@contoso.com může zpráva z vaší aplikace obdržet nevyžádaný e-mail.</span><span class="sxs-lookup"><span data-stu-id="9417f-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="9417f-232">Předpokládejme, že uživatel omylem registrovaný jako " ylo@example.com " a jste zaznamenal chybu "Yli".</span><span class="sxs-lookup"><span data-stu-id="9417f-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="9417f-233">Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail.</span><span class="sxs-lookup"><span data-stu-id="9417f-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="9417f-234">Potvrzení e-mailu poskytuje omezené ochrany z roboty.</span><span class="sxs-lookup"><span data-stu-id="9417f-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="9417f-235">Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty</span><span class="sxs-lookup"><span data-stu-id="9417f-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="9417f-236">Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.</span><span class="sxs-lookup"><span data-stu-id="9417f-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="9417f-237">Aktualizace `Startup.ConfigureServices` pro vyžadování potvrzeného e-mailové adresy:</span><span class="sxs-lookup"><span data-stu-id="9417f-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="9417f-238">`config.SignIn.RequireConfirmedEmail = true;`zabrání registrovaným uživatelům v přihlášení, dokud se nepotvrdí jejich e-maily.</span><span class="sxs-lookup"><span data-stu-id="9417f-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="9417f-239">Konfigurovat poskytovatele e-mailů</span><span class="sxs-lookup"><span data-stu-id="9417f-239">Configure email provider</span></span>

<span data-ttu-id="9417f-240">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="9417f-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="9417f-241">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="9417f-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="9417f-242">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="9417f-242">You can use other email providers.</span></span> <span data-ttu-id="9417f-243">ASP.NET Core 2. x zahrnuje `System.Net.Mail` , což umožňuje odesílat e-maily z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="9417f-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="9417f-244">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="9417f-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="9417f-245">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="9417f-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="9417f-246">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="9417f-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="9417f-247">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="9417f-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="9417f-248">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="9417f-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="9417f-249">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="9417f-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="9417f-250">Například:</span><span class="sxs-lookup"><span data-stu-id="9417f-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="9417f-251">Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="9417f-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="9417f-252">Obsah *secrets.jsv* souboru není šifrovaný.</span><span class="sxs-lookup"><span data-stu-id="9417f-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="9417f-253">Následující kód ukazuje *secrets.jsv* souboru.</span><span class="sxs-lookup"><span data-stu-id="9417f-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="9417f-254">`SendGridKey`Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="9417f-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="9417f-255">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="9417f-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="9417f-256">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="9417f-256">Install SendGrid</span></span>

<span data-ttu-id="9417f-257">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="9417f-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="9417f-258">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="9417f-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9417f-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9417f-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9417f-260">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9417f-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="9417f-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9417f-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9417f-262">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="9417f-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="9417f-263">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="9417f-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="9417f-264">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="9417f-264">Implement IEmailSender</span></span>

<span data-ttu-id="9417f-265">K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="9417f-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="9417f-266">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-266">Configure startup to support email</span></span>

<span data-ttu-id="9417f-267">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="9417f-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="9417f-268">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="9417f-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="9417f-269">Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.</span><span class="sxs-lookup"><span data-stu-id="9417f-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="9417f-270">Povolení potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="9417f-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="9417f-271">Šablona obsahuje kód pro potvrzení účtu a obnovení hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="9417f-272">Vyhledejte `OnPostAsync` metodu v *oblasti/ Identity /Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="9417f-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="9417f-273">Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:</span><span class="sxs-lookup"><span data-stu-id="9417f-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="9417f-274">Metoda Complete se zobrazí se zvýrazněným změněným řádkem:</span><span class="sxs-lookup"><span data-stu-id="9417f-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="9417f-275">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="9417f-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="9417f-276">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="9417f-277">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="9417f-277">Run the app and register a new user</span></span>
* <span data-ttu-id="9417f-278">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="9417f-279">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="9417f-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="9417f-280">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="9417f-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="9417f-281">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="9417f-282">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="9417f-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="9417f-283">Zobrazit stránku pro správu</span><span class="sxs-lookup"><span data-stu-id="9417f-283">View the manage page</span></span>

<span data-ttu-id="9417f-284">V prohlížeči vyberte své uživatelské jméno: ![ okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="9417f-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="9417f-285">Stránka Správa se zobrazí s vybranou kartou **profil** .</span><span class="sxs-lookup"><span data-stu-id="9417f-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="9417f-286">**E-mail** zobrazuje zaškrtávací políčko označující, že byl e-mail potvrzen.</span><span class="sxs-lookup"><span data-stu-id="9417f-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="9417f-287">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="9417f-287">Test password reset</span></span>

* <span data-ttu-id="9417f-288">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="9417f-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="9417f-289">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="9417f-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="9417f-290">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="9417f-291">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="9417f-292">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="9417f-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="9417f-293">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="9417f-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="9417f-294">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="9417f-294">Change email and activity timeout</span></span>

<span data-ttu-id="9417f-295">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="9417f-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="9417f-296">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="9417f-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="9417f-297">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="9417f-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="9417f-298">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="9417f-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="9417f-299">Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9417f-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="9417f-300">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="9417f-300">Change the email token lifespan</span></span>

<span data-ttu-id="9417f-301">Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="9417f-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="9417f-302">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="9417f-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="9417f-303">Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9417f-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="9417f-304">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="9417f-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="9417f-305">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="9417f-305">Resend email confirmation</span></span>

<span data-ttu-id="9417f-306">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="9417f-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="9417f-307">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="9417f-307">Debug email</span></span>

<span data-ttu-id="9417f-308">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="9417f-308">If you can't get email working:</span></span>

* <span data-ttu-id="9417f-309">Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="9417f-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="9417f-310">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="9417f-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="9417f-311">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="9417f-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="9417f-312">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="9417f-312">Check your spam folder.</span></span>
* <span data-ttu-id="9417f-313">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="9417f-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="9417f-314">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="9417f-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="9417f-315">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="9417f-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="9417f-316">Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="9417f-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="9417f-317">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="9417f-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="9417f-318">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="9417f-318">Combine social and local login accounts</span></span>

<span data-ttu-id="9417f-319">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="9417f-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="9417f-320">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="9417f-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="9417f-321">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="9417f-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="9417f-322">V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="9417f-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="9417f-324">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="9417f-324">Click on the **Manage** link.</span></span> <span data-ttu-id="9417f-325">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-325">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="9417f-327">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="9417f-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="9417f-328">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="9417f-328">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="9417f-330">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="9417f-330">The two accounts have been combined.</span></span> <span data-ttu-id="9417f-331">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="9417f-331">You are able to sign in with either account.</span></span> <span data-ttu-id="9417f-332">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="9417f-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="9417f-333">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="9417f-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="9417f-334">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="9417f-335">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="9417f-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="9417f-336">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="9417f-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="9417f-337">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="9417f-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="9417f-338">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="9417f-338">Confirm existing users.</span></span> <span data-ttu-id="9417f-339">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="9417f-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
