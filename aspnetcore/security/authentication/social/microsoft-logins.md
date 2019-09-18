---
title: Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka demonstruje integraci účet Microsoft ověřování uživatelů do existující aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 91ace293fd16cd180b3d5c183c637af6db1d08c3
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082342"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Nastavení externího přihlášení k účtu Microsoft pomocí ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V této ukázce se dozvíte, jak uživatelům povolit, aby se k účet Microsoft přihlásili pomocí projektu ASP.NET Core 2,2 vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Vytvoření aplikace na portálu Microsoftu pro vývojáře

* Přejděte na stránku [Azure Portal-registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) a vytvořte nebo Přihlaste se ke účet Microsoft:

Pokud nemáte účet Microsoft, vyberte **vytvořit**. Po přihlášení budete přesměrováni na stránku **Registrace aplikací** :

* Vybrat **novou registraci**
* Zadejte **název**.
* Vyberte možnost pro **podporované typy účtů**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* V části **identifikátor URI přesměrování**zadejte adresu URL pro `/signin-microsoft` vývoj s připojením. Například, `https://localhost:44389/signin-microsoft`. Schéma ověřování společnosti Microsoft nakonfigurované později v této ukázce bude automaticky zpracovávat požadavky `/signin-microsoft` na trase za účelem implementace toku OAuth.
* Vybrat **registraci**

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

* V levém podokně vyberte **certifikáty & tajných**kódů.
* V části **tajné klíče klienta**vyberte **nový tajný klíč klienta** .

  * Přidejte popis pro tajný klíč klienta.
  * Vyberte tlačítko **Přidat** .

* V části **tajné klíče klienta**Zkopírujte hodnotu tajného klíče klienta.

> [!NOTE]
> Segment `/signin-microsoft` identifikátoru URI je nastaven jako výchozí zpětné volání poskytovatele ověřování společnosti Microsoft. Výchozí identifikátor URI zpětného volání můžete změnit během konfigurace middleware ověřování od společnosti Microsoft prostřednictvím zděděné vlastnosti [RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) třídy [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) .

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Uložení ID klienta Microsoft a tajného kódu klienta

Spuštěním následujících příkazů bezpečně uložte `ClientId` a `ClientSecret` použijte [správce tajných klíčů](xref:security/app-secrets):

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Pomocí [správce tajných](xref:security/app-secrets)kódů `ClientId` propojte citlivá nastavení, jako je Microsoft, a `ClientSecret` konfiguraci vaší aplikace. Pro účely této ukázky pojmenujte tokeny `Authentication:Microsoft:ClientId` a. `Authentication:Microsoft:ClientSecret`

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurace ověřování účtu Microsoft

Přidejte službu účtu Microsoft do `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Další informace o možnostech Konfigurace podporovaných ověřováním účtů Microsoft najdete v referenčních informacích k rozhraní [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-microsoft-account"></a>Účet Přihlásit se účtem Microsoft

Spusťte rozhraní a klikněte na **Přihlásit se**. Zobrazí se možnost přihlásit se s Microsoftem. Když kliknete na Microsoft, budete přesměrováni na Microsoft pro ověřování. Po přihlášení pomocí účtu Microsoft (Pokud ještě není přihlášený) se zobrazí výzva, abyste aplikaci dali přístup k vašim informacím:

Klepněte na **Ano** a budete přesměrováni zpět na web, kde můžete nastavit e-mail.

Nyní jste přihlášeni pomocí přihlašovacích údajů Microsoftu:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud vám poskytovatel účtu Microsoft přesměruje na chybovou stránku pro přihlášení, poznamenejte si parametry řetězce chyby a popis v parametrech řetězce dotazu `#` přímo za (hashtag) v identifikátoru URI.

  I když se chybová zpráva jeví jako problém s ověřováním Microsoftu, Nejběžnější příčinou je, že identifikátor URI vaší aplikace neodpovídá žádnému identifikátoru **URI přesměrování** , který je zadaný pro **webovou** platformu.
* Pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, výsledkem *pokusu o ověření bude ArgumentException: Je nutné zadat*možnost SignInScheme. Šablona projektu použitá v této ukázce zajišťuje, že je to hotové.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další postup

* Tento článek ukazuje, jak se dá ověřit u Microsoftu. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure vytvořte nové tajné klíče klienta na portálu Microsoftu pro vývojáře.

* Nastavte `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.