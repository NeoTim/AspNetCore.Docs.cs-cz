---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 5b6bfaafba68eaf15a60b7c512a9e7406e3112ee
ms.sourcegitcommit: 6d8c362a3cb4c634e64cbb3d28069c36bc973581
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418663"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Nastavení Google externí přihlášení v technologii ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V lednu 2019 Google začali [vypnout](https://developers.google.com/+/api-shutdown) Google +. Přihlaste se a vývojáři musí přesunout na znaménko Google nová v systému podle dne. ASP.NET Core 2.1 a 2,2 balíčky pro ověřování Google se aktualizují v únoru s ohledem změny. Další informace a dočasné opatření pro ASP.NET Core najdete v tématu [tento problém Githubu](https://github.com/aspnet/AspNetCore/issues/6486). V tomto kurzu má aktualizované a přinášejí nové procesu instalace.

V tomto kurzu se dozvíte, jak povolit uživatelům přihlásit se přes jejich účet Google pomocí ASP.NET Core 2.2 projektu vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Vytvořit ID projektu a klientské konzole rozhraní API Google

* Přejděte do [integraci Google přihlásit do své webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **konfigurace projektu A**.
* V **konfigurace klienta OAuth** dialogového okna, vyberte **webový server**.
* V **identifikátory URI pro přesměrování autorizovaní** pole pro zadání textu, nastavte identifikátor URI pro přesměrování. Třeba `https://localhost:5001/signin-google`.
* Uložit **ID klienta** a **tajný kód klienta**.
* Při nasazování webu, registrovat novou veřejnou adresu url z **konzoly Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID a ClientSecret

Citlivá nastavení, například ke službě Google Store `Client ID` a `Client Secret` s [manažera tajných](xref:security/app-secrets). Pro účely tohoto kurzu se název tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

Můžete spravovat vaše přihlašovací údaje rozhraní API a jeho použití v [rozhraní API konzoly](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Konfigurace ověřování Google

Přidat služby Google `Startup.ConfigureServices`.

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Přihlásit se přes Google

* Spusťte aplikaci a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se přes Google.
* Klikněte na tlačítko **Google** tlačítka, který přesměruje do Googlu pro ověřování.
* Jakmile zadáte svoje přihlašovací údaje Google, budete přesměrováni zpět na web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobrazit [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Google. To umožňuje požádat o jiné informace o uživateli.

## <a name="change-the-default-callback-uri"></a>Změnit výchozí identifikátor URI zpětného volání

Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud se nezobrazují žádné chyby přihlášení nebude fungovat, přepněte do režimu vývoj snazší ladit problém.
* Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, při pokusu o ověření za následek *ArgumentException: Musí být Zadaná možnost "SignInScheme"*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* V tomto článku jsme si ukázali, jak můžete ověřit s Google. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).
* Po publikování aplikace do Azure, resetovat `ClientSecret` v konzole rozhraní API Google.
* Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
