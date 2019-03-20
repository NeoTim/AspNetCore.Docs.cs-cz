---
title: Dvoufaktorové ověřování přes SMS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nastavit dvojúrovňového ověřování (2FA) s aplikací ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 116249a7cd4faebd0c899e383d86f5c5c3c7146a
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265238"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="fc129-103">Dvoufaktorové ověřování přes SMS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc129-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="fc129-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Swiss vývojáři](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="fc129-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="fc129-105">Dva faktoru ověřování (2FA), pomocí časovou synchronizací jednorázové heslo algoritmus (TOTP), jsou tyto aplikace v oboru doporučenému přístupu pro 2FA.</span><span class="sxs-lookup"><span data-stu-id="fc129-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="fc129-106">2FA pomocí TOTP je upřednostňována před SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="fc129-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="fc129-107">Další informace najdete v tématu [generování kódu QR povolit pro TOTP aplikace v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pro ASP.NET Core 2.0 a novější.</span><span class="sxs-lookup"><span data-stu-id="fc129-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="fc129-108">Tento kurz ukazuje, jak nastavit dvojúrovňového ověřování (2FA) pomocí serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="fc129-109">Jsou uvedeny pokyny pro [twilio](https://www.twilio.com/) a [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale můžete použít jakýkoli jiný poskytovatel serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="fc129-110">Doporučujeme je provést [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm) před zahájením tohoto kurzu.</span><span class="sxs-lookup"><span data-stu-id="fc129-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="fc129-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="fc129-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="fc129-112">[Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="fc129-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="fc129-113">Vytvořte nový projekt ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fc129-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="fc129-114">Vytvořit novou webovou aplikaci ASP.NET Core s názvem `Web2FA` s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="fc129-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="fc129-115">Postupujte podle pokynů v <xref:security/enforcing-ssl> nastavit a vyžadují protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fc129-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="fc129-116">Vytvoření účtu služby SMS</span><span class="sxs-lookup"><span data-stu-id="fc129-116">Create an SMS account</span></span>

<span data-ttu-id="fc129-117">Vytvoření účtu služby SMS, například [twilio](https://www.twilio.com/) nebo [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="fc129-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="fc129-118">Zaznamenejte přihlašovací údaje pro ověřování (pro twilio: accountSid a pro ASPSMS ověřovacího tokenu: Vlastnosti userkey jedná a heslo).</span><span class="sxs-lookup"><span data-stu-id="fc129-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="fc129-119">Zjištění přihlašovací údaje poskytovatele serveru SMS</span><span class="sxs-lookup"><span data-stu-id="fc129-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="fc129-120">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="fc129-120">**Twilio:**</span></span>

<span data-ttu-id="fc129-121">Z karty řídicí panel svého účtu Twilio, zkopírujte **SID účtu** a **ověřovací token**.</span><span class="sxs-lookup"><span data-stu-id="fc129-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="fc129-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="fc129-122">**ASPSMS:**</span></span>

<span data-ttu-id="fc129-123">V nastavení účtu, přejděte na **vlastnosti userkey jedná** a zkopírujte ho spolu s vaší **heslo**.</span><span class="sxs-lookup"><span data-stu-id="fc129-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="fc129-124">Později jsme uloží tyto hodnoty se pomocí nástroje Správce tajný klíč v rámci klíče `SMSAccountIdentification` a `SMSAccountPassword`.</span><span class="sxs-lookup"><span data-stu-id="fc129-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="fc129-125">Určení SenderID / původce</span><span class="sxs-lookup"><span data-stu-id="fc129-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="fc129-126">**Twilio:** Na kartě čísla zkopírujte váš Twilio **telefonní číslo**.</span><span class="sxs-lookup"><span data-stu-id="fc129-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="fc129-127">**ASPSMS:** V rámci nabídky autoru odemknout odemknout jeden nebo více autoru nebo zvolte příkazce alfanumerické znaky (není podporováno ve všech sítích).</span><span class="sxs-lookup"><span data-stu-id="fc129-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="fc129-128">Dále jsme uloží tuto hodnotu pomocí nástroje Správce tajný klíč v klíči `SMSAccountFrom`.</span><span class="sxs-lookup"><span data-stu-id="fc129-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="fc129-129">Zadejte přihlašovací údaje služby SMS</span><span class="sxs-lookup"><span data-stu-id="fc129-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="fc129-130">Použijeme [možnosti vzor](xref:fundamentals/configuration/options) pro přístup k účtu a klíč nastavení.</span><span class="sxs-lookup"><span data-stu-id="fc129-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="fc129-131">Vytvoření třídy k načtení zabezpečený klíč serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="fc129-132">V tomto příkladu `SMSoptions` třída se vytvoří v *Services/SMSoptions.cs* souboru.</span><span class="sxs-lookup"><span data-stu-id="fc129-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="fc129-133">Nastavte `SMSAccountIdentification`, `SMSAccountPassword` a `SMSAccountFrom` s [manažera tajných nástroj](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="fc129-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="fc129-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="fc129-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="fc129-135">Přidání balíčku NuGet pro poskytovatele serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="fc129-136">Z balíčku správce konzoly (konzolu PMC) spusťte:</span><span class="sxs-lookup"><span data-stu-id="fc129-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="fc129-137">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="fc129-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="fc129-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="fc129-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="fc129-139">Přidejte kód *Services/MessageServices.cs* soubor serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="fc129-140">Použijte Twilio nebo ASPSMS části:</span><span class="sxs-lookup"><span data-stu-id="fc129-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="fc129-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span><span class="sxs-lookup"><span data-stu-id="fc129-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span></span>

<span data-ttu-id="fc129-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span><span class="sxs-lookup"><span data-stu-id="fc129-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span></span>

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="fc129-143">Konfigurace spuštění používat `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="fc129-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="fc129-144">Přidat `SMSoptions` ke kontejneru služby v `ConfigureServices` metodu *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fc129-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="fc129-145">Povolení dvoufaktorového ověřování</span><span class="sxs-lookup"><span data-stu-id="fc129-145">Enable two-factor authentication</span></span>

<span data-ttu-id="fc129-146">Otevřít *Views/Manage/Index.cshtml* soubor zobrazení Razor a odeberte znaky komentáře (abyste je zakomentovaný, žádné značky).</span><span class="sxs-lookup"><span data-stu-id="fc129-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="fc129-147">Přihlaste se pomocí dvojúrovňového ověřování</span><span class="sxs-lookup"><span data-stu-id="fc129-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="fc129-148">Spusťte aplikaci a zaregistrovat nový uživatel</span><span class="sxs-lookup"><span data-stu-id="fc129-148">Run the app and register a new user</span></span>

![Webová aplikace zaregistrovat, zobrazení otevřít v Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="fc129-150">Klepněte na jméno uživatele, která se aktivuje `Index` metody akce v kontroleru spravovat.</span><span class="sxs-lookup"><span data-stu-id="fc129-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="fc129-151">Potom klepněte na telefonní číslo **přidat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="fc129-151">Then tap the phone number **Add** link.</span></span>

![Správa zobrazení – klepnout na odkaz "Přidání"](2fa/_static/login2fa2.png)

* <span data-ttu-id="fc129-153">Přidat telefonní číslo, který bude přijímat ověřovací kód a klepněte na **poslat ověřovací kód**.</span><span class="sxs-lookup"><span data-stu-id="fc129-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Přidat telefonní číslo stránky](2fa/_static/login2fa3.png)

* <span data-ttu-id="fc129-155">Zobrazí se zpráva SMS s ověřovacím kódem.</span><span class="sxs-lookup"><span data-stu-id="fc129-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="fc129-156">Zadejte je a klepněte na **odeslat**</span><span class="sxs-lookup"><span data-stu-id="fc129-156">Enter it and tap **Submit**</span></span>

![Ověřit telefonní číslo stránky](2fa/_static/login2fa4.png)

<span data-ttu-id="fc129-158">Pokud vám textovou zprávu, přečtěte si téma twilio protokolu stránky.</span><span class="sxs-lookup"><span data-stu-id="fc129-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="fc129-159">Správa zobrazení ukazuje že vaše telefonní číslo bylo úspěšně přidán.</span><span class="sxs-lookup"><span data-stu-id="fc129-159">The Manage view shows your phone number was added successfully.</span></span>

![Správa zobrazení – telefonní číslo se úspěšně přidal](2fa/_static/login2fa5.png)

* <span data-ttu-id="fc129-161">Klepněte na **povolit** povolení dvoufaktorového ověřování.</span><span class="sxs-lookup"><span data-stu-id="fc129-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Správa zobrazení – povolení dvoufaktorového ověřování](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="fc129-163">Test dvoufaktorového ověřování</span><span class="sxs-lookup"><span data-stu-id="fc129-163">Test two-factor authentication</span></span>

* <span data-ttu-id="fc129-164">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="fc129-164">Log off.</span></span>

* <span data-ttu-id="fc129-165">Přihlásit se.</span><span class="sxs-lookup"><span data-stu-id="fc129-165">Log in.</span></span>

* <span data-ttu-id="fc129-166">Uživatelský účet povolila dvojúrovňové ověřování, takže je nutné zadat druhý faktor ověřování.</span><span class="sxs-lookup"><span data-stu-id="fc129-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="fc129-167">V tomto kurzu jste povolili ověření pomocí telefonu.</span><span class="sxs-lookup"><span data-stu-id="fc129-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="fc129-168">Integrované šablony lze také nastavit e-mail jako druhý faktor.</span><span class="sxs-lookup"><span data-stu-id="fc129-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="fc129-169">Můžete nastavit další druhý faktory pro ověřování, jako je kódy QR.</span><span class="sxs-lookup"><span data-stu-id="fc129-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="fc129-170">Klepněte na **odeslat**.</span><span class="sxs-lookup"><span data-stu-id="fc129-170">Tap **Submit**.</span></span>

![Poslat ověřovací kód zobrazení](2fa/_static/login2fa7.png)

* <span data-ttu-id="fc129-172">Zadejte kód, který se zobrazí ve zprávě SMS.</span><span class="sxs-lookup"><span data-stu-id="fc129-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="fc129-173">Kliknutím na **chcete Zapamatovat tento prohlížeč** zaškrtávacího políčka můžete vyloučit před nutností pomocí 2FA přihlášení při použití stejného zařízení a prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fc129-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="fc129-174">Povolení 2FA a kliknete na **chcete Zapamatovat tento prohlížeč** vám poskytne silné 2FA ochrany z uživateli se zlými úmysly pokusu o přístup ke svému účtu, za předpokladu, že nemají přístup k zařízení.</span><span class="sxs-lookup"><span data-stu-id="fc129-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="fc129-175">Můžete to provést na libovolném privátní zařízení, kterou používáte pravidelně.</span><span class="sxs-lookup"><span data-stu-id="fc129-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="fc129-176">Nastavením **chcete Zapamatovat tento prohlížeč**, získat zvýšení zabezpečení 2FA ze zařízení, které nepoužíváte pravidelně, a získáte usnadnění práce na nebudete muset absolvovat 2FA na vlastních zařízeních.</span><span class="sxs-lookup"><span data-stu-id="fc129-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Ověření zobrazení](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="fc129-178">Uzamčení účtu pro ochranu před útoky hrubou silou</span><span class="sxs-lookup"><span data-stu-id="fc129-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="fc129-179">Uzamčení účtu, doporučujeme pomocí 2FA.</span><span class="sxs-lookup"><span data-stu-id="fc129-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="fc129-180">Když se uživatel přihlásí pomocí místního účtu nebo účtu na sociální síti, se ukládají jednotlivé neúspěšné pokusy o na 2FA.</span><span class="sxs-lookup"><span data-stu-id="fc129-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="fc129-181">Pokud je dosaženo maximální neúspěšných pokusů o přístup, je uživatel uzamčen (výchozí: 5 minut uzamčení po 5 neúspěšných pokusů o přístup).</span><span class="sxs-lookup"><span data-stu-id="fc129-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="fc129-182">Po provedení úspěšného ověření resetuje počet pokusů o neúspěšných přístupů a resetuje na hodiny.</span><span class="sxs-lookup"><span data-stu-id="fc129-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="fc129-183">Maximální počet neúspěšných pokusů o přístup a doby uzamčení lze nastavit pomocí [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) a [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="fc129-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="fc129-184">Následující nakonfiguruje uzamčení účtu po dobu 10 minut po 10 neúspěšných pokusů o přístup:</span><span class="sxs-lookup"><span data-stu-id="fc129-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="fc129-185">Ujistěte se, že [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) nastaví `lockoutOnFailure` k `true`:</span><span class="sxs-lookup"><span data-stu-id="fc129-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
