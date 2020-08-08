---
title: Nastavení externího přihlášení Google v ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Google do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/google-logins
ms.openlocfilehash: 7a10b66be7f4dda25f94437b55bad9746bda97e0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020480"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>Nastavení externího přihlášení Google v ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak uživatelům povolit, aby se k účtu Google přihlásili pomocí projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-a-google-api-console-project-and-client-id"></a>Vytvoření projektu konzoly rozhraní Google API a ID klienta

* Nainstalujte [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).
* Přejděte k [integraci přihlášení Google do vaší webové aplikace](https://developers.google.com/identity/sign-in/web/sign-in) a vyberte **Konfigurovat projekt**.
* V dialogovém okně **Konfigurace klienta OAuth** vyberte možnost **webový server**.
* Do textového pole **autorizovaných** identifikátorů URI pro přesměrování nastavte identifikátor URI přesměrování. Například `https://localhost:44312/signin-google`.
* Uložte **ID klienta** a **tajný klíč klienta**.
* Při nasazování lokality Zaregistrujte novou veřejnou adresu URL z **konzoly Google**.

## <a name="store-the-google-client-id-and-secret"></a>Uložte si ID a tajný kód klienta Google.

Uložte citlivá nastavení, jako je třeba ID klienta Google a tajné hodnoty pomocí [správce tajných](xref:security/app-secrets)kódů. V této ukázce použijte následující postup:

1. Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení do místního úložiště tajných klíčů pomocí tajných klíčů `Authentication:Google:ClientId` a `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Přihlašovací údaje a využití rozhraní API můžete spravovat v [konzole API](https://console.developers.google.com/apis/dashboard).

## <a name="configure-google-authentication"></a>Konfigurace ověřování Google

Přidat službu Google do `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Přihlásit se přes Google

* Spusťte aplikaci a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se přes Google.
* Klikněte na tlačítko **Google** , které přesměruje na Google pro ověřování.
* Po zadání přihlašovacích údajů Google budete přesměrováni zpátky na web.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Další informace o možnostech konfigurace, které podporuje ověřování Google, najdete v referenčních informacích k rozhraní API. Dá se použít k vyžádání různých informací o uživateli.

## <a name="change-the-default-callback-uri"></a>Změna výchozího identifikátoru URI zpětného volání

Segment identifikátoru URI `/signin-google` je nastaven jako výchozí zpětné volání poskytovatele ověřování Google. Výchozí identifikátor URI zpětného volání můžete změnit při konfiguraci middleware pro ověřování Google prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) .

## <a name="troubleshooting"></a>Řešení potíží

* Pokud přihlášení nefunguje a nezískáváte žádné chyby, přepněte do vývojového režimu, aby se tento problém zjednodušil při ladění.
* Pokud Identity není nakonfigurováno voláním `services.AddIdentity` v `ConfigureServices` , probíhá pokus o ověření výsledků v *souboru ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v tomto kurzu zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při *zpracování chyby žádosti se zobrazí operace databáze* . Vyberte možnost **použít migrace** k vytvoření databáze a aktualizujte stránku, aby bylo možné pokračovat v minulosti.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Google. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).
* Jakmile aplikaci publikujete do Azure, resetujte ji `ClientSecret` v konzole rozhraní Google API.
* Nastavte `Authentication:Google:ClientId` nastavení a `Authentication:Google:ClientSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
