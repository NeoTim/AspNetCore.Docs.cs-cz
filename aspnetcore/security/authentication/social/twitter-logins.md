---
title: Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitteru do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5d0695160d90d0c5d31b8e35bc6c4cc984829333
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944210"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se dozvíte, jak uživatelům povolit, aby se k [účtu Twitter přihlásili](https://dev.twitter.com/web/sign-in/desktop-browser) pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvoření aplikace na Twitteru

* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) .

* Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se. Pokud ještě nemáte účet na Twitteru, vytvořte si ho pomocí odkazu **[zaregistrovat nyní](https://twitter.com/signup)** .

* Vyberte **Vytvořit aplikaci**. Vyplňte **název aplikace**, **Popis aplikace** a identifikátor URI veřejného **webu** (může to být dočasné, dokud nezaregistrujete název domény):

* Zadejte identifikátor URI pro vývoj s `/signin-twitter` připojený do pole **adresy URL zpětného volání** (například: `https://webapp128.azurewebsites.net/signin-twitter`). Schéma ověřování Twitteru, které je nakonfigurované později v této ukázce, automaticky zpracuje požadavky na trasách `/signin-twitter` k implementaci toku OAuth.

  > [!NOTE]
  > Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětné volání poskytovatele ověřování Twitteru. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování Twitteru prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Vyplňte zbytek formuláře a vyberte **vytvořit**. Zobrazí se nové podrobnosti o aplikaci:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Ukládá se klíč rozhraní API pro příjemce Twitteru a tajný kód.

Spuštěním následujících příkazů bezpečně uložte `ClientId` a `ClientSecret` pomocí [správce tajných klíčů](xref:security/app-secrets):

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

Pomocí [správce tajného](xref:security/app-secrets)kódu můžete propojit citlivá nastavení, jako je Twitter `Consumer Key`, a `Consumer Secret` na konfiguraci vaší aplikace. Pro účely této ukázky pojmenujte tokeny `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret`.

Tyto tokeny najdete na kartě **klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování na Twitteru

Do souboru *Startup.cs* přidejte do metody `ConfigureServices` službu Twitter:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním pomocí Twitteru najdete v referenčních informacích k rozhraní [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlášení pomocí Twitteru

Spusťte aplikaci a vyberte **Přihlásit se**. Zobrazí se možnost přihlásit se pomocí Twitteru:

Kliknutí na **Twitter** přesměruje na Twitter pro ověřování:

Po zadání přihlašovacích údajů pro Twitter budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Twitteru:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Odstraňování problémů

* **ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"* . Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Twitteru. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat `ConsumerSecret` na portálu pro vývojáře na Twitteru.

* Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
