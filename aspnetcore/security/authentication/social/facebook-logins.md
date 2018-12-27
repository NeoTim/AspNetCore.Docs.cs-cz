---
title: Externí přihlášení nastavení sítě Facebook v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování pomocí účtu uživatele Facebooku do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/18/2018
uid: security/authentication/facebook-logins
ms.openlocfilehash: 66f895c7c8dcc00d991c0ea57535f2ed56431a77
ms.sourcegitcommit: 3e94d192b2ed9409fe72e3735e158b333354964c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53735775"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Externí přihlášení nastavení sítě Facebook v ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak povolit vašim uživatelům přihlašovat se pomocí svého účtu sítě Facebook použitím ukázkového projektu ASP.NET Core 2.0 vytvořit na [předchozí stránce](xref:security/authentication/social/index). Začneme tím, že vytvoříte podle ID aplikace pro Facebook [oficiální kroky](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Vytvoření aplikace na Facebooku

* Přejděte [Facebook vývojáři aplikace](https://developers.facebook.com/apps/) stránky a přihlaste se. Pokud ještě nemáte účet služby Facebook, použijte **zaregistrovat Facebooku** odkaz na přihlašovací stránku k jejímu vytvoření.

* Klepněte **přidejte novou aplikaci** tlačítko v pravém horním rohu k vytvoření nového ID aplikace.

   ![Facebook pro portál pro vývojáře otevřít v Microsoft Edge](index/_static/FBMyApps.png)

* Vyplňte formulář a klepněte **vytvoření ID aplikace** tlačítko.

  ![Vytvoření nové aplikace ID formuláře](index/_static/FBNewAppId.png)

* Na **vyberte produkt** klikněte na **Set Up** na **přihlášení k Facebooku** karty.

  ![Stránka nastavení produktu](index/_static/FBProductSetup.png)

* **Rychlý Start** průvodce se spustí s **zvolte platformu** jako první stránka. Vynechat Průvodce teď kliknutím **nastavení** odkaz v nabídce na levé straně:

  ![Přeskočit rychlý Start](index/_static/FBSkipQuickStart.png)

* Zobrazí se **nastavení klienta OAuth** stránky:

  ![Stránka nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* Zadejte identifikátor URI vývoje s */signin-facebook* připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://localhost:44320/signin-facebook`). Ověřování přes síť Facebook později v tomto kurzu konfiguruje automaticky zpracovává požadavky na */signin-facebook* trasy, která má implementovat tok OAuth.

> [!NOTE]
> Identifikátor URI */signin-facebook* je nastaven jako výchozí zpětného volání zprostředkovatele ověřování sítě Facebook. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru Facebook ověřování prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) Třída.

* Klikněte na tlačítko **uložit změny**.

* Klikněte na tlačítko **nastavení** > **základní** odkaz v levém navigačním panelu.

  Na této stránce si poznamenejte vaše `App ID` a `App Secret`. Přidá do vaší aplikace ASP.NET Core v následující části:

* Při nasazování webu potřebujete revidovat **přihlášení k Facebooku** stránce instalace a registrace nový veřejný identifikátor URI.

## <a name="store-facebook-app-id-and-app-secret"></a>ID aplikace pro Store Facebook a tajný klíč aplikace

Propojit citlivá nastavení, jako je Facebook `App ID` a `App Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto kurzu se název tokeny `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`.

Spusťte následující příkazy zabezpečeně ukládat `App ID` a `App Secret` pomocí manažera tajných:

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Konfigurace ověřování sítě Facebook

::: moniker range=">= aspnetcore-2.0"

Přidání služby Facebook v `ConfigureServices` metodu *Startup.cs* souboru:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Nainstalujte [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíčku.

* K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
* Instalace s .NET Core CLI, spusťte v adresáři projektu následující:

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

Přidat v middlewaru Facebook `Configure` metoda ve *Startup.cs* souboru:

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

Zobrazit [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování sítě Facebook. Možnosti konfigurace umožňuje:

* Požádat o jiné informace o uživateli.
* Přidáte argumenty řetězce dotazu přizpůsobit přihlašovací prostředí.

## <a name="sign-in-with-facebook"></a>Přihlášení pomocí Facebooku

Spusťte aplikaci a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se přes Facebook.

![Webové aplikace: Uživatel nebyl ověřen](index/_static/DoneFacebook.png)

Po kliknutí na **Facebook**, budete přesměrováni na Facebook pro ověření:

![Stránka pro ověřování sítě Facebook](index/_static/FBLogin.png)

Požadavky na ověření sítě Facebook veřejný profil a e-mailovou adresu ve výchozím nastavení:

![Obrazovka pro vyjádření souhlasu stránky ověřování sítě Facebook](index/_static/FBLoginDone.png)

Po zadání vašich přihlašovacích údajů k Facebooku budete přesměrováni zpět na váš web, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí vašich přihlašovacích údajů k Facebooku:

![Webové aplikace: Uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* **ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* Přidat [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) balíček NuGet do projektu pro pokročilé scénáře ověřování služby Facebook. Tento balíček nevyžaduje integrovat funkce externí přihlášení Facebooku s vaší aplikací. 

* V tomto článku jsme si ukázali, jak ověřování pomocí Facebooku. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, měli byste resetovat `AppSecret` na portálu pro vývojáře služby Facebook.

* Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
