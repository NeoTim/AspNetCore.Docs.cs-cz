---
title: Použití ASP.NET SignalR jádra s TypeScriptem a webpackem
author: ssougnez
description: V tomto kurzu nakonfigurujete webový SignalR balíček tak, aby sdružoval a vytvářel ASP.NET webovou aplikaci Core, jejíž klient je napsán v typescriptu.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511337"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Použití ASP.NET core signalr s TypeScriptem a webpackem

[Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)

[Webpack](https://webpack.js.org/) umožňuje vývojářům sdružovat a vytvářet prostředky webové aplikace na straně klienta. Tento kurz ukazuje použití webového balíčku v ASP.NET webové aplikace Core SignalR, jejíž klient je napsán v [typescriptu](https://www.typescriptlang.org/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Lešení startér ASP.NET aplikace Core SignalR
> * Konfigurace klienta TypeScript SignalR
> * Konfigurace kanálu sestavení pomocí webového balíčku
> * Konfigurace serveru SignalR
> * Povolení komunikace mezi klientem a serverem

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) [(jak stáhnout)](xref:index#how-to-download-a-sample)

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**
* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pro kód visual studia verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte Visual Studio tak, aby v proměnné prostředí *PATH* hledalo npm. Ve výchozím nastavení používá visual studio verzi npm, která se nachází v instalačním adresáři. Postupujte podle následujících pokynů v sadě Visual Studio:

1. Spusťte Visual Studio. V počátečním okně vyberte **pokračovat bez kódu**.
1. Přejděte na **nástroje** > **možnosti** > **projekty a řešení** > **webová správa** > balíčků externí webové **nástroje**.
1. Ze seznamu vyberte položku *$(PATH).* Kliknutím na šipku nahoru přesuňte položku na druhé místo v seznamu a vyberte **OK**.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio je dokončena.

1. Použijte možnost nabídky**New** > **Nový** **projekt souboru** > a zvolte šablonu **ASP.NET základní webové aplikace.** Vyberte **další**.
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím souboru *Rozhraní .NET Core* vyberte z rozevíracího souboru cílové ho rozhraní a z rozevíracího souboru selektoru rozhraní vyberte *ASP.NET jádra 3.1.* Vyberte **prázdnou** šablonu a vyberte **Vytvořit**.

Přidejte `Microsoft.TypeScript.MSBuild` balíček do projektu:

1. V **Průzkumníku řešení** (pravé podokno) klikněte pravým tlačítkem myši na uzel projektu a vyberte **spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte `Microsoft.TypeScript.MSBuild`položku a potom klikněte na **nainstalovat** vpravo a nainstalujte balíček.

Visual Studio přidá balíček NuGet pod uzel **Závislosti** v **Průzkumníkovi řešení**, povolení kompilace TypeScript v projektu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz :

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* Příkaz `dotnet new` vytvoří prázdnou ASP.NET webovou aplikaci Core v adresáři *SignalRWebPack.*
* Příkaz `code` otevře složku *SignalRWebPack* v aktuální instanci kódu sady Visual Studio.

Spusťte následující příkaz cli jádra .NET v **integrovaném terminálu**:

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

Předchozí příkaz přidá balíček [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) který umožňuje kompilaci typescriptu v projektu.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace webového balíčku a jazyka TypeScript

Následující kroky nakonfigurují převod jazyka TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Chcete-li vytvořit soubor *package.json,* spusťte v kořenovém adresáři projektu následující příkaz:

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do souboru *package.json* a uložte změny souboru:

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    Nastavení `private` vlastnosti `true` zabránit upozornění instalace balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. V kořenovém adresáři projektu spusťte následující příkaz:

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    Některé podrobnosti příkazu na vědomí:

    * Číslo verze následuje `@` za znaménkem pro každý název balíčku. npm nainstaluje tyto konkrétní verze balíčků.
    * Tato `-E` možnost zakáže výchozí chování npm psaní [sémantické versioning](https://semver.org/) operátory rozsahu *package.json*. Například `"webpack": "4.41.5"` se používá `"webpack": "^4.41.5"`místo . Tato možnost zabraňuje nechtěnému upgradu na novější verze balíčků.

    Viz [npm-install](https://docs.npmjs.com/cli/install) docs pro více podrobností.

1. Nahraďte `scripts` vlastnost souboru *package.json* následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: Sdružuje prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že sada regenerovat pokaždé, když se změní soubor projektu. Tato `mode` možnost zakáže optimalizace výroby, jako je například třepání stromů a minifikace. Používejte `build` pouze ve vývoji.
    * `release`: Sdružuje prostředky na straně klienta v produkčním režimu.
    * `publish`: Spustí `release` skript pro sbalení prostředků na straně klienta v produkčním režimu. Volá příkaz publikování rozhraní [PŘÍKAZU](/dotnet/core/tools/dotnet-publish) .NET Core pro publikování aplikace.

1. Vytvořte soubor s názvem *webpack.config.js*v kořenovém adresáři projektu s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    Předchozí soubor konfiguruje kompilaci webpacku. Některé podrobnosti o konfiguraci na vědomí:

    * Vlastnost `output` přepíše výchozí hodnotu *dist*. Balíček je místo toho vyzařován v adresáři *wwwroot.*
    * Pole `resolve.extensions` obsahuje *.js* pro import klienta SignalR JavaScript.

1. Vytvořte nový adresář *src* v kořenovém adresáři projektu pro uložení prostředků projektu na straně klienta.

1. Vytvořte *src/index.html* pomocí následujících značek.

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    Předchozí kód HTML definuje standardní značky domovské stránky.

1. Vytvořte nový adresář *src/css.* Jeho účelem je uložit soubory *css* projektu.

1. Vytvořte *src/css/main.css* s následujícími CSS:

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    Předchozí soubor *main.css* styly aplikace.

1. Vytvořte *src/tsconfig.json* s následujícím JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    Předchozí kód konfiguruje kompilátor TypeScript tak, aby vytvářel JavaScript kompatibilní s [ECMAScriptem](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *soubor src/index.ts* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí typescript načte odkazy na prvky DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje, `tbMessage`když uživatel zadá do textového pole. Funkce `send` je volána, když uživatel stiskne klávesu **Enter.**
    * `click`: Tato událost se aktivuje, když uživatel klepne na tlačítko **Odeslat.** Volá se funkce `send`.

## <a name="configure-the-app"></a>Konfigurace aplikace

1. V `Startup.Configure`aplikace přidejte volání do [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html.*  Soubor se zobrazuje bez ohledu na to, zda uživatel zadá úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Na konci `Startup.Configure`namapujte trasu */hub* do rozbočovače. `ChatHub` Nahradit kód, který zobrazuje *Hello World!* s následujícím řádkem: 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. V `Startup.ConfigureServices`, volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. Vytvořte nový adresář s názvem *Rozbočovače* v kořenovém projektu *SignalRWebPack/* pro uložení rozbočovače SignalR.

1. Vytvořte *hubhubs/ChatHub.cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Přidejte `using` do horní části *souboru Startup.cs* `ChatHub` následující příkaz, který chcete odkaz přeložit:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolení komunikace klienta a serveru

Aplikace v současné době zobrazuje základní formulář pro odesílání zpráv, ale ještě není funkční. Server naslouchá určité trase, ale s odeslanými zprávami neprovede žádné informace.

1. Spusťte následující příkaz v kořenovém adresáři projektu:

    ```console
    npm i @microsoft/signalr @types/node
    ```

    Předchozí příkaz nainstaluje:

     * [Klient TypeScript SignalR](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.
     * Definice typu TypeScript pro soubor Node.js, který umožňuje kontrolu typů Node.js v době kompilace.

1. Přidejte zvýrazněný kód do souboru *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru. Funkce `withUrl` konfiguruje adresu URL centra.

    SignalR umožňuje výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s `messageReceived` názvem můžete spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Poslech konkrétní zprávy lze provést `on` prostřednictvím funkce. Libovolný počet názvů zpráv lze poslouchat. Je také možné předat parametry zprávy, jako je jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří `div` se nový prvek se jménem autora `innerHTML` a obsahem zprávy v jeho atributu. Je přidán do hlavního `div` prvku zobrazujícího zprávy.

1. Nyní, když klient může přijímat zprávy, nakonfigurovat pro odesílání zpráv. Přidejte zvýrazněný kód do souboru *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets `send` vyžaduje volání metody. První parametr metody je název zprávy. Data zprávy obývají další parametry. V tomto příkladu je `newMessage` zpráva označená jako odeslaná na server. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud odeslání funguje, hodnota textového pole je vymazána.

1. Do třídy `ChatHub` přidejte metodu `NewMessage`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí kód vysílá přijaté zprávy všem připojeným uživatelům, jakmile je server přijme. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná za názvem zprávy stačí.

    V tomto příkladu klient TypeScript odešle zprávu označenou jako `newMessage`. Metoda Jazyka `NewMessage` C# očekává data odeslaná klientem. Volání [sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy jsou odesílány všem klientům připojeným k rozbočovači.

## <a name="test-the-app"></a>Otestování aplikace

Zkontrolujte, zda aplikace pracuje s následujícími kroky.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte webpack v režimu *vydání.* Pomocí okna **Konzola správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz. Pokud nejste v kořenovém `cd SignalRWebPack` adresáři projektu, zadejte před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **Ladění** > **start bez ladění** spustit aplikaci v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* je `http://localhost:<port_number>`doručen na adrese .

   Pokud se zvásňují chyby kompilace, zkuste řešení zavřít a znovu otevřít. 

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do adresního řádku.

1. Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.** Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spustit webpack v *režimu vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vytvořte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na localhost.

1. Otevřete prohlížeč `http://localhost:<port_number>`aplikace . Soubor *wwwroot/index.html* je doručena. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do adresního řádku.

1. Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.** Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.

---

![zpráva zobrazená v obou oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**
* [Sada .NET Core SDK 2.2 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [Sada .NET Core SDK 2.2 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [C# pro kód visual studia verze 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nakonfigurujte Visual Studio tak, aby v proměnné prostředí *PATH* hledalo npm. Ve výchozím nastavení používá visual studio verzi npm, která se nachází v instalačním adresáři. Postupujte podle následujících pokynů v sadě Visual Studio:

1. Přejděte na **nástroje** > **možnosti** > **projekty a řešení** > **webová správa** > balíčků externí webové **nástroje**.
1. Ze seznamu vyberte položku *$(PATH).* Kliknutím na šipku nahoru přesuňte položku na druhé místo v seznamu.

    ![Konfigurace sady Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace sady Visual Studio je dokončena. Je čas vytvořit projekt.

1. Použijte možnost nabídky **New** > **Nový** **projekt souboru** > a zvolte šablonu **ASP.NET základní webové aplikace.**
1. Pojmenujte projekt *SignalRWebPack*a vyberte **vytvořit**.
1. V rozevíracím souboru *Rozhraní .NET Core* vyberte z rozevíracího souboru cílového rozhraní a z rozevíracího souboru selektoru rozhraní vyberte *ASP.NET jádra 2.2.* Vyberte **prázdnou** šablonu a vyberte **Vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

V **integrovaném terminálu**spusťte následující příkaz :

```dotnetcli
dotnet new web -o SignalRWebPack
```

V adresáři *SignalRWebPack* je vytvořena prázdná ASP.NET webová aplikace Core, která cílí na rozhraní .NET Core.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace webového balíčku a jazyka TypeScript

Následující kroky nakonfigurují převod jazyka TypeScript na JavaScript a sdružování prostředků na straně klienta.

1. Chcete-li vytvořit soubor *package.json,* spusťte v kořenovém adresáři projektu následující příkaz:

    ```console
    npm init -y
    ```

1. Přidejte zvýrazněnou vlastnost do souboru *package.json:*

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    Nastavení `private` vlastnosti `true` zabránit upozornění instalace balíčku v dalším kroku.

1. Nainstalujte požadované balíčky npm. V kořenovém adresáři projektu spusťte následující příkaz:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Některé podrobnosti příkazu na vědomí:

    * Číslo verze následuje `@` za znaménkem pro každý název balíčku. npm nainstaluje tyto konkrétní verze balíčků.
    * Tato `-E` možnost zakáže výchozí chování npm psaní [sémantické versioning](https://semver.org/) operátory rozsahu *package.json*. Například `"webpack": "4.29.3"` se používá `"webpack": "^4.29.3"`místo . Tato možnost zabraňuje nechtěnému upgradu na novější verze balíčků.

    Viz [npm-install](https://docs.npmjs.com/cli/install) docs pro více podrobností.

1. Nahraďte `scripts` vlastnost souboru *package.json* následujícím kódem:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Některé vysvětlení skriptů:

    * `build`: Sdružuje prostředky na straně klienta v režimu vývoje a sleduje změny souborů. Sledovací proces souboru způsobí, že sada regenerovat pokaždé, když se změní soubor projektu. Tato `mode` možnost zakáže optimalizace výroby, jako je například třepání stromů a minifikace. Používejte `build` pouze ve vývoji.
    * `release`: Sdružuje prostředky na straně klienta v produkčním režimu.
    * `publish`: Spustí `release` skript pro sbalení prostředků na straně klienta v produkčním režimu. Volá příkaz publikování rozhraní [PŘÍKAZU](/dotnet/core/tools/dotnet-publish) .NET Core pro publikování aplikace.

1. Vytvořte soubor s názvem *webpack.config.js* v kořenovém adresáři projektu s následujícím kódem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    Předchozí soubor konfiguruje kompilaci webpacku. Některé podrobnosti o konfiguraci na vědomí:

    * Vlastnost `output` přepíše výchozí hodnotu *dist*. Balíček je místo toho vyzařován v adresáři *wwwroot.*
    * Pole `resolve.extensions` obsahuje *.js* pro import klienta SignalR JavaScript.

1. Vytvořte nový adresář *src* v kořenovém adresáři projektu pro uložení prostředků projektu na straně klienta.

1. Vytvořte *src/index.html* pomocí následujících značek.

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    Předchozí kód HTML definuje standardní značky domovské stránky.

1. Vytvořte nový adresář *src/css.* Jeho účelem je uložit soubory *css* projektu.

1. Vytvořte *src/css/main.css* s následujícími značkami:

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    Předchozí soubor *main.css* styly aplikace.

1. Vytvořte *src/tsconfig.json* s následujícím JSON:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    Předchozí kód konfiguruje kompilátor TypeScript tak, aby vytvářel JavaScript kompatibilní s [ECMAScriptem](https://wikipedia.org/wiki/ECMAScript) 5.

1. Vytvořte *soubor src/index.ts* s následujícím kódem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí typescript načte odkazy na prvky DOM a připojí dvě obslužné rutiny událostí:

    * `keyup`: Tato událost se aktivuje, `tbMessage` když uživatel zadá do textového pole. Funkce `send` je volána, když uživatel stiskne klávesu **Enter.**
    * `click`: Tato událost se aktivuje, když uživatel klepne na tlačítko **Odeslat.** Volá se funkce `send`.

## <a name="configure-the-aspnet-core-app"></a>Konfigurace aplikace ASP.NET Core

1. Kód uvedený v `Startup.Configure` metodě zobrazuje *Hello World!*. Nahraďte volání `app.Run` metody voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Předchozí kód umožňuje serveru vyhledat a obsluhovat soubor *index.html* bez ohledu na to, zda uživatel zadá úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices`. Přidá služby SignalR do projektu.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. Namapujte trasu `ChatHub` */hub* do rozbočovače. Na konec přidejte následující `Startup.Configure`řádky:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. Vytvořte nový adresář s názvem *Rozbočovače*v kořenovém adresáři projektu. Jeho účelem je uložit rozbočovač SignalR, který je vytvořen v dalším kroku.

1. Vytvořte *hubhubs/ChatHub.cs* s následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Chcete-li `ChatHub` přeložit odkaz, přidejte v horní části souboru *Startup.cs* následující kód:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolení komunikace klienta a serveru

Aplikace v současné době zobrazuje jednoduchý formulář pro odesílání zpráv. Nic se nestane, když se o to pokusíte. Server naslouchá určité trase, ale s odeslanými zprávami neprovede žádné informace.

1. Spusťte následující příkaz v kořenovém adresáři projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Předchozí příkaz nainstaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), který umožňuje klientovi odesílat zprávy na server.

1. Přidejte zvýrazněný kód do souboru *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. Třída `HubConnectionBuilder` vytvoří nového tvůrce pro konfiguraci připojení k serveru. Funkce `withUrl` konfiguruje adresu URL centra.

    SignalR umožňuje výměnu zpráv mezi klientem a serverem. Každá zpráva má konkrétní název. Například zprávy s `messageReceived` názvem můžete spustit logiku odpovědnou za zobrazení nové zprávy v zóně zprávy. Poslech konkrétní zprávy lze provést `on` prostřednictvím funkce. Můžete poslouchat libovolný počet názvů zpráv. Je také možné předat parametry zprávy, jako je jméno autora a obsah přijaté zprávy. Jakmile klient obdrží zprávu, vytvoří `div` se nový prvek se jménem autora `innerHTML` a obsahem zprávy v jeho atributu. Nová zpráva je přidána `div` do hlavního prvku zobrazujícího zprávy.

1. Nyní, když klient může přijímat zprávy, nakonfigurovat pro odesílání zpráv. Přidejte zvýrazněný kód do souboru *src/index.ts:*

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    Odeslání zprávy prostřednictvím připojení WebSockets `send` vyžaduje volání metody. První parametr metody je název zprávy. Data zprávy obývají další parametry. V tomto příkladu je `newMessage` zpráva označená jako odeslaná na server. Zpráva se skládá z uživatelského jména a vstupu uživatele z textového pole. Pokud odeslání funguje, hodnota textového pole je vymazána.

1. Do třídy `ChatHub` přidejte metodu `NewMessage`:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí kód vysílá přijaté zprávy všem připojeným uživatelům, jakmile je server přijme. Není nutné mít obecnou `on` metodu pro příjem všech zpráv. Metoda pojmenovaná za názvem zprávy stačí.

    V tomto příkladu klient TypeScript odešle zprávu označenou jako `newMessage`. Metoda Jazyka `NewMessage` C# očekává data odeslaná klientem. Volání [sendasync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy jsou odesílány všem klientům připojeným k rozbočovači.

## <a name="test-the-app"></a>Otestování aplikace

Zkontrolujte, zda aplikace pracuje s následujícími kroky.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spusťte webpack v režimu *vydání.* Pomocí okna **Konzola správce balíčků** spusťte v kořenovém adresáři projektu následující příkaz. Pokud nejste v kořenovém `cd SignalRWebPack` adresáři projektu, zadejte před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **Ladění** > **start bez ladění** spustit aplikaci v prohlížeči bez připojení ladicího programu. Soubor *wwwroot/index.html* je `http://localhost:<port_number>`doručen na adrese .

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do adresního řádku.

1. Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.** Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spustit webpack v *režimu vydání* spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vytvořte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```dotnetcli
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupní ji na localhost.

1. Otevřete prohlížeč `http://localhost:<port_number>`aplikace . Soubor *wwwroot/index.html* je doručena. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jinou instanci prohlížeče (libovolný prohlížeč). Vložte adresu URL do adresního řádku.

1. Zvolte některý z prohlížečů, něco zadejte do textového pole **Zpráva** a klikněte na tlačítko **Odeslat.** Jedinečné uživatelské jméno a zpráva se zobrazí na obou stránkách okamžitě.

---

![zpráva zobrazená v obou oknech prohlížeče](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
