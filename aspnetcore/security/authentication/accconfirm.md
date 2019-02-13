---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit aplikaci ASP.NET Core s e-mailové potvrzení a resetováním hesla.
ms.author: riande
ms.date: 2/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 77d7b209d57f9ee44f158798ff780ce85c87aaf2
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56159405"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="492ba-103">Potvrzení účtu a obnovení hesla v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="492ba-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="492ba-104">Zobrazit [tento soubor PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core 1.1 a verze 2.1.</span><span class="sxs-lookup"><span data-stu-id="492ba-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="492ba-105">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="492ba-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="492ba-106">Tento kurz ukazuje, jak vytvářet aplikace v ASP.NET Core s e-mailové potvrzení a resetováním hesla.</span><span class="sxs-lookup"><span data-stu-id="492ba-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="492ba-107">Tento kurz je **není** začátku tématu.</span><span class="sxs-lookup"><span data-stu-id="492ba-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="492ba-108">Měli byste se seznámit s:</span><span class="sxs-lookup"><span data-stu-id="492ba-108">You should be familiar with:</span></span>

* [<span data-ttu-id="492ba-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="492ba-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="492ba-110">Ověřování</span><span class="sxs-lookup"><span data-stu-id="492ba-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="492ba-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="492ba-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="492ba-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="492ba-112">Prerequisites</span></span>

[!INCLUDE [](~/includes/2.1-SDK.md)]

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="492ba-113">Vytvoření webové aplikace a generování uživatelského rozhraní Identity</span><span class="sxs-lookup"><span data-stu-id="492ba-113">Create a web  app and scaffold Identity</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="492ba-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="492ba-114">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="492ba-115">V sadě Visual Studio vytvořte nový **webovou aplikaci** projekt s názvem **WebPWrecover**.</span><span class="sxs-lookup"><span data-stu-id="492ba-115">In Visual Studio, create a new **Web Application** project named **WebPWrecover**.</span></span>
* <span data-ttu-id="492ba-116">Vyberte **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="492ba-116">Select **ASP.NET Core 2.1**.</span></span>
* <span data-ttu-id="492ba-117">Ponechte výchozí **ověřování** nastavena na **bez ověřování**.</span><span class="sxs-lookup"><span data-stu-id="492ba-117">Keep the default **Authentication** set to **No Authentication**.</span></span> <span data-ttu-id="492ba-118">V dalším kroku se přidá ověřování.</span><span class="sxs-lookup"><span data-stu-id="492ba-118">Authentication is added in the next step.</span></span>

<span data-ttu-id="492ba-119">V dalším kroku:</span><span class="sxs-lookup"><span data-stu-id="492ba-119">In the next step:</span></span>

* <span data-ttu-id="492ba-120">Nastavení stránky rozložení *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="492ba-120">Set the layout page to *~/Pages/Shared/_Layout.cshtml*</span></span>
* <span data-ttu-id="492ba-121">Vyberte *účtu/registrace*</span><span class="sxs-lookup"><span data-stu-id="492ba-121">Select *Account/Register*</span></span>
* <span data-ttu-id="492ba-122">Vytvořte nový **třída kontextu dat**</span><span class="sxs-lookup"><span data-stu-id="492ba-122">Create a new **Data context class**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="492ba-123">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="492ba-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet new webapp -o WebPWrecover
cd WebPWrecover
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -fi Account.Register -dc WebPWrecover.Models.WebPWrecoverContext
dotnet ef migrations add CreateIdentitySchema
dotnet ef database drop -f
dotnet ef database update
dotnet build
```

<span data-ttu-id="492ba-124">Spustit `dotnet aspnet-codegenerator identity --help` můžete zobrazit nápovědu pro nástroj pro generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="492ba-124">Run `dotnet aspnet-codegenerator identity --help` to get help on the scaffolding tool.</span></span>

------

<span data-ttu-id="492ba-125">Postupujte podle pokynů v [povolení ověřování](xref:security/authentication/scaffold-identity#useauthentication):</span><span class="sxs-lookup"><span data-stu-id="492ba-125">Follow the instructions in [Enable authentication](xref:security/authentication/scaffold-identity#useauthentication):</span></span>

* <span data-ttu-id="492ba-126">Přidat `app.UseAuthentication();` do `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="492ba-126">Add `app.UseAuthentication();` to `Startup.Configure`</span></span>
* <span data-ttu-id="492ba-127">Přidat `<partial name="_LoginPartial" />` do rozložení souboru.</span><span class="sxs-lookup"><span data-stu-id="492ba-127">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="492ba-128">Otestovat nové registrace uživatele</span><span class="sxs-lookup"><span data-stu-id="492ba-128">Test new user registration</span></span>

<span data-ttu-id="492ba-129">Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele.</span><span class="sxs-lookup"><span data-stu-id="492ba-129">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="492ba-130">V tomto okamžiku je pouze ověření na e-mailu [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="492ba-130">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="492ba-131">Po odeslání registrace, jste přihlášení do aplikace.</span><span class="sxs-lookup"><span data-stu-id="492ba-131">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="492ba-132">Později v tomto kurzu se kód aktualizuje tak, že noví uživatelé nemůže přihlásit, dokud se ověří e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-132">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="492ba-133">Poznámka: v tabulce `EmailConfirmed` pole je `False`.</span><span class="sxs-lookup"><span data-stu-id="492ba-133">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="492ba-134">Můžete chtít tento e-mail znovu použít v dalším kroku při ní odešle e-mail s potvrzením.</span><span class="sxs-lookup"><span data-stu-id="492ba-134">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="492ba-135">Klikněte pravým tlačítkem na řádek a vyberte **odstranit**.</span><span class="sxs-lookup"><span data-stu-id="492ba-135">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="492ba-136">Odstraňuje se e-mailový alias usnadňuje v následujících krocích.</span><span class="sxs-lookup"><span data-stu-id="492ba-136">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="492ba-137">Vyžádání potvrzení e-mailu</span><span class="sxs-lookup"><span data-stu-id="492ba-137">Require email confirmation</span></span>

<span data-ttu-id="492ba-138">Je osvědčeným postupem je potvrďte e-mailu nové registrace uživatele.</span><span class="sxs-lookup"><span data-stu-id="492ba-138">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="492ba-139">E-mailu pomáhá potvrzení ověření někdo jiný, nejsou zosobnění (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu).</span><span class="sxs-lookup"><span data-stu-id="492ba-139">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="492ba-140">Předpokládejme, že jste měli diskusní fórum, a chtěli byste zabránit "yli@example.com"registroval jako"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="492ba-140">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="492ba-141">Bez potvrzení e-mailu "nolivetto@contoso.com" mohli dostávat nežádoucí e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="492ba-141">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="492ba-142">Předpokládejme, že uživatel zaregistrován nechtěně jako "ylo@example.com" a kdyby si všimli chyba "yli".</span><span class="sxs-lookup"><span data-stu-id="492ba-142">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="492ba-143">Se nebude moci použít obnovení hesla, protože aplikace nemá správnou e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-143">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="492ba-144">Potvrzení e-mailu zajišťuje omezenou ochranu před roboty.</span><span class="sxs-lookup"><span data-stu-id="492ba-144">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="492ba-145">Potvrzení e-mailu neposkytuje ochranu z uživateli se zlými úmysly s mnoha e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="492ba-145">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="492ba-146">Obvykle chcete novým uživatelům zabránit v účtování všechna data na webový server dříve, než potvrzeno e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-146">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="492ba-147">Aktualizace *Areas/Identity/IdentityHostingStartup.cs* tak, aby vyžadovala potvrzeno e-mailu:</span><span class="sxs-lookup"><span data-stu-id="492ba-147">Update *Areas/Identity/IdentityHostingStartup.cs*  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/IdentityHostingStartup.cs?name=snippet1&highlight=10-13)]

<span data-ttu-id="492ba-148">`config.SignIn.RequireConfirmedEmail = true;` zabraňuje registrovaných uživatelů přihlásit, dokud není potvrzené e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-148">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="492ba-149">Konfigurace poskytovatele e-mailu</span><span class="sxs-lookup"><span data-stu-id="492ba-149">Configure email provider</span></span>

<span data-ttu-id="492ba-150">V tomto kurzu [SendGrid](https://sendgrid.com) se používá k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-150">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="492ba-151">Potřebujete účet SendGrid a klíč k odesílání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-151">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="492ba-152">Můžete použít jiné poskytovateli e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-152">You can use other email providers.</span></span> <span data-ttu-id="492ba-153">ASP.NET Core 2.x zahrnuje `System.Net.Mail`, který umožňuje odeslání e-mailu z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="492ba-153">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="492ba-154">Doporučujeme že použít SendGrid nebo jiné služby e-mailu k odeslání e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-154">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="492ba-155">SMTP je obtížné zabezpečení a zařídit správné nastavení.</span><span class="sxs-lookup"><span data-stu-id="492ba-155">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="492ba-156">Vytvoření třídy k načtení klíče zabezpečeného e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-156">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="492ba-157">V tomto příkladu vytvoření *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="492ba-157">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="492ba-158">Konfigurace služby SendGrid tajných klíčů uživatelů</span><span class="sxs-lookup"><span data-stu-id="492ba-158">Configure SendGrid user secrets</span></span>

<span data-ttu-id="492ba-159">Přidat jedinečný `<UserSecretsId>` hodnota, která se `<PropertyGroup>` prvek souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="492ba-159">Add a unique `<UserSecretsId>` value to the `<PropertyGroup>` element of the project file:</span></span>

[!code-xml[](accconfirm/sample/WebPWrecover21/WebPWrecover.csproj?highlight=5)]

<span data-ttu-id="492ba-160">Nastavte `SendGridUser` a `SendGridKey` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="492ba-160">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="492ba-161">Příklad:</span><span class="sxs-lookup"><span data-stu-id="492ba-161">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="492ba-162">Na Windows, manažera tajných ukládá dvojice klíčů/hodnota v *secrets.json* soubor `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáře.</span><span class="sxs-lookup"><span data-stu-id="492ba-162">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="492ba-163">Obsah *secrets.json* souboru nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="492ba-163">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="492ba-164">*Secrets.json* souboru je uveden níže ( `SendGridKey` hodnota byla odebrána.)</span><span class="sxs-lookup"><span data-stu-id="492ba-164">The *secrets.json* file is shown below (the `SendGridKey` value has been removed.)</span></span>

 ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```
 
<span data-ttu-id="492ba-165">Další informace najdete v tématu [možnosti vzor](xref:fundamentals/configuration/options) a [konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="492ba-165">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="492ba-166">Instalace služby SendGrid</span><span class="sxs-lookup"><span data-stu-id="492ba-166">Install SendGrid</span></span>

<span data-ttu-id="492ba-167">Tento kurz ukazuje, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete odesílat e-mailu pomocí protokolu SMTP a další mechanismy.</span><span class="sxs-lookup"><span data-stu-id="492ba-167">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="492ba-168">Nainstalujte `SendGrid` balíček NuGet:</span><span class="sxs-lookup"><span data-stu-id="492ba-168">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="492ba-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="492ba-169">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="492ba-170">V konzole Správce balíčků zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="492ba-170">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="492ba-171">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="492ba-171">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="492ba-172">V konzole zadejte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="492ba-172">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

------

<span data-ttu-id="492ba-173">Naleznete v tématu [začít pomocí Sendgridu zdarma](https://sendgrid.com/free/) k registraci bezplatného účtu SendGrid.</span><span class="sxs-lookup"><span data-stu-id="492ba-173">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>
### <a name="implement-iemailsender"></a><span data-ttu-id="492ba-174">Implementace IEmailSender</span><span class="sxs-lookup"><span data-stu-id="492ba-174">Implement IEmailSender</span></span>

<span data-ttu-id="492ba-175">Implementovat `IEmailSender`, vytvořit *Services/EmailSender.cs* podobně jako následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="492ba-175">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="492ba-176">Konfigurace spuštění pro podporu e-mailu</span><span class="sxs-lookup"><span data-stu-id="492ba-176">Configure startup to support email</span></span>

<span data-ttu-id="492ba-177">Přidejte následující kód, který `ConfigureServices` metoda ve *Startup.cs* souboru:</span><span class="sxs-lookup"><span data-stu-id="492ba-177">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="492ba-178">Přidat `EmailSender` jako přechodné služby.</span><span class="sxs-lookup"><span data-stu-id="492ba-178">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="492ba-179">Zaregistrujte `AuthMessageSenderOptions` instance konfigurace.</span><span class="sxs-lookup"><span data-stu-id="492ba-179">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Startup.cs?name=snippet2&highlight=12-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="492ba-180">Povolit obnovení hesla a potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="492ba-180">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="492ba-181">Šablona má kód pro obnovení potvrzení a heslo účtu.</span><span class="sxs-lookup"><span data-stu-id="492ba-181">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="492ba-182">Najít `OnPostAsync` metoda *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="492ba-182">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="492ba-183">Nově zaregistrovaný uživatelům zabránit v automaticky přihlášeni tak následující řádek:</span><span class="sxs-lookup"><span data-stu-id="492ba-183">Prevent newly registered users from being automatically logged on by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="492ba-184">Úplná metoda se zobrazí s změněný řádek zvýrazněný:</span><span class="sxs-lookup"><span data-stu-id="492ba-184">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="492ba-185">Zaregistrovat a potvrďte e-mailu a resetování hesla</span><span class="sxs-lookup"><span data-stu-id="492ba-185">Register, confirm email, and reset password</span></span>

<span data-ttu-id="492ba-186">Spuštění webové aplikace a testů potvrzení účtu a heslo pro obnovení toku.</span><span class="sxs-lookup"><span data-stu-id="492ba-186">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="492ba-187">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="492ba-187">Run the app and register a new user</span></span>

  ![Zobrazení účtu zaregistrovat webové aplikace](accconfirm/_static/loginaccconfirm1.png)

* <span data-ttu-id="492ba-189">Zkontrolujte svého e-mailu na odkaz pro potvrzení účtu.</span><span class="sxs-lookup"><span data-stu-id="492ba-189">Check your email for the account confirmation link.</span></span> <span data-ttu-id="492ba-190">Zobrazit [ladění e-mailu](#debug) Pokud neobdržíte e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-190">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="492ba-191">Klikněte na odkaz pro potvrzení e-mailu.</span><span class="sxs-lookup"><span data-stu-id="492ba-191">Click the link to confirm your email.</span></span>
* <span data-ttu-id="492ba-192">Přihlaste se pomocí e-mailu a hesla.</span><span class="sxs-lookup"><span data-stu-id="492ba-192">Sign in with your email and password.</span></span>
* <span data-ttu-id="492ba-193">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="492ba-193">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="492ba-194">Zobrazení stránky Správa</span><span class="sxs-lookup"><span data-stu-id="492ba-194">View the manage page</span></span>

<span data-ttu-id="492ba-195">Vyberte své uživatelské jméno v prohlížeči: ![okna prohlížeče s uživatelským jménem](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="492ba-195">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="492ba-196">Potřebujete rozšířit na navigačním panelu zobrazíte uživatelské jméno.</span><span class="sxs-lookup"><span data-stu-id="492ba-196">You might need to expand the navbar to see user name.</span></span>

![navbar](accconfirm/_static/x.png)

<span data-ttu-id="492ba-198">Zobrazí se stránka Správa s **profilu** vybraná karta.</span><span class="sxs-lookup"><span data-stu-id="492ba-198">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="492ba-199">**E-mailu** zobrazí zaškrtávací políčko označující e-mailu byl potvrzen.</span><span class="sxs-lookup"><span data-stu-id="492ba-199">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="492ba-200">Resetování hesla testu</span><span class="sxs-lookup"><span data-stu-id="492ba-200">Test password reset</span></span>

* <span data-ttu-id="492ba-201">Pokud jste přihlášeni, vyberte **odhlášení**.</span><span class="sxs-lookup"><span data-stu-id="492ba-201">If you're logged in, select **Logout**.</span></span>
* <span data-ttu-id="492ba-202">Vyberte **přihlášení** spojit a vybrat možnost **zapomněli jste heslo?** odkaz.</span><span class="sxs-lookup"><span data-stu-id="492ba-202">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="492ba-203">Zadejte e-mail, který jste použili k registraci účtu.</span><span class="sxs-lookup"><span data-stu-id="492ba-203">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="492ba-204">Odešle e-mail s odkazem k resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="492ba-204">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="492ba-205">Zkontrolujte e-mailu a klikněte na odkaz pro resetování hesla.</span><span class="sxs-lookup"><span data-stu-id="492ba-205">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="492ba-206">Po úspěšném resetování vašeho hesla se můžete přihlásit pomocí své e-mailu a nové heslo.</span><span class="sxs-lookup"><span data-stu-id="492ba-206">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="492ba-207">Ladění e-mailu</span><span class="sxs-lookup"><span data-stu-id="492ba-207">Debug email</span></span>

<span data-ttu-id="492ba-208">Pokud nelze získat pracovní e-mailu:</span><span class="sxs-lookup"><span data-stu-id="492ba-208">If you can't get email working:</span></span>

* <span data-ttu-id="492ba-209">Nastavte zarážku v `EmailSender.Execute` ověření `SendGridClient.SendEmailAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="492ba-209">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="492ba-210">Vytvoření [konzolovou aplikaci k odesílání e-mailu](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) pomocí podobné kódu `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="492ba-210">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="492ba-211">Zkontrolujte [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) stránky.</span><span class="sxs-lookup"><span data-stu-id="492ba-211">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="492ba-212">Zkontrolujte složku s nevyžádanou poštou.</span><span class="sxs-lookup"><span data-stu-id="492ba-212">Check your spam folder.</span></span>
* <span data-ttu-id="492ba-213">Zkuste jinou e-mailový alias na jinou e-mailovou zprostředkovatele (Microsoft, Yahoo, Gmail, atd.)</span><span class="sxs-lookup"><span data-stu-id="492ba-213">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="492ba-214">Pokuste se odeslat na jiné e-mailové účty.</span><span class="sxs-lookup"><span data-stu-id="492ba-214">Try sending to different email accounts.</span></span>

<span data-ttu-id="492ba-215">**Z bezpečnostních důvodů** je **není** použití produkční tajných kódů v vývoj a testování.</span><span class="sxs-lookup"><span data-stu-id="492ba-215">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="492ba-216">Pokud publikujete aplikaci do Azure, můžete nastavit SendGrid tajné kódy jako nastavení aplikace ve webové aplikaci Azure portal.</span><span class="sxs-lookup"><span data-stu-id="492ba-216">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="492ba-217">Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="492ba-217">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="492ba-218">Sloučit účty sociálních sítí a místní přihlášení</span><span class="sxs-lookup"><span data-stu-id="492ba-218">Combine social and local login accounts</span></span>

<span data-ttu-id="492ba-219">K dokončení této části, je nutné nejprve povolit externí zprostředkovatel ověřování.</span><span class="sxs-lookup"><span data-stu-id="492ba-219">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="492ba-220">Zobrazit [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="492ba-220">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="492ba-221">Kliknutím na odkaz na vaši e-mailu můžete kombinovat místní a sociální účty.</span><span class="sxs-lookup"><span data-stu-id="492ba-221">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="492ba-222">V tomto pořadí "RickAndMSFT@gmail.com" se nejprve vytvoří jako místní přihlášení; však můžete nejprve vytvořte účet jako přihlášení prostřednictvím sociální sítě a pak přidat místní přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="492ba-222">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webová aplikace: RickAndMSFT@gmail.com uživatel byl ověřen](accconfirm/_static/rick.png)

<span data-ttu-id="492ba-224">Klikněte na **spravovat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="492ba-224">Click on the **Manage** link.</span></span> <span data-ttu-id="492ba-225">Poznámka: externí 0 (přihlašování přes sociální sítě) spojená s tímto účtem.</span><span class="sxs-lookup"><span data-stu-id="492ba-225">Note the 0 external (social logins) associated with this account.</span></span>

![Správa zobrazení](accconfirm/_static/manage.png)

<span data-ttu-id="492ba-227">Klikněte na odkaz pro další přihlášení služby a přijímání požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="492ba-227">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="492ba-228">Na následujícím obrázku je Facebooku zprostředkovatele externího ověřování:</span><span class="sxs-lookup"><span data-stu-id="492ba-228">In the following image, Facebook is the external authentication provider:</span></span>

![Spravovat externí přihlášení zobrazení výpisu Facebooku](accconfirm/_static/fb.png)

<span data-ttu-id="492ba-230">Byli sloučeni dva účty.</span><span class="sxs-lookup"><span data-stu-id="492ba-230">The two accounts have been combined.</span></span> <span data-ttu-id="492ba-231">Máte možnost přihlásit se přes účet.</span><span class="sxs-lookup"><span data-stu-id="492ba-231">You are able to sign in with either account.</span></span> <span data-ttu-id="492ba-232">Můžete chtít uživatelům přidat místní účty v případě nefungující Služba ověřování v jejich přihlášení prostřednictvím sociální sítě nebo spíše se jste ztratili přístup k jejich účtu na sociální síti.</span><span class="sxs-lookup"><span data-stu-id="492ba-232">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="492ba-233">Po lokality má uživatelům povolit potvrzení účtu</span><span class="sxs-lookup"><span data-stu-id="492ba-233">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="492ba-234">Povolení potvrzení účtu na webu s uživateli zamezí všichni stávající uživatelé.</span><span class="sxs-lookup"><span data-stu-id="492ba-234">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="492ba-235">Stávající uživatelé jsou uzamčen, protože jejich účty nejsou potvrzeny.</span><span class="sxs-lookup"><span data-stu-id="492ba-235">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="492ba-236">Obejít existující uzamčení uživatelů, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="492ba-236">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="492ba-237">Aktualizujte databázi pro označení všichni stávající uživatelé, jako je potvrzen.</span><span class="sxs-lookup"><span data-stu-id="492ba-237">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="492ba-238">Zkontrolujte stávající uživatele.</span><span class="sxs-lookup"><span data-stu-id="492ba-238">Confirm existing users.</span></span> <span data-ttu-id="492ba-239">Třeba dávkové odeslání e-mailů s potvrzovací odkazy.</span><span class="sxs-lookup"><span data-stu-id="492ba-239">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
