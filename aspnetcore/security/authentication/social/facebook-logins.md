---
title: Externí přihlášení nastavení sítě Facebook v ASP.NET Core
author: rick-anderson
description: Kurz s příklady kódu, které demonstrují integraci ověřování uživatelů z účtu Facebook do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989782"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>Externí přihlášení nastavení sítě Facebook v ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento kurz s příklady kódu ukazuje, jak se uživatelům umožní přihlásit se pomocí účtu na Facebooku pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index). Začneme vytvořením ID aplikace Facebook pomocí následujících [oficiálních kroků](https://developers.facebook.com).

## <a name="create-the-app-in-facebook"></a>Vytvoření aplikace na Facebooku

* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) .

* Přejděte na stránku [aplikace pro vývojáře na Facebooku](https://developers.facebook.com/apps/) a přihlaste se. Pokud ještě nemáte účet Facebook, vytvořte ho pomocí odkazu **zaregistrovat pro Facebook** na přihlašovací stránce.  Jakmile budete mít účet Facebook, zaregistrujte se jako vývojář Facebooku podle pokynů.

* V nabídce **Moje aplikace** vyberte **vytvořit aplikaci** a vytvořte nové ID aplikace.

   ![Facebook pro portál pro vývojáře otevřít v Microsoft Edge](index/_static/FBMyApps.png)

* Vyplňte formulář a klepněte na tlačítko **vytvořit ID aplikace** .

  ![Vytvoření nové aplikace ID formuláře](index/_static/FBNewAppId.png)

* Na nové kartě aplikace vyberte **Přidat produkt**.  Na **přihlašovací kartě Facebooku** klikněte na **nastavit** . 

  ![Stránka nastavení produktu](index/_static/FBProductSetup.png)

* Průvodce **rychlým startem** se spustí s **volbou platformy** jako první stránky. Vynechejte Průvodce hned kliknutím na odkaz nastavení **přihlášení na Facebooku** v nabídce na spodní levé straně:

  ![Přeskočit rychlý Start](index/_static/FBSkipQuickStart.png)

* Zobrazí se stránka **Nastavení OAuth pro klienta** :

  ![Stránka nastavení OAuth klienta](index/_static/FBOAuthSetup.png)

* Zadejte identifikátor URI pro vývoj s */SignIn-Facebook* připojením k platným poli identifikátorů **URI přesměrování OAuth** (například: `https://localhost:44320/signin-facebook`). Ověřování na Facebooku nakonfigurované později v tomto kurzu automaticky zpracuje žádosti na trase */SignIn-Facebook* k implementaci toku OAuth.

> [!NOTE]
> Identifikátor URI */SignIn-Facebook* je nastaven jako výchozí zpětné volání poskytovatele ověřování Facebooku. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování na Facebooku prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) .

* Klikněte na **Uložit změny**.

* V levém navigačním panelu klikněte na **nastavení** > **základní** odkaz.

  Na této stránce si poznamenejte `App ID` a `App Secret`. Přidá do vaší aplikace ASP.NET Core v následující části:

* Když nasadíte lokalitu, musíte znovu navštívit stránku nastavení **přihlášení na Facebooku** a zaregistrovat nový veřejný identifikátor URI.

## <a name="store-the-facebook-app-id-and-secret"></a>Uložení ID a tajného klíče aplikace na Facebooku

Uložte citlivá nastavení, jako je například ID aplikace Facebook a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů. V této ukázce použijte následující postup:

1. Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret`:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Konfigurace ověřování sítě Facebook

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

Další informace o možnostech Konfigurace podporovaných ověřováním na Facebooku najdete v referenčních informacích k rozhraní [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API. Možnosti konfigurace umožňuje:

* Požádat o jiné informace o uživateli.
* Přidáte argumenty řetězce dotazu přizpůsobit přihlašovací prostředí.

## <a name="sign-in-with-facebook"></a>Přihlášení pomocí Facebooku

Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se přes Facebook.

![Webová aplikace: uživatel nebyl ověřen](index/_static/DoneFacebook.png)

Když kliknete na **Facebook**, budete přesměrováni na Facebook pro ověřování:

![Stránka pro ověřování sítě Facebook](index/_static/FBLogin.png)

Požadavky na ověření sítě Facebook veřejný profil a e-mailovou adresu ve výchozím nastavení:

![Obrazovka pro vyjádření souhlasu stránky ověřování sítě Facebook](index/_static/FBLoginDone.png)

Po zadání vašich přihlašovacích údajů k Facebooku budete přesměrováni zpět na váš web, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí vašich přihlašovacích údajů k Facebooku:

![Webová aplikace: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Řešení potíží

* **Pouze ASP.NET Core 2. x:** Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* . Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.

## <a name="next-steps"></a>Další kroky

* V tomto článku jsme si ukázali, jak ověřování pomocí Facebooku. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat `AppSecret` na portálu pro vývojáře na Facebooku.

* Nastavte `Authentication:Facebook:AppId` a `Authentication:Facebook:AppSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
