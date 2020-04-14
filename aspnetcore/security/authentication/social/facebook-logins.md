---
title: Nastavení externího přihlášení na Facebooku v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277298"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Nastavení externího přihlášení na Facebooku v ASP.NET Core

[Valerij Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Tento kurz s příklady kódu ukazuje, jak povolit uživatelům přihlásit se pomocí svého účtu Facebook pomocí ukázkové ASP.NET projektu Core 3.0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index). Začneme vytvořením ID aplikace Facebook podle [oficiálních kroků](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Vytvoření aplikace na Facebooku

* Přidejte do projektu balíček [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet.

* Přejděte na stránku [aplikace Facebook Developers](https://developers.facebook.com/apps/) a přihlaste se. Pokud ještě nemáte facebookový účet, vytvořte ho pomocí odkazu **Zaregistrovat se na Facebooku** na přihlašovací stránce.  Jakmile budete mít účet na Facebooku, postupujte podle pokynů a zaregistrujte se jako vývojář facebooku.

* V nabídce **Moje aplikace** vyberte **Vytvořit aplikaci** a vytvořte nové ID aplikace.

   ![Facebook pro vývojáře portál otevřený v Microsoft Edge](index/_static/FBMyApps.png)

* Vyplňte formulář a klepněte na tlačítko **Vytvořit ID aplikace.**

  ![Vytvoření nového formuláře ID aplikace](index/_static/FBNewAppId.png)

* Na nové kartě aplikace vyberte **Přidat produkt**.  Na **přihlašovací** kartě Facebook klikněte na **Nastavit.** 

  ![Stránka Nastavení produktu](index/_static/FBProductSetup.png)

* Průvodce **rychlým startem** se spustí s **výběrem platformy** jako první stránky. Obejít průvodce pro tuto chvíli kliknutím na **facebook Přihlašovací** **nastavení** odkaz v nabídce v levém dolním rohu:

  ![Přeskočit rychlý start](index/_static/FBSkipQuickStart.png)

* Zobrazí se stránka **Nastavení oauth klienta:**

  ![Stránka Nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* Zadejte identifikátor URI vývoje s *parametrem /signin-facebook* připojeným do pole Platné `https://localhost:44320/signin-facebook` **identifikátory URI přesměrování OAuth** (například: ). Ověřování na Facebooku nakonfigurované dále v tomto kurzu bude automaticky zpracovávat požadavky na trase */signin-facebook* k implementaci toku OAuth.

> [!NOTE]
> Identifikátor URI */signin-facebook* je nastaven jako výchozí zpětné volání zprostředkovatele ověřování na Facebooku. Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middlewaru ověřování na Facebooku prostřednictvím zděděné [vlastnosti RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* Klikněte na **Save Changes** (Uložit změny).

* V levé navigaci klikněte na odkaz **Nastavení** > **základní.**

  Na této stránce si poznamenejte svůj `App ID` a svůj `App Secret`. Obě aplikace ASP.NET core přidáte do aplikace v další části:

* Při nasazování webu musíte znovu navštívit stránku **nastavení přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Uložení ID aplikace Facebook a tajného klíče

Ukládat citlivá nastavení, jako je ID aplikace Facebook a tajné hodnoty pomocí [Správce tajných barev](xref:security/app-secrets). Pro tuto ukázku použijte následující kroky:

1. Inicializovat projekt pro tajné úložiště podle pokynů na [Povolit tajné úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení v místním tajném úložišti s tajnými klíči `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurace ověřování na Facebooku

Přidejte službu `ConfigureServices` Facebook do metody v *souboru Startup.cs:*

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Přihlášení přes Facebook

* Spusťte aplikaci a **vyberte Přihlásit se**. 
* V části **Použít jinou službu pro přihlášení vyberte**Facebook.
* Budete přesměrováni na **Facebook** k ověření.
* Zadejte své přihlašovací údaje na Facebooku.
* Budete přesměrováni zpět na svůj web, kde můžete nastavit svůj e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů na Facebooku:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reagovat na zrušení autorizace externího přihlášení

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>může poskytnout cestu přesměrování k uživatelskému agentovi, pokud uživatel neschválí požadovanou žádost o autorizaci.

Následující kód `AccessDeniedPath` nastaví `"/AccessDeniedPathInfo"`na :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Doporučujeme, `AccessDeniedPath` aby stránka obsahovala následující informace:

*  Vzdálené ověřování bylo zrušeno.
* Tato aplikace vyžaduje ověření.
* Chcete-li se přihlásit znovu, vyberte odkaz Přihlásit se.

### <a name="test-accessdeniedpath"></a>Testovat accessdeniedpath

* Přejděte na [facebook.com](https://www.facebook.com/)
* Pokud jste přihlášeni, musíte se odhlásit.
* Spusťte aplikaci a vyberte Přihlášení na Facebooku.
* Vyberte **možnost Ne nyní**. Budete přesměrováni na `AccessDeniedPath` zadanou stránku.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech konfigurace podporovaných ověřováním na Facebooku najdete v referenční příručce rozhraní API [FacebookOptions.](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Možnosti konfigurace lze použít k:

* Vyžádejte si různé informace o uživateli.
* Přidejte argumenty řetězce dotazu pro přizpůsobení přihlašovacího prostředí.

## <a name="troubleshooting"></a>Řešení potíží

* **pouze ASP.NET Core 2.x:** Pokud identita není nakonfigurován `services.AddIdentity` `ConfigureServices`voláním , pokus o ověření bude mít za následek *ArgumentException: "SignInScheme" možnost musí být k dispozici*. Šablona projektu použitá v tomto kurzu zajišťuje, že se tak děje.
* Pokud databáze lokality nebyla vytvořena použitím počáteční migrace, zobrazí se *operace databáze, která se nezdařila při zpracování chyby požadavku.* Klepnutím na **Použít migrace vytvořte** databázi a aktualizujte, abyste pokračovali v minulosti za chybou.

## <a name="next-steps"></a>Další kroky

* Tento článek ukázal, jak se můžete ověřit pomocí Facebooku. Můžete postupovat podobným způsobem k ověření s jinými zprostředkovateli uvedenými na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste `AppSecret` měli obnovit web na facebookovém vývojářském portálu.

* Nastavte `Authentication:Facebook:AppId` nastavení `Authentication:Facebook:AppSecret` aplikace a jako na webu Azure Portal. Konfigurační systém je nastaven pro čtení klíčů z proměnných prostředí.
