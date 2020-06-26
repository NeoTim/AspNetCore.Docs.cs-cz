---
title: Použití ASP.NET Core SignalR s TypeScript a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete Webpack a vytvoříte ASP.NET Core SignalR webovou aplikaci, jejíž klient je napsaný v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 49d185ce941d5f8e841224e7de3b72b8350a1c47
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407899"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Použití ASP.NET Core SignalR s TypeScript a webpackem

Od [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) umožňuje vývojářům seskupit a sestavit prostředky webové aplikace na straně klienta. Tento kurz ukazuje použití webpacku ve ASP.NET Core SignalR webové aplikaci, jejíž klient je napsaný v [TypeScript](https://www.typescriptlang.org/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Uživatelské rozhraní aplikace úvodní ASP.NET Core SignalR
> * Konfigurace SignalR klienta TypeScript
> * Konfigurace kanálu sestavení pomocí webpacku
> * Konfigurace SignalR serveru
> * Povolit komunikaci mezi klientem a serverem

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample))

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pro Visual Studio Code verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace v ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* . Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři. Postupujte podle těchto pokynů v aplikaci Visual Studio:

1. Spusťte Visual Studio. V okně Start vyberte **pokračovat bez kódu**.
1. Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.
1. Ze seznamu vyberte položku *$ (cesta)* . Kliknutím na šipku nahoru přesuňte položku do druhé pozice v seznamu a vyberte **OK**.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio byla dokončena.

1. Použijte možnost **soubor**  >  **Nový**  >  **projekt** a vyberte šablonu **webové aplikace ASP.NET Core** . Vyberte **Další**.
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 3,1* . Vyberte **prázdnou** šablonu a vyberte **vytvořit**.

Přidejte `Microsoft.TypeScript.MSBuild` balíček do projektu:

1. V **Průzkumník řešení** (pravé podokno) klikněte pravým tlačítkem myši na uzel projektu a vyberte možnost **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte `Microsoft.TypeScript.MSBuild` a kliknutím na **nainstalovat** napravo nainstalujte balíček.

Sada Visual Studio přidá balíček NuGet pod uzel **závislosti** v **Průzkumník řešení**a povolí v projektu kompilaci TypeScript.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz:

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* `dotnet new`Příkaz vytvoří prázdnou ASP.NET Core webovou aplikaci v adresáři *SignalRWebPack* .
* `code`Příkaz otevře složku *SignalRWebPack* v aktuální instanci Visual Studio Code.

V **integrovaném terminálu**spusťte následující příkaz .NET Core CLI:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Předchozí příkaz přidá balíček [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , který umožňuje kompilaci TypeScript v projektu.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace sady Webpack a TypeScriptu

Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte *package.jsv* souboru:

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do *package.jsv* souboru a uložte změny souboru:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Nastavení `private` vlastnosti tak, aby se `true` zabránilo upozorněním na instalaci balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. V kořenovém adresáři projektu spusťte následující příkaz:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Některé podrobnosti příkazu si všimněte:

    * Číslo verze následuje po `@` znaménku pro každý název balíčku. NPM nainstaluje tyto konkrétní verze balíčku.
    * `-E`Možnost zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *package.js*. Například `"webpack": "4.41.5"` je použit místo `"webpack": "^4.41.5"` . Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.

    Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.

1. Nahraďte `scripts` vlastnost *package.jsv* souboru následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: Rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu. `mode`Možnost zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace. Používejte pouze `build` ve vývoji.
    * `release`: Rozbalí prostředky na straně klienta v provozním režimu.
    * `publish`: Spustí `release` skript, který bude seskupit prostředky na straně klienta v provozním režimu. Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.

1. V kořenovém adresáři projektu vytvořte soubor s názvem *webpack.config.js*s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Předchozí soubor nakonfiguruje kompilaci sady Webpack. Některé podrobnosti konfigurace k poznámení:

    * `output`Vlastnost přepisuje výchozí hodnotu *DIST*. Místo toho se svazek vydává v adresáři *wwwroot* .
    * `resolve.extensions`Pole obsahuje *. js* pro import SignalR klientského JavaScriptu.

1. Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.

1. Pomocí následujícího kódu vytvořte *Src/index.html* .

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Předchozí kód HTML definuje standardní kód domovské stránky.

1. Vytvořte nový adresář *Src/CSS* . Jeho účelem je uložit soubory *. CSS* projektu.

1. Vytvořte *Src/CSS/Main. CSS* s následujícími šablonami stylů CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Předchozí *hlavní soubor. CSS* styly aplikace.

1. Pomocí následujícího formátu JSON vytvořte *Src/tsconfig.js* :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *Src/index. TS* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje při uživatelském typu v `tbMessage` textovém poli. `send`Funkce se volá, když uživatel stiskne klávesu **ENTER** .
    * `click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** . Volá se funkce `send`.

## <a name="configure-the-app"></a>Konfigurace aplikace

1. V `Startup.Configure` přidejte volání do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html* .  Soubor se obsluhuje, jestli uživatel zadá svoji úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Na konci `Startup.Configure` namapujte cestu */hub* k `ChatHub` centru. Nahraďte kód, který se zobrazí *Hello World!* s následujícím řádkem: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. V `Startup.ConfigureServices` volejte volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Vytvořte nový adresář *s názvem* hub v kořenovém adresáři projektu *SignalRWebPack/* pro uložení SignalR centra.

1. Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. `using`Do horní části souboru *Startup.cs* přidejte následující příkaz pro vyřešení `ChatHub` odkazu:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolit komunikaci klienta a serveru

Aplikace aktuálně zobrazuje základní formulář pro posílání zpráv, ale ještě není funkční. Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.

1. Spusťte následující příkaz v kořenu projektu:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Předchozí příkaz nainstaluje:

     * [ SignalR Klient TypeScript](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.
     * Definice typu TypeScript pro Node.js, které umožňují kontrolu Node.js typů při kompilaci.

1. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. `HubConnectionBuilder`Třída vytvoří nového tvůrce pro konfiguraci připojení k serveru. `withUrl`Funkce nakonfiguruje adresu URL centra.

    SignalRpovolí výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Naslouchat konkrétní zprávě se dá provést prostřednictvím `on` funkce. Na je možné naslouchat libovolný počet názvů zpráv. Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří se nový `div` prvek s názvem autora a obsahem zprávy v jeho `innerHTML` atributu. Je přidána do hlavního `div` prvku zobrazujícího zprávy.

1. Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání `send` metody. První parametr metody je název zprávy. Data zprávy jsou v ostatních parametrech nezvyklá. V tomto příkladu se zobrazí zpráva, která je označena jako `newMessage` Odeslaná na server. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud funkce Send funguje, hodnota textového pole se nevymaže.

1. Do třídy `ChatHub` přidejte metodu `NewMessage`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná po názvu zprávy postačuje.

    V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage` . Metoda jazyka C# `NewMessage` očekává data odesílaná klientem. Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy se odesílají všem klientům připojeným k centru.

## <a name="test-the-app"></a>Otestování aplikace

Ověřte, že aplikace funguje s následujícím postupem.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte příkaz Webpack v režimu *vydání* . Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu. Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **ladit**  >  **Spustit bez ladění** , aby se aplikace spouštěla v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>` .

   Pokud se zobrazí chyby kompilace, zkuste řešení zavřít a znovu otevřít. 

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.

1. Otevřete prohlížeč `http://localhost:<port_number>` . Soubor *wwwroot/index.html* se obsluhuje. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [.NET Core SDK 2,2 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2,2 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pro Visual Studio Code verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace v ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte aplikaci Visual Studio tak, aby vyhledala npm v proměnné prostředí *path* . Ve výchozím nastavení používá Visual Studio verzi npm nalezenou v instalačním adresáři. Postupujte podle těchto pokynů v aplikaci Visual Studio:

1. Přejděte na **nástroje** > **Možnosti** > **projekty a řešení** > **Web Správa balíčků** > **externích webových nástrojů**.
1. Ze seznamu vyberte položku *$ (cesta)* . Kliknutím na šipku nahoru tuto položku přesunete do druhé pozice v seznamu.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio byla dokončena. Je čas vytvořit projekt.

1. Použijte možnost **soubor** > **Nový** > **projekt** a vyberte šablonu **webové aplikace ASP.NET Core** .
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím seznamu cílové rozhraní vyberte *.NET Core* a v rozevíracím seznamu rozhraní vyberte *ASP.NET Core 2,2* . Vyberte **prázdnou** šablonu a vyberte **vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz:

```dotnetcli
dotnet new web -o SignalRWebPack
```

Prázdná webová aplikace ASP.NET Core, která cílí na .NET Core, se vytvoří v adresáři *SignalRWebPack* .

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace sady Webpack a TypeScriptu

Následující postup nakonfiguruje převod TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Spusťte následující příkaz v kořenovém adresáři projektu a vytvořte *package.jsv* souboru:

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do *package.js* souboru:

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Nastavení `private` vlastnosti tak, aby se `true` zabránilo upozorněním na instalaci balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. V kořenovém adresáři projektu spusťte následující příkaz:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Některé podrobnosti příkazu si všimněte:

    * Číslo verze následuje po `@` znaménku pro každý název balíčku. NPM nainstaluje tyto konkrétní verze balíčku.
    * `-E`Možnost zakáže výchozí chování npm při psaní operátorů rozsahu [sémantických verzí](https://semver.org/) do *package.js*. Například `"webpack": "4.29.3"` je použit místo `"webpack": "^4.29.3"` . Tato možnost zabrání nezamýšleným upgradům na novější verze balíčků.

    Další podrobnosti najdete v tématu [npm-Install](https://docs.npmjs.com/cli/install) docs.

1. Nahraďte `scripts` vlastnost *package.jsv* souboru následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: Rozbalí prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že se sada znovu generuje při každém změně souboru projektu. `mode`Možnost zakáže optimalizace produkčního prostředí, jako je například protřepání stromu a minifikace. Používejte pouze `build` ve vývoji.
    * `release`: Rozbalí prostředky na straně klienta v provozním režimu.
    * `publish`: Spustí `release` skript, který bude seskupit prostředky na straně klienta v provozním režimu. Volá příkaz [publish](/dotnet/core/tools/dotnet-publish) .NET Core CLI k publikování aplikace.

1. V kořenovém adresáři projektu vytvořte soubor s názvem *webpack.config.js* s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Předchozí soubor nakonfiguruje kompilaci sady Webpack. Některé podrobnosti konfigurace k poznámení:

    * `output`Vlastnost přepisuje výchozí hodnotu *DIST*. Místo toho se svazek vydává v adresáři *wwwroot* .
    * `resolve.extensions`Pole obsahuje *. js* pro import SignalR klientského JavaScriptu.

1. Vytvořte nový *zdrojový* adresář v kořenovém adresáři projektu pro uložení prostředků na straně klienta v projektu.

1. Pomocí následujícího kódu vytvořte *Src/index.html* .

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Předchozí kód HTML definuje standardní kód domovské stránky.

1. Vytvořte nový adresář *Src/CSS* . Jeho účelem je uložit soubory *. CSS* projektu.

1. Vytvořte *Src/CSS/Main. CSS* s následujícím kódem:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Předchozí *hlavní soubor. CSS* styly aplikace.

1. Pomocí následujícího formátu JSON vytvořte *Src/tsconfig.js* :

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Předchozí kód nakonfiguruje kompilátor TypeScript, aby vytvořil JavaScript kompatibilní s [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *Src/index. TS* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje při uživatelském typu v `tbMessage` textovém poli. `send`Funkce se volá, když uživatel stiskne klávesu **ENTER** .
    * `click`: Tato událost se aktivuje, když uživatel klikne na tlačítko **Odeslat** . Volá se funkce `send`.

## <a name="configure-the-aspnet-core-app"></a>Konfigurace aplikace ASP.NET Core

1. Kód uvedený v `Startup.Configure` metodě zobrazuje *Hello World!*. Nahraďte `app.Run` volání metody voláním [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html* , ať už uživatel zadá svou úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Zavolejte [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices` . Přidá SignalR služby do projektu.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Namapujte */hub* trasu na `ChatHub` centrum. Přidejte následující řádky na konec `Startup.Configure` :

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. V kořenovém adresáři projektu vytvořte nový adresář s názvem *centra*. Jejím účelem je uložit SignalR centrum, které se vytvoří v dalším kroku.

1. Vytvořte centra rozbočovačů */ChatHub. cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Přidejte následující kód na začátek souboru *Startup.cs* pro vyřešení `ChatHub` odkazu:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolit komunikaci klienta a serveru

Aplikace aktuálně zobrazuje jednoduchý formulář pro posílání zpráv. Při pokusu o provedení akce nedojde k žádné akci. Server naslouchá konkrétní trase, ale neprovádí žádnou akci s odeslanými zprávami.

1. Spusťte následující příkaz v kořenu projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Předchozí příkaz nainstaluje [ SignalR klienta TypeScript](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.

1. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. `HubConnectionBuilder`Třída vytvoří nového tvůrce pro konfiguraci připojení k serveru. `withUrl`Funkce nakonfiguruje adresu URL centra.

    SignalRpovolí výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s názvem `messageReceived` mohou spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Naslouchat konkrétní zprávě se dá provést prostřednictvím `on` funkce. Můžete naslouchat libovolnému počtu názvů zpráv. Je také možné předat parametry do zprávy, jako je například jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří se nový `div` prvek s názvem autora a obsahem zprávy v jeho `innerHTML` atributu. Nová zpráva je přidána do hlavního `div` prvku zobrazujícího zprávy.

1. Teď, když klient může obdržet zprávu, ji nakonfiguruje k odesílání zpráv. Přidejte zvýrazněný kód do souboru *Src/index. TS* :

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets vyžaduje volání `send` metody. První parametr metody je název zprávy. Data zprávy jsou v ostatních parametrech nezvyklá. V tomto příkladu se zobrazí zpráva, která je označena jako `newMessage` Odeslaná na server. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud funkce Send funguje, hodnota textového pole se nevymaže.

1. Do třídy `ChatHub` přidejte metodu `NewMessage`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí vysílání kódu přijalo zprávy všem připojeným uživatelům, jakmile je server obdrží. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná po názvu zprávy postačuje.

    V tomto příkladu pošle klient TypeScript zprávu identifikovanou jako `newMessage` . Metoda jazyka C# `NewMessage` očekává data odesílaná klientem. Bylo provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientech. vše](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy se odesílají všem klientům připojeným k centru.

## <a name="test-the-app"></a>Otestování aplikace

Ověřte, že aplikace funguje s následujícím postupem.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte příkaz Webpack v režimu *vydání* . Pomocí okna **konzoly Správce balíčků** spusťte následující příkaz v kořenovém adresáři projektu. Pokud nejste v kořenovém adresáři projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **ladit**  >  **Spustit bez ladění** , aby se aplikace spouštěla v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* se obsluhuje na `http://localhost:<port_number>` .

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte příkaz Webpack v režimu *vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na místním hostiteli.

1. Otevřete prohlížeč `http://localhost:<port_number>` . Soubor *wwwroot/index.html* se obsluhuje. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do panelu Adresa.

1. Vyberte možnost prohlížeč, do textového pole **zpráva** zadejte něco a klikněte na tlačítko **Odeslat** . Jedinečné uživatelské jméno a zpráva se okamžitě zobrazí na obou stránkách.

---

![zpráva zobrazená v oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
