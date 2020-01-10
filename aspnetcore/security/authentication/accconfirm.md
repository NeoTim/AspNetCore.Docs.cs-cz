---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Naučte se vytvářet aplikace ASP.NET Core s potvrzením e-mailu a resetováním hesla.
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 49d3d214fd64edc5b17df2df929ddc3c2af47ede
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829267"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Potvrzení účtu a obnovení hesla v ASP.NET Core

Od [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)a [Jan Audette](https://twitter.com/joeaudette)

V tomto kurzu se dozvíte, jak vytvořit aplikaci ASP.NET Core s potvrzením e-mailu a resetováním hesla. Tento **kurz není** úvodním tématem. Měli byste se seznámit s:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Ověřování](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

V [tomto souboru PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) se zobrazí verze ASP.NET Core 1,1.

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>Požadavky

[.NET Core 3,0 SDK nebo novější](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Vytvoření a otestování webové aplikace s ověřováním

Spuštěním následujících příkazů vytvořte webovou aplikaci s ověřováním.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele. Po registraci budete přesměrováni na stránku `/Identity/Account/RegisterConfirmation`, která obsahuje odkaz pro simulaci potvrzení e-mailu:

* Vyberte odkaz `Click here to confirm your account`.
* Vyberte **přihlašovací** odkaz a přihlaste se se stejnými přihlašovacími údaji.
* Vyberte odkaz `Hello YourEmail@provider.com!`, který vás přesměruje na stránku `/Identity/Account/Manage/PersonalData`.
* Na levé straně vyberte kartu **osobní data** a pak vyberte **Odstranit**.

### <a name="configure-an-email-provider"></a>Konfigurace poskytovatele e-mailu

V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) . K odeslání e-mailu potřebujete účet SendGrid a klíč. Můžete použít jiné poskytovatele e-mailů. K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu. Protokol SMTP je obtížné zabezpečit a nastavit správně.

Vytvořte třídu, která načte zabezpečený e-mailový klíč. V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurace uživatelských tajných klíčů SendGrid

Pomocí [nástroje Správce tajných](xref:security/app-secrets)kódů nastavte `SendGridUser` a `SendGridKey`. Příklad:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných kódů. JSON* v adresáři `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.

Obsah souboru *tajných kódů. JSON* není zašifrovaný. Následující kód ukazuje soubor *tajných kódů. JSON* . Hodnota `SendGridKey` byla odebrána.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Nainstalovat SendGrid

V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.

Instalace balíčku `SendGrid` NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V konzole správce balíčků zadejte následující příkaz:

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z konzoly zadejte následující příkaz:

```dotnetcli
dotnet add package SendGrid
```

---

Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.

### <a name="implement-iemailsender"></a>Implementovat IEmailSender

Chcete-li implementovat `IEmailSender`, vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurace spuštění pro podporu e-mailu

Do metody `ConfigureServices` v souboru *Startup.cs* přidejte následující kód:

* Přidejte `EmailSender` jako přechodné služby.
* Zaregistrujte instanci konfigurace `AuthMessageSenderOptions`.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>Registrace, potvrzení e-mailu a resetování hesla

Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.

* Spusťte aplikaci a zaregistrovat nový uživatel
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

Předdefinované tokeny uživatelů identity (viz [AspNetCore/src/identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Změna životnosti tokenu e-mailu

Výchozí životnost tokenu [tokenů identity uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). V této části se dozvíte, jak změnit životnost tokenu e-mailu.

Přidat vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Přidejte vlastního zprostředkovatele do kontejneru služby:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>Odeslat potvrzení znovu e-mailem

Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Ladit e-mail

Pokud nemůžete získat e-mail:

* Nastavte zarážku v `EmailSender.Execute` pro ověření, že `SendGridClient.SendEmailAsync` je volána.
* Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem pro `EmailSender.Execute`.
* Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Ověřte složku s nevyžádanou poštou.
* Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)
* Zkuste odesílat do jiných e-mailových účtů.

**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje. Pokud publikujete aplikaci do Azure, nastavte tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.

## <a name="combine-social-and-local-login-accounts"></a>Kombinování sociálních a místních přihlašovacích účtů

Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování. Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).

Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz. V následujícím pořadí se jako místní přihlášení jako první vytvoří "RickAndMSFT@gmail.com"; účet ale můžete vytvořit jako nejdřív jako sociální přihlášení a pak přidat místní přihlašovací jméno.

![Webová aplikace: RickAndMSFT@gmail.com ověřený uživatelem](accconfirm/_static/rick.png)

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

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

[.NET Core 2,2 SDK nebo novější](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>Vytvoření webové aplikace a identity uživatelského rozhraní

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

## <a name="test-new-user-registration"></a>Otestovat registraci nových uživatelů

Spusťte aplikaci, vyberte odkaz **Registrovat** a zaregistrujte uživatele. V tomto okamžiku je jediným ověřením e-mailu s atributem [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) . Po odeslání registrace jste přihlášení do aplikace. Později v tomto kurzu se kód aktualizuje, aby se noví uživatelé nemohli přihlásit, dokud se neověří jejich e-mailová adresa.

[!INCLUDE[](~/includes/view-identity-db.md)]

Všimněte si, že pole `EmailConfirmed` tabulky je `False`.

Tento e-mail můžete chtít znovu použít v dalším kroku, když aplikace pošle potvrzovací e-mail. Klikněte pravým tlačítkem na řádek a vyberte **Odstranit**. Odstranění aliasu e-mailu usnadňuje následující kroky.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Vyžadovat potvrzení e-mailu

Osvědčeným postupem je potvrdit e-mailovou registraci nového uživatele. Potvrzení e-mailu pomáhá ověřit, že nezosobňuje někoho jiného (tj. není zaregistrované u e-mailu někoho jiného). Předpokládejme, že máte diskuzní fórum a Vy jste se rozhodli "yli@example.com" zabránit v registraci jako "nolivetto@contoso.com". Bez potvrzení e-mailu může "nolivetto@contoso.com" obdržet z vaší aplikace nevyžádaný e-mail. Předpokládejme, že uživatel omylem zaregistroval označení "ylo@example.com" a jste zaznamenal chybu "Yli". Nebylo by možné použít obnovení hesla, protože aplikace nemá správný e-mail. Potvrzení e-mailu poskytuje omezené ochrany z roboty. Potvrzení e-mailu neposkytuje ochranu před uživateli se zlými úmysly s mnoha e-mailovými účty

Obecně chcete, aby noví uživatelé před odesláním jakýchkoli dat na web nemuseli odeslat potvrzené e-maily.

Aktualizace `Startup.ConfigureServices` pro vyžadování potvrzeného e-mailu:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` znemožňuje přihlášení registrovaným uživatelům, dokud se nepotvrdí jejich e-mailové adresy.

### <a name="configure-email-provider"></a>Konfigurovat poskytovatele e-mailů

V tomto kurzu se k odeslání e-mailu používá [SendGrid](https://sendgrid.com) . K odeslání e-mailu potřebujete účet SendGrid a klíč. Můžete použít jiné poskytovatele e-mailů. ASP.NET Core 2. x zahrnuje `System.Net.Mail`, které vám umožní odeslat e-maily z vaší aplikace. K odeslání e-mailu doporučujeme použít SendGrid nebo jinou e-mailovou službu. Protokol SMTP je obtížné zabezpečit a nastavit správně.

Vytvořte třídu, která načte zabezpečený e-mailový klíč. V této ukázce vytvořte *Services/AuthMessageSenderOptions. cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurace uživatelských tajných klíčů SendGrid

Pomocí [nástroje Správce tajných](xref:security/app-secrets)kódů nastavte `SendGridUser` a `SendGridKey`. Příklad:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Správce tajných klíčů v systému Windows ukládá páry klíče/hodnoty do souboru *tajných kódů. JSON* v adresáři `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`.

Obsah souboru *tajných kódů. JSON* není zašifrovaný. Následující kód ukazuje soubor *tajných kódů. JSON* . Hodnota `SendGridKey` byla odebrána.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Další informace najdete v tématu [vzor možností](xref:fundamentals/configuration/options) a [Konfigurace](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Nainstalovat SendGrid

V tomto kurzu se dozvíte, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete posílat e-maily pomocí protokolu SMTP a dalších mechanismů.

Instalace balíčku `SendGrid` NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

V konzole správce balíčků zadejte následující příkaz:

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Z konzoly zadejte následující příkaz:

```dotnetcli
dotnet add package SendGrid
```

---

Pokud si chcete zaregistrovat bezplatný účet SendGrid, přečtěte si téma Začínáme [se službou SendGrid](https://sendgrid.com/free/) zdarma.

### <a name="implement-iemailsender"></a>Implementovat IEmailSender

Chcete-li implementovat `IEmailSender`, vytvořte *služby/EmailSender. cs* s kódem podobným následujícímu:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurace spuštění pro podporu e-mailu

Do metody `ConfigureServices` v souboru *Startup.cs* přidejte následující kód:

* Přidejte `EmailSender` jako přechodné služby.
* Zaregistrujte instanci konfigurace `AuthMessageSenderOptions`.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Povolení potvrzení účtu a obnovení hesla

Šablona obsahuje kód pro potvrzení účtu a obnovení hesla. Vyhledejte metodu `OnPostAsync` v *oblasti/identita/stránky/účet/registr. cshtml. cs*.

Zabraňte automatického přihlášení nově registrovaných uživatelů pomocí komentáře k následujícímu řádku:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Metoda Complete se zobrazí se zvýrazněným změněným řádkem:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrace, potvrzení e-mailu a resetování hesla

Spusťte webovou aplikaci a otestujte postup potvrzení a obnovení hesla účtu.

* Spusťte aplikaci a zaregistrovat nový uživatel
* Ověřte si e-mail s odkazem na potvrzení účtu. Pokud e-mail neobdržíte, přečtěte si téma [ladění e-mailu](#debug) .
* Kliknutím na odkaz potvrďte svůj e-mail.
* Přihlaste se pomocí svého e-mailu a hesla.
* Odhlaste se.

### <a name="view-the-manage-page"></a>Zobrazit stránku pro správu

V prohlížeči vyberte své uživatelské jméno: ![okno prohlížeče s uživatelským jménem](accconfirm/_static/un.png)

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

Předdefinované tokeny uživatelů identity (viz [AspNetCore/src/identity/Extensions. Core/src/TokenOptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) mají [časový limit jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Změna životnosti tokenu e-mailu

Výchozí životnost tokenu [tokenů identity uživatele](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) je [jeden den](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). V této části se dozvíte, jak změnit životnost tokenu e-mailu.

Přidat vlastní [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) a <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Přidejte vlastního zprostředkovatele do kontejneru služby:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>Odeslat potvrzení znovu e-mailem

Podívejte se na [Tento problém GitHubu](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>Ladit e-mail

Pokud nemůžete získat e-mail:

* Nastavte zarážku v `EmailSender.Execute` pro ověření, že `SendGridClient.SendEmailAsync` je volána.
* Vytvořte [konzolovou aplikaci pro odesílání e-mailů](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) s podobným kódem pro `EmailSender.Execute`.
* Zkontrolujte stránku [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Ověřte složku s nevyžádanou poštou.
* Vyzkoušejte si jiný e-mailový alias u jiného poskytovatele e-mailu (Microsoft, Yahoo, Gmail atd.)
* Zkuste odesílat do jiných e-mailových účtů.

**Osvědčeným postupem z hlediska zabezpečení** je **Nepoužívat provozní** tajemství v rámci testování a vývoje. Pokud publikujete aplikaci do Azure, můžete nastavit tajné klíče SendGrid jako nastavení aplikace na portálu webové aplikace Azure. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.

## <a name="combine-social-and-local-login-accounts"></a>Kombinování sociálních a místních přihlašovacích účtů

Chcete-li dokončit tuto část, je nutné nejprve povolit externího zprostředkovatele ověřování. Prohlédněte si [ověřování pro Facebook, Google a externí poskytovatele](xref:security/authentication/social/index).

Místní a sociální účty můžete zkombinovat kliknutím na svůj e-mailový odkaz. V následujícím pořadí se jako místní přihlášení jako první vytvoří "RickAndMSFT@gmail.com"; účet ale můžete vytvořit jako nejdřív jako sociální přihlášení a pak přidat místní přihlašovací jméno.

![Webová aplikace: RickAndMSFT@gmail.com ověřený uživatelem](accconfirm/_static/rick.png)

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
