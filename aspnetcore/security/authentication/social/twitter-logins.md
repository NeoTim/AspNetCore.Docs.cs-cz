---
title: Nastavení externí přihlášení pomocí ASP.NET Core na twitteru
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Twitter do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/twitter-logins
ms.openlocfilehash: 49db8b921fde169380ca284f46e535786b2b8a30
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735801"
---
# <a name="twitter-external-login-setup-with-aspnet-core"></a>Nastavení externí přihlášení pomocí ASP.NET Core na twitteru

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak povolit uživatelům [Přihlaste se pomocí svého účtu Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) použitím ukázkového projektu ASP.NET Core 2.0 na vytvoří [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvořit aplikaci na Twitteru

* Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se. Pokud ještě nemáte účet na Twitteru, použijte **[zaregistrujte se hned teď](https://twitter.com/signup)** odkaz pro vytvoření. Po přihlášení **správy aplikací** stránka je zobrazena:

  ![Správa aplikací Twitter otevřít v Microsoft Edge](index/_static/TwitterAppManage.png)

* Klepněte na **vytvořit novou aplikaci** a vyplnit **název**, **popis** a veřejné **webu** identifikátoru URI (může to být dočasný dokud Tento název domény zaregistrujte):

  ![Vytvoření stránky aplikace](index/_static/TwitterCreate.png)

* Zadejte identifikátor URI vývoje s `/signin-twitter` připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://localhost:44320/signin-twitter`). Schéma ověřování Twitteru později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-twitter` trasy, která má implementovat tok OAuth.

  > [!NOTE]
  > Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Twitteru. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování služby Twitter prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) Třída.

* Vyplňte zbývající části formuláře a klepněte na **vytvoření aplikace Twitter**. Podrobnosti o nové aplikace se zobrazí:

  ![Podrobnosti karty na stránce aplikace](index/_static/TwitterAppDetails.png)

* Při nasazování webu budete potřebovat revidovat **správy aplikací** stránky a zaregistrujte nový veřejný identifikátor URI.

## <a name="storing-twitter-consumerkey-and-consumersecret"></a>Ukládání Twitter ConsumerKey a ConsumerSecret

Propojit citlivá nastavení, jako je Twitter `Consumer Key` a `Consumer Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto kurzu se název tokeny `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret`.

Tyto tokeny můžete najít na **klíče a přístupové tokeny** kartu po vytvoření nové aplikace Twitter:

![Karta klíče a přístupové tokeny](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování Twitteru

Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) balíček je už nainstalovaný.

* K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
* Instalace s .NET Core CLI, spusťte v adresáři projektu následující:

  `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

::: moniker range=">= aspnetcore-2.0"

Přidat službu Twitter `ConfigureServices` metoda ve *Startup.cs* souboru:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidat v middlewaru Twitter `Configure` metoda ve *Startup.cs* souboru:

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

::: moniker-end

Zobrazit [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Twitteru. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlaste se pomocí Twitteru

Spusťte aplikaci a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se přes Twitter:

![Webové aplikace: Uživatel nebyl ověřen](index/_static/DoneTwitter.png)

Kliknutím na **Twitteru** přesměruje na Twitter pro ověřování:

![Stránka ověřování twitteru](index/_static/TwitterLogin.png)

Po zadání vaše přihlašovací údaje k Twitteru, budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů Twitter:

![Webové aplikace: Uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* **ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* V tomto článku jsme si ukázali, jak ověřování pomocí Twitteru. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, měli byste resetovat `ConsumerSecret` na portálu pro vývojáře Twitter.

* Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
