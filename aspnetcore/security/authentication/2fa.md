---
title: Dvoufaktorové ověřování přes SMS v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak nastavit dvojúrovňového ověřování (2FA) s aplikací ASP.NET Core.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 424d21e446de02b39daa7685205a00931361b326
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661453"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Dvoufaktorové ověřování přes SMS v ASP.NET Core

Od [Ricke Anderson](https://twitter.com/RickAndMSFT) a [Švýcarska – vývojáři](https://github.com/Swiss-Devs)

>[!WARNING]
> Dva faktoru ověřování (2FA), pomocí časovou synchronizací jednorázové heslo algoritmus (TOTP), jsou tyto aplikace v oboru doporučenému přístupu pro 2FA. 2FA pomocí TOTP je upřednostňována před SMS 2FA. Další informace najdete v tématu [Povolení generování kódu QR pro aplikace TOTP Authenticator v ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) pro ASP.NET Core 2,0 a novější.

Tento kurz ukazuje, jak nastavit dvojúrovňového ověřování (2FA) pomocí serveru SMS. Pokyny jsou uvedené pro [Twilio](https://www.twilio.com/) a [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), ale můžete použít jakéhokoli jiného poskytovatele serveru SMS. Než začnete s tímto kurzem, doporučujeme dokončit [potvrzení účtu a obnovení hesla](xref:security/authentication/accconfirm) .

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA). [Jak stáhnout](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Vytvořte nový projekt ASP.NET Core

Vytvořte novou ASP.NET Core webovou aplikaci s názvem `Web2FA` s jednotlivými uživatelskými účty. Podle pokynů v části <xref:security/enforcing-ssl> nastavte a vyžadovat protokol HTTPS.

### <a name="create-an-sms-account"></a>Vytvoření účtu služby SMS

Vytvořte účet serveru SMS, například z [Twilio](https://www.twilio.com/) nebo [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Zaznamenejte přihlašovací údaje pro ověřování (pro twilio: accountSid a ověřovacího tokenu pro ASPSMS: vlastnosti userkey jedná a heslo).

#### <a name="figuring-out-sms-provider-credentials"></a>Zjištění přihlašovací údaje poskytovatele serveru SMS

**Twilio**

Na kartě řídicí panel účtu Twilio zkopírujte **identifikátor SID účtu** a **ověřovací token**.

**ASPSMS:**

V nastavení účtu přejděte na **vlastnosti UserKey** a zkopírujte ho společně s vaším **heslem**.

Tyto hodnoty budeme později ukládat pomocí nástroje Správce tajných klíčů v rámci klíčů `SMSAccountIdentification` a `SMSAccountPassword`.

#### <a name="specifying-senderid--originator"></a>Určení SenderID / původce

**Twilio:** Na kartě čísla zkopírujte své Twilio **telefonní číslo**.

**ASPSMS:** V nabídce odemknout původci odemkněte jeden nebo více původců nebo vyberte alfanumerický původ (není podporován všemi sítěmi).

Později tuto hodnotu uložíme pomocí nástroje Správce tajných klíčů v rámci klíče `SMSAccountFrom`.

### <a name="provide-credentials-for-the-sms-service"></a>Zadejte přihlašovací údaje služby SMS

Pro přístup k uživatelskému účtu a nastavení klíče použijeme [vzor možností](xref:fundamentals/configuration/options) .

* Vytvoření třídy k načtení zabezpečený klíč serveru SMS. V této ukázce je třída `SMSoptions` vytvořena v souboru *Services/SMSoptions. cs* .

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Pomocí [nástroje Správce tajných](xref:security/app-secrets)kódů nastavte `SMSAccountIdentification``SMSAccountPassword` a `SMSAccountFrom`. Příklad:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Přidání balíčku NuGet pro poskytovatele serveru SMS. Z balíčku správce konzoly (konzolu PMC) spusťte:

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* Přidejte kód do souboru *Services/MessageServices. cs* pro povolení serveru SMS. Použijte Twilio nebo ASPSMS části:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Konfigurace spuštění pro použití `SMSoptions`

Přidejte `SMSoptions` do kontejneru služby v metodě `ConfigureServices` v *Startup.cs*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Povolení dvoufaktorového ověřování

Otevřete soubor zobrazení */Správa/index. cshtml* Razor zobrazení a odeberte znaky komentáře (takže se neoznačí jako komentář žádné značky).

## <a name="log-in-with-two-factor-authentication"></a>Přihlaste se pomocí dvojúrovňového ověřování

* Spusťte aplikaci a zaregistrovat nový uživatel

![Webová aplikace zaregistrovat, zobrazení otevřít v Microsoft Edge](2fa/_static/login2fa1.png)

* Klepněte na své uživatelské jméno, které aktivuje metodu `Index` akce na stránce Spravovat kontroler. Pak klepněte na telefonní číslo **Přidat** odkaz.

![Správa zobrazení – klepnout na odkaz "Přidání"](2fa/_static/login2fa2.png)

* Přidejte telefonní číslo, které bude přijímat ověřovací kód, a klepněte na **Odeslat ověřovací kód**.

![Přidat telefonní číslo stránky](2fa/_static/login2fa3.png)

* Zobrazí se zpráva SMS s ověřovacím kódem. Zadejte ho a klepněte na **Odeslat** .

![Ověřit telefonní číslo stránky](2fa/_static/login2fa4.png)

Pokud vám textovou zprávu, přečtěte si téma twilio protokolu stránky.

* Správa zobrazení ukazuje že vaše telefonní číslo bylo úspěšně přidán.

![Správa zobrazení – telefonní číslo se úspěšně přidal](2fa/_static/login2fa5.png)

* Klepnutím na **Povolit** povolte dvojúrovňové ověřování.

![Správa zobrazení – povolení dvoufaktorového ověřování](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Test dvoufaktorového ověřování

* Odhlaste se.

* Přihlaste se.

* Uživatelský účet povolila dvojúrovňové ověřování, takže je nutné zadat druhý faktor ověřování. V tomto kurzu jste povolili ověření pomocí telefonu. Integrované šablony lze také nastavit e-mail jako druhý faktor. Můžete nastavit další druhý faktory pro ověřování, jako je kódy QR. Klepněte na **Odeslat**.

![Poslat ověřovací kód zobrazení](2fa/_static/login2fa7.png)

* Zadejte kód, který se zobrazí ve zprávě SMS.

* Kliknutím na zaškrtávací políčko **Zapamatovat tento prohlížeč** nebudete muset použít 2FA k přihlášení při použití stejného zařízení a prohlížeče. Povolení 2FA a kliknutí na **Zapamatovat si tento prohlížeč** vám poskytne silnou 2FA ochranu od uživatelů se zlými úmysly, kteří se pokoušejí získat přístup k vašemu účtu, pokud nemají přístup k vašemu zařízení. Můžete to provést na libovolném privátní zařízení, kterou používáte pravidelně. Nastavením **Zapamatovat si tento prohlížeč**, získáte přizpůsobenou bezpečnost 2FA ze zařízení, která pravidelně nepoužíváte, a získáte pohodlí, takže nemusíte přecházet na 2FA na svých vlastních zařízeních.

![Ověření zobrazení](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Uzamčení účtu pro ochranu před útoky hrubou silou

Uzamčení účtu, doporučujeme pomocí 2FA. Když se uživatel přihlásí pomocí místního účtu nebo účtu na sociální síti, se ukládají jednotlivé neúspěšné pokusy o na 2FA. Pokud je dosaženo maximální neúspěšných pokusů o přístup, je uživatel uzamčen (výchozí: pětiminutový uzamčení po 5 neúspěšných pokusů o přístup). Po provedení úspěšného ověření resetuje počet pokusů o neúspěšných přístupů a resetuje na hodiny. Maximální počet neúspěšných pokusů o přístup a čas uzamčení lze nastavit pomocí [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) a [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). Následující nakonfiguruje uzamčení účtu po dobu 10 minut po 10 neúspěšných pokusů o přístup:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Potvrďte, že [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sady `lockoutOnFailure` `true`:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
