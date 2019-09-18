---
title: Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje integraci ověřování uživatelů účtu Twitteru do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 5182f1647acb664bf35f086fcddbe909559a62f7
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082294"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Nastavení externího přihlášení k Twitteru pomocí ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se dozvíte, jak uživatelům povolit, aby se k [účtu Twitter přihlásili](https://dev.twitter.com/web/sign-in/desktop-browser) pomocí ukázkového projektu ASP.NET Core 2,2 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvoření aplikace na Twitteru

* Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se. Pokud ještě nemáte účet na Twitteru, vytvořte si ho pomocí odkazu **[zaregistrovat nyní](https://twitter.com/signup)** .

* Klepněte na **vytvořit novou aplikaci** a vyplňte **název**aplikace, **Popis** a identifikátor URI veřejného **webu** (může to být dočasné, dokud nezaregistrujete název domény):

* Zadejte identifikátor URI pro vývoj `/signin-twitter` s připojením k **platným poli s platnými identifikátory URI pro přesměrování OAuth** (například: `https://webapp128.azurewebsites.net/signin-twitter`). Schéma ověřování Twitteru, které je nakonfigurované později v této ukázce, `/signin-twitter` automaticky zpracuje požadavky na trase k implementaci toku OAuth.

  > [!NOTE]
  > Segment `/signin-twitter` identifikátoru URI je nastaven jako výchozí zpětné volání poskytovatele ověřování Twitteru. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware pro ověřování Twitteru prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) .

* Vyplňte zbytek formuláře a klepněte na **vytvořit aplikaci Twitter**. Zobrazí se nové podrobnosti o aplikaci:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Ukládá se klíč rozhraní API pro příjemce Twitteru a tajný kód.

Spuštěním následujících příkazů bezpečně uložte `ClientId` a `ClientSecret` použijte [správce tajných klíčů](xref:security/app-secrets):

```dotnetcli
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

Pomocí [správce tajného](xref:security/app-secrets)kódu `Consumer Key` můžete `Consumer Secret` propojit citlivá nastavení, jako je Twitter a konfigurace vaší aplikace. Pro účely této ukázky pojmenujte tokeny `Authentication:Twitter:ConsumerKey` a. `Authentication:Twitter:ConsumerSecret`

Tyto tokeny najdete na kartě **klíče a přístupové tokeny** po vytvoření nové aplikace Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování na Twitteru

Přidejte službu Twitter do `ConfigureServices` metody v souboru *Startup.cs* :

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním pomocí Twitteru najdete v referenčních informacích k rozhraní [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlášení pomocí Twitteru

Spusťte aplikaci a vyberte **Přihlásit se**. Zobrazí se možnost přihlásit se pomocí Twitteru:

Kliknutí na **Twitter** přesměruje na Twitter pro ověřování:

Po zadání přihlašovacích údajů pro Twitter budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů k Twitteru:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* **Pouze ASP.NET Core 2. x:** Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, výsledkem *pokusu o ověření bude ArgumentException: Je nutné zadat*možnost SignInScheme. Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další postup

* Tento článek ukazuje, jak se dá ověřit pomocí Twitteru. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure byste měli resetovat na `ConsumerSecret` portálu pro vývojáře na Twitteru.

* Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
