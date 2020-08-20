---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 57607390e7d5e58df9f27437faecd57504ad64df
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635369"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Potvrzení účtu a obnovení hesla v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)

V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla. Tento **kurz není** úvodním tématem. Měli byste být obeznámeni s:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Authentication](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Předpoklady

[.NET Core 3,0 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Vytvoření a otestování webové aplikace s ověřováním

Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele. Po zaregistrování budete přesměrováni na `/Identity/Account/RegisterConfirmation` stránku, která obsahuje odkaz pro simulaci potvrzení e-mailu:

* Vyberte odkaz `Click here to confirm your account`.
* Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.
* Vyberte `Hello YourEmail@provider.com!` odkaz, který vás přesměruje na `/Identity/Account/Manage/PersonalData` stránku.
* Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.

### <a name="configure-an-email-provider"></a>Konfigurace poskytovatele e-mailu

V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) . K odeslání e-mailu potřebujete účet SendGrid a klíč. Můžete použít jiné poskytovatele e-mailů. K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu. Protokol SMTP je obtížné zabezpečit a nastavit správně.

Účet SendGrid může vyžadovat [Přidání odesílatele](https://sendgrid.com/docs/ui/sending-email/senders/).

Vytvořte třídu, která načte zabezpečený e-mailový klíč. V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurace uživatelských tajných klíčů SendGrid

Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets). Příklad:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.

Obsah *secrets.jsv* souboru není šifrovaný. Následující kód ukazuje *secrets.jsv* souboru. `SendGridKey`Hodnota byla odebrána.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Nainstalovat SendGrid

V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.

Nainstalujte `SendGrid` balíček NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V konzole správce balíčků zadejte následující příkaz:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z konzoly zadejte následující příkaz:

```dotnetcli
dotnet add package SendGrid
```

---

Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.

### <a name="implement-iemailsender"></a>Implementovat IEmailSender

K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurace spuštění pro podporu e-mailu

Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:

* Přidejte `EmailSender` jako přechodné služby.
* Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>RegisterConfirmation uživatelského rozhraní

Postupujte podle pokynů pro [generování Identity uživatelského rozhraní](xref:security/authentication/scaffold-identity) a uživatelského rozhraní `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Registrace, potvrzení e-mailu a resetování hesla

Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.

* Spuštění aplikace a registrace nového uživatele
* Ověřte si e-mail s odkazem na potvrzení účtu. Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .
* Kliknutím na odkaz potvrďte svůj e-mail.
* Přihlaste se pomocí svého e-mailu a hesla.
* Odhlaste se.

### <a name="test-password-reset"></a>Test resetování hesla

* Pokud jste přihlášeni, vyberte **Odhlásit**.
* Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .
* Zadejte e-mail, který jste použili k registraci účtu.
* Pošle se e-mail s odkazem na resetování hesla. Podívejte se na e-mail a kliknutím na odkaz resetujte heslo. Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.

## <a name="change-email-and-activity-timeout"></a>Časový limit změny e-mailu a aktivity

Výchozí časový limit nečinnosti je 14 dní. Následující kód nastaví časový limit nečinnosti na 5 dní:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Změna všech životností tokenů ochrany dat

Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Změna životnosti tokenu e-mailu

Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). V této části se dozvíte, jak změnit životnost tokenu e-mailu.

Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Přidejte vlastního zprostředkovatele do kontejneru služby:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>Odeslat potvrzení znovu e-mailem

Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Ladit e-mail

Pokud nemůžete získat e-mail:

* Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.
* Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .
* Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Ověřte složku s nevyžádanou poštou.
* Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)
* Zkuste odesílat do jiných e-mailových účtů.

**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje. Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.

## <a name="combine-social-and-local-login-accounts"></a>Kombinování sociálních a místních přihlašovacích účtů

Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování. Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).

Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz. V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

Klikněte na odkaz **Správa** . Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.

![Spravovat zobrazení](accconfirm/_static/manage.png)

Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci. Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

Tyto dva účty byly kombinovány. Můžete se přihlásit pomocí obou účtů. Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Povolit potvrzení účtu, jakmile má lokalita uživatele

Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele. Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené. Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:

* Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.
* Potvrďte stávající uživatele. Například Batch – posílání e-mailů s odkazy na potvrzení.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Předpoklady

[.NET Core 2,2 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a>Vytvoření webové aplikace a uživatelského rozhraní Identity

Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.

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

> [!NOTE]
> Pokud <xref:Microsoft.AspNetCore.Identity.PasswordOptions> jsou nakonfigurovány v `Startup.ConfigureServices` , může být pro [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` vlastnost v generovaných stránkách vyžadována konfigurace atributu Identity . `InputModel` `Password` Vlastnost se nachází v `Areas/Identity/Pages/Account/Register.cshtml.cs` souboru po vygenerování uživatelského rozhraní Identity .

## <a name="test-new-user-registration"></a>Otestovat registraci nových uživatelů

Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele. V tomto okamžiku je jediným ověřením e-mailu [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut. Po odeslání registrace jste přihlášení do aplikace. Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.

[!INCLUDE[](~/includes/view-identity-db.md)]

Všimněte si, že `EmailConfirmed` pole tabulky je `False` .

Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail. Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**. Odstranění aliasu e-mailu usnadňuje následující kroky.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Vyžadovat potvrzení e-mailu

Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele. Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného). Předpokládejme, že máte diskuzní fórum a chtěli jste zabránit yli@example.com v registraci jako nolivetto@contoso.com . Bez potvrzení e-mailu nolivetto@contoso.com může zpráva z vaší aplikace obdržet nevyžádaný e-mail. Předpokládejme, že uživatel omylem registrovaný jako " ylo@example.com " a jste zaznamenal chybu "Yli". Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail. Potvrzení e-mailu poskytuje omezené ochrany z roboty. Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty

Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.

Aktualizace `Startup.ConfigureServices`  pro vyžadování potvrzeného e-mailové adresy:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` zabrání registrovaným uživatelům v přihlášení, dokud se nepotvrdí jejich e-maily.

### <a name="configure-email-provider"></a>Konfigurovat poskytovatele e-mailů

V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) . K odeslání e-mailu potřebujete účet SendGrid a klíč. Můžete použít jiné poskytovatele e-mailů. ASP.NET Core 2. x zahrnuje `System.Net.Mail` , což umožňuje odesílat e-maily z vaší aplikace. K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu. Protokol SMTP je obtížné zabezpečit a nastavit správně.

Vytvořte třídu, která načte zabezpečený e-mailový klíč. V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurace uživatelských tajných klíčů SendGrid

Nastavte `SendGridUser` a `SendGridKey` pomocí [nástroje Správce tajných klíčů](xref:security/app-secrets). Příklad:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do *secrets.js* souboru v `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáři.

Obsah *secrets.jsv* souboru není šifrovaný. Následující kód ukazuje *secrets.jsv* souboru. `SendGridKey`Hodnota byla odebrána.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Nainstalovat SendGrid

V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.

Nainstalujte `SendGrid` balíček NuGet:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

V konzole správce balíčků zadejte následující příkaz:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Z konzoly zadejte následující příkaz:

```dotnetcli
dotnet add package SendGrid
```

---

Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.

### <a name="implement-iemailsender"></a>Implementovat IEmailSender

K implementaci `IEmailSender` vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurace spuštění pro podporu e-mailu

Do `ConfigureServices` metody v souboru *Startup.cs* přidejte následující kód:

* Přidejte `EmailSender` jako přechodné služby.
* Zaregistrujte `AuthMessageSenderOptions` instanci konfigurace.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Povolení potvrzení účtu a obnovení hesla

Šablona obsahuje kód pro potvrzení účtu a obnovení hesla. Vyhledejte `OnPostAsync` metodu v *oblasti/ Identity /Pages/Account/Register.cshtml.cs*.

Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Metoda Complete se zobrazí se zvýrazněným změněným řádkem:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrace, potvrzení e-mailu a resetování hesla

Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.

* Spuštění aplikace a registrace nového uživatele
* Ověřte si e-mail s odkazem na potvrzení účtu. Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .
* Kliknutím na odkaz potvrďte svůj e-mail.
* Přihlaste se pomocí svého e-mailu a hesla.
* Odhlaste se.

### <a name="view-the-manage-page"></a>Zobrazit stránku pro správu

V prohlížeči vyberte své uživatelské jméno: ![ okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)

Stránka Správa se zobrazí s vybranou kartou **profil** . **E-mail** zobrazuje zaškrtávací políčko označující, že byl e-mail potvrzen.

### <a name="test-password-reset"></a>Test resetování hesla

* Pokud jste přihlášeni, vyberte **Odhlásit**.
* Vyberte odkaz **Přihlásit** a vyberte odkaz **zapomenuté heslo?** .
* Zadejte e-mail, který jste použili k registraci účtu.
* Pošle se e-mail s odkazem na resetování hesla. Podívejte se na e-mail a kliknutím na odkaz resetujte heslo. Po úspěšném resetování hesla se můžete přihlásit pomocí e-mailu a nového hesla.

## <a name="change-email-and-activity-timeout"></a>Časový limit změny e-mailu a aktivity

Výchozí časový limit nečinnosti je 14 dní. Následující kód nastaví časový limit nečinnosti na 5 dní:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Změna všech životností tokenů ochrany dat

Následující kód změní všechna období časového limitu tokenů ochrany dat na 3 hodiny:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Předdefinované Identity tokeny uživatelů (viz [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Změna životnosti tokenu e-mailu

Výchozí životnost tokenu [ Identity tokenů uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). V této části se dozvíte, jak změnit životnost tokenu e-mailu.

Přidejte vlastní [DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Přidejte vlastního zprostředkovatele do kontejneru služby:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Odeslat potvrzení znovu e-mailem

Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Ladit e-mail

Pokud nemůžete získat e-mail:

* Nastavte zarážku v `EmailSender.Execute` na `SendGridClient.SendEmailAsync` hodnotu ověřit je volána.
* Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem `EmailSender.Execute` .
* Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Ověřte složku s nevyžádanou poštou.
* Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)
* Zkuste odesílat do jiných e-mailových účtů.

**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje. Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.

## <a name="combine-social-and-local-login-accounts"></a>Kombinování sociálních a místních přihlašovacích účtů

Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování. Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).

Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz. V následujícím pořadí RickAndMSFT@gmail.com se "" nejdřív vytvoří jako místní přihlašovací jméno. účet ale můžete vytvořit jako nejdřív sociální přihlášení a pak přidat místní přihlašovací jméno.

![Webová aplikace: RickAndMSFT@gmail.com ověřené uživatelem](accconfirm/_static/rick.png)

Klikněte na odkaz **Správa** . Poznamenejte si externí (sociální přihlášení) přidružená k tomuto účtu.

![Spravovat zobrazení](accconfirm/_static/manage.png)

Klikněte na odkaz na jinou přihlašovací službu a přijměte žádosti o aplikaci. Na následujícím obrázku je Facebook externí zprostředkovatel ověřování:

![Správa zobrazení externích přihlášení s výpisem Facebooku](accconfirm/_static/fb.png)

Tyto dva účty byly kombinovány. Můžete se přihlásit pomocí obou účtů. Je možné, že budete chtít, aby uživatelé mohli přidávat místní účty pro případ výpadku služby ověřování v síti pro sociální přihlášení nebo pravděpodobně ztratili přístup ke svému sociálnímu účtu.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Povolit potvrzení účtu, jakmile má lokalita uživatele

Povolení potvrzení účtu na webu s uživateli zamkne všechny existující uživatele. Stávající uživatelé jsou uzamčeni, protože jejich účty nejsou potvrzené. Chcete-li vyřešit stávající uzamčení uživatele, použijte jeden z následujících přístupů:

* Aktualizujte databázi tak, aby označila všechny existující uživatele jako potvrzené.
* Potvrďte stávající uživatele. Například Batch – posílání e-mailů s odkazy na potvrzení.

::: moniker-end
