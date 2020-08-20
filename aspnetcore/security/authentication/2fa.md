---
title: Dvojúrovňové ověřování pomocí SMS v ASP.NET Core
author: rick-anderson
description: Naučte se, jak pomocí aplikace ASP.NET Core nastavit dvojúrovňové ověřování (2FA).
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: e5e606afaf0219f3a0eb7301203b7142a00322be
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634108"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="c3b5d-103">Dvojúrovňové ověřování pomocí SMS v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3b5d-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="c3b5d-104">Od [Ricke Anderson](https://twitter.com/RickAndMSFT) a [Švýcarska – vývojáři](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="c3b5d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="c3b5d-105">TOTP (2FA) ověřovací aplikace pro ověřování pomocí času založeného na čase () jsou doporučeným oborem přístupu pro 2FA.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="c3b5d-106">2FA pomocí TOTP se upřednostňuje pro SMS 2FA.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="c3b5d-107">Další informace najdete v tématu [Povolení generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pro ASP.NET Core 2,0 a novější.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="c3b5d-108">V tomto kurzu se dozvíte, jak nastavit dvojúrovňové ověřování (2FA) pomocí serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="c3b5d-109">Pokyny jsou uvedené pro [Twilio](https://www.twilio.com/) a [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale můžete použít jakéhokoli jiného poskytovatele serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="c3b5d-110">Než začnete s tímto kurzem, doporučujeme dokončit [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm) .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="c3b5d-111">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="c3b5d-112">[Jak stáhnout](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="c3b5d-113">Vytvoření nového projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c3b5d-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="c3b5d-114">Vytvořte novou ASP.NET Core webovou aplikaci s názvem `Web2FA` s jednotlivými uživatelskými účty.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="c3b5d-115">Při <xref:security/enforcing-ssl> nastavování a vyžadování protokolu HTTPS postupujte podle pokynů v části.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="c3b5d-116">Vytvoření účtu serveru SMS</span><span class="sxs-lookup"><span data-stu-id="c3b5d-116">Create an SMS account</span></span>

<span data-ttu-id="c3b5d-117">Vytvořte účet serveru SMS, například z [Twilio](https://www.twilio.com/) nebo [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="c3b5d-118">Zaznamenejte přihlašovací údaje pro ověřování (pro Twilio: accountSid a authToken, pro ASPSMS: vlastnosti UserKey a heslo).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="c3b5d-119">Zjišťování přihlašovacích údajů poskytovatele služby SMS</span><span class="sxs-lookup"><span data-stu-id="c3b5d-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="c3b5d-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-120">**Twilio:**</span></span>

<span data-ttu-id="c3b5d-121">Na kartě řídicí panel účtu Twilio zkopírujte **identifikátor SID účtu** a **ověřovací token**.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="c3b5d-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-122">**ASPSMS:**</span></span>

<span data-ttu-id="c3b5d-123">V nastavení účtu přejděte na **vlastnosti UserKey** a zkopírujte ho společně s vaším **heslem**.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="c3b5d-124">Tyto hodnoty budeme později ukládat v nástroji pomocí Správce tajného klíče v rámci klíčů `SMSAccountIdentification` a `SMSAccountPassword` .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="c3b5d-125">Zadání SenderID/původce</span><span class="sxs-lookup"><span data-stu-id="c3b5d-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="c3b5d-126">**Twilio:** Na kartě čísla zkopírujte své Twilio **telefonní číslo**.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="c3b5d-127">**ASPSMS:** V nabídce odemknout původci odemkněte jeden nebo více původců nebo vyberte alfanumerický původ (není podporován všemi sítěmi).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="c3b5d-128">Později tuto hodnotu uložíme pomocí nástroje Správce tajných klíčů v rámci klíče `SMSAccountFrom` .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="c3b5d-129">Zadejte přihlašovací údaje pro službu SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="c3b5d-130">Pro přístup k uživatelskému účtu a nastavení klíče použijeme [vzor možností](xref:fundamentals/configuration/options) .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="c3b5d-131">Vytvořte třídu, která načte zabezpečený klíč SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="c3b5d-132">V této ukázce `SMSoptions` je třída vytvořena v souboru *Services/SMSoptions. cs* .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="c3b5d-133">`SMSAccountIdentification` `SMSAccountPassword` `SMSAccountFrom` Pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets)nastavte a.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c3b5d-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="c3b5d-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="c3b5d-135">Přidejte balíček NuGet pro poskytovatele služby SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="c3b5d-136">V konzole správce balíčků (PMC) spusťte:</span><span class="sxs-lookup"><span data-stu-id="c3b5d-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="c3b5d-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="c3b5d-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="c3b5d-139">Přidejte kód do souboru *Services/MessageServices. cs* pro povolení serveru SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="c3b5d-140">Použijte buď Twilio, nebo část ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="c3b5d-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="c3b5d-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="c3b5d-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c3b5d-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="c3b5d-143">Konfigurace spuštění pro použití `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="c3b5d-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="c3b5d-144">Přidejte `SMSoptions` do kontejneru služby v `ConfigureServices` metodě v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="c3b5d-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="c3b5d-145">Povolení dvoufaktorového ověřování</span><span class="sxs-lookup"><span data-stu-id="c3b5d-145">Enable two-factor authentication</span></span>

<span data-ttu-id="c3b5d-146">Otevřete soubor zobrazení */Správa/index. cshtml* Razor zobrazení a odeberte znaky komentáře (takže se neoznačí jako komentář žádné značky).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="c3b5d-147">Přihlášení Pomocí dvojúrovňového ověřování</span><span class="sxs-lookup"><span data-stu-id="c3b5d-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="c3b5d-148">Spuštění aplikace a registrace nového uživatele</span><span class="sxs-lookup"><span data-stu-id="c3b5d-148">Run the app and register a new user</span></span>

![Zobrazení registru webové aplikace otevřené v Microsoft Edge](2fa/_static/login2fa1.png)

* <span data-ttu-id="c3b5d-150">Klepněte na své uživatelské jméno, které aktivuje `Index` metodu Action na stránce Spravovat kontroler.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="c3b5d-151">Pak klepněte na telefonní číslo **Přidat** odkaz.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-151">Then tap the phone number **Add** link.</span></span>

![Spravovat zobrazení – klepněte na odkaz Přidat](2fa/_static/login2fa2.png)

* <span data-ttu-id="c3b5d-153">Přidejte telefonní číslo, které bude přijímat ověřovací kód, a klepněte na **Odeslat ověřovací kód**.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Přidat stránku telefonního čísla](2fa/_static/login2fa3.png)

* <span data-ttu-id="c3b5d-155">Obdržíte textovou zprávu s ověřovacím kódem.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="c3b5d-156">Zadejte ho a klepněte na **Odeslat** .</span><span class="sxs-lookup"><span data-stu-id="c3b5d-156">Enter it and tap **Submit**</span></span>

![Ověřit stránku telefonního čísla](2fa/_static/login2fa4.png)

<span data-ttu-id="c3b5d-158">Pokud neobdržíte textovou zprávu, přečtěte si stránku protokol Twilio.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="c3b5d-159">V zobrazení Správa se zobrazí vaše telefonní číslo bylo úspěšně přidáno.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-159">The Manage view shows your phone number was added successfully.</span></span>

![Správa zobrazení – Telefonní číslo bylo úspěšně přidáno.](2fa/_static/login2fa5.png)

* <span data-ttu-id="c3b5d-161">Klepnutím na **Povolit** povolte dvojúrovňové ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Spravovat zobrazení – povolit dvojúrovňové ověřování](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="c3b5d-163">Test dvojúrovňového ověřování</span><span class="sxs-lookup"><span data-stu-id="c3b5d-163">Test two-factor authentication</span></span>

* <span data-ttu-id="c3b5d-164">Odhlaste se.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-164">Log off.</span></span>

* <span data-ttu-id="c3b5d-165">Přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-165">Log in.</span></span>

* <span data-ttu-id="c3b5d-166">Uživatelský účet má povolené dvojúrovňové ověřování, takže musíte zadat druhý faktor ověřování.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="c3b5d-167">V tomto kurzu jste povolili ověřování pro telefon.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="c3b5d-168">Předdefinované šablony také umožňují nastavit e-maily jako druhý faktor.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="c3b5d-169">Můžete nastavit další druhý faktor pro ověřování, jako jsou kódy QR.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="c3b5d-170">Klepněte na **Odeslat**.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-170">Tap **Submit**.</span></span>

![Odeslat zobrazení ověřovacího kódu](2fa/_static/login2fa7.png)

* <span data-ttu-id="c3b5d-172">Zadejte kód, který se zobrazí ve zprávě SMS.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="c3b5d-173">Kliknutím na zaškrtávací políčko **Zapamatovat tento prohlížeč** nebudete muset použít 2FA k přihlášení při použití stejného zařízení a prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="c3b5d-174">Povolení 2FA a kliknutí na **Zapamatovat si tento prohlížeč** vám poskytne silnou 2FA ochranu od uživatelů se zlými úmysly, kteří se pokoušejí získat přístup k vašemu účtu, pokud nemají přístup k vašemu zařízení.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="c3b5d-175">To můžete provést na jakémkoli soukromém zařízení, které pravidelně používáte.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="c3b5d-176">Nastavením  **Zapamatovat si tento prohlížeč**, získáte přizpůsobenou bezpečnost 2FA ze zařízení, která pravidelně nepoužíváte, a získáte pohodlí, takže nemusíte přecházet na 2FA na svých vlastních zařízeních.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Ověřit zobrazení](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="c3b5d-178">Uzamčení účtu pro ochranu před útoky hrubou silou</span><span class="sxs-lookup"><span data-stu-id="c3b5d-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="c3b5d-179">Pro 2FA se doporučuje uzamčení účtu.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="c3b5d-180">Jakmile se uživatel přihlásí prostřednictvím místního účtu nebo účtu sociálních sítí, uloží se každý pokus o selhání na 2FA.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="c3b5d-181">Pokud je dosaženo maximálního počtu neúspěšných pokusů o přístup, uživatel je uzamčen (výchozí: uzamčení je 5 minut po 5 neúspěšných pokusech o přístup).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="c3b5d-182">Úspěšné ověření obnoví počet neúspěšných pokusů o přístup a resetuje hodiny.</span><span class="sxs-lookup"><span data-stu-id="c3b5d-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="c3b5d-183">Maximální počet neúspěšných pokusů o přístup a čas uzamčení lze nastavit pomocí [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) a [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="c3b5d-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="c3b5d-184">Následující konfigurace uzamčení účtu po 10 minutách po 10 neúspěšných pokusech o přístup:</span><span class="sxs-lookup"><span data-stu-id="c3b5d-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="c3b5d-185">Potvrďte, že [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sady `lockoutOnFailure` `true` :</span><span class="sxs-lookup"><span data-stu-id="c3b5d-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
