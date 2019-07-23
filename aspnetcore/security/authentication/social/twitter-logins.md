---
title: Nastavení externí přihlášení pomocí ASP.NET Core na twitteru
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Twitter do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814072"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Nastavení externí přihlášení pomocí ASP.NET Core na twitteru

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento příklad ukazuje, jak uživatelům umožnit [Přihlaste se pomocí svého účtu Twitter](https://dev.twitter.com/web/sign-in/desktop-browser) ukázkový projekt v ASP.NET Core 2.2 využitím vytvořené na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-twitter"></a>Vytvořit aplikaci na Twitteru

* Přejděte do [ https://apps.twitter.com/ ](https://apps.twitter.com/) a přihlaste se. Pokud ještě nemáte účet na Twitteru, použijte **[zaregistrujte se hned teď](https://twitter.com/signup)** odkaz pro vytvoření.

* Klepněte na **vytvořit novou aplikaci** a vyplnit **název**, **popis** a veřejné **webu** identifikátoru URI (může to být dočasný dokud Tento název domény zaregistrujte):

* Zadejte identifikátor URI vývoje s `/signin-twitter` připojí do **identifikátory URI pro přesměrování platný OAuth** pole (například: `https://webapp128.azurewebsites.net/signin-twitter`). Schéma ověřování Twitteru nakonfigurovat později v tomto příkladu bude automaticky zpracovávat požadavky na `/signin-twitter` trasy, která má implementovat tok OAuth.

  > [!NOTE]
  > Segment identifikátoru URI `/signin-twitter` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování Twitteru. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middleware ověřování služby Twitter prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) Třída.

* Vyplňte zbývající části formuláře a klepněte na **vytvoření aplikace Twitter**. Podrobnosti o nové aplikace se zobrazí:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>Ukládání rozhraní Twitter API pro příjemce klíče a tajného kódu

Spusťte následující příkazy zabezpečeně ukládat `ClientId` a `ClientSecret` pomocí [manažera tajných](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

Propojit citlivá nastavení, jako je Twitter `Consumer Key` a `Consumer Secret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto příkladu název tokeny `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret`.

Tyto tokeny můžete najít na **klíče a přístupové tokeny** kartu po vytvoření nové aplikace pro Twitter:

## <a name="configure-twitter-authentication"></a>Konfigurace ověřování Twitteru

Přidat službu Twitter `ConfigureServices` metoda ve *Startup.cs* souboru:

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobrazit [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem ověřování Twitteru. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-twitter"></a>Přihlaste se pomocí Twitteru

Spusťte aplikaci a vyberte **přihlášení**. Zobrazí se možnost přihlásit se přes Twitter:

Kliknutím na **Twitteru** přesměruje na Twitter pro ověřování:

Po zadání vaše přihlašovací údaje k Twitteru, budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů Twitter:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* **ASP.NET Core 2.x pouze:** Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"* . Šablona projektu používané v tomto příkladu zajistí, že se to.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další postup

* V tomto článku jsme si ukázali, jak ověřování pomocí Twitteru. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, měli byste resetovat `ConsumerSecret` na portálu pro vývojáře Twitter.

* Nastavte `Authentication:Twitter:ConsumerKey` a `Authentication:Twitter:ConsumerSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.