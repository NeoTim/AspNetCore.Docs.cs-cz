---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: b7856a3004cfc76acfb485ff8f1fadf87f5aa904
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777108"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="8dd0d-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8dd0d-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="8dd0d-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="8dd0d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="8dd0d-105">V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="8dd0d-106">Tento **kurz není** úvodním tématem.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="8dd0d-107">Měli byste být obeznámeni s:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-107">You should be familiar with:</span></span>

* [<span data-ttu-id="8dd0d-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8dd0d-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="8dd0d-109">Authentication</span><span class="sxs-lookup"><span data-stu-id="8dd0d-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="8dd0d-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="8dd0d-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8dd0d-111">V [tomto souboru PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) se zobrazí verze ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="8dd0d-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8dd0d-112">Prerequisites</span></span>

[<span data-ttu-id="8dd0d-113">.NET Core 3,0 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="8dd0d-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="8dd0d-114">Vytvoření a otestování webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="8dd0d-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="8dd0d-115">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="8dd0d-116">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="8dd0d-117">Po zaregistrování budete přesměrováni na stránku `/Identity/Account/RegisterConfirmation` , která obsahuje odkaz pro simulaci potvrzení e-mailu:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="8dd0d-118">Vyberte `Click here to confirm your account` odkaz.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="8dd0d-119">Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="8dd0d-120">Vyberte `Hello YourEmail@provider.com!` odkaz, který vás přesměruje na `/Identity/Account/Manage/PersonalData` stránku.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="8dd0d-121">Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="8dd0d-122">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-122">Configure an email provider</span></span>

<span data-ttu-id="8dd0d-123">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="8dd0d-124">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="8dd0d-125">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-125">You can use other email providers.</span></span> <span data-ttu-id="8dd0d-126">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="8dd0d-127">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="8dd0d-128">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="8dd0d-129">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="8dd0d-130">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="8dd0d-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="8dd0d-131">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8dd0d-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="8dd0d-133">Správce tajných kódů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných klíčů. JSON* v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="8dd0d-134">Obsah souboru *tajných kódů. JSON* není zašifrovaný.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="8dd0d-135">Následující kód ukazuje soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="8dd0d-136">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="8dd0d-137">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="8dd0d-138">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="8dd0d-138">Install SendGrid</span></span>

<span data-ttu-id="8dd0d-139">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="8dd0d-140">Nainstalujte balíček `SendGrid` NuGet:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd0d-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd0d-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd0d-142">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="8dd0d-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8dd0d-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8dd0d-144">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="8dd0d-145">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="8dd0d-146">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="8dd0d-146">Implement IEmailSender</span></span>

<span data-ttu-id="8dd0d-147">K implementaci `IEmailSender`vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="8dd0d-148">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-148">Configure startup to support email</span></span>

<span data-ttu-id="8dd0d-149">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="8dd0d-150">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="8dd0d-151">Zaregistrujte instanci `AuthMessageSenderOptions` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="8dd0d-152">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="8dd0d-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="8dd0d-153">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="8dd0d-154">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="8dd0d-154">Run the app and register a new user</span></span>
* <span data-ttu-id="8dd0d-155">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="8dd0d-156">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="8dd0d-157">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="8dd0d-158">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="8dd0d-159">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="8dd0d-160">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="8dd0d-160">Test password reset</span></span>

* <span data-ttu-id="8dd0d-161">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="8dd0d-162">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="8dd0d-163">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="8dd0d-164">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="8dd0d-165">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="8dd0d-166">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="8dd0d-167">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="8dd0d-167">Change email and activity timeout</span></span>

<span data-ttu-id="8dd0d-168">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="8dd0d-169">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="8dd0d-170">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="8dd0d-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="8dd0d-171">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="8dd0d-172">Předdefinované tokeny uživatelů identity (viz [AspNetCore/src/identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="8dd0d-173">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-173">Change the email token lifespan</span></span>

<span data-ttu-id="8dd0d-174">Výchozí životnost tokenu [tokenů identity uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="8dd0d-175">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="8dd0d-176">Přidejte vlastní [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="8dd0d-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="8dd0d-177">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="8dd0d-178">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="8dd0d-178">Resend email confirmation</span></span>

<span data-ttu-id="8dd0d-179">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="8dd0d-180">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="8dd0d-180">Debug email</span></span>

<span data-ttu-id="8dd0d-181">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-181">If you can't get email working:</span></span>

* <span data-ttu-id="8dd0d-182">Nastavte zarážku v `EmailSender.Execute` na hodnotu `SendGridClient.SendEmailAsync` ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="8dd0d-183">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s `EmailSender.Execute`podobným kódem.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="8dd0d-184">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="8dd0d-185">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-185">Check your spam folder.</span></span>
* <span data-ttu-id="8dd0d-186">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="8dd0d-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="8dd0d-187">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="8dd0d-188">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="8dd0d-189">Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="8dd0d-190">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="8dd0d-191">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="8dd0d-191">Combine social and local login accounts</span></span>

<span data-ttu-id="8dd0d-192">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="8dd0d-193">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="8dd0d-194">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="8dd0d-195">V následující sekvenciRickAndMSFT@gmail.comje nejprve vytvořena jako místní přihlášení; účet ale můžete vytvořit jako nejdřív jako sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="8dd0d-197">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-197">Click on the **Manage** link.</span></span> <span data-ttu-id="8dd0d-198">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-198">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="8dd0d-200">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="8dd0d-201">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-201">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="8dd0d-203">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-203">The two accounts have been combined.</span></span> <span data-ttu-id="8dd0d-204">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-204">You are able to sign in with either account.</span></span> <span data-ttu-id="8dd0d-205">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="8dd0d-206">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="8dd0d-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="8dd0d-207">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="8dd0d-208">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="8dd0d-209">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="8dd0d-210">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="8dd0d-211">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-211">Confirm existing users.</span></span> <span data-ttu-id="8dd0d-212">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="8dd0d-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8dd0d-213">Prerequisites</span></span>

[<span data-ttu-id="8dd0d-214">.NET Core 2,2 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="8dd0d-214">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="8dd0d-215">Vytvoření webové aplikace a identity uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="8dd0d-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="8dd0d-216">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="8dd0d-217">Otestovat registraci nových uživatelů</span><span class="sxs-lookup"><span data-stu-id="8dd0d-217">Test new user registration</span></span>

<span data-ttu-id="8dd0d-218">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="8dd0d-219">V tomto okamžiku je jediným ověřením e-mailu [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="8dd0d-220">Po odeslání registrace jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="8dd0d-221">Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="8dd0d-222">Všimněte si, že `EmailConfirmed` pole tabulky `False`je.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="8dd0d-223">Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="8dd0d-224">Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="8dd0d-225">Odstranění aliasu e-mailu usnadňuje následující kroky.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="8dd0d-226">Vyžadovat potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-226">Require email confirmation</span></span>

<span data-ttu-id="8dd0d-227">Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="8dd0d-228">Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="8dd0d-229">Předpokládejme, že máte diskuzní fórum a chtěli jste zabránityli@example.comv registraci jako.nolivetto@contoso.com</span><span class="sxs-lookup"><span data-stu-id="8dd0d-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="8dd0d-230">Bez potvrzení e-mailu může zprávanolivetto@contoso.comz vaší aplikace obdržet nevyžádaný e-mail.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="8dd0d-231">Předpokládejme, že uživatel omylem registrovanýylo@example.comjako "" a jste zaznamenal chybu "Yli".</span><span class="sxs-lookup"><span data-stu-id="8dd0d-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="8dd0d-232">Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="8dd0d-233">Potvrzení e-mailu poskytuje omezené ochrany z roboty.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="8dd0d-234">Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty</span><span class="sxs-lookup"><span data-stu-id="8dd0d-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="8dd0d-235">Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="8dd0d-236">Aktualizace `Startup.ConfigureServices` pro vyžadování potvrzeného e-mailové adresy:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="8dd0d-237">`config.SignIn.RequireConfirmedEmail = true;`zabrání registrovaným uživatelům v přihlášení, dokud se nepotvrdí jejich e-maily.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="8dd0d-238">Konfigurovat poskytovatele e-mailů</span><span class="sxs-lookup"><span data-stu-id="8dd0d-238">Configure email provider</span></span>

<span data-ttu-id="8dd0d-239">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="8dd0d-240">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="8dd0d-241">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-241">You can use other email providers.</span></span> <span data-ttu-id="8dd0d-242">ASP.NET Core 2. x zahrnuje `System.Net.Mail`, což umožňuje odesílat e-maily z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="8dd0d-243">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="8dd0d-244">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="8dd0d-245">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="8dd0d-246">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="8dd0d-247">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="8dd0d-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="8dd0d-248">Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8dd0d-249">Příklad:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="8dd0d-250">Správce tajných kódů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných klíčů. JSON* v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="8dd0d-251">Obsah souboru *tajných kódů. JSON* není zašifrovaný.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="8dd0d-252">Následující kód ukazuje soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="8dd0d-253">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="8dd0d-254">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="8dd0d-255">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="8dd0d-255">Install SendGrid</span></span>

<span data-ttu-id="8dd0d-256">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="8dd0d-257">Nainstalujte balíček `SendGrid` NuGet:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8dd0d-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8dd0d-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8dd0d-259">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="8dd0d-260">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="8dd0d-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8dd0d-261">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="8dd0d-262">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="8dd0d-263">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="8dd0d-263">Implement IEmailSender</span></span>

<span data-ttu-id="8dd0d-264">K implementaci `IEmailSender`vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="8dd0d-265">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-265">Configure startup to support email</span></span>

<span data-ttu-id="8dd0d-266">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="8dd0d-267">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="8dd0d-268">Zaregistrujte instanci `AuthMessageSenderOptions` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="8dd0d-269">Povolení potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="8dd0d-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="8dd0d-270">Šablona obsahuje kód pro potvrzení účtu a obnovení hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="8dd0d-271">Vyhledejte `OnPostAsync` metodu v *oblasti/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="8dd0d-272">Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="8dd0d-273">Metoda Complete se zobrazí se zvýrazněným změněným řádkem:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="8dd0d-274">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="8dd0d-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="8dd0d-275">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="8dd0d-276">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="8dd0d-276">Run the app and register a new user</span></span>
* <span data-ttu-id="8dd0d-277">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="8dd0d-278">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="8dd0d-279">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="8dd0d-280">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="8dd0d-281">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="8dd0d-282">Zobrazit stránku pro správu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-282">View the manage page</span></span>

<span data-ttu-id="8dd0d-283">V prohlížeči vyberte své uživatelské jméno: ![okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="8dd0d-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="8dd0d-284">Stránka Správa se zobrazí s vybranou kartou **profil** .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="8dd0d-285">**E-mail** zobrazuje zaškrtávací políčko označující, že byl e-mail potvrzen.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="8dd0d-286">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="8dd0d-286">Test password reset</span></span>

* <span data-ttu-id="8dd0d-287">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="8dd0d-288">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="8dd0d-289">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="8dd0d-290">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="8dd0d-291">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="8dd0d-292">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="8dd0d-293">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="8dd0d-293">Change email and activity timeout</span></span>

<span data-ttu-id="8dd0d-294">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="8dd0d-295">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="8dd0d-296">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="8dd0d-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="8dd0d-297">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="8dd0d-298">Předdefinované tokeny Identity uživatelů (viz [AspNetCore/srcIdentity//Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="8dd0d-299">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="8dd0d-299">Change the email token lifespan</span></span>

<span data-ttu-id="8dd0d-300">Výchozí životnost tokenu [tokenů Identity uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="8dd0d-301">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="8dd0d-302">Přidejte vlastní [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="8dd0d-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="8dd0d-303">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="8dd0d-304">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="8dd0d-304">Resend email confirmation</span></span>

<span data-ttu-id="8dd0d-305">Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="8dd0d-306">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="8dd0d-306">Debug email</span></span>

<span data-ttu-id="8dd0d-307">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-307">If you can't get email working:</span></span>

* <span data-ttu-id="8dd0d-308">Nastavte zarážku v `EmailSender.Execute` na hodnotu `SendGridClient.SendEmailAsync` ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="8dd0d-309">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s `EmailSender.Execute`podobným kódem.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="8dd0d-310">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="8dd0d-311">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-311">Check your spam folder.</span></span>
* <span data-ttu-id="8dd0d-312">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="8dd0d-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="8dd0d-313">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="8dd0d-314">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="8dd0d-315">Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="8dd0d-316">Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="8dd0d-317">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="8dd0d-317">Combine social and local login accounts</span></span>

<span data-ttu-id="8dd0d-318">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="8dd0d-319">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="8dd0d-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="8dd0d-320">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="8dd0d-321">V následující sekvenciRickAndMSFT@gmail.comje nejprve vytvořena jako místní přihlášení; účet ale můžete vytvořit jako nejdřív jako sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="8dd0d-323">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="8dd0d-323">Click on the **Manage** link.</span></span> <span data-ttu-id="8dd0d-324">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-324">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="8dd0d-326">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="8dd0d-327">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-327">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="8dd0d-329">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-329">The two accounts have been combined.</span></span> <span data-ttu-id="8dd0d-330">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-330">You are able to sign in with either account.</span></span> <span data-ttu-id="8dd0d-331">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="8dd0d-332">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="8dd0d-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="8dd0d-333">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="8dd0d-334">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="8dd0d-335">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8dd0d-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="8dd0d-336">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="8dd0d-337">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-337">Confirm existing users.</span></span> <span data-ttu-id="8dd0d-338">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="8dd0d-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
