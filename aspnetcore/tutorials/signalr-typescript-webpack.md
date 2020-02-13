---
title: Použití ASP.NET Core SignalR s TypeScript a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete sadu Webpack tak, aby se vytvořila sada ASP.NET Core SignalR webovou aplikaci, jejíž klient je napsaný v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: f8bbd9ed2e9c792197eb29be459f7e5ee499bfd1
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171985"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Použití signalizace ASP.NET Core s TypeScript a webpackem

Od [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) umožňuje vývojářům seskupit a sestavit prostředky webové aplikace na straně klienta. V tomto kurzu se dozvíte, jak používat Webpack ve webové aplikaci ASP.NET Coreového signálu, jejíž klient je napsaný v [TypeScript](https://www.typescriptlang.org/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Generování uživatelského rozhraní pro aplikaci signalizace úvodní ASP.NET Core
> * Konfigurace klienta TypeScript nástroje Signal
> * Konfigurace kanálu sestavení pomocí webpacku
> * Konfigurace serveru signálu
> * Povolit komunikaci mezi klientem a serverem

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [.NET Core SDK 3.0 nebo novější](https://www.microsoft.com/net/download/all)
* [Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 nebo novější](https://www.microsoft.com/net/download/all)
* [C#pro Visual Studio Code verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace v ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* . Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři. Postupujte podle těchto pokynů v aplikaci Visual Studio:

1. Spusťte sadu Visual Studio. V okně Start vyberte **pokračovat bez kódu**.
1. Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.
1. Ze seznamu vyberte položku *$ (cesta)* . Kliknutím na šipku nahoru přesuňte položku do druhé pozice v seznamu a vyberte **OK**.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio byla dokončena.

1. Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** . Vyberte **Další**.
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 3,1* . Vyberte **prázdnou** šablonu a vyberte **vytvořit**.

Přidejte do projektu balíček `Microsoft.TypeScript.MSBuild`:

1. V **Průzkumník řešení** (pravé podokno) klikněte pravým tlačítkem myši na uzel projektu a vyberte možnost **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte `Microsoft.TypeScript.MSBuild`a kliknutím na **nainstalovat** napravo nainstalujte balíček.

Sada Visual Studio přidá balíček NuGet pod uzel **závislosti** v **Průzkumník řešení**a povolí v projektu kompilaci TypeScript.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz:

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* Příkaz `dotnet new` vytvoří prázdnou ASP.NET Core webovou aplikaci v adresáři *SignalRWebPack* .
* Příkaz `code` otevře složku *SignalRWebPack* v aktuální instanci Visual Studio Code.

V **integrovaném terminálu**spusťte následující příkaz .NET Core CLI:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Předchozí příkaz přidá balíček (Microsoft. TypeScript. MSBuild) [https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/], který povoluje kompilaci TypeScript v projektu.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace sady Webpack a TypeScriptu

Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* a uložte změny souborů:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. Z kořenového adresáře projektu spusťte následující příkaz:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Některé podrobnosti příkazu si všimněte:

    * Číslo verze následuje po znaménku `@` pro každý název balíčku. NPM nainstaluje tyto konkrétní verze balíčku.
    * Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*. Místo `"webpack": "^4.41.5"`se například používá `"webpack": "4.41.5"`. Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.

    Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.

1. Vlastnost `scripts` souboru *Package. JSON* nahraďte následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu. Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace. Používejte `build` jenom při vývoji.
    * `release`: rozbalí prostředky na straně klienta v provozním režimu.
    * `publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu. Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.

1. V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js*s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Předchozí soubor nakonfiguruje kompilaci sady Webpack. Některé podrobnosti konfigurace k poznámení:

    * Vlastnost `output` přepisuje výchozí hodnotu *DIST*. Místo toho se svazek vydává v adresáři *wwwroot* .
    * `resolve.extensions` pole obsahuje *. js* pro Import klientského JavaScriptu pro signalizaci.

1. Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.

1. Vytvořte *Src/index.html* pomocí následujícího kódu.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Předchozí kód HTML definuje standardní kód domovské stránky.

1. Vytvořte nový adresář *Src/CSS* . Jeho účelem je uložit soubory *. CSS* projektu.

1. Vytvořte *Src/CSS/Main. CSS* s následujícími šablonami stylů CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Předchozí *hlavní soubor. CSS* styly aplikace.

1. Vytvořte *Src/tsconfig. JSON* s následujícím JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *Src/index. TS* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje, když uživatel zadá do textového pole `tbMessage`. Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .
    * `click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** . Je volána funkce `send`.

## <a name="configure-the-app"></a>Konfigurace aplikace

1. V `Startup.Configure`přidejte volání [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* .  Soubor se obsluhuje, jestli uživatel zadá svoji úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Na konci `Startup.Configure`namapujte */hub* trasu na centrum `ChatHub`. Nahraďte kód, který se zobrazí *Hello World!* s následujícím řádkem: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. V `Startup.ConfigureServices`volejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Vytvořte nový adresář *s názvem hub* v kořenovém adresáři projektu *SignalRWebPack/* pro uložení centra signalizace.

1. Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Do horní části souboru *Startup.cs* přidejte následující příkaz `using`, který vyřeší `ChatHub` odkaz:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolit komunikaci klienta a serveru

Aplikace aktuálně zobrazuje základní formulář pro posílání zpráv, ale ještě není funkční. Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.

1. Spusťte následující příkaz v kořenu projektu:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Předchozí příkaz nainstaluje:

     * [Klient TypeScript Signal](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.
     * Definice typu TypeScript pro Node. js, které umožňují kontrolu typů Node. js v době kompilace.

1. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru. Funkce `withUrl` nakonfiguruje adresu URL centra.

    Signalizace umožňuje výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`. Na je možné naslouchat libovolný počet názvů zpráv. Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`. Je přidáno do hlavního `div` elementu, který zobrazuje zprávy.

1. Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`. První parametr metody je název zprávy. Data zprávy jsou v ostatních parametrech nezvyklá. V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud funkce Send funguje, hodnota textového pole se nevymaže.

1. Do třídy `NewMessage` přidejte metodu `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná po názvu zprávy postačuje.

    V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`. Metoda C# `NewMessage` očekává data odesílaná klientem. Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy se odesílají všem klientům připojeným k centru.

## <a name="test-the-app"></a>Otestování aplikace

Ověřte, že aplikace funguje s následujícím postupem.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte příkaz Webpack v režimu *vydání* . Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu. Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.

   Pokud se zobrazí chyby kompilace, zkuste řešení zavřít a znovu otevřít. 

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.

1. Otevřete prohlížeč a `http://localhost:<port_number>`. Soubor *wwwroot/index.html* se obsluhuje. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Předpoklady

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [.NET Core SDK 2,2 nebo novější](https://www.microsoft.com/net/download/all)
* [Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2,2 nebo novější](https://www.microsoft.com/net/download/all)
* [C#pro Visual Studio Code verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node. js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace v ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* . Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři. Postupujte podle těchto pokynů v aplikaci Visual Studio:

1. Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.
1. Ze seznamu vyberte položku *$ (cesta)* . Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio byla dokončena. Je čas vytvořit projekt.

1. Použijte možnost **soubor** > **Nový** > možnosti nabídky **projekt** a vyberte šablonu **ASP.NET Core webové aplikace** .
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 2,2* . Vyberte **prázdnou** šablonu a vyberte **vytvořit**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz:

```dotnetcli
dotnet new web -o SignalRWebPack
```

Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace sady Webpack a TypeScriptu

Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte soubor *Package. JSON* :

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do souboru *Package. JSON* :

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Nastavením vlastnosti `private` na `true` zabráníte zobrazování upozornění při instalaci balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. Z kořenového adresáře projektu spusťte následující příkaz:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Některé podrobnosti příkazu si všimněte:

    * Číslo verze následuje po znaménku `@` pro každý název balíčku. NPM nainstaluje tyto konkrétní verze balíčku.
    * Možnost `-E` zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *Package. JSON*. Místo `"webpack": "^4.29.3"`se například používá `"webpack": "4.29.3"`. Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.

    Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.

1. Vlastnost `scripts` souboru *Package. JSON* nahraďte následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu. Možnost `mode` zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace. Používejte `build` jenom při vývoji.
    * `release`: rozbalí prostředky na straně klienta v provozním režimu.
    * `publish`: spustí skript `release`, který vyplní prostředky na straně klienta v provozním režimu. Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.

1. V kořenovém adresáři projektu vytvořte soubor s názvem *Webpack. config. js* s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Předchozí soubor nakonfiguruje kompilaci sady Webpack. Některé podrobnosti konfigurace k poznámení:

    * Vlastnost `output` přepisuje výchozí hodnotu *DIST*. Místo toho se svazek vydává v adresáři *wwwroot* .
    * `resolve.extensions` pole obsahuje *. js* pro Import klientského JavaScriptu pro signalizaci.

1. Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.

1. Vytvořte *Src/index.html* pomocí následujícího kódu.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Předchozí kód HTML definuje standardní kód domovské stránky.

1. Vytvořte nový adresář *Src/CSS* . Jeho účelem je uložit soubory *. CSS* projektu.

1. Vytvořte *Src/CSS/Main. CSS* s následujícím kódem:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Předchozí *hlavní soubor. CSS* styly aplikace.

1. Vytvořte *Src/tsconfig. JSON* s následujícím JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *Src/index. TS* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje, když uživatel zadá do textového pole `tbMessage`. Funkce `send` se volá, když uživatel stiskne klávesu **ENTER** .
    * `click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** . Je volána funkce `send`.

## <a name="configure-the-aspnet-core-app"></a>Konfigurace aplikace ASP.NET Core

1. Kód, který je uveden v metodě `Startup.Configure`, zobrazí *Hello World!* . Nahraďte volání metody `app.Run` voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Předchozí kód umožňuje serveru vyhledat a zpracovat soubor *index. html* bez ohledu na to, zda uživatel zadal svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices`. Přidá do projektu služby signalizace.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Namapujte */hub* trasu na centrum `ChatHub`. Na konec `Startup.Configure`přidejte následující řádky:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*. Jejím účelem je uložit centrum signalizace, které se vytvoří v dalším kroku.

1. Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Do horní části souboru *Startup.cs* přidejte následující kód, který vyřeší odkaz na `ChatHub`:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolit komunikaci klienta a serveru

Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv. Při pokusu o provedení akce nedojde k žádné akci. Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.

1. Spusťte následující příkaz v kořenu projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Předchozí příkaz nainstaluje [klienta TypeScript Signal](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.

1. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru. Funkce `withUrl` nakonfiguruje adresu URL centra.

    Signalizace umožňuje výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Naslouchat konkrétní zprávě se dá provést pomocí funkce `on`. Můžete naslouchat libovolnému počtu názvů zpráv. Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří se nový prvek `div` s názvem autora a obsahem zprávy ve svém atributu `innerHTML`. Nová zpráva je přidána do hlavního `div` elementu, který zobrazuje zprávy.

1. Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání metody `send`. První parametr metody je název zprávy. Data zprávy jsou v ostatních parametrech nezvyklá. V tomto příkladu se do serveru pošle zpráva identifikovaná jako `newMessage`. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud funkce Send funguje, hodnota textového pole se nevymaže.

1. Do třídy `NewMessage` přidejte metodu `ChatHub`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná po názvu zprávy postačuje.

    V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage`. Metoda C# `NewMessage` očekává data odesílaná klientem. Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy se odesílají všem klientům připojeným k centru.

## <a name="test-the-app"></a>Otestování aplikace

Ověřte, že aplikace funguje s následujícím postupem.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte příkaz Webpack v režimu *vydání* . Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu. Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **ladit** > **Spustit bez ladění** a spusťte aplikaci v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>`.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.

1. Otevřete prohlížeč a `http://localhost:<port_number>`. Soubor *wwwroot/index.html* se obsluhuje. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
