---
title: Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core
author: rick-anderson
description: Tato ukázka předvádí, integrace ověřování uživatele účtu Microsoft do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815578"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

Tento příklad ukazuje, jak povolit uživatelům přihlašovat se pomocí svého účtu Microsoft pomocí ASP.NET Core 2.2 projektu vytvořeného na [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Vytvoření aplikace portálu společnosti Microsoft pro vývojáře

* Přejděte [portál Azure – registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky a vytvoření nebo přihlášení účtem Microsoft:

Pokud nemáte účet Microsoft, vyberte **vytvořit**. Po přihlášení budete přesměrováni **registrace aplikací** stránky:

* Vyberte **nové registrace**
* Zadejte **název**.
* Vyberte možnost pro **podporovaných typů účtu**.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* V části **identifikátor URI pro přesměrování**, zadejte adresu URL svého vývoj s `/signin-microsoft` připojí. Například, `https://localhost:44389/signin-microsoft`. Schéma ověřování Microsoft nakonfigurovat později v tomto příkladu bude automaticky zpracovávat požadavky na `/signin-microsoft` trasy, která má implementovat tok OAuth.
* Vyberte **zaregistrovat**

### <a name="create-client-secret"></a>Vytvořit tajný kód klienta

* V levém podokně vyberte **certifikáty a tajné kódy**.
* V části **tajné klíče klienta**vyberte **nový tajný kód klienta**

  * Přidáte popis tajný kód klienta.
  * Vyberte **přidat** tlačítko.

* V části **tajné klíče klienta**, zkopírujte hodnotu tajný kód klienta.

> [!NOTE]
> Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování společnosti Microsoft. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) třídy.

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Store Microsoft klienta ID a tajný klíč klienta

Spusťte následující příkazy zabezpečeně ukládat `ClientId` a `ClientSecret` pomocí [manažera tajných](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

Propojit citlivá nastavení, jako je Microsoft `ClientId` a `ClientSecret` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto příkladu název tokeny `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurace ověřování pomocí účtu Microsoft

Přidat službu Microsoft Account `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Zobrazit [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem Account Microsoft ověřování. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-microsoft-account"></a>Přihlaste se pomocí účtu Microsoft

Spustit a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se účtem Microsoft. Po kliknutí na Microsoft, budete přesměrováni do společnosti Microsoft pro ověřování. Po přihlášení pomocí Account Microsoftu (pokud ještě nejste přihlášení) se výzva k povolení přístup k informacím:

Klepněte na **Ano** a budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů společnosti Microsoft:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud poskytovatel Account Microsoft vás přesměruje na přihlašovací stránce chyby, vezměte na vědomí chyba nadpis a popis parametrů řetězce dotazu přímo po `#` (hashtag) v identifikátoru Uri.

  I když chybová zpráva vypadá to, že indikovat problém s ověřováním Microsoft, Nejběžnější příčinou je identifikátor Uri neodpovídá žádné z vaší aplikace **identifikátory URI přesměrování** zadaný pro **webové** platformy .
* Pokud není nakonfigurovaná identita voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: Musí být Zadaná možnost "SignInScheme"* . Šablona projektu používané v tomto příkladu zajistí, že se to.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další postup

* V tomto článku jsme si ukázali, jak můžete ověřit s Microsoftem. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, vytvořte nový klient tajných kódů na portálu pro vývojáře společnosti Microsoft.

* Nastavte `Authentication:Microsoft:ClientId` a `Authentication:Microsoft:ClientSecret` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.