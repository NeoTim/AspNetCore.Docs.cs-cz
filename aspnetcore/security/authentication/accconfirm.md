---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit aplikaci ASP.NET Core s e-mailové potvrzení a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 802ba446af04df6a35ac73187ad693b8ec80c654
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814837"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="ae29c-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae29c-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="ae29c-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ae29c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ae29c-105">Tento kurz ukazuje, jak vytvářet aplikace v ASP.NET Core s e-mailové potvrzení a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="ae29c-106">Tento kurz je **není** začátku tématu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="ae29c-107">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="ae29c-107">You should be familiar with:</span></span>

* [<span data-ttu-id="ae29c-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae29c-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ae29c-109">Ověřování</span><span class="sxs-lookup"><span data-stu-id="ae29c-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ae29c-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ae29c-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ae29c-111">Zobrazit [tento soubor PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro verzi technologie ASP.NET Core 1.1.</span><span class="sxs-lookup"><span data-stu-id="ae29c-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="ae29c-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ae29c-112">Prerequisites</span></span>

[<span data-ttu-id="ae29c-113">Sady SDK .NET core 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae29c-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="ae29c-114">Vytvoření a otestování webové aplikace s ověřováním</span><span class="sxs-lookup"><span data-stu-id="ae29c-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="ae29c-115">Spusťte následující příkazy k vytvoření webové aplikace s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="ae29c-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="ae29c-116">Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae29c-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ae29c-117">Po registraci, budete přesměrováni na na do `/Identity/Account/RegisterConfirmation` stránku, která obsahuje odkaz pro simulaci potvrzení e-mailu:</span><span class="sxs-lookup"><span data-stu-id="ae29c-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="ae29c-118">Vyberte `Click here to confirm your account` odkaz.</span><span class="sxs-lookup"><span data-stu-id="ae29c-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="ae29c-119">Vyberte **přihlášení** odkaz a přihlaste se pomocí stejných přihlašovacích údajů.</span><span class="sxs-lookup"><span data-stu-id="ae29c-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="ae29c-120">Vyberte `Hello YourEmail@provider.com!` odkaz, což vás přesměruje na `/Identity/Account/Manage/PersonalData` stránky.</span><span class="sxs-lookup"><span data-stu-id="ae29c-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="ae29c-121">Vyberte **osobní údaje** kartu na levé straně a pak vyberte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="ae29c-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="ae29c-122">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-122">Configure an email provider</span></span>

<span data-ttu-id="ae29c-123">V tomto kurzu [SendGrid](https://sendgrid.com) se používá k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ae29c-124">Potřebujete účet SendGrid a klíč k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ae29c-125">Můžete použít jiné poskytovateli e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-125">You can use other email providers.</span></span> <span data-ttu-id="ae29c-126">Doporučujeme že použít SendGrid nebo jiné služby e-mailu k odeslání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ae29c-127">SMTP je obtížné zabezpečení a zařídit správné nastavení.</span><span class="sxs-lookup"><span data-stu-id="ae29c-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ae29c-128">Vytvoření třídy k načtení klíče zabezpečeného e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ae29c-129">V tomto příkladu vytvoření *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae29c-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ae29c-130">Konfigurace služby SendGrid tajných klíčů uživatelů</span><span class="sxs-lookup"><span data-stu-id="ae29c-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ae29c-131">Nastavte `SendGridUser` a `SendGridKey` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ae29c-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ae29c-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ae29c-132">For example:</span></span>

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ae29c-133">Na Windows, manažera tajných ukládá dvojice klíčů/hodnota v *secrets.json* soubor `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="ae29c-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ae29c-134">Obsah *secrets.json* souboru nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="ae29c-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ae29c-135">Následující kód ukazuje *secrets.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="ae29c-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ae29c-136">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="ae29c-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ae29c-137">Další informace najdete v tématu [možnosti vzor](xref:fundamentals/configuration/options) a [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ae29c-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ae29c-138">Instalace služby SendGrid</span><span class="sxs-lookup"><span data-stu-id="ae29c-138">Install SendGrid</span></span>

<span data-ttu-id="ae29c-139">Tento kurz ukazuje, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete odesílat e-mailu pomocí protokolu SMTP a další mechanismy.</span><span class="sxs-lookup"><span data-stu-id="ae29c-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ae29c-140">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="ae29c-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae29c-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae29c-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae29c-142">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ae29c-142">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ae29c-143">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ae29c-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ae29c-144">V konzole zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ae29c-144">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="ae29c-145">Naleznete v tématu [začít pomocí Sendgridu zdarma](https://sendgrid.com/free/) k registraci bezplatného účtu SendGrid.</span><span class="sxs-lookup"><span data-stu-id="ae29c-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ae29c-146">Implementace IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ae29c-146">Implement IEmailSender</span></span>

<span data-ttu-id="ae29c-147">Implementovat `IEmailSender`, vytvořit *Services/EmailSender.cs* podobně jako následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ae29c-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ae29c-148">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-148">Configure startup to support email</span></span>

<span data-ttu-id="ae29c-149">Přidejte následující kód, který `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="ae29c-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ae29c-150">Přidat `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="ae29c-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ae29c-151">Zaregistrujte `AuthMessageSenderOptions` instance konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ae29c-152">Zaregistrovat a potvrďte e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="ae29c-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ae29c-153">Spuštění webové aplikace a testů potvrzení účtu a heslo pro obnovení toku.</span><span class="sxs-lookup"><span data-stu-id="ae29c-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ae29c-154">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="ae29c-154">Run the app and register a new user</span></span>
* <span data-ttu-id="ae29c-155">Zkontrolujte svého e-mailu na odkaz pro potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ae29c-156">Zobrazit [ladění e-mailu](#debug) Pokud neobdržíte e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ae29c-157">Klikněte na odkaz pro potvrzení e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ae29c-158">Přihlaste se pomocí e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="ae29c-159">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="ae29c-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ae29c-160">Resetování hesla testu</span><span class="sxs-lookup"><span data-stu-id="ae29c-160">Test password reset</span></span>

* <span data-ttu-id="ae29c-161">Pokud jste přihlášeni, vyberte **odhlášení**.</span><span class="sxs-lookup"><span data-stu-id="ae29c-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ae29c-162">Vyberte **přihlášení** spojit a vybrat možnost **zapomněli jste heslo?** odkaz.</span><span class="sxs-lookup"><span data-stu-id="ae29c-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ae29c-163">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ae29c-164">Odešle e-mail s odkazem k resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ae29c-165">Zkontrolujte e-mailu a klikněte na odkaz pro resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ae29c-166">Po úspěšném resetování vašeho hesla se můžete přihlásit pomocí své e-mailu a nové heslo.</span><span class="sxs-lookup"><span data-stu-id="ae29c-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ae29c-167">Časový limit změny e-mailu a aktivita</span><span class="sxs-lookup"><span data-stu-id="ae29c-167">Change email and activity timeout</span></span>

<span data-ttu-id="ae29c-168">Výchozí hodnota časového limitu nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="ae29c-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ae29c-169">Následující kód nastaví časový limit neaktivity na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="ae29c-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ae29c-170">Změnit všechny lifespans token ochrany dat</span><span class="sxs-lookup"><span data-stu-id="ae29c-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="ae29c-171">Následující kód změní všechny data protection tokeny časový limit na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="ae29c-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="ae29c-172">Integrovaná v tokenech identitu uživatele (naleznete v tématu [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mít [vypršení časového limitu jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ae29c-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ae29c-173">Změnit životnost tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-173">Change the email token lifespan</span></span>

<span data-ttu-id="ae29c-174">Výchozí token životnosti [tokeny Identity uživatele](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ae29c-174">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ae29c-175">Tato část ukazuje, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ae29c-176">Přidat vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="ae29c-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ae29c-177">Přidáte vlastního zprostředkovatele do služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="ae29c-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ae29c-178">Znovu poslat e-mailové potvrzení</span><span class="sxs-lookup"><span data-stu-id="ae29c-178">Resend email confirmation</span></span>

<span data-ttu-id="ae29c-179">Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="ae29c-179">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ae29c-180">Ladění e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-180">Debug email</span></span>

<span data-ttu-id="ae29c-181">Pokud nelze získat pracovní e-mailu:</span><span class="sxs-lookup"><span data-stu-id="ae29c-181">If you can't get email working:</span></span>

* <span data-ttu-id="ae29c-182">Nastavte zarážku v `EmailSender.Execute` ověření `SendGridClient.SendEmailAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="ae29c-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ae29c-183">Vytvoření [konzolovou aplikaci k odesílání e-mailu](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) pomocí podobné kódu `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="ae29c-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ae29c-184">Zkontrolujte [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) stránky.</span><span class="sxs-lookup"><span data-stu-id="ae29c-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ae29c-185">Zkontrolujte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="ae29c-185">Check your spam folder.</span></span>
* <span data-ttu-id="ae29c-186">Zkuste jinou e-mailový alias na jinou e-mailovou zprostředkovatele (Microsoft, Yahoo, Gmail, atd.)</span><span class="sxs-lookup"><span data-stu-id="ae29c-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ae29c-187">Pokuste se odeslat na jiné e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="ae29c-188">**Z bezpečnostních důvodů** je **není** použití produkční tajných kódů v vývoj a testování.</span><span class="sxs-lookup"><span data-stu-id="ae29c-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ae29c-189">Pokud publikujete aplikaci do Azure, nastavte SendGrid tajné kódy jako nastavení aplikace na portálu Azure Web App.</span><span class="sxs-lookup"><span data-stu-id="ae29c-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ae29c-190">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae29c-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ae29c-191">Sloučit účty sociálních sítí a místní přihlášení</span><span class="sxs-lookup"><span data-stu-id="ae29c-191">Combine social and local login accounts</span></span>

<span data-ttu-id="ae29c-192">K dokončení této části, je nutné nejprve povolit externí zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="ae29c-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ae29c-193">Zobrazit [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ae29c-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ae29c-194">Kliknutím na odkaz na vaši e-mailu můžete kombinovat místní a sociální účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ae29c-195">V tomto pořadí "RickAndMSFT@gmail.com" se nejprve vytvoří jako místní přihlášení; však můžete nejprve vytvořte účet jako přihlášení prostřednictvím sociální sítě a pak přidat místní přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="ae29c-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com uživatel byl ověřen](accconfirm/_static/rick.png)

<span data-ttu-id="ae29c-197">Klikněte na **spravovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="ae29c-197">Click on the **Manage** link.</span></span> <span data-ttu-id="ae29c-198">Poznámka: externí 0 (přihlašování přes sociální sítě) spojená s tímto účtem.</span><span class="sxs-lookup"><span data-stu-id="ae29c-198">Note the 0 external (social logins) associated with this account.</span></span>

![Správa zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="ae29c-200">Klikněte na odkaz pro další přihlášení služby a přijímání požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ae29c-201">Na následujícím obrázku je Facebooku zprostředkovatele externího ověřování:</span><span class="sxs-lookup"><span data-stu-id="ae29c-201">In the following image, Facebook is the external authentication provider:</span></span>

![Spravovat externí přihlášení zobrazení výpisu Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="ae29c-203">Byli sloučeni dva účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-203">The two accounts have been combined.</span></span> <span data-ttu-id="ae29c-204">Máte možnost přihlásit se přes účet.</span><span class="sxs-lookup"><span data-stu-id="ae29c-204">You are able to sign in with either account.</span></span> <span data-ttu-id="ae29c-205">Můžete chtít uživatelům přidat místní účty v případě nefungující Služba ověřování v jejich přihlášení prostřednictvím sociální sítě nebo spíše se jste ztratili přístup k jejich účtu na sociální síti.</span><span class="sxs-lookup"><span data-stu-id="ae29c-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ae29c-206">Po lokality má uživatelům povolit potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="ae29c-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ae29c-207">Povolení potvrzení účtu na webu s uživateli zamezí všichni stávající uživatelé.</span><span class="sxs-lookup"><span data-stu-id="ae29c-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ae29c-208">Stávající uživatelé jsou uzamčen, protože jejich účty nejsou potvrzeny.</span><span class="sxs-lookup"><span data-stu-id="ae29c-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ae29c-209">Obejít existující uzamčení uživatelů, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="ae29c-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ae29c-210">Aktualizujte databázi pro označení všichni stávající uživatelé, jako je potvrzen.</span><span class="sxs-lookup"><span data-stu-id="ae29c-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ae29c-211">Zkontrolujte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae29c-211">Confirm existing users.</span></span> <span data-ttu-id="ae29c-212">Třeba dávkové odeslání e-mailů s potvrzovací odkazy.</span><span class="sxs-lookup"><span data-stu-id="ae29c-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ae29c-213">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ae29c-213">Prerequisites</span></span>

[<span data-ttu-id="ae29c-214">Sady SDK .NET core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="ae29c-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="ae29c-215">Vytvoření webové aplikace a generování uživatelského rozhraní Identity</span><span class="sxs-lookup"><span data-stu-id="ae29c-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="ae29c-216">Spusťte následující příkazy k vytvoření webové aplikace s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="ae29c-216">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="ae29c-217">Otestovat nové registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="ae29c-217">Test new user registration</span></span>

<span data-ttu-id="ae29c-218">Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae29c-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ae29c-219">V tomto okamžiku je pouze ověření na e-mailu [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="ae29c-219">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="ae29c-220">Po odeslání registrace, jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="ae29c-221">Později v tomto kurzu se kód aktualizuje tak, že noví uživatelé nemůže přihlásit, dokud se ověří e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="ae29c-222">Poznámka: v tabulce `EmailConfirmed` pole je `False`.</span><span class="sxs-lookup"><span data-stu-id="ae29c-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="ae29c-223">Můžete chtít tento e-mail znovu použít v dalším kroku při ní odešle e-mail s potvrzením.</span><span class="sxs-lookup"><span data-stu-id="ae29c-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="ae29c-224">Klikněte pravým tlačítkem na řádek a vyberte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="ae29c-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="ae29c-225">Odstraňuje se e-mailový alias usnadňuje v následujících krocích.</span><span class="sxs-lookup"><span data-stu-id="ae29c-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="ae29c-226">Vyžádání potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-226">Require email confirmation</span></span>

<span data-ttu-id="ae29c-227">Je osvědčeným postupem je potvrďte e-mailu nové registrace uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae29c-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="ae29c-228">E-mailu pomáhá potvrzení ověření někdo jiný, nejsou zosobnění (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu).</span><span class="sxs-lookup"><span data-stu-id="ae29c-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="ae29c-229">Předpokládejme, že jste měli diskusní fórum, a chtěli byste zabránit "yli@example.com"registroval jako"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="ae29c-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="ae29c-230">Bez potvrzení e-mailu "nolivetto@contoso.com" mohli dostávat nežádoucí e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="ae29c-231">Předpokládejme, že uživatel zaregistrován nechtěně jako "ylo@example.com" a kdyby si všimli chyba "yli".</span><span class="sxs-lookup"><span data-stu-id="ae29c-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="ae29c-232">Se nebude moci použít obnovení hesla, protože aplikace nemá správnou e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="ae29c-233">Potvrzení e-mailu zajišťuje omezenou ochranu před roboty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="ae29c-234">Potvrzení e-mailu neposkytuje ochranu z uživateli se zlými úmysly s mnoha e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="ae29c-235">Obvykle chcete novým uživatelům zabránit v účtování všechna data na webový server dříve, než potvrzeno e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="ae29c-236">Aktualizace `Startup.ConfigureServices` tak, aby vyžadovala potvrzeno e-mailu:</span><span class="sxs-lookup"><span data-stu-id="ae29c-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="ae29c-237">`config.SignIn.RequireConfirmedEmail = true;` zabraňuje registrovaných uživatelů přihlásit, dokud není potvrzené e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="ae29c-238">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-238">Configure email provider</span></span>

<span data-ttu-id="ae29c-239">V tomto kurzu [SendGrid](https://sendgrid.com) se používá k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ae29c-240">Potřebujete účet SendGrid a klíč k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ae29c-241">Můžete použít jiné poskytovateli e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-241">You can use other email providers.</span></span> <span data-ttu-id="ae29c-242">ASP.NET Core 2.x zahrnuje `System.Net.Mail`, který umožňuje odeslání e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="ae29c-243">Doporučujeme že použít SendGrid nebo jiné služby e-mailu k odeslání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ae29c-244">SMTP je obtížné zabezpečení a zařídit správné nastavení.</span><span class="sxs-lookup"><span data-stu-id="ae29c-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ae29c-245">Vytvoření třídy k načtení klíče zabezpečeného e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ae29c-246">V tomto příkladu vytvoření *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ae29c-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ae29c-247">Konfigurace služby SendGrid tajných klíčů uživatelů</span><span class="sxs-lookup"><span data-stu-id="ae29c-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ae29c-248">Nastavte `SendGridUser` a `SendGridKey` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ae29c-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ae29c-249">Příklad:</span><span class="sxs-lookup"><span data-stu-id="ae29c-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ae29c-250">Na Windows, manažera tajných ukládá dvojice klíčů/hodnota v *secrets.json* soubor `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="ae29c-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ae29c-251">Obsah *secrets.json* souboru nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="ae29c-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ae29c-252">Následující kód ukazuje *secrets.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="ae29c-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ae29c-253">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="ae29c-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ae29c-254">Další informace najdete v tématu [možnosti vzor](xref:fundamentals/configuration/options) a [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ae29c-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ae29c-255">Instalace služby SendGrid</span><span class="sxs-lookup"><span data-stu-id="ae29c-255">Install SendGrid</span></span>

<span data-ttu-id="ae29c-256">Tento kurz ukazuje, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete odesílat e-mailu pomocí protokolu SMTP a další mechanismy.</span><span class="sxs-lookup"><span data-stu-id="ae29c-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ae29c-257">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="ae29c-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ae29c-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ae29c-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ae29c-259">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ae29c-259">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ae29c-260">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="ae29c-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ae29c-261">V konzole zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ae29c-261">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="ae29c-262">Naleznete v tématu [začít pomocí Sendgridu zdarma](https://sendgrid.com/free/) k registraci bezplatného účtu SendGrid.</span><span class="sxs-lookup"><span data-stu-id="ae29c-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ae29c-263">Implementace IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ae29c-263">Implement IEmailSender</span></span>

<span data-ttu-id="ae29c-264">Implementovat `IEmailSender`, vytvořit *Services/EmailSender.cs* podobně jako následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ae29c-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ae29c-265">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-265">Configure startup to support email</span></span>

<span data-ttu-id="ae29c-266">Přidejte následující kód, který `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="ae29c-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ae29c-267">Přidat `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="ae29c-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ae29c-268">Zaregistrujte `AuthMessageSenderOptions` instance konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="ae29c-269">Povolit obnovení hesla a potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="ae29c-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="ae29c-270">Šablona má kód pro obnovení potvrzení a heslo účtu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="ae29c-271">Najít `OnPostAsync` metoda *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ae29c-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="ae29c-272">Nově zaregistrovaný uživatelům zabránit v probíhá automaticky přihlašování tak následující řádek:</span><span class="sxs-lookup"><span data-stu-id="ae29c-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="ae29c-273">Úplná metoda se zobrazí s změněný řádek zvýrazněný:</span><span class="sxs-lookup"><span data-stu-id="ae29c-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ae29c-274">Zaregistrovat a potvrďte e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="ae29c-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ae29c-275">Spuštění webové aplikace a testů potvrzení účtu a heslo pro obnovení toku.</span><span class="sxs-lookup"><span data-stu-id="ae29c-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ae29c-276">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="ae29c-276">Run the app and register a new user</span></span>
* <span data-ttu-id="ae29c-277">Zkontrolujte svého e-mailu na odkaz pro potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ae29c-278">Zobrazit [ladění e-mailu](#debug) Pokud neobdržíte e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ae29c-279">Klikněte na odkaz pro potvrzení e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ae29c-280">Přihlaste se pomocí e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="ae29c-281">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="ae29c-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="ae29c-282">Zobrazení stránky Správa</span><span class="sxs-lookup"><span data-stu-id="ae29c-282">View the manage page</span></span>

<span data-ttu-id="ae29c-283">Vyberte své uživatelské jméno v prohlížeči: ![okna prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="ae29c-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="ae29c-284">Zobrazí se stránka Správa s **profilu** vybraná karta.</span><span class="sxs-lookup"><span data-stu-id="ae29c-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="ae29c-285">**E-mailu** zobrazí zaškrtávací políčko označující e-mailu byl potvrzen.</span><span class="sxs-lookup"><span data-stu-id="ae29c-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ae29c-286">Resetování hesla testu</span><span class="sxs-lookup"><span data-stu-id="ae29c-286">Test password reset</span></span>

* <span data-ttu-id="ae29c-287">Pokud jste přihlášeni, vyberte **odhlášení**.</span><span class="sxs-lookup"><span data-stu-id="ae29c-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ae29c-288">Vyberte **přihlášení** spojit a vybrat možnost **zapomněli jste heslo?** odkaz.</span><span class="sxs-lookup"><span data-stu-id="ae29c-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ae29c-289">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ae29c-290">Odešle e-mail s odkazem k resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ae29c-291">Zkontrolujte e-mailu a klikněte na odkaz pro resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="ae29c-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ae29c-292">Po úspěšném resetování vašeho hesla se můžete přihlásit pomocí své e-mailu a nové heslo.</span><span class="sxs-lookup"><span data-stu-id="ae29c-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ae29c-293">Časový limit změny e-mailu a aktivita</span><span class="sxs-lookup"><span data-stu-id="ae29c-293">Change email and activity timeout</span></span>

<span data-ttu-id="ae29c-294">Výchozí hodnota časového limitu nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="ae29c-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ae29c-295">Následující kód nastaví časový limit neaktivity na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="ae29c-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ae29c-296">Změnit všechny lifespans token ochrany dat</span><span class="sxs-lookup"><span data-stu-id="ae29c-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="ae29c-297">Následující kód změní všechny data protection tokeny časový limit na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="ae29c-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="ae29c-298">Integrovaná v tokenech identitu uživatele (naleznete v tématu [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mít [vypršení časového limitu jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ae29c-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ae29c-299">Změnit životnost tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-299">Change the email token lifespan</span></span>

<span data-ttu-id="ae29c-300">Výchozí token životnosti [tokeny Identity uživatele](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ae29c-300">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ae29c-301">Tato část ukazuje, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="ae29c-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ae29c-302">Přidat vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="ae29c-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ae29c-303">Přidáte vlastního zprostředkovatele do služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="ae29c-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ae29c-304">Znovu poslat e-mailové potvrzení</span><span class="sxs-lookup"><span data-stu-id="ae29c-304">Resend email confirmation</span></span>

<span data-ttu-id="ae29c-305">Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="ae29c-305">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ae29c-306">Ladění e-mailu</span><span class="sxs-lookup"><span data-stu-id="ae29c-306">Debug email</span></span>

<span data-ttu-id="ae29c-307">Pokud nelze získat pracovní e-mailu:</span><span class="sxs-lookup"><span data-stu-id="ae29c-307">If you can't get email working:</span></span>

* <span data-ttu-id="ae29c-308">Nastavte zarážku v `EmailSender.Execute` ověření `SendGridClient.SendEmailAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="ae29c-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ae29c-309">Vytvoření [konzolovou aplikaci k odesílání e-mailu](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) pomocí podobné kódu `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="ae29c-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ae29c-310">Zkontrolujte [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) stránky.</span><span class="sxs-lookup"><span data-stu-id="ae29c-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ae29c-311">Zkontrolujte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="ae29c-311">Check your spam folder.</span></span>
* <span data-ttu-id="ae29c-312">Zkuste jinou e-mailový alias na jinou e-mailovou zprostředkovatele (Microsoft, Yahoo, Gmail, atd.)</span><span class="sxs-lookup"><span data-stu-id="ae29c-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ae29c-313">Pokuste se odeslat na jiné e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="ae29c-314">**Z bezpečnostních důvodů** je **není** použití produkční tajných kódů v vývoj a testování.</span><span class="sxs-lookup"><span data-stu-id="ae29c-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ae29c-315">Pokud publikujete aplikaci do Azure, můžete nastavit SendGrid tajné kódy jako nastavení aplikace ve webové aplikaci Azure portal.</span><span class="sxs-lookup"><span data-stu-id="ae29c-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ae29c-316">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="ae29c-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ae29c-317">Sloučit účty sociálních sítí a místní přihlášení</span><span class="sxs-lookup"><span data-stu-id="ae29c-317">Combine social and local login accounts</span></span>

<span data-ttu-id="ae29c-318">K dokončení této části, je nutné nejprve povolit externí zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="ae29c-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ae29c-319">Zobrazit [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ae29c-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ae29c-320">Kliknutím na odkaz na vaši e-mailu můžete kombinovat místní a sociální účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ae29c-321">V tomto pořadí "RickAndMSFT@gmail.com" se nejprve vytvoří jako místní přihlášení; však můžete nejprve vytvořte účet jako přihlášení prostřednictvím sociální sítě a pak přidat místní přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="ae29c-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com uživatel byl ověřen](accconfirm/_static/rick.png)

<span data-ttu-id="ae29c-323">Klikněte na **spravovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="ae29c-323">Click on the **Manage** link.</span></span> <span data-ttu-id="ae29c-324">Poznámka: externí 0 (přihlašování přes sociální sítě) spojená s tímto účtem.</span><span class="sxs-lookup"><span data-stu-id="ae29c-324">Note the 0 external (social logins) associated with this account.</span></span>

![Správa zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="ae29c-326">Klikněte na odkaz pro další přihlášení služby a přijímání požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ae29c-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ae29c-327">Na následujícím obrázku je Facebooku zprostředkovatele externího ověřování:</span><span class="sxs-lookup"><span data-stu-id="ae29c-327">In the following image, Facebook is the external authentication provider:</span></span>

![Spravovat externí přihlášení zobrazení výpisu Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="ae29c-329">Byli sloučeni dva účty.</span><span class="sxs-lookup"><span data-stu-id="ae29c-329">The two accounts have been combined.</span></span> <span data-ttu-id="ae29c-330">Máte možnost přihlásit se přes účet.</span><span class="sxs-lookup"><span data-stu-id="ae29c-330">You are able to sign in with either account.</span></span> <span data-ttu-id="ae29c-331">Můžete chtít uživatelům přidat místní účty v případě nefungující Služba ověřování v jejich přihlášení prostřednictvím sociální sítě nebo spíše se jste ztratili přístup k jejich účtu na sociální síti.</span><span class="sxs-lookup"><span data-stu-id="ae29c-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ae29c-332">Po lokality má uživatelům povolit potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="ae29c-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ae29c-333">Povolení potvrzení účtu na webu s uživateli zamezí všichni stávající uživatelé.</span><span class="sxs-lookup"><span data-stu-id="ae29c-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ae29c-334">Stávající uživatelé jsou uzamčen, protože jejich účty nejsou potvrzeny.</span><span class="sxs-lookup"><span data-stu-id="ae29c-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ae29c-335">Obejít existující uzamčení uživatelů, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="ae29c-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ae29c-336">Aktualizujte databázi pro označení všichni stávající uživatelé, jako je potvrzen.</span><span class="sxs-lookup"><span data-stu-id="ae29c-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ae29c-337">Zkontrolujte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="ae29c-337">Confirm existing users.</span></span> <span data-ttu-id="ae29c-338">Třeba dávkové odeslání e-mailů s potvrzovací odkazy.</span><span class="sxs-lookup"><span data-stu-id="ae29c-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
