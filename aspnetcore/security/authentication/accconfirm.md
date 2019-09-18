---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 8a515990be584aa1233fc3bf77811ae3784d9b1c
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081565"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="7cb79-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cb79-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="7cb79-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="7cb79-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="7cb79-105">V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="7cb79-106">Tento **kurz není** úvodním tématem.</span><span class="sxs-lookup"><span data-stu-id="7cb79-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="7cb79-107">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="7cb79-107">You should be familiar with:</span></span>

* [<span data-ttu-id="7cb79-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7cb79-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="7cb79-109">Ověřování</span><span class="sxs-lookup"><span data-stu-id="7cb79-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="7cb79-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7cb79-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="7cb79-111">V [tomto souboru PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) se zobrazí verze ASP.NET Core 1,1.</span><span class="sxs-lookup"><span data-stu-id="7cb79-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="7cb79-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="7cb79-112">Prerequisites</span></span>

[<span data-ttu-id="7cb79-113">.NET Core 3,0 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="7cb79-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="7cb79-114">Vytvoření a otestování webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="7cb79-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="7cb79-115">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="7cb79-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="7cb79-116">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="7cb79-117">Po zaregistrování budete přesměrováni na stránku `/Identity/Account/RegisterConfirmation` , která obsahuje odkaz pro simulaci potvrzení e-mailu:</span><span class="sxs-lookup"><span data-stu-id="7cb79-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="7cb79-118">`Click here to confirm your account` Vyberte odkaz.</span><span class="sxs-lookup"><span data-stu-id="7cb79-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="7cb79-119">Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.</span><span class="sxs-lookup"><span data-stu-id="7cb79-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="7cb79-120">Vyberte odkaz, který vás přesměruje `/Identity/Account/Manage/PersonalData` na stránku. `Hello YourEmail@provider.com!`</span><span class="sxs-lookup"><span data-stu-id="7cb79-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="7cb79-121">Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7cb79-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="7cb79-122">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-122">Configure an email provider</span></span>

<span data-ttu-id="7cb79-123">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="7cb79-124">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="7cb79-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="7cb79-125">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-125">You can use other email providers.</span></span> <span data-ttu-id="7cb79-126">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="7cb79-127">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="7cb79-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="7cb79-128">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="7cb79-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="7cb79-129">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cb79-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="7cb79-130">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="7cb79-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="7cb79-131">`SendGridUser` Nastavte a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7cb79-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7cb79-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7cb79-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="7cb79-133">Správce tajných kódů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných klíčů. JSON* v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="7cb79-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="7cb79-134">Obsah souboru *tajných kódů. JSON* není zašifrovaný.</span><span class="sxs-lookup"><span data-stu-id="7cb79-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="7cb79-135">Následující kód ukazuje soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="7cb79-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="7cb79-136">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="7cb79-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="7cb79-137">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7cb79-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="7cb79-138">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="7cb79-138">Install SendGrid</span></span>

<span data-ttu-id="7cb79-139">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="7cb79-140">Nainstalujte balíček `SendGrid` NuGet:</span><span class="sxs-lookup"><span data-stu-id="7cb79-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7cb79-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cb79-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7cb79-142">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7cb79-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7cb79-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cb79-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7cb79-144">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7cb79-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="7cb79-145">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="7cb79-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="7cb79-146">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="7cb79-146">Implement IEmailSender</span></span>

<span data-ttu-id="7cb79-147">K implementaci `IEmailSender`vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="7cb79-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="7cb79-148">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-148">Configure startup to support email</span></span>

<span data-ttu-id="7cb79-149">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="7cb79-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="7cb79-150">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="7cb79-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="7cb79-151">Zaregistrujte instanci `AuthMessageSenderOptions` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7cb79-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="7cb79-152">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="7cb79-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="7cb79-153">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="7cb79-154">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="7cb79-154">Run the app and register a new user</span></span>
* <span data-ttu-id="7cb79-155">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="7cb79-156">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="7cb79-157">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="7cb79-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="7cb79-158">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="7cb79-159">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="7cb79-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="7cb79-160">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="7cb79-160">Test password reset</span></span>

* <span data-ttu-id="7cb79-161">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="7cb79-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="7cb79-162">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="7cb79-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="7cb79-163">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="7cb79-164">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="7cb79-165">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="7cb79-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="7cb79-166">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="7cb79-167">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="7cb79-167">Change email and activity timeout</span></span>

<span data-ttu-id="7cb79-168">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="7cb79-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="7cb79-169">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="7cb79-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="7cb79-170">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="7cb79-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="7cb79-171">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="7cb79-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="7cb79-172">Předdefinované tokeny uživatelů identity (viz [AspNetCore/src/identity/Extensions. Core/src/TokenOptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7cb79-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="7cb79-173">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-173">Change the email token lifespan</span></span>

<span data-ttu-id="7cb79-174">Výchozí životnost tokenu [tokenů identity uživatele](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7cb79-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="7cb79-175">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="7cb79-176">Přidejte vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="7cb79-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="7cb79-177">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="7cb79-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="7cb79-178">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="7cb79-178">Resend email confirmation</span></span>

<span data-ttu-id="7cb79-179">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="7cb79-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="7cb79-180">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="7cb79-180">Debug email</span></span>

<span data-ttu-id="7cb79-181">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="7cb79-181">If you can't get email working:</span></span>

* <span data-ttu-id="7cb79-182">Nastavte zarážku v `EmailSender.Execute` na hodnotu `SendGridClient.SendEmailAsync` ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="7cb79-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="7cb79-183">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s `EmailSender.Execute`podobným kódem.</span><span class="sxs-lookup"><span data-stu-id="7cb79-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="7cb79-184">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="7cb79-185">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="7cb79-185">Check your spam folder.</span></span>
* <span data-ttu-id="7cb79-186">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="7cb79-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="7cb79-187">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="7cb79-188">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="7cb79-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="7cb79-189">Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="7cb79-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7cb79-190">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="7cb79-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="7cb79-191">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="7cb79-191">Combine social and local login accounts</span></span>

<span data-ttu-id="7cb79-192">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="7cb79-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="7cb79-193">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7cb79-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7cb79-194">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="7cb79-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="7cb79-195">V následujícím pořadí se "RickAndMSFT@gmail.com" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="7cb79-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="7cb79-197">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="7cb79-197">Click on the **Manage** link.</span></span> <span data-ttu-id="7cb79-198">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-198">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="7cb79-200">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7cb79-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="7cb79-201">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="7cb79-201">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="7cb79-203">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="7cb79-203">The two accounts have been combined.</span></span> <span data-ttu-id="7cb79-204">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-204">You are able to sign in with either account.</span></span> <span data-ttu-id="7cb79-205">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="7cb79-206">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="7cb79-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="7cb79-207">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="7cb79-208">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="7cb79-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="7cb79-209">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7cb79-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="7cb79-210">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="7cb79-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="7cb79-211">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-211">Confirm existing users.</span></span> <span data-ttu-id="7cb79-212">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="7cb79-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="7cb79-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="7cb79-213">Prerequisites</span></span>

[<span data-ttu-id="7cb79-214">.NET Core 2,2 SDK nebo novější</span><span class="sxs-lookup"><span data-stu-id="7cb79-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="7cb79-215">Vytvoření webové aplikace a identity uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="7cb79-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="7cb79-216">Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="7cb79-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="7cb79-217">Otestovat registraci nových uživatelů</span><span class="sxs-lookup"><span data-stu-id="7cb79-217">Test new user registration</span></span>

<span data-ttu-id="7cb79-218">Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="7cb79-219">V tomto okamžiku je jediným ověřením e-mailu atribut [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="7cb79-220">Po odeslání registrace jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="7cb79-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="7cb79-221">Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.</span><span class="sxs-lookup"><span data-stu-id="7cb79-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="7cb79-222">Všimněte si, že `EmailConfirmed` pole tabulky `False`je.</span><span class="sxs-lookup"><span data-stu-id="7cb79-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="7cb79-223">Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail.</span><span class="sxs-lookup"><span data-stu-id="7cb79-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="7cb79-224">Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**.</span><span class="sxs-lookup"><span data-stu-id="7cb79-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="7cb79-225">Odstranění aliasu e-mailu usnadňuje následující kroky.</span><span class="sxs-lookup"><span data-stu-id="7cb79-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="7cb79-226">Vyžadovat potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-226">Require email confirmation</span></span>

<span data-ttu-id="7cb79-227">Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="7cb79-228">Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného).</span><span class="sxs-lookup"><span data-stu-id="7cb79-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="7cb79-229">Předpokládejme, že máte diskuzní fórum a chtěli jste zabránityli@example.comv registracinolivetto@contoso.comjako.</span><span class="sxs-lookup"><span data-stu-id="7cb79-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="7cb79-230">Bez potvrzení e-mailu může zprávanolivetto@contoso.comz vaší aplikace obdržet nevyžádaný e-mail.</span><span class="sxs-lookup"><span data-stu-id="7cb79-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="7cb79-231">Předpokládejme, že uživatel omylem registrovanýylo@example.comjako "" a jste zaznamenal chybu "Yli".</span><span class="sxs-lookup"><span data-stu-id="7cb79-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="7cb79-232">Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail.</span><span class="sxs-lookup"><span data-stu-id="7cb79-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="7cb79-233">Potvrzení e-mailu poskytuje omezené ochrany z roboty.</span><span class="sxs-lookup"><span data-stu-id="7cb79-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="7cb79-234">Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty</span><span class="sxs-lookup"><span data-stu-id="7cb79-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="7cb79-235">Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.</span><span class="sxs-lookup"><span data-stu-id="7cb79-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="7cb79-236">Aktualizace `Startup.ConfigureServices` pro vyžadování potvrzeného e-mailové adresy:</span><span class="sxs-lookup"><span data-stu-id="7cb79-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="7cb79-237">`config.SignIn.RequireConfirmedEmail = true;`zabrání registrovaným uživatelům v přihlášení, dokud se nepotvrdí jejich e-maily.</span><span class="sxs-lookup"><span data-stu-id="7cb79-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="7cb79-238">Konfigurovat poskytovatele e-mailů</span><span class="sxs-lookup"><span data-stu-id="7cb79-238">Configure email provider</span></span>

<span data-ttu-id="7cb79-239">V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="7cb79-240">K odeslání e-mailu potřebujete účet SendGrid a klíč.</span><span class="sxs-lookup"><span data-stu-id="7cb79-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="7cb79-241">Můžete použít jiné poskytovatele e-mailů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-241">You can use other email providers.</span></span> <span data-ttu-id="7cb79-242">ASP.NET Core 2. x zahrnuje `System.Net.Mail`, což umožňuje odesílat e-maily z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="7cb79-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="7cb79-243">K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="7cb79-244">Protokol SMTP je obtížné zabezpečit a nastavit správně.</span><span class="sxs-lookup"><span data-stu-id="7cb79-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="7cb79-245">Vytvořte třídu, která načte zabezpečený e-mailový klíč.</span><span class="sxs-lookup"><span data-stu-id="7cb79-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="7cb79-246">V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="7cb79-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="7cb79-247">Konfigurace uživatelských tajných klíčů SendGrid</span><span class="sxs-lookup"><span data-stu-id="7cb79-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="7cb79-248">`SendGridUser` Nastavte a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="7cb79-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="7cb79-249">Příklad:</span><span class="sxs-lookup"><span data-stu-id="7cb79-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="7cb79-250">Správce tajných kódů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných klíčů. JSON* v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="7cb79-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="7cb79-251">Obsah souboru *tajných kódů. JSON* není zašifrovaný.</span><span class="sxs-lookup"><span data-stu-id="7cb79-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="7cb79-252">Následující kód ukazuje soubor *tajných kódů. JSON* .</span><span class="sxs-lookup"><span data-stu-id="7cb79-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="7cb79-253">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="7cb79-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="7cb79-254">Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="7cb79-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="7cb79-255">Nainstalovat SendGrid</span><span class="sxs-lookup"><span data-stu-id="7cb79-255">Install SendGrid</span></span>

<span data-ttu-id="7cb79-256">V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="7cb79-257">Nainstalujte balíček `SendGrid` NuGet:</span><span class="sxs-lookup"><span data-stu-id="7cb79-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7cb79-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7cb79-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7cb79-259">V konzole správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7cb79-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="7cb79-260">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="7cb79-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="7cb79-261">Z konzoly zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7cb79-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="7cb79-262">Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.</span><span class="sxs-lookup"><span data-stu-id="7cb79-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="7cb79-263">Implementovat IEmailSender</span><span class="sxs-lookup"><span data-stu-id="7cb79-263">Implement IEmailSender</span></span>

<span data-ttu-id="7cb79-264">K implementaci `IEmailSender`vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:</span><span class="sxs-lookup"><span data-stu-id="7cb79-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="7cb79-265">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-265">Configure startup to support email</span></span>

<span data-ttu-id="7cb79-266">Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="7cb79-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="7cb79-267">Přidejte `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="7cb79-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="7cb79-268">Zaregistrujte instanci `AuthMessageSenderOptions` konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7cb79-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="7cb79-269">Povolení potvrzení účtu a obnovení hesla</span><span class="sxs-lookup"><span data-stu-id="7cb79-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="7cb79-270">Šablona obsahuje kód pro potvrzení účtu a obnovení hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="7cb79-271">Vyhledejte metodu v *oblasti/identita/stránky/účet/Register. cshtml. cs.* `OnPostAsync`</span><span class="sxs-lookup"><span data-stu-id="7cb79-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="7cb79-272">Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:</span><span class="sxs-lookup"><span data-stu-id="7cb79-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="7cb79-273">Metoda Complete se zobrazí se zvýrazněným změněným řádkem:</span><span class="sxs-lookup"><span data-stu-id="7cb79-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="7cb79-274">Registrace, potvrzení e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="7cb79-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="7cb79-275">Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="7cb79-276">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="7cb79-276">Run the app and register a new user</span></span>
* <span data-ttu-id="7cb79-277">Ověřte si e-mail s odkazem na potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="7cb79-278">Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="7cb79-279">Kliknutím na odkaz potvrďte svůj e-mail.</span><span class="sxs-lookup"><span data-stu-id="7cb79-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="7cb79-280">Přihlaste se pomocí svého e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="7cb79-281">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="7cb79-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="7cb79-282">Zobrazit stránku pro správu</span><span class="sxs-lookup"><span data-stu-id="7cb79-282">View the manage page</span></span>

<span data-ttu-id="7cb79-283">V prohlížeči vyberte své uživatelské jméno: ![okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="7cb79-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="7cb79-284">Stránka Správa se zobrazí s vybranou kartou **profil** .</span><span class="sxs-lookup"><span data-stu-id="7cb79-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="7cb79-285">**E-mail** zobrazuje zaškrtávací políčko označující, že byl e-mail potvrzen.</span><span class="sxs-lookup"><span data-stu-id="7cb79-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="7cb79-286">Test resetování hesla</span><span class="sxs-lookup"><span data-stu-id="7cb79-286">Test password reset</span></span>

* <span data-ttu-id="7cb79-287">Pokud jste přihlášeni, vyberte **Odhlásit**.</span><span class="sxs-lookup"><span data-stu-id="7cb79-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="7cb79-288">Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .</span><span class="sxs-lookup"><span data-stu-id="7cb79-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="7cb79-289">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="7cb79-290">Pošle se e-mail s odkazem na resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="7cb79-291">Podívejte se na e-mail a kliknutím na odkaz resetujte heslo.</span><span class="sxs-lookup"><span data-stu-id="7cb79-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="7cb79-292">Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.</span><span class="sxs-lookup"><span data-stu-id="7cb79-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="7cb79-293">Časový limit změny e-mailu a aktivity</span><span class="sxs-lookup"><span data-stu-id="7cb79-293">Change email and activity timeout</span></span>

<span data-ttu-id="7cb79-294">Výchozí časový limit nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="7cb79-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="7cb79-295">Následující kód nastaví časový limit nečinnosti na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="7cb79-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="7cb79-296">Změna všech životností tokenů ochrany dat</span><span class="sxs-lookup"><span data-stu-id="7cb79-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="7cb79-297">Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="7cb79-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="7cb79-298">Předdefinované tokeny uživatelů identity (viz [AspNetCore/src/identity/Extensions. Core/src/TokenOptions. cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7cb79-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="7cb79-299">Změna životnosti tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="7cb79-299">Change the email token lifespan</span></span>

<span data-ttu-id="7cb79-300">Výchozí životnost tokenu [tokenů identity uživatele](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="7cb79-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="7cb79-301">V této části se dozvíte, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="7cb79-302">Přidejte vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a: <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions></span><span class="sxs-lookup"><span data-stu-id="7cb79-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="7cb79-303">Přidejte vlastního zprostředkovatele do kontejneru služby:</span><span class="sxs-lookup"><span data-stu-id="7cb79-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="7cb79-304">Odeslat potvrzení znovu e-mailem</span><span class="sxs-lookup"><span data-stu-id="7cb79-304">Resend email confirmation</span></span>

<span data-ttu-id="7cb79-305">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="7cb79-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="7cb79-306">Ladit e-mail</span><span class="sxs-lookup"><span data-stu-id="7cb79-306">Debug email</span></span>

<span data-ttu-id="7cb79-307">Pokud nemůžete získat e-mail:</span><span class="sxs-lookup"><span data-stu-id="7cb79-307">If you can't get email working:</span></span>

* <span data-ttu-id="7cb79-308">Nastavte zarážku v `EmailSender.Execute` na hodnotu `SendGridClient.SendEmailAsync` ověřit je volána.</span><span class="sxs-lookup"><span data-stu-id="7cb79-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="7cb79-309">Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s `EmailSender.Execute`podobným kódem.</span><span class="sxs-lookup"><span data-stu-id="7cb79-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="7cb79-310">Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="7cb79-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="7cb79-311">Ověřte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="7cb79-311">Check your spam folder.</span></span>
* <span data-ttu-id="7cb79-312">Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)</span><span class="sxs-lookup"><span data-stu-id="7cb79-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="7cb79-313">Zkuste odesílat do jiných e-mailových účtů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="7cb79-314">**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje.</span><span class="sxs-lookup"><span data-stu-id="7cb79-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="7cb79-315">Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure.</span><span class="sxs-lookup"><span data-stu-id="7cb79-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="7cb79-316">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="7cb79-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="7cb79-317">Kombinování sociálních a místních přihlašovacích účtů</span><span class="sxs-lookup"><span data-stu-id="7cb79-317">Combine social and local login accounts</span></span>

<span data-ttu-id="7cb79-318">Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování.</span><span class="sxs-lookup"><span data-stu-id="7cb79-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="7cb79-319">Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="7cb79-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="7cb79-320">Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz.</span><span class="sxs-lookup"><span data-stu-id="7cb79-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="7cb79-321">V následujícím pořadí se "RickAndMSFT@gmail.com" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.</span><span class="sxs-lookup"><span data-stu-id="7cb79-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

<span data-ttu-id="7cb79-323">Klikněte na odkaz **Správa** .</span><span class="sxs-lookup"><span data-stu-id="7cb79-323">Click on the **Manage** link.</span></span> <span data-ttu-id="7cb79-324">Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-324">Note the 0 external (social logins) associated with this account.</span></span>

![Spravovat zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="7cb79-326">Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci.</span><span class="sxs-lookup"><span data-stu-id="7cb79-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="7cb79-327">Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:</span><span class="sxs-lookup"><span data-stu-id="7cb79-327">In the following image, Facebook is the external authentication provider:</span></span>

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="7cb79-329">Tyto dva účty byly kombinovány.</span><span class="sxs-lookup"><span data-stu-id="7cb79-329">The two accounts have been combined.</span></span> <span data-ttu-id="7cb79-330">Můžete se přihlásit pomocí obou účtů.</span><span class="sxs-lookup"><span data-stu-id="7cb79-330">You are able to sign in with either account.</span></span> <span data-ttu-id="7cb79-331">Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.</span><span class="sxs-lookup"><span data-stu-id="7cb79-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="7cb79-332">Povolit potvrzení účtu, jakmile má lokalita uživatele</span><span class="sxs-lookup"><span data-stu-id="7cb79-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="7cb79-333">Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="7cb79-334">Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené.</span><span class="sxs-lookup"><span data-stu-id="7cb79-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="7cb79-335">Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="7cb79-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="7cb79-336">Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.</span><span class="sxs-lookup"><span data-stu-id="7cb79-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="7cb79-337">Potvrďte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="7cb79-337">Confirm existing users.</span></span> <span data-ttu-id="7cb79-338">Například Batch – posílání e-mailů s odkazy na potvrzení.</span><span class="sxs-lookup"><span data-stu-id="7cb79-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
