---
title: Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core
author: rick-anderson
description: Tento kurz ukazuje, integrace ověřování uživatele účtu Microsoft do stávající aplikace ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 89969370cea66b7b6632f1b0be59e135767c831e
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708397"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Nastavení Microsoft Account externí přihlášení pomocí ASP.NET Core

Podle [Valeriy Novytskyy](https://github.com/01binary) a [Rick Anderson](https://twitter.com/RickAndMSFT)

V tomto kurzu se dozvíte, jak povolit vašim uživatelům přihlašovat se pomocí svého účtu Microsoft použitím ukázkového projektu ASP.NET Core 2.0 na vytvoří [předchozí stránce](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Vytvoření aplikace portálu společnosti Microsoft pro vývojáře

* Přejděte do [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) a vytvořte nebo přihlášení účtem Microsoft:

![Přihlaste se dialogové okno](index/_static/MicrosoftDevLogin.png)

Pokud ještě nemáte účet Microsoft, klepněte na  **[vytvořte si ho!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** Po přihlášení budete přesměrováni na **Moje aplikace** stránky:

![Portál pro vývojáře Microsoft otevřít v Microsoft Edge](index/_static/MicrosoftDev.png)

* Klepněte na **přidat aplikaci** v pravém horním rohu, zadejte vaše **název_aplikace** a **E-mail kontaktu**:

![Dialogové okno nové registrace aplikace](index/_static/MicrosoftDevAppCreate.png)

* Pro účely tohoto kurzu, zrušte **instalační program s asistencí** zaškrtávací políčko.

* Klepněte na **vytvořit** a pokračujte **registrace** stránky. Zadejte **název** a poznamenejte si hodnotu **Id aplikace**, který použijete jako `ClientId` dále v tomto kurzu:

![Stránka pro registraci do](index/_static/MicrosoftDevAppReg.png)

* Klepněte na **přidat platformy** v **platformy** a vyberte **webové** platformy:

![Přidat dialog platformy](index/_static/MicrosoftDevAppPlatform.png)

* Na novém **webové** platformy části, zadejte adresu URL svého vývoj s `/signin-microsoft` připojí do **adresy URL pro přesměrování** pole (například: `https://localhost:44320/signin-microsoft`). Schéma ověřování Microsoft později v tomto kurzu konfiguruje automaticky zpracovává požadavky na `/signin-microsoft` trasy, která má implementovat tok OAuth:

![Části webové platformy](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> Segment identifikátoru URI `/signin-microsoft` je nastaven jako výchozí zpětného volání zprostředkovatele ověřování společnosti Microsoft. Můžete změnit výchozí identifikátor URI zpětného volání při konfiguraci middlewaru ověřování společnosti Microsoft prostřednictvím zděděnou [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) vlastnost [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) třídy.

* Klepněte na **přidat adresu URL** aby adresa URL byla přidána.

* V případě potřeby zadejte další nastavení aplikace a klepněte na **Uložit** v dolní části stránky a uložit změny konfigurace aplikace.

* Při nasazování webu budete potřebovat revidovat **registrace** stránku a nastavit novou veřejnou adresu URL.

## <a name="store-microsoft-application-id-and-password"></a>Store aplikace Microsoft Id a heslo

* Poznámka: `Application Id` zobrazené na **registrace** stránky.

* Klepněte na **generovat nové heslo** v **tajných klíčů aplikací** oddílu. Zobrazí pole, kde můžete kopírovat heslo aplikace:

![Dialogové okno Nový vygenerované heslo](index/_static/MicrosoftDevPassword.png)

Propojit citlivá nastavení, jako je Microsoft `Application ID` a `Password` pomocí konfigurace aplikace [manažera tajných](xref:security/app-secrets). Pro účely tohoto kurzu se název tokeny `Authentication:Microsoft:ApplicationId` a `Authentication:Microsoft:Password`.

## <a name="configure-microsoft-account-authentication"></a>Konfigurace ověřování pomocí účtu Microsoft

Šablona projektu použité v tomto kurzu zajistí, že [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) balíček je už nainstalovaný.

* K instalaci tohoto balíčku pomocí sady Visual Studio 2017, klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**.
* Instalace s .NET Core CLI, spusťte v adresáři projektu následující:

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

Přidat službu Microsoft Account v `ConfigureServices` metoda *Startup.cs* souboru:

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přidat middlewaru Microsoft Account v `Configure` metoda *Startup.cs* souboru:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

I když terminologii používané v portálu pro vývojáře Microsoft názvy těchto tokenů `ApplicationId` a `Password`, jsou vystaveny jako `ClientId` a `ClientSecret` v konfiguraci rozhraní API.

Zobrazit [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) reference k rozhraní API pro další informace o konfiguraci možností podporovaných příkazem Account Microsoft ověřování. To umožňuje požádat o jiné informace o uživateli.

## <a name="sign-in-with-microsoft-account"></a>Přihlaste se pomocí účtu Microsoft

Spusťte aplikaci a klikněte na tlačítko **přihlášení**. Zobrazí se možnost přihlásit se účtem Microsoft:

![Webovou aplikaci protokolu na stránce: uživatel nebyl ověřen](index/_static/DoneMicrosoft.png)

Po kliknutí na Microsoft, budete přesměrováni do společnosti Microsoft pro ověřování. Po přihlášení pomocí Account Microsoftu (pokud ještě nejste přihlášení) se výzva k povolení přístup k informacím:

![Microsoft dialog ověřování.](index/_static/MicrosoftLogin.png)

Klepněte na **Ano** a budete přesměrováni zpět na webovou stránku, kde můžete nastavit e-mailu.

Nyní jste přihlášeni pomocí svých přihlašovacích údajů společnosti Microsoft:

![Webová aplikace: uživatel byl ověřen](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Poradce při potížích

* Pokud poskytovatel Account Microsoft vás přesměruje na přihlašovací stránce chyby, vezměte na vědomí chyba nadpis a popis parametrů řetězce dotazu přímo po `#` (hashtag) v identifikátoru Uri.

  I když chybová zpráva vypadá to, že indikovat problém s ověřováním Microsoft, Nejběžnější příčinou je identifikátor Uri neodpovídá žádné z vaší aplikace **identifikátory URI přesměrování** zadaný pro **webové** platformy .
* **ASP.NET Core 2.x pouze:** pokud identita není nakonfigurována voláním `services.AddIdentity` v `ConfigureServices`, bude výsledkem pokusu o ověření *ArgumentException: musí být Zadaná možnost "SignInScheme"*. Šablona projektu použité v tomto kurzu zajistí, že to se provádí.
* Pokud nebyl vytvořen použití počáteční migraci databáze lokality, se zobrazí *databázová operace selhala při zpracování požadavku* chyby. Klepněte na **migrace použít** k vytvoření databáze a aktualizovat a pokračovat po chybě.

## <a name="next-steps"></a>Další kroky

* V tomto článku jsme si ukázali, jak můžete ověřit s Microsoftem. Můžete postupovat podle podobný přístup k ověření u jiných poskytovatelů na [předchozí stránce](xref:security/authentication/social/index).

* Po publikování webu do webové aplikace Azure, měli byste vytvořit nový `Password` na portálu pro vývojáře společnosti Microsoft.

* Nastavte `Authentication:Microsoft:ApplicationId` a `Authentication:Microsoft:Password` jako nastavení aplikace na webu Azure Portal. Konfigurační systém je nastavený na klíče pro čtení z proměnných prostředí.
