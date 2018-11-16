---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/google-logins
ms.openlocfilehash: dfda83e1d7cf3c5ff8e31de20c15d468de5d15c0
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708449"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Nastavení Google externí přihlášení v technologii ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak mohou uživatelé přihlásit s účtem Google + použitím ukázkového projektu ASP.NET Core 2.0 vytvořené na [předchozí stránce](xref:security/authentication/social/index). Začneme podle [oficiální kroky](https://developers.google.com/identity/sign-in/web/devconsole-project) k vytvoření nové aplikace konzoly rozhraní API Google.

## <a name="create-the-app-in-google-api-console"></a>Vytvoření aplikace v konzole rozhraní API Google

* Přejděte do [ https://console.developers.google.com/projectselector/apis/library ](https://console.developers.google.com/projectselector/apis/library) a přihlaste se. Pokud ještě nemáte účet Google, použijte **další možnosti** > **[vytvořit účet](https://accounts.google.com/SignUpWithoutGmail?service=cloudconsole&continue=https%3A%2F%2Fconsole.developers.google.com%2Fprojectselector%2Fapis%2Flibrary&ltmpl=api)**  odkaz pro vytvoření:

![Konzola rozhraní API Google](index/_static/GoogleConsoleLogin.png)

* Budete přesměrováni na **správce rozhraní API knihovny** stránky:

![Stránka rozhraní API Správce knihovny](index/_static/GoogleConsoleSwitchboard.png)

* Klepněte na **vytvořit** a zadejte vaše **název projektu**:

![Dialogové okno nového projektu](index/_static/GoogleConsoleNewProj.png)

* Po přijetí dialogového okna, budete přesměrováni zpět na stránku knihovny umožňuje zvolit funkce pro novou aplikaci. Najít **rozhraní API Google +** v seznamu a klikněte na odkaz Přidat funkci rozhraní API:

![Stránka rozhraní API Správce knihovny](index/_static/GoogleConsoleChooseApi.png)

* Zobrazí se stránka pro nově přidané rozhraní API. Klepněte na **povolit** přidání funkce do vaší aplikace Google + přihlásit ve funkci:

![Stránka Správce rozhraní API Google + API](index/_static/GoogleConsoleEnableApi.png)

* Po povolení rozhraní API, klepněte na **Vytvořte přihlašovací údaje** konfigurace tajné klíče:

![Stránka Správce rozhraní API Google + API](index/_static/GoogleConsoleGoCredentials.png)

* Zvolte:
  * **Google + API**
  * **Webový server (například node.js, Tomcat)**, a
  * **Uživatelská data**:

![Stránka přihlašovací údaje správce rozhraní API: Podívejte se, jaké druhy přihlašovací údaje můžete potřebovat panel](index/_static/GoogleConsoleChooseCred.png)

* Klepněte na **jaké přihlašovací údaje potřebuji?** který přejde na druhý krok konfigurace aplikace **vytvoření ID klienta OAuth 2.0**:

![Stránka přihlašovací údaje správce rozhraní API: vytvoření ID klienta OAuth 2.0](index/_static/GoogleConsoleCreateClient.png)

* Protože se právě jednu funkci (přihlášení), abychom mohli zadat stejné vytváříme projektu Google + **název** pro ID klienta OAuth 2.0, jako jste použili pro projekt.

* Zadejte identifikátor URI vývoje s `/signin-google` připojí do **identifikátory URI pro přesměrování autorizovaní** pole (například: `https://localhost:44320/signin-google`). Ověřování Google později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-google` trasy, která má implementovat tok OAuth.

> [!NOTE]
> Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.

* Stiskněte klávesu TAB pro přidání **identifikátory URI pro přesměrování autorizovaní** položka.

* Klepněte na **vytvořit ID klienta**, což vás přesměruje na třetí krok **nastavení OAuth 2.0 obrazovkami pro vyjádření souhlasu**:

![Stránka přihlašovací údaje správce rozhraní API: nastavení obrazovce pro vyjádření souhlasu OAuth 2.0](index/_static/GoogleConsoleAddCred.png)

* Zadejte vaše veřejné **e-mailová adresa** a **název produktu** uvedené pro vaši aplikaci při Google + se zobrazí výzva k přihlášení. Další možnosti jsou k dispozici v rámci **další možnosti vlastního nastavení**.

* Klepněte na **pokračovat** k přejděte k poslednímu kroku **stáhnout přihlašovací údaje**:

![Stránka přihlašovací údaje správce rozhraní API: Stáhněte si přihlašovací údaje](index/_static/GoogleConsoleFinish.png)

* Klepněte na **Stáhnout** uložit soubor JSON s tajných klíčů aplikací a **provádí** nezbytných k dokončení vytvoření nové aplikace.

* Při nasazování webu budete potřebovat revidovat **konzoly Google** a zaregistrujte novou veřejnou adresu url.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID a ClientSecret

Propojit citlivá nastavení, jako je Google `Client ID` a `Client Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto kurzu se název tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`.

Hodnoty pro tyto tokeny najdete v souboru JSON si stáhli v předchozím kroku v části `web.client_id` a `web.client_secret`.

## <a name="configure-google-authentication"></a>Konfigurace ověřování Google

::: moniker range=">= aspnetcore-2.0"

Přidat služby Google v `ConfigureServices` metoda *Startup.cs* souboru:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddGoogle(googleOptions =>
{
    googleOptions.ClientId = Configuration["Authentication:Google:ClientId"];
    googleOptions.ClientSecret = Configuration["Authentication:Google:ClientSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google) instalaci balíčku.

* K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
* Instalace s .NET Core CLI, spusťte v adresáři projektu následující:

`dotnet add package Microsoft.AspNetCore.Authentication.Google`

Přidat v middlewaru Google `Configure` metoda *Startup.cs* souboru:

```csharp
app.UseGoogleAuthentication(new GoogleOptions()
{
    ClientId = Configuration["Authentication:Google:ClientId"],
    ClientSecret = Configuration["Authentication:Google:ClientSecret"]
});
```

::: moniker-end

Zobrazit [GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Google. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-google"></a>Přihlásit se přes Google

Spusťte aplikaci a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se přes Google:

![Webové aplikaci běžící v Microsoft Edge: uživatel nebyl ověřen](index/_static/DoneGoogle.png)

Když kliknete na Googlu, budete přesměrováni do Googlu pro ověřování:

![Dialog ověřování Google](index/_static/GoogleLogin.png)

Jakmile zadáte svoje přihlašovací údaje Google, pak budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů Google:

![Webové aplikaci běžící v Microsoft Edge: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud se zobrazí `403 (Forbidden)` chybovou stránku z vaší vlastní aplikace při spuštění v režimu pro vývoj (nebo přerušení do ladicího programu ke stejné chybě), ujistěte se, že **rozhraní API Google +** byla povolena v **správce rozhraní API knihovny** pomocí následujících kroků uvedených v tomto [starších na této stránce](#create-the-app-in-google-api-console). Pokud nebude fungovat přihlašování a nezobrazuje se nějaké chyby, přepněte do režimu vývoj usnadnit problém ladit.
* **ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* V tomto článku jsme si ukázali, jak můžete ověřit s Google. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, měli byste resetovat `ClientSecret` v konzole rozhraní API Google.

* Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
