---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit aplikaci ASP.NET Core s e-mailové potvrzení a resetováním hesla.
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 59041bcf11f7deb351a2f0bb075ed80c8af5e12b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64898317"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="3f0c1-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f0c1-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="3f0c1-104">Zobrazit [tento soubor PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core 1.1 a verze 2.1.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3f0c1-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="3f0c1-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="3f0c1-106">Tento kurz ukazuje, jak vytvářet aplikace v ASP.NET Core s e-mailové potvrzení a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="3f0c1-107">Tento kurz je **není** začátku tématu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="3f0c1-108">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-108">You should be familiar with:</span></span>

* [<span data-ttu-id="3f0c1-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3f0c1-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="3f0c1-110">Ověřování</span><span class="sxs-lookup"><span data-stu-id="3f0c1-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="3f0c1-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3f0c1-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="3f0c1-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3f0c1-112">Prerequisites</span></span>

[<span data-ttu-id="3f0c1-113">Sady SDK .NET core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="3f0c1-113">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="3f0c1-114">Vytvoření webové aplikace a generování uživatelského rozhraní Identity</span><span class="sxs-lookup"><span data-stu-id="3f0c1-114">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="3f0c1-115">Spusťte následující příkazy k vytvoření webové aplikace s ověřováním.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="3f0c1-116">Otestovat nové registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="3f0c1-116">Test new user registration</span></span>

<span data-ttu-id="3f0c1-117">Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-117">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="3f0c1-118">V tomto okamžiku je pouze ověření na e-mailu [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-118">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="3f0c1-119">Po odeslání registrace, jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-119">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="3f0c1-120">Později v tomto kurzu se kód aktualizuje tak, že noví uživatelé nemůže přihlásit, dokud se ověří e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-120">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="3f0c1-121">Poznámka: v tabulce `EmailConfirmed` pole je `False`.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-121">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="3f0c1-122">Můžete chtít tento e-mail znovu použít v dalším kroku při ní odešle e-mail s potvrzením.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-122">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="3f0c1-123">Klikněte pravým tlačítkem na řádek a vyberte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-123">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="3f0c1-124">Odstraňuje se e-mailový alias usnadňuje v následujících krocích.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-124">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="3f0c1-125">Vyžádání potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-125">Require email confirmation</span></span>

<span data-ttu-id="3f0c1-126">Je osvědčeným postupem je potvrďte e-mailu nové registrace uživatele.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-126">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="3f0c1-127">E-mailu pomáhá potvrzení ověření někdo jiný, nejsou zosobnění (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-127">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="3f0c1-128">Předpokládejme, že jste měli diskusní fórum, a chtěli byste zabránit "yli@example.com"registroval jako"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="3f0c1-128">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="3f0c1-129">Bez potvrzení e-mailu "nolivetto@contoso.com" mohli dostávat nežádoucí e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-129">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="3f0c1-130">Předpokládejme, že uživatel zaregistrován nechtěně jako "ylo@example.com" a kdyby si všimli chyba "yli".</span><span class="sxs-lookup"><span data-stu-id="3f0c1-130">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="3f0c1-131">Se nebude moci použít obnovení hesla, protože aplikace nemá správnou e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-131">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="3f0c1-132">Potvrzení e-mailu zajišťuje omezenou ochranu před roboty.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-132">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="3f0c1-133">Potvrzení e-mailu neposkytuje ochranu z uživateli se zlými úmysly s mnoha e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-133">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="3f0c1-134">Obvykle chcete novým uživatelům zabránit v účtování všechna data na webový server dříve, než potvrzeno e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-134">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="3f0c1-135">Aktualizace `Startup.ConfigureServices` tak, aby vyžadovala potvrzeno e-mailu:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-135">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="3f0c1-136">`config.SignIn.RequireConfirmedEmail = true;` zabraňuje registrovaných uživatelů přihlásit, dokud není potvrzené e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-136">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="3f0c1-137">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-137">Configure email provider</span></span>

<span data-ttu-id="3f0c1-138">V tomto kurzu [SendGrid](https://sendgrid.com) se používá k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-138">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="3f0c1-139">Potřebujete účet SendGrid a klíč k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-139">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="3f0c1-140">Můžete použít jiné poskytovateli e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-140">You can use other email providers.</span></span> <span data-ttu-id="3f0c1-141">ASP.NET Core 2.x zahrnuje `System.Net.Mail`, který umožňuje odeslání e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-141">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="3f0c1-142">Doporučujeme že použít SendGrid nebo jiné služby e-mailu k odeslání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-142">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="3f0c1-143">SMTP je obtížné zabezpečení a zařídit správné nastavení.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-143">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="3f0c1-144">Vytvoření třídy k načtení klíče zabezpečeného e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-144">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="3f0c1-145">V tomto příkladu vytvoření *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-145">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="3f0c1-146">Konfigurace služby SendGrid tajných klíčů uživatelů</span><span class="sxs-lookup"><span data-stu-id="3f0c1-146">Configure SendGrid user secrets</span></span>

<span data-ttu-id="3f0c1-147">Nastavte `SendGridUser` a `SendGridKey` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-147">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="3f0c1-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-148">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="3f0c1-149">Na Windows, manažera tajných ukládá dvojice klíčů/hodnota v *secrets.json* soubor `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-149">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="3f0c1-150">Obsah *secrets.json* souboru nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-150">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="3f0c1-151">Následující kód ukazuje *secrets.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-151">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="3f0c1-152">`SendGridKey` Hodnota byla odebrána.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-152">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="3f0c1-153">Další informace najdete v tématu [možnosti vzor](xref:fundamentals/configuration/options) a [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-153">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="3f0c1-154">Instalace služby SendGrid</span><span class="sxs-lookup"><span data-stu-id="3f0c1-154">Install SendGrid</span></span>

<span data-ttu-id="3f0c1-155">Tento kurz ukazuje, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete odesílat e-mailu pomocí protokolu SMTP a další mechanismy.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-155">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="3f0c1-156">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-156">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3f0c1-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3f0c1-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3f0c1-158">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-158">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3f0c1-159">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="3f0c1-159">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3f0c1-160">V konzole zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-160">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="3f0c1-161">Naleznete v tématu [začít pomocí Sendgridu zdarma](https://sendgrid.com/free/) k registraci bezplatného účtu SendGrid.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-161">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="3f0c1-162">Implementace IEmailSender</span><span class="sxs-lookup"><span data-stu-id="3f0c1-162">Implement IEmailSender</span></span>

<span data-ttu-id="3f0c1-163">Implementovat `IEmailSender`, vytvořit *Services/EmailSender.cs* podobně jako následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-163">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="3f0c1-164">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-164">Configure startup to support email</span></span>

<span data-ttu-id="3f0c1-165">Přidejte následující kód, který `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-165">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="3f0c1-166">Přidat `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-166">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="3f0c1-167">Zaregistrujte `AuthMessageSenderOptions` instance konfigurace.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-167">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="3f0c1-168">Povolit obnovení hesla a potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-168">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="3f0c1-169">Šablona má kód pro obnovení potvrzení a heslo účtu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-169">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="3f0c1-170">Najít `OnPostAsync` metoda *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-170">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="3f0c1-171">Nově zaregistrovaný uživatelům zabránit v probíhá automaticky přihlašování tak následující řádek:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-171">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="3f0c1-172">Úplná metoda se zobrazí s změněný řádek zvýrazněný:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-172">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="3f0c1-173">Zaregistrovat a potvrďte e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="3f0c1-173">Register, confirm email, and reset password</span></span>

<span data-ttu-id="3f0c1-174">Spuštění webové aplikace a testů potvrzení účtu a heslo pro obnovení toku.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-174">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="3f0c1-175">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="3f0c1-175">Run the app and register a new user</span></span>
* <span data-ttu-id="3f0c1-176">Zkontrolujte svého e-mailu na odkaz pro potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-176">Check your email for the account confirmation link.</span></span> <span data-ttu-id="3f0c1-177">Zobrazit [ladění e-mailu](#debug) Pokud neobdržíte e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-177">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="3f0c1-178">Klikněte na odkaz pro potvrzení e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-178">Click the link to confirm your email.</span></span>
* <span data-ttu-id="3f0c1-179">Přihlaste se pomocí e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-179">Sign in with your email and password.</span></span>
* <span data-ttu-id="3f0c1-180">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-180">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="3f0c1-181">Zobrazení stránky Správa</span><span class="sxs-lookup"><span data-stu-id="3f0c1-181">View the manage page</span></span>

<span data-ttu-id="3f0c1-182">Vyberte své uživatelské jméno v prohlížeči: ![okna prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="3f0c1-182">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="3f0c1-183">Zobrazí se stránka Správa s **profilu** vybraná karta.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-183">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="3f0c1-184">**E-mailu** zobrazí zaškrtávací políčko označující e-mailu byl potvrzen.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-184">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="3f0c1-185">Resetování hesla testu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-185">Test password reset</span></span>

* <span data-ttu-id="3f0c1-186">Pokud jste přihlášeni, vyberte **odhlášení**.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-186">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="3f0c1-187">Vyberte **přihlášení** spojit a vybrat možnost **zapomněli jste heslo?** odkaz.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-187">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="3f0c1-188">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-188">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="3f0c1-189">Odešle e-mail s odkazem k resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-189">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="3f0c1-190">Zkontrolujte e-mailu a klikněte na odkaz pro resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-190">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="3f0c1-191">Po úspěšném resetování vašeho hesla se můžete přihlásit pomocí své e-mailu a nové heslo.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-191">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="3f0c1-192">Časový limit změny e-mailu a aktivita</span><span class="sxs-lookup"><span data-stu-id="3f0c1-192">Change email and activity timeout</span></span>

<span data-ttu-id="3f0c1-193">Výchozí hodnota časového limitu nečinnosti je 14 dní.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-193">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="3f0c1-194">Následující kód nastaví časový limit neaktivity na 5 dní:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-194">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="3f0c1-195">Změnit všechny lifespans token ochrany dat</span><span class="sxs-lookup"><span data-stu-id="3f0c1-195">Change all data protection token lifespans</span></span>

<span data-ttu-id="3f0c1-196">Následující kód změní všechny data protection tokeny časový limit na 3 hodiny:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-196">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="3f0c1-197">Integrovaná v tokenech identitu uživatele (naleznete v tématu [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mít [vypršení časového limitu jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-197">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="3f0c1-198">Změnit životnost tokenu e-mailu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-198">Change the email token lifespan</span></span>

<span data-ttu-id="3f0c1-199">Výchozí token životnosti [tokeny Identity uživatele](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-199">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="3f0c1-200">Tato část ukazuje, jak změnit životnost tokenu e-mailu.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-200">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="3f0c1-201">Přidat vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-201">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="3f0c1-202">Přidáte vlastního zprostředkovatele do služby kontejneru:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-202">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="3f0c1-203">Znovu poslat e-mailové potvrzení</span><span class="sxs-lookup"><span data-stu-id="3f0c1-203">Resend email confirmation</span></span>

<span data-ttu-id="3f0c1-204">Zobrazit [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-204">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="3f0c1-205">Ladění e-mailu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-205">Debug email</span></span>

<span data-ttu-id="3f0c1-206">Pokud nelze získat pracovní e-mailu:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-206">If you can't get email working:</span></span>

* <span data-ttu-id="3f0c1-207">Nastavte zarážku v `EmailSender.Execute` ověření `SendGridClient.SendEmailAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-207">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="3f0c1-208">Vytvoření [konzolovou aplikaci k odesílání e-mailu](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) pomocí podobné kódu `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-208">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="3f0c1-209">Zkontrolujte [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) stránky.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-209">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="3f0c1-210">Zkontrolujte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-210">Check your spam folder.</span></span>
* <span data-ttu-id="3f0c1-211">Zkuste jinou e-mailový alias na jinou e-mailovou zprostředkovatele (Microsoft, Yahoo, Gmail, atd.)</span><span class="sxs-lookup"><span data-stu-id="3f0c1-211">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="3f0c1-212">Pokuste se odeslat na jiné e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-212">Try sending to different email accounts.</span></span>

<span data-ttu-id="3f0c1-213">**Z bezpečnostních důvodů** je **není** použití produkční tajných kódů v vývoj a testování.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-213">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="3f0c1-214">Pokud publikujete aplikaci do Azure, můžete nastavit SendGrid tajné kódy jako nastavení aplikace ve webové aplikaci Azure portal.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-214">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="3f0c1-215">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-215">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="3f0c1-216">Sloučit účty sociálních sítí a místní přihlášení</span><span class="sxs-lookup"><span data-stu-id="3f0c1-216">Combine social and local login accounts</span></span>

<span data-ttu-id="3f0c1-217">K dokončení této části, je nutné nejprve povolit externí zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-217">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="3f0c1-218">Zobrazit [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="3f0c1-218">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="3f0c1-219">Kliknutím na odkaz na vaši e-mailu můžete kombinovat místní a sociální účty.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-219">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="3f0c1-220">V tomto pořadí "RickAndMSFT@gmail.com" se nejprve vytvoří jako místní přihlášení; však můžete nejprve vytvořte účet jako přihlášení prostřednictvím sociální sítě a pak přidat místní přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-220">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com uživatel byl ověřen](accconfirm/_static/rick.png)

<span data-ttu-id="3f0c1-222">Klikněte na **spravovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-222">Click on the **Manage** link.</span></span> <span data-ttu-id="3f0c1-223">Poznámka: externí 0 (přihlašování přes sociální sítě) spojená s tímto účtem.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-223">Note the 0 external (social logins) associated with this account.</span></span>

![Správa zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="3f0c1-225">Klikněte na odkaz pro další přihlášení služby a přijímání požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-225">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="3f0c1-226">Na následujícím obrázku je Facebooku zprostředkovatele externího ověřování:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-226">In the following image, Facebook is the external authentication provider:</span></span>

![Spravovat externí přihlášení zobrazení výpisu Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="3f0c1-228">Byli sloučeni dva účty.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-228">The two accounts have been combined.</span></span> <span data-ttu-id="3f0c1-229">Máte možnost přihlásit se přes účet.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-229">You are able to sign in with either account.</span></span> <span data-ttu-id="3f0c1-230">Můžete chtít uživatelům přidat místní účty v případě nefungující Služba ověřování v jejich přihlášení prostřednictvím sociální sítě nebo spíše se jste ztratili přístup k jejich účtu na sociální síti.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-230">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="3f0c1-231">Po lokality má uživatelům povolit potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="3f0c1-231">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="3f0c1-232">Povolení potvrzení účtu na webu s uživateli zamezí všichni stávající uživatelé.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-232">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="3f0c1-233">Stávající uživatelé jsou uzamčen, protože jejich účty nejsou potvrzeny.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-233">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="3f0c1-234">Obejít existující uzamčení uživatelů, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="3f0c1-234">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="3f0c1-235">Aktualizujte databázi pro označení všichni stávající uživatelé, jako je potvrzen.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-235">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="3f0c1-236">Zkontrolujte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-236">Confirm existing users.</span></span> <span data-ttu-id="3f0c1-237">Třeba dávkové odeslání e-mailů s potvrzovací odkazy.</span><span class="sxs-lookup"><span data-stu-id="3f0c1-237">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
