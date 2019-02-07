---
title: Potvrzení účtu a obnovení hesla v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak vytvořit aplikaci ASP.NET Core s e-mailové potvrzení a resetováním hesla.
ms.author: riande
ms.date: 7/11/2018
uid: security/authentication/accconfirm
ms.openlocfilehash: 0dc9907f9f54c8a0daf2e05a3769897e5145935f
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "54444139"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Potvrzení účtu a obnovení hesla v ASP.NET Core

::: moniker range="<= aspnetcore-2.0"

Zobrazit [tento soubor PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) pro ASP.NET Core 1.1 a verze 2.1.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Joe Audette](https://twitter.com/joeaudette)

Tento kurz ukazuje, jak vytvářet aplikace v ASP.NET Core s e-mailové potvrzení a resetováním hesla. Tento kurz je **není** začátku tématu. Měli byste se seznámit s:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Ověřování](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [](~/includes/2.1-SDK.md)]

## <a name="create-a-web--app-and-scaffold-identity"></a>Vytvoření webové aplikace a generování uživatelského rozhraní Identity

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

* V sadě Visual Studio vytvořte nový **webovou aplikaci** projekt s názvem **WebPWrecover**.
* Vyberte **ASP.NET Core 2.1**.
* Ponechte výchozí **ověřování** nastavena na **bez ověřování**. V dalším kroku se přidá ověřování.

V dalším kroku:

* Nastavení stránky rozložení *~/Pages/Shared/_Layout.cshtml*
* Vyberte *účtu/registrace*
* Vytvořte nový **třída kontextu dat**

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

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

Spustit `dotnet aspnet-codegenerator identity --help` můžete zobrazit nápovědu pro nástroj pro generování uživatelského rozhraní.

------

Postupujte podle pokynů v [povolení ověřování](xref:security/authentication/scaffold-identity#useauthentication):

* Přidat `app.UseAuthentication();` do `Startup.Configure`
* Přidat `<partial name="_LoginPartial" />` do rozložení souboru.

## <a name="test-new-user-registration"></a>Otestovat nové registrace uživatele

Spusťte aplikaci, vyberte **zaregistrovat** propojit a zaregistrovat uživatele. V tomto okamžiku je pouze ověření na e-mailu [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) atribut. Po odeslání registrace, jste přihlášení do aplikace. Později v tomto kurzu se kód aktualizuje tak, že noví uživatelé nemůže přihlásit, dokud se ověří e-mailu.

[!INCLUDE[](~/includes/view-identity-db.md)]

Poznámka: v tabulce `EmailConfirmed` pole je `False`.

Můžete chtít tento e-mail znovu použít v dalším kroku při ní odešle e-mail s potvrzením. Klikněte pravým tlačítkem na řádek a vyberte **odstranit**. Odstraňuje se e-mailový alias usnadňuje v následujících krocích.

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a>Vyžádání potvrzení e-mailu

Je osvědčeným postupem je potvrďte e-mailu nové registrace uživatele. E-mailu pomáhá potvrzení ověření někdo jiný, nejsou zosobnění (to znamená, že se ještě nezaregistrovali někoho jiného e-mailu). Předpokládejme, že jste měli diskusní fórum, a chtěli byste zabránit "yli@example.com"registroval jako"nolivetto@contoso.com". Bez potvrzení e-mailu "nolivetto@contoso.com" mohli dostávat nežádoucí e-mailu z vaší aplikace. Předpokládejme, že uživatel zaregistrován nechtěně jako "ylo@example.com" a kdyby si všimli chyba "yli". Se nebude moci použít obnovení hesla, protože aplikace nemá správnou e-mailu. Potvrzení e-mailu zajišťuje omezenou ochranu před roboty. Potvrzení e-mailu neposkytuje ochranu z uživateli se zlými úmysly s mnoha e-mailové účty.

Obvykle chcete novým uživatelům zabránit v účtování všechna data na webový server dříve, než potvrzeno e-mailu.

Aktualizace *Areas/Identity/IdentityHostingStartup.cs* tak, aby vyžadovala potvrzeno e-mailu:

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/IdentityHostingStartup.cs?name=snippet1&highlight=10-13)]

`config.SignIn.RequireConfirmedEmail = true;` zabraňuje registrovaných uživatelů přihlásit, dokud není potvrzené e-mailu.

### <a name="configure-email-provider"></a>Konfigurace poskytovatele e-mailu

V tomto kurzu [SendGrid](https://sendgrid.com) se používá k odesílání e-mailu. Potřebujete účet SendGrid a klíč k odesílání e-mailu. Můžete použít jiné poskytovateli e-mailu. ASP.NET Core 2.x zahrnuje `System.Net.Mail`, který umožňuje odeslání e-mailu z vaší aplikace. Doporučujeme že použít SendGrid nebo jiné služby e-mailu k odeslání e-mailu. SMTP je obtížné zabezpečení a zařídit správné nastavení.

Vytvoření třídy k načtení klíče zabezpečeného e-mailu. V tomto příkladu vytvoření *Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurace služby SendGrid tajných klíčů uživatelů

Přidat jedinečný `<UserSecretsId>` hodnota, která se `<PropertyGroup>` prvek souboru projektu:

[!code-xml[](accconfirm/sample/WebPWrecover21/WebPWrecover.csproj?highlight=5)]

Nastavte `SendGridUser` a `SendGridKey` s [manažera tajných nástroj](xref:security/app-secrets). Příklad:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Na Windows, manažera tajných ukládá dvojice klíčů/hodnota v *secrets.json* soubor `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` adresáře.

Obsah *secrets.json* souboru nejsou šifrovány. *Secrets.json* souboru je uveden níže ( `SendGridKey` hodnota byla odebrána.)

 ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```
 
Další informace najdete v tématu [možnosti vzor](xref:fundamentals/configuration/options) a [konfigurace](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Instalace služby SendGrid

Tento kurz ukazuje, jak přidat e-mailová oznámení prostřednictvím [SendGrid](https://sendgrid.com/), ale můžete odesílat e-mailu pomocí protokolu SMTP a další mechanismy.

Nainstalujte `SendGrid` balíček NuGet:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

V konzole Správce balíčků zadejte následující příkaz:

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

V konzole zadejte následující příkaz:

```cli
dotnet add package SendGrid
```

------

Naleznete v tématu [začít pomocí Sendgridu zdarma](https://sendgrid.com/free/) k registraci bezplatného účtu SendGrid.
### <a name="implement-iemailsender"></a>Implementace IEmailSender

Implementovat `IEmailSender`, vytvořit *Services/EmailSender.cs* podobně jako následujícím kódem:

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurace spuštění pro podporu e-mailu

Přidejte následující kód, který `ConfigureServices` metoda ve *Startup.cs* souboru:

* Přidat `EmailSender` jako služba typu singleton.
* Zaregistrujte `AuthMessageSenderOptions` instance konfigurace.

[!code-csharp[](accconfirm/sample/WebPWrecover21/Startup.cs?name=snippet2&highlight=12-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Povolit obnovení hesla a potvrzení účtu

Šablona má kód pro obnovení potvrzení a heslo účtu. Najít `OnPostAsync` metoda *Areas/Identity/Pages/Account/Register.cshtml.cs*.

Nově zaregistrovaný uživatelům zabránit v automaticky přihlášeni tak následující řádek:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Úplná metoda se zobrazí s změněný řádek zvýrazněný:

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Zaregistrovat a potvrďte e-mailu a resetování hesla

Spuštění webové aplikace a testů potvrzení účtu a heslo pro obnovení toku.

* Spusťte aplikaci a zaregistrovat nový uživatel

  ![Zobrazení účtu zaregistrovat webové aplikace](accconfirm/_static/loginaccconfirm1.png)

* Zkontrolujte svého e-mailu na odkaz pro potvrzení účtu. Zobrazit [ladění e-mailu](#debug) Pokud neobdržíte e-mailu.
* Klikněte na odkaz pro potvrzení e-mailu.
* Přihlaste se pomocí e-mailu a hesla.
* Odhlaste se.

### <a name="view-the-manage-page"></a>Zobrazení stránky Správa

Vyberte své uživatelské jméno v prohlížeči: ![okna prohlížeče s uživatelským jménem](accconfirm/_static/un.png)

Potřebujete rozšířit na navigačním panelu zobrazíte uživatelské jméno.

![navbar](accconfirm/_static/x.png)

Zobrazí se stránka Správa s **profilu** vybraná karta. **E-mailu** zobrazí zaškrtávací políčko označující e-mailu byl potvrzen.

### <a name="test-password-reset"></a>Resetování hesla testu

* Pokud jste přihlášeni, vyberte **odhlášení**.
* Vyberte **přihlášení** spojit a vybrat možnost **zapomněli jste heslo?** odkaz.
* Zadejte e-mail, který jste použili k registraci účtu.
* Odešle e-mail s odkazem k resetování hesla. Zkontrolujte e-mailu a klikněte na odkaz pro resetování hesla. Po úspěšném resetování vašeho hesla můžete přihlásit e-mailu a nové heslo.

<a name="debug"></a>

### <a name="debug-email"></a>Ladění e-mailu

Pokud nelze získat pracovní e-mailu:

* Nastavte zarážku v `EmailSender.Execute` ověření `SendGridClient.SendEmailAsync` je volána.
* Vytvoření [konzolovou aplikaci k odesílání e-mailu](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) pomocí podobné kódu `EmailSender.Execute`.
* Zkontrolujte [e-mailové aktivity](https://sendgrid.com/docs/User_Guide/email_activity.html) stránky.
* Zkontrolujte složku s nevyžádanou poštou.
* Zkuste jinou e-mailový alias na jinou e-mailovou zprostředkovatele (Microsoft, Yahoo, Gmail, atd.)
* Pokuste se odeslat na jiné e-mailové účty.

**Z bezpečnostních důvodů** je **není** použití produkční tajných kódů v vývoj a testování. Pokud publikujete aplikaci do Azure, můžete nastavit SendGrid tajné kódy jako nastavení aplikace ve webové aplikaci Azure portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.

## <a name="combine-social-and-local-login-accounts"></a>Sloučit účty sociálních sítí a místní přihlášení

K dokončení této části, je nutné nejprve povolit externí zprostředkovatel ověřování. Zobrazit [Facebook, Google a externí zprostředkovatel ověřování](xref:security/authentication/social/index).

Kliknutím na odkaz na vaši e-mailu můžete kombinovat místní a sociální účty. V tomto pořadí "RickAndMSFT@gmail.com" se nejprve vytvoří jako místní přihlášení; však můžete nejprve vytvořte účet jako přihlášení prostřednictvím sociální sítě a pak přidat místní přihlašovací údaje.

![Webová aplikace: RickAndMSFT@gmail.com uživatel byl ověřen](accconfirm/_static/rick.png)

Klikněte na **spravovat** odkaz. Poznámka: externí 0 (přihlašování přes sociální sítě) spojená s tímto účtem.

![Správa zobrazení](accconfirm/_static/manage.png)

Klikněte na odkaz pro další přihlášení služby a přijímání požadavků aplikace. Na následujícím obrázku je Facebooku zprostředkovatele externího ověřování:

![Spravovat externí přihlášení zobrazení výpisu Facebooku](accconfirm/_static/fb.png)

Byli sloučeni dva účty. Budete moct přihlásit pomocí obou. Můžete chtít uživatelům přidat místní účty v případě nefungující Služba ověřování v jejich přihlášení prostřednictvím sociální sítě nebo spíše se jste ztratili přístup k jejich účtu na sociální síti.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Po lokality má uživatelům povolit potvrzení účtu

Povolení potvrzení účtu na webu s uživateli zamezí všichni stávající uživatelé. Stávající uživatelé jsou uzamčen, protože jejich účty nejsou potvrzeny. Obejít existující uzamčení uživatelů, použijte jednu z následujících postupů:

* Aktualizujte databázi pro označení všichni stávající uživatelé, jako je potvrzen.
* Zkontrolujte stávající uživatele. Třeba dávkové odeslání e-mailů s potvrzovací odkazy.

::: moniker-end
