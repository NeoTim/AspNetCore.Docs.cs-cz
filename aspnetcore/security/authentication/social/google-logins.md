---
title: Nastavení Google externí přihlášení v technologii ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Google do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082476"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Nastavení Google externí přihlášení v technologii ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

[Starší verze Google + API se ukončily od 7. března 2019](https://developers.google.com/+/api-shutdown). Google + Signing a vývojáři musí přejít na nový systém přihlašování Google. Balíčky ASP.NET Core 2,1 a 2,2 pro ověřování Google se aktualizují, aby se vešly změny. Další informace a dočasné omezení pro ASP.NET Core najdete v [tomto problému GitHubu](https://github.com/aspnet/AspNetCore/issues/6486). Tento kurz byl aktualizován pomocí nového procesu instalace.

V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 2,2 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Vytvoření projektu konzoly rozhraní Google API a ID klienta

* Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **Konfigurovat projekt**.
* V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.
* Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování. Třeba `https://localhost:5001/signin-google`.
* Uložte **ID klienta** a **tajný klíč klienta**.
* Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Store Google ClientID a ClientSecret

Uložte citlivá nastavení, jako je `Client ID` Google `Client Secret` a pomocí [správce tajných klíčů](xref:security/app-secrets). Pro účely tohoto kurzu pojmenujte tokeny `Authentication:Google:ClientId` a: `Authentication:Google:ClientSecret`

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Konfigurace ověřování Google

Přidat službu Google do `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Přihlásit se přes Google

* Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se přes Google.
* Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.
* Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraníAPI.<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> To umožňuje požádat o jiné informace o uživateli.

## <a name="change-the-default-callback-uri"></a>Změna výchozího identifikátoru URI zpětného volání

Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Google. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování Google přes zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) třídy.

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.
* Pokud není identita nakonfigurovaná `services.AddIdentity` voláním `ConfigureServices`v, zkuste ověřit výsledky v *ArgumentException: Je nutné zadat*možnost SignInScheme. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, můžete získat *databázová operace selhala při zpracování požadavku* chyby. Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.

## <a name="next-steps"></a>Další postup

* V tomto článku jsme si ukázali, jak můžete ověřit s Google. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).
* Jakmile aplikaci publikujete do Azure, resetujte ji `ClientSecret` v konzole rozhraní Google API.
* Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
