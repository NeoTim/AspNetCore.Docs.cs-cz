---
title: Nastavení externího přihlášení ke službě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů z účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717091"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Nastavení externího přihlášení ke službě Facebook v ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

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

* Průvodce **rychlým startem** se spustí s **volbou platformy** jako první stránky. Vynechejte Průvodce hned kliknutím na odkaz nastavení **přihlášení na Facebooku** v nabídce na spodní levé straně:

  ![Přeskočit rychlé zprovoznění](index/_static/FBSkipQuickStart.png)

* Zobrazí se stránka **Nastavení OAuth pro klienta** :

  ![Stránka nastavení OAuth pro klienta](index/_static/FBOAuthSetup.png)

* Zadejte identifikátor URI pro vývoj s */SignIn-Facebook* připojením k platným poli identifikátorů **URI přesměrování OAuth** (například: `https://localhost:44320/signin-facebook`). Ověřování na Facebooku nakonfigurované později v tomto kurzu automaticky zpracuje žádosti na trase */SignIn-Facebook* k implementaci toku OAuth.

> [!NOTE]
> Identifikátor URI */SignIn-Facebook* je nastaven jako výchozí zpětné volání poskytovatele ověřování Facebooku. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování na Facebooku prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Klikněte na **Uložit změny**.

* V levém navigačním panelu klikněte na **nastavení** > **základní** odkaz.

  Na této stránce si poznamenejte `App ID` a `App Secret`. Do své aplikace ASP.NET Core přidáte v následující části:

* Když nasadíte lokalitu, musíte znovu navštívit stránku nastavení **přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.

## <a name="store-facebook-app-id-and-app-secret"></a>Uložení ID aplikace Facebook a tajného klíče aplikace

Pomocí [správce tajného](xref:security/app-secrets)kódu můžete propojit citlivá nastavení, jako je Facebook `App ID`, a `App Secret` na konfiguraci vaší aplikace. Pro účely tohoto kurzu pojmenujte tokeny `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Spusťte následující příkazy, abyste bezpečně ukládali `App ID` a `App Secret` pomocí Správce tajných klíčů:

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>Konfigurace ověřování na Facebooku

Do souboru *Startup.cs* přidejte do metody `ConfigureServices` službu Facebook:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním na Facebooku najdete v referenčních informacích k rozhraní [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API. Možnosti konfigurace lze použít k těmto akcím:

* Vyžádejte si jiné informace o uživateli.
* Přidejte argumenty řetězce dotazu pro přizpůsobení možností přihlášení.

## <a name="sign-in-with-facebook"></a>Přihlásit se přes Facebook

Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se přes Facebook.

![Webová aplikace: uživatel není ověřen.](index/_static/DoneFacebook.png)

Když kliknete na **Facebook**, budete přesměrováni na Facebook pro ověřování:

![Stránka pro ověřování na Facebooku](index/_static/FBLogin.png)

Ověřování na Facebooku požaduje ve výchozím nastavení veřejný profil a e-mailovou adresu:

![Obrazovka pro vyjádření souhlasu stránky pro ověřování na Facebooku](index/_static/FBLoginDone.png)

Jakmile zadáte svoje přihlašovací údaje pro Facebook, budete přesměrováni zpátky na svou lokalitu, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Facebooku:

![Webová aplikace: ověřené uživatelem](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Odstraňování problémů

* **Pouze ASP.NET Core 2. x:** Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v tomto kurzu zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* . Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Facebooku. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat `AppSecret` na portálu pro vývojáře na Facebooku.

* Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
