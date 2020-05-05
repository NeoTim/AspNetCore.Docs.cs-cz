---
title: Dvojúrovňové ověřování pomocí SMS v ASP.NET Core
author: rick-anderson
description: Naučte se, jak pomocí aplikace ASP.NET Core nastavit dvojúrovňové ověřování (2FA).
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/2fa
ms.openlocfilehash: e33f22356de983c8c4e0211822d5027a33b48de6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775827"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Dvojúrovňové ověřování pomocí SMS v ASP.NET Core

Od [Ricke Anderson](https://twitter.com/RickAndMSFT) a [Švýcarska – vývojáři](https://github.com/Swiss-Devs)

>[!WARNING]
> TOTP (2FA) ověřovací aplikace pro ověřování pomocí času založeného na čase () jsou doporučeným oborem přístupu pro 2FA. 2FA pomocí TOTP se upřednostňuje pro SMS 2FA. Další informace najdete v tématu [Povolení generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pro ASP.NET Core 2,0 a novější.

V tomto kurzu se dozvíte, jak nastavit dvojúrovňové ověřování (2FA) pomocí serveru SMS. Pokyny jsou uvedené pro [Twilio](https://www.twilio.com/) a [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale můžete použít jakéhokoli jiného poskytovatele serveru SMS. Než začnete s tímto kurzem, doporučujeme dokončit [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm) .

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Jak stáhnout](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Vytvoření nového projektu ASP.NET Core

Vytvořte novou ASP.NET Core webovou aplikaci s názvem `Web2FA` s jednotlivými uživatelskými účty. Při nastavování <xref:security/enforcing-ssl> a VYŽADOVÁNÍ protokolu HTTPS postupujte podle pokynů v části.

### <a name="create-an-sms-account"></a>Vytvoření účtu serveru SMS

Vytvořte účet serveru SMS, například z [Twilio](https://www.twilio.com/) nebo [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Zaznamenejte přihlašovací údaje pro ověřování (pro Twilio: accountSid a authToken, pro ASPSMS: vlastnosti UserKey a heslo).

#### <a name="figuring-out-sms-provider-credentials"></a>Zjišťování přihlašovacích údajů poskytovatele služby SMS

**Twilio**

Na kartě řídicí panel účtu Twilio zkopírujte **identifikátor SID účtu** a **ověřovací token**.

**ASPSMS:**

V nastavení účtu přejděte na **vlastnosti UserKey** a zkopírujte ho společně s vaším **heslem**.

Tyto hodnoty budeme později ukládat v nástroji pomocí Správce tajného klíče v rámci klíčů `SMSAccountIdentification` a. `SMSAccountPassword`

#### <a name="specifying-senderid--originator"></a>Zadání SenderID/původce

**Twilio:** Na kartě čísla zkopírujte své Twilio **telefonní číslo**.

**ASPSMS:** V nabídce odemknout původci odemkněte jeden nebo více původců nebo vyberte alfanumerický původ (není podporován všemi sítěmi).

Později tuto hodnotu uložíme pomocí nástroje Správce tajných klíčů v rámci klíče `SMSAccountFrom`.

### <a name="provide-credentials-for-the-sms-service"></a>Zadejte přihlašovací údaje pro službu SMS.

Pro přístup k uživatelskému účtu a nastavení klíče použijeme [vzor možností](xref:fundamentals/configuration/options) .

* Vytvořte třídu, která načte zabezpečený klíč SMS. V této ukázce je `SMSoptions` třída vytvořena v souboru *Services/SMSoptions. cs* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

`SMSAccountIdentification`Pomocí `SMSAccountPassword` [nástroje Správce tajných klíčů](xref:security/app-secrets)nastavte a `SMSAccountFrom` . Příklad:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Přidejte balíček NuGet pro poskytovatele služby SMS. V konzole správce balíčků (PMC) spusťte:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Přidejte kód do souboru *Services/MessageServices. cs* pro povolení serveru SMS. Použijte buď Twilio, nebo část ASPSMS:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Konfigurace spuštění pro použití`SMSoptions`

Přidejte `SMSoptions` do kontejneru služby v `ConfigureServices` metodě v *Startup.cs*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Povolení dvoufaktorového ověřování

Otevřete soubor zobrazení */Správa/index. cshtml* Razor zobrazení a odeberte znaky komentáře (takže se neoznačí jako komentář žádné značky).

## <a name="log-in-with-two-factor-authentication"></a>Přihlášení Pomocí dvojúrovňového ověřování

* Spuštění aplikace a registrace nového uživatele

![Zobrazení registru webové aplikace otevřené v Microsoft Edge](2fa/_static/login2fa1.png)

* Klepněte na své uživatelské jméno, které aktivuje metodu `Index` Action na stránce Spravovat kontroler. Pak klepněte na telefonní číslo **Přidat** odkaz.

![Spravovat zobrazení – klepněte na odkaz Přidat](2fa/_static/login2fa2.png)

* Přidejte telefonní číslo, které bude přijímat ověřovací kód, a klepněte na **Odeslat ověřovací kód**.

![Přidat stránku telefonního čísla](2fa/_static/login2fa3.png)

* Obdržíte textovou zprávu s ověřovacím kódem. Zadejte ho a klepněte na **Odeslat** .

![Ověřit stránku telefonního čísla](2fa/_static/login2fa4.png)

Pokud neobdržíte textovou zprávu, přečtěte si stránku protokol Twilio.

* V zobrazení Správa se zobrazí vaše telefonní číslo bylo úspěšně přidáno.

![Správa zobrazení – Telefonní číslo bylo úspěšně přidáno.](2fa/_static/login2fa5.png)

* Klepnutím na **Povolit** povolte dvojúrovňové ověřování.

![Spravovat zobrazení – povolit dvojúrovňové ověřování](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Test dvojúrovňového ověřování

* Odhlaste se.

* Přihlaste se.

* Uživatelský účet má povolené dvojúrovňové ověřování, takže musíte zadat druhý faktor ověřování. V tomto kurzu jste povolili ověřování pro telefon. Předdefinované šablony také umožňují nastavit e-maily jako druhý faktor. Můžete nastavit další druhý faktor pro ověřování, jako jsou kódy QR. Klepněte na **Odeslat**.

![Odeslat zobrazení ověřovacího kódu](2fa/_static/login2fa7.png)

* Zadejte kód, který se zobrazí ve zprávě SMS.

* Kliknutím na zaškrtávací políčko **Zapamatovat tento prohlížeč** nebudete muset použít 2FA k přihlášení při použití stejného zařízení a prohlížeče. Povolení 2FA a kliknutí na **Zapamatovat si tento prohlížeč** vám poskytne silnou 2FA ochranu od uživatelů se zlými úmysly, kteří se pokoušejí získat přístup k vašemu účtu, pokud nemají přístup k vašemu zařízení. To můžete provést na jakémkoli soukromém zařízení, které pravidelně používáte. Nastavením **Zapamatovat si tento prohlížeč**, získáte přizpůsobenou bezpečnost 2FA ze zařízení, která pravidelně nepoužíváte, a získáte pohodlí, takže nemusíte přecházet na 2FA na svých vlastních zařízeních.

![Ověřit zobrazení](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Uzamčení účtu pro ochranu před útoky hrubou silou

Pro 2FA se doporučuje uzamčení účtu. Jakmile se uživatel přihlásí prostřednictvím místního účtu nebo účtu sociálních sítí, uloží se každý pokus o selhání na 2FA. Pokud je dosaženo maximálního počtu neúspěšných pokusů o přístup, uživatel je uzamčen (výchozí: uzamčení je 5 minut po 5 neúspěšných pokusech o přístup). Úspěšné ověření obnoví počet neúspěšných pokusů o přístup a resetuje hodiny. Maximální počet neúspěšných pokusů o přístup a čas uzamčení lze nastavit pomocí [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) a [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). Následující konfigurace uzamčení účtu po 10 minutách po 10 neúspěšných pokusech o přístup:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Potvrďte [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) , že `lockoutOnFailure` PasswordSignInAsync `true`sady:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
