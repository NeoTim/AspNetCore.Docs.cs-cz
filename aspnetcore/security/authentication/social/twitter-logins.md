---
title: Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitteru do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775788"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core

Od [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se dozvíte, jak uživatelům povolit, aby se k [účtu Twitter přihlásili](https://dev.twitter.com/web/sign-in/desktop-browser) pomocí ukázkového projektu ASP.NET Core 3,0 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvoření aplikace na Twitteru

* Do projektu přidejte balíček NuGet [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) .

* Přejděte na [https://apps.twitter.com/](https://apps.twitter.com/) a přihlaste se. Pokud ještě nemáte účet na Twitteru, vytvořte si ho pomocí odkazu **[zaregistrovat nyní](https://twitter.com/signup)** .

* Vyberte **vytvořit aplikaci**. Vyplňte **název aplikace**, **Popis aplikace** a identifikátor URI veřejného **webu** (může to být dočasné, dokud nezaregistrujete název domény):

* Zaškrtněte políčko vedle **Povolit možnost přihlásit se pomocí Twitteru** .

* Microsoft. AspNetCore.Identity vyžaduje, aby uživatelé měli ve výchozím nastavení e-mailovou adresu. Přejděte na kartu **oprávnění** , klikněte na tlačítko **Upravit** a zaškrtněte políčko u možnosti **požádat uživatele o e-mailovou adresu**.

* Zadejte identifikátor URI pro vývoj `/signin-twitter` s připojením k poli **adresy URL zpětného volání** (například `https://webapp128.azurewebsites.net/signin-twitter`:). Schéma ověřování Twitteru, které je nakonfigurované později v této ukázce, `/signin-twitter` automaticky zpracuje požadavky na trase k implementaci toku OAuth.

  > [!NOTE]
  > Segment `/signin-twitter` identifikátoru URI je nastaven jako výchozí zpětné volání poskytovatele ověřování Twitteru. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování Twitteru prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Vyplňte zbytek formuláře a vyberte **vytvořit**. Zobrazí se nové podrobnosti o aplikaci:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Uložit klíč rozhraní API pro příjemce Twitteru a tajný klíč

Uložte citlivá nastavení, jako je klíč rozhraní API pro Twitter Consumer a tajný klíč pomocí [správce tajných](xref:security/app-secrets)klíčů. V této ukázce použijte následující postup:

1. Inicializujte projekt pro tajné úložiště podle pokynů v tématu [Povolení tajného úložiště](xref:security/app-secrets#enable-secret-storage).
1. Uložte citlivá nastavení do místního úložiště tajného klíče s klíči `Authentication:Twitter:ConsumerKey` tajných `Authentication:Twitter:ConsumerSecret`klíčů a:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Tyto tokeny najdete na kartě **klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování na Twitteru

Přidejte službu Twitter do `ConfigureServices` metody v souboru *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním pomocí Twitteru najdete v referenčních informacích k rozhraní [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API. Dá se použít k vyžádání různých informací o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlášení pomocí Twitteru

Spusťte aplikaci a vyberte **Přihlásit se**. Zobrazí se možnost přihlásit se pomocí Twitteru:

Kliknutí na **Twitter** přesměruje na Twitter pro ověřování:

Po zadání přihlašovacích údajů pro Twitter budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Twitteru:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Řešení potíží

* **Pouze ASP.NET Core 2. x:** Pokud Identity není nakonfigurováno `services.AddIdentity` voláním `ConfigureServices`v, pokus o ověření bude mít za následek *ArgumentException: je třeba zadat možnost SignInScheme*. Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.
* Pokud se databáze lokality nevytvořila při použití prvotní migrace, při zpracování chyby žádosti se zobrazí *operace databáze* . Klepnutím na **použít migrace** vytvořte databázi a aktualizujte, aby pokračovala i po chybě.

## <a name="next-steps"></a>Další kroky

* Tento článek ukazuje, jak se dá ověřit pomocí Twitteru. Můžete postupovat podle podobného přístupu k ověřování u jiných poskytovatelů uvedených na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat na `ConsumerSecret` portálu pro vývojáře na Twitteru.

* Nastavte nastavení `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace v Azure Portal. Konfigurační systém je nastaven na čtení klíčů z proměnných prostředí.
