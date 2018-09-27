---
title: Dvoufaktorové ověřování přes SMS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nastavit dvojúrovňového ověřování (2FA) s aplikací ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
uid: security/authentication/2fa
ms.openlocfilehash: 19cc4b5326e8359afd47dd75aca3d661c3f92a30
ms.sourcegitcommit: 9bdba90b2c97a4016188434657194b2d7027d6e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47402117"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="cd321-103">Dvoufaktorové ověřování přes SMS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd321-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="cd321-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Swiss vývojáři](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="cd321-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="cd321-105">Dva faktoru ověřování (2FA), pomocí časovou synchronizací jednorázové heslo algoritmus (TOTP), jsou tyto aplikace v oboru doporučenému přístupu pro 2FA.</span><span class="sxs-lookup"><span data-stu-id="cd321-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="cd321-106">2FA pomocí TOTP je upřednostňována před SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="cd321-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="cd321-107">Další informace najdete v tématu [generování kódu QR povolit pro TOTP aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pro ASP.NET Core 2.0 a novější.</span><span class="sxs-lookup"><span data-stu-id="cd321-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="cd321-108">Tento kurz ukazuje, jak nastavit dvojúrovňového ověřování (2FA) pomocí serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="cd321-109">Jsou uvedeny pokyny pro [twilio](https://www.twilio.com/) a [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale můžete použít jakýkoli jiný poskytovatel serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="cd321-110">Doporučujeme je provést [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm) před zahájením tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="cd321-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="cd321-111">Zobrazení [úplnou vzorovou](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="cd321-111">View the [completed sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="cd321-112">[Jak stáhnout](xref:tutorials/index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="cd321-112">[How to download](xref:tutorials/index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="cd321-113">Vytvořte nový projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd321-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="cd321-114">Vytvořit novou webovou aplikaci ASP.NET Core s názvem `Web2FA` s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="cd321-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="cd321-115">Postupujte podle pokynů v [vynucování SSL v aplikaci ASP.NET Core](xref:security/enforcing-ssl) nastavit a vyžadovat protokol SSL.</span><span class="sxs-lookup"><span data-stu-id="cd321-115">Follow the instructions in [Enforce SSL in an ASP.NET Core app](xref:security/enforcing-ssl) to set up and require SSL.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="cd321-116">Vytvoření účtu služby SMS</span><span class="sxs-lookup"><span data-stu-id="cd321-116">Create an SMS account</span></span>

<span data-ttu-id="cd321-117">Vytvoření účtu služby SMS, například [twilio](https://www.twilio.com/) nebo [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="cd321-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="cd321-118">Zaznamenejte přihlašovací údaje pro ověřování (pro twilio: accountSid a ověřovacího tokenu pro ASPSMS: vlastnosti userkey jedná a heslo).</span><span class="sxs-lookup"><span data-stu-id="cd321-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="cd321-119">Zjištění přihlašovací údaje poskytovatele serveru SMS</span><span class="sxs-lookup"><span data-stu-id="cd321-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="cd321-120">**Twilio:** z karty řídicí panel svého účtu Twilio, zkopírujte **SID účtu** a **ověřovací token**.</span><span class="sxs-lookup"><span data-stu-id="cd321-120">**Twilio:** From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="cd321-121">**ASPSMS:** v nastavení účtu, přejděte na **vlastnosti userkey jedná** a zkopírujte ho spolu s vaší **heslo**.</span><span class="sxs-lookup"><span data-stu-id="cd321-121">**ASPSMS:** From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="cd321-122">Později jsme uloží tyto hodnoty se pomocí nástroje Správce tajný klíč v rámci klíče `SMSAccountIdentification` a `SMSAccountPassword`.</span><span class="sxs-lookup"><span data-stu-id="cd321-122">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="cd321-123">Určení SenderID / původce</span><span class="sxs-lookup"><span data-stu-id="cd321-123">Specifying SenderID / Originator</span></span>

<span data-ttu-id="cd321-124">**Twilio:** z karty čísla, zkopírujte váš Twilio **telefonní číslo**.</span><span class="sxs-lookup"><span data-stu-id="cd321-124">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="cd321-125">**ASPSMS:** v rámci nabídky autoru odemknout odemknout jeden nebo více autoru nebo zvolte příkazce alfanumerické znaky (není podporováno ve všech sítích).</span><span class="sxs-lookup"><span data-stu-id="cd321-125">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="cd321-126">Dále jsme uloží tuto hodnotu pomocí nástroje Správce tajný klíč v klíči `SMSAccountFrom`.</span><span class="sxs-lookup"><span data-stu-id="cd321-126">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>


### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="cd321-127">Zadejte přihlašovací údaje služby SMS</span><span class="sxs-lookup"><span data-stu-id="cd321-127">Provide credentials for the SMS service</span></span>

<span data-ttu-id="cd321-128">Použijeme [možnosti vzor](xref:fundamentals/configuration/options) pro přístup k účtu a klíč nastavení.</span><span class="sxs-lookup"><span data-stu-id="cd321-128">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

   * <span data-ttu-id="cd321-129">Vytvoření třídy k načtení zabezpečený klíč serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-129">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="cd321-130">V tomto příkladu `SMSoptions` třída se vytvoří v *Services/SMSoptions.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="cd321-130">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="cd321-131">Nastavte `SMSAccountIdentification`, `SMSAccountPassword` a `SMSAccountFrom` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="cd321-131">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cd321-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="cd321-132">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* <span data-ttu-id="cd321-133">Přidání balíčku NuGet pro poskytovatele serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-133">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="cd321-134">Z balíčku správce konzoly (konzolu PMC) spusťte:</span><span class="sxs-lookup"><span data-stu-id="cd321-134">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="cd321-135">**Twilio:**
`Install-Package Twilio`</span><span class="sxs-lookup"><span data-stu-id="cd321-135">**Twilio:**
`Install-Package Twilio`</span></span>

<span data-ttu-id="cd321-136">**ASPSMS:**
`Install-Package ASPSMS`</span><span class="sxs-lookup"><span data-stu-id="cd321-136">**ASPSMS:**
`Install-Package ASPSMS`</span></span>


* <span data-ttu-id="cd321-137">Přidejte kód *Services/MessageServices.cs* soubor serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-137">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="cd321-138">Použijte Twilio nebo ASPSMS části:</span><span class="sxs-lookup"><span data-stu-id="cd321-138">Use either the Twilio or the ASPSMS section:</span></span>


<span data-ttu-id="cd321-139">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span><span class="sxs-lookup"><span data-stu-id="cd321-139">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span></span>

<span data-ttu-id="cd321-140">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span><span class="sxs-lookup"><span data-stu-id="cd321-140">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span></span>

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="cd321-141">Konfigurace spuštění používat `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="cd321-141">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="cd321-142">Přidat `SMSoptions` ke kontejneru služby v `ConfigureServices` metodu *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cd321-142">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="cd321-143">Povolení dvoufaktorového ověřování</span><span class="sxs-lookup"><span data-stu-id="cd321-143">Enable two-factor authentication</span></span>

<span data-ttu-id="cd321-144">Otevřít *Views/Manage/Index.cshtml* soubor zobrazení Razor a odeberte znaky komentáře (takže žádné značky je odkomentovaný).</span><span class="sxs-lookup"><span data-stu-id="cd321-144">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commnted out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="cd321-145">Přihlaste se pomocí dvojúrovňového ověřování</span><span class="sxs-lookup"><span data-stu-id="cd321-145">Log in with two-factor authentication</span></span>

* <span data-ttu-id="cd321-146">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="cd321-146">Run the app and register a new user</span></span>

![Webová aplikace zaregistrovat, zobrazení otevřít v Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="cd321-148">Klepněte na jméno uživatele, která se aktivuje `Index` metody akce v kontroleru spravovat.</span><span class="sxs-lookup"><span data-stu-id="cd321-148">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="cd321-149">Potom klepněte na telefonní číslo **přidat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="cd321-149">Then tap the phone number **Add** link.</span></span>

![Správa zobrazení](2fa/_static/login2fa2.png)

* <span data-ttu-id="cd321-151">Přidat telefonní číslo, který bude přijímat ověřovací kód a klepněte na **poslat ověřovací kód**.</span><span class="sxs-lookup"><span data-stu-id="cd321-151">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Přidat telefonní číslo stránky](2fa/_static/login2fa3.png)

* <span data-ttu-id="cd321-153">Zobrazí se zpráva SMS s ověřovacím kódem.</span><span class="sxs-lookup"><span data-stu-id="cd321-153">You will get a text message with the verification code.</span></span> <span data-ttu-id="cd321-154">Zadejte je a klepněte na **odeslat**</span><span class="sxs-lookup"><span data-stu-id="cd321-154">Enter it and tap **Submit**</span></span>

![Ověřit telefonní číslo stránky](2fa/_static/login2fa4.png)

<span data-ttu-id="cd321-156">Pokud vám textovou zprávu, přečtěte si téma twilio protokolu stránky.</span><span class="sxs-lookup"><span data-stu-id="cd321-156">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="cd321-157">Správa zobrazení ukazuje že vaše telefonní číslo bylo úspěšně přidán.</span><span class="sxs-lookup"><span data-stu-id="cd321-157">The Manage view shows your phone number was added successfully.</span></span>

![Správa zobrazení](2fa/_static/login2fa5.png)

* <span data-ttu-id="cd321-159">Klepněte na **povolit** povolení dvoufaktorového ověřování.</span><span class="sxs-lookup"><span data-stu-id="cd321-159">Tap **Enable** to enable two-factor authentication.</span></span>

![Správa zobrazení](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="cd321-161">Test dvoufaktorového ověřování</span><span class="sxs-lookup"><span data-stu-id="cd321-161">Test two-factor authentication</span></span>

* <span data-ttu-id="cd321-162">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="cd321-162">Log off.</span></span>

* <span data-ttu-id="cd321-163">Přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="cd321-163">Log in.</span></span>

* <span data-ttu-id="cd321-164">Uživatelský účet povolila dvojúrovňové ověřování, takže je nutné zadat druhý faktor ověřování.</span><span class="sxs-lookup"><span data-stu-id="cd321-164">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="cd321-165">V tomto kurzu jste povolili ověření pomocí telefonu.</span><span class="sxs-lookup"><span data-stu-id="cd321-165">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="cd321-166">Integrované šablony lze také nastavit e-mail jako druhý faktor.</span><span class="sxs-lookup"><span data-stu-id="cd321-166">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="cd321-167">Můžete nastavit další druhý faktory pro ověřování, jako je kódy QR.</span><span class="sxs-lookup"><span data-stu-id="cd321-167">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="cd321-168">Klepněte na **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="cd321-168">Tap **Submit**.</span></span>

![Poslat ověřovací kód zobrazení](2fa/_static/login2fa7.png)

* <span data-ttu-id="cd321-170">Zadejte kód, který se zobrazí ve zprávě SMS.</span><span class="sxs-lookup"><span data-stu-id="cd321-170">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="cd321-171">Kliknutím na **chcete Zapamatovat tento prohlížeč** zaškrtávacího políčka můžete vyloučit před nutností pomocí 2FA přihlášení při použití stejného zařízení a prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="cd321-171">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="cd321-172">Povolení 2FA a kliknete na **chcete Zapamatovat tento prohlížeč** vám poskytne silné 2FA ochrany z uživateli se zlými úmysly pokusu o přístup ke svému účtu, za předpokladu, že nemají přístup k zařízení.</span><span class="sxs-lookup"><span data-stu-id="cd321-172">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="cd321-173">Můžete to provést na libovolném privátní zařízení, kterou používáte pravidelně.</span><span class="sxs-lookup"><span data-stu-id="cd321-173">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="cd321-174">Nastavením **chcete Zapamatovat tento prohlížeč**, získat zvýšení zabezpečení 2FA ze zařízení, které nepoužíváte pravidelně, a získáte usnadnění práce na nebudete muset absolvovat 2FA na vlastních zařízeních.</span><span class="sxs-lookup"><span data-stu-id="cd321-174">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Ověření zobrazení](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="cd321-176">Uzamčení účtu pro ochranu před útoky hrubou silou</span><span class="sxs-lookup"><span data-stu-id="cd321-176">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="cd321-177">Uzamčení účtu, doporučujeme pomocí 2FA.</span><span class="sxs-lookup"><span data-stu-id="cd321-177">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="cd321-178">Když se uživatel přihlásí pomocí místního účtu nebo účtu na sociální síti, se ukládají jednotlivé neúspěšné pokusy o na 2FA.</span><span class="sxs-lookup"><span data-stu-id="cd321-178">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="cd321-179">Pokud je dosaženo maximální neúspěšných pokusů o přístup, je uživatel uzamčen (výchozí: pětiminutový uzamčení po 5 neúspěšných pokusů o přístup).</span><span class="sxs-lookup"><span data-stu-id="cd321-179">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="cd321-180">Po provedení úspěšného ověření resetuje počet pokusů o neúspěšných přístupů a resetuje na hodiny.</span><span class="sxs-lookup"><span data-stu-id="cd321-180">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="cd321-181">Maximální počet neúspěšných pokusů o přístup a doby uzamčení lze nastavit pomocí [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) a [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="cd321-181">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="cd321-182">Následující nakonfiguruje uzamčení účtu po dobu 10 minut po 10 neúspěšných pokusů o přístup:</span><span class="sxs-lookup"><span data-stu-id="cd321-182">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="cd321-183">Ujistěte se, že [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) nastaví `lockoutOnFailure` k `true`:</span><span class="sxs-lookup"><span data-stu-id="cd321-183">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
