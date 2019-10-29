---
title: Nastavení externího přihlášení Google v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Google do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/28/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 663029ecab99efd4f63f8deca026957c19c64710
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034310"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Nastavení externího přihlášení Google v ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Vytvoření projektu konzoly rozhraní Google API a ID klienta

* Nainstalujte [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/devconsole-project) a vyberte **Konfigurovat projekt**.
* V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.
* Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování. Třeba `https://localhost:44312/signin-google`.
* Uložte **ID klienta** a **tajný klíč klienta**.
* Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.

## <a name="store-google-clientid-and-clientsecret"></a>Uložení Google ClientID a ClientSecret

Uložte citlivá nastavení, jako je Google `Client ID` a `Client Secret`, pomocí [správce tajných klíčů](xref:security/app-secrets). Pro účely tohoto kurzu pojmenujte tokeny `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret`:

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "<client id>"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Konfigurace ověřování Google

Přidejte službu Google pro `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Přihlásit se přes Google

* Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se přes Google.
* Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.
* Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraní <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API. Dá se použít k vyžádání různých informací o uživateli.

## <a name="change-the-default-callback-uri"></a>Změna výchozího identifikátoru URI zpětného volání

Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětné volání poskytovatele ověřování Google. Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middleware pro ověřování Google prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.
* Pokud identita není konfigurována voláním `services.AddIdentity` v `ConfigureServices`, pokus o ověření výsledků v *souboru ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v tomto kurzu zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* . Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Google. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).
* Jakmile aplikaci publikujete do Azure, resetujte `ClientSecret` v konzole rozhraní Google API.
* Nastavte `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
