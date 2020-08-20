---
title: Nastavení externího přihlášení ke službě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů z účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: ce0e7ad30c137562b74dc9fe5c53235e3599e575
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634355"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Nastavení externího přihlášení ke službě Facebook v ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Tento kurz s příklady kódu ukazuje, jak se uživatelům umožní přihlásit se pomocí účtu na Facebooku pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index). Začneme vytvořením ID aplikace Facebook pomocí následujících [oficiálních kroků](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Vytvoření aplikace na Facebooku

* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .

* Přejděte na stránku [aplikace pro vývojáře na Facebooku](https://developers.facebook.com/apps/) a přihlaste se. Pokud ještě nemáte účet Facebook, vytvořte ho pomocí odkazu **zaregistrovat pro Facebook** na přihlašovací stránce.  Jakmile budete mít účet Facebook, zaregistrujte se jako vývojář Facebooku podle pokynů.

* V nabídce **Moje aplikace** vyberte **vytvořit aplikaci** a vytvořte nové ID aplikace.

   ![Portál Facebooku pro vývojáře otevřený v Microsoft Edge](index/_static/FBMyApps.png)

* Vyplňte formulář a klepněte na tlačítko **vytvořit ID aplikace** .

  ![Vytvoření nového formuláře ID aplikace](index/_static/FBNewAppId.png)

* Na nové kartě aplikace vyberte **Přidat produkt**.  Na **přihlašovací kartě Facebooku** klikněte na **nastavit** . 

  ![Stránka instalace produktu](index/_static/FBProductSetup.png)

* Průvodce **rychlým startem** se spustí s **volbou platformy** jako první stránky. Vynechejte Průvodce hned kliknutím na odkaz nastavení **přihlášení na Facebooku** **Settings** v nabídce na spodní levé straně:

  ![Přeskočit rychlé zprovoznění](index/_static/FBSkipQuickStart.png)

* Zobrazí se stránka **Nastavení OAuth pro klienta** :

  ![Stránka nastavení OAuth pro klienta](index/_static/FBOAuthSetup.png)

* Zadejte identifikátor URI pro vývoj s */SignIn-Facebook* připojením k platným poli identifikátorů **URI přesměrování OAuth** (například: `https://localhost:44320/signin-facebook` ). Ověřování na Facebooku nakonfigurované později v tomto kurzu automaticky zpracuje žádosti na trase */SignIn-Facebook* k implementaci toku OAuth.

> [!NOTE]
> Identifikátor URI */SignIn-Facebook* je nastaven jako výchozí zpětné volání poskytovatele ověřování Facebooku. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování na Facebooku prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Klikněte na **Save Changes** (Uložit změny).

* V levém navigačním panelu klikněte na **Nastavení**  >  **základní** odkaz.

  Na této stránce si poznamenejte vaše `App ID` a `App Secret` . Do své aplikace ASP.NET Core přidáte v následující části:

* Když nasadíte lokalitu, musíte znovu navštívit stránku nastavení **přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Uložení ID a tajného klíče aplikace na Facebooku

Uložte citlivá nastavení, jako je například ID aplikace Facebook a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů. V této ukázce použijte následující postup:

1. Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurace ověřování na Facebooku

Přidejte službu Facebook do `ConfigureServices` metody v souboru *Startup.cs* :

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Přihlásit se přes Facebook

* Spusťte aplikaci a vyberte **Přihlásit se**. 
* V části **použít jinou službu pro přihlášení**vyberte Facebook.
* Budete přesměrováni na **Facebook** pro ověřování.
* Zadejte svoje přihlašovací údaje k Facebooku.
* Budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Facebooku:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Reakce na zrušení autorizace externího přihlášení

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> může zadat cestu přesměrování pro uživatelského agenta, když uživatel neschválí požadovanou žádost o autorizaci.

Následující kód nastaví na `AccessDeniedPath` `"/AccessDeniedPathInfo"` :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Doporučujeme, aby `AccessDeniedPath` stránka obsahovala tyto informace:

*  Vzdálené ověřování se zrušilo.
* Tato aplikace vyžaduje ověření.
* Pokud se chcete zkusit znovu přihlásit, vyberte odkaz pro přihlášení.

### <a name="test-accessdeniedpath"></a>AccessDeniedPath testu

* Přejít na [Facebook.com](https://www.facebook.com/)
* Pokud jste přihlášeni, musíte se odhlásit.
* Spusťte aplikaci a vyberte možnost přihlašování ke službě Facebook.
* Vyberte **ne nyní**. Budete přesměrováni na určenou `AccessDeniedPath` stránku.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním na Facebooku najdete v referenčních informacích k rozhraní [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API. Možnosti konfigurace lze použít k těmto akcím:

* Vyžádejte si jiné informace o uživateli.
* Přidejte argumenty řetězce dotazu pro přizpůsobení možností přihlášení.

## <a name="troubleshooting"></a>Řešení potíží

* **Pouze ASP.NET Core 2. x:** Pokud Identity není nakonfigurováno voláním `services.AddIdentity` v `ConfigureServices` , pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v tomto kurzu zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* . Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Facebooku. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat na `AppSecret` portálu pro vývojáře na Facebooku.

* Nastavte `Authentication:Facebook:AppId` nastavení a `Authentication:Facebook:AppSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
