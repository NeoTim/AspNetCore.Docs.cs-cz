---
title: Použití funkce SignalR technologie ASP.NET Core s TypeScript a Webpacku
author: ssougnez
description: V tomto kurzu nakonfigurujete Webpacku k vytvoření balíčku a sestavit webovou aplikaci funkce SignalR technologie ASP.NET Core, jejichž klienta je napsána v TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 04/23/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: b186dffd724fb2fed49bbc2587ec066db319dffc
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610436"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Použití funkce SignalR technologie ASP.NET Core s TypeScript a Webpacku

Podle [Sébastien Sougnez](https://twitter.com/ssougnez) a [Scott Addie](https://twitter.com/Scott_Addie)

[Webpacku](https://webpack.js.org/) vývojářům umožňuje vytvořit balíček a vytvářet prostředky webové aplikace na straně klienta. Tento kurz ukazuje použití Webpacku ve webové aplikaci funkce SignalR technologie ASP.NET Core, jejichž klienta je napsána v [TypeScript](https://www.typescriptlang.org/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Generování uživatelského rozhraní si první aplikaci funkce SignalR technologie ASP.NET Core
> * Konfigurace klienta SignalR TypeScript
> * Nakonfigurujte kanál sestavení pomocí Webpacku
> * Konfigurace serveru SignalR
> * Povolení komunikace mezi klientem a serverem

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro ASP.NET a web** pracovního vytížení
* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [C#pro verzi Visual Studio Code 1.17.1 nebo novější](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node.js](https://nodejs.org/) s [npm](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Vytvoření webové aplikace ASP.NET Core

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Konfigurace sady Visual Studio k vyhledání npm v *cesta* proměnné prostředí. Ve výchozím nastavení používá verzi npm nalezen v adresáři instalace sady Visual Studio. Postupujte podle těchto pokynů v sadě Visual Studio:

1. Přejděte do **nástroje** > **možnosti** > **projekty a řešení** > **webové správy balíčků**  >  **Externí webové nástroje**.
1. Vyberte *$(PATH)* položku seznamu. Klikněte na šipku nahoru přesunout položku na druhém pozici v seznamu.

    ![Visual Studio Configuration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Konfigurace Visual Studio je dokončen. Je čas vytvoření projektu.

1. Použití **souboru** > **nový** > **projektu** nabídku a vyberte **webové aplikace ASP.NET Core** Šablona.
1. Pojmenujte projekt *SignalRWebPack*a vyberte **OK**.
1. Vyberte *.NET Core* cílovém rozhraní rozevíracího seznamu a vyberte *2.2 technologie ASP.NET Core* z rozevírací seznam pro výběr framework. Vyberte **prázdný** šablony a vyberte **OK**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu **integrovaný terminál**:

```console
dotnet new web -o SignalRWebPack
```

Prázdné webové aplikace ASP.NET Core, jejichž cílem je .NET Core, je vytvořen v *SignalRWebPack* adresáře.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurace Webpacku a TypeScript

Následující kroky konfigurace převod TypeScript pro JavaScript a vytváření prostředků na straně klienta.

1. Spuštěním následujícího příkazu v kořenovém adresáři projektu vytvořte *package.json* souboru:

    ```console
    npm init -y
    ```

1. Přidat vlastnost zvýrazněný na *package.json* souboru:

    [!code-json[package.json](signalr-typescript-webpack/sample/snippets/package1.json?highlight=4)]

    Nastavení `private` vlastnost `true` brání upozornění instalace balíčku v dalším kroku.

1. Instalace balíčků npm vyžaduje. Spusťte následující příkaz z kořenového adresáře projektu:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Některé podrobnosti příkazu mějte na paměti:

    * Následuje číslo verze `@` přihlášení pro každý název balíčku. npm instalaci těchto verzí určitého balíčku.
    * `-E` Možnost zakáže npm, a výchozí chování psaní [sémantické správy verzí](https://semver.org/) rozsahu operátorům *package.json*. Například `"webpack": "4.29.3"` se použije namísto `"webpack": "^4.29.3"`. Tato možnost zabraňuje neúmyslnému upgrade na novější verze balíčku.

    Najdete v oficiální [npm install](https://docs.npmjs.com/cli/install) dokumentace pro více podrobností.

1. Nahradit `scripts` vlastnost *package.json* souboru následujícím fragmentem kódu:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Vysvětlení skriptů:

    * `build`: Obsahuje ureitou vašich prostředků na straně klienta v režimu pro vývoj a sleduje změny souborů. Sledovací proces souborů způsobí, že sada, která má znovu pokaždé, když změny souborů projektu. `mode` Možnost zakáže optimalizace produkčního prostředí, jako je například strom, přičemž a připravenost k minifikaci. Používejte pouze `build` ve vývoji.
    * `release`: Obsahuje prostředky na straně klienta ureitou v provozním režimu.
    * `publish`: Spuštění `release` skript k vytvoření balíčku sady prostředků na straně klienta v provozním režimu. Volá v .NET Core CLI [publikovat](/dotnet/core/tools/dotnet-publish) příkaz pro publikování aplikace.

1. Vytvořte soubor s názvem *webpack.config.js*, v kořenové složce projektu s následujícím obsahem:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/webpack.config.js)]

    Předchozí soubor nastaví Webpacku kompilace. Některé podrobnosti konfigurace mějte na paměti:

    * `output` Vlastnost přepíše výchozí hodnoty *dist*. Do sady prostředků místo toho je vygenerován v *wwwroot* adresáře.
    * `resolve.extensions` Pole zahrnuje *js* k importu jazyka JavaScript klienta SignalR.

1. Vytvořte nový *src* adresáře v kořenové složce projektu. Jeho účelem je ukládat prostředky projektu na straně klienta.

1. Vytvoření *src/index.html* s následujícím obsahem.

    [!code-html[index.html](signalr-typescript-webpack/sample/src/index.html)]

    Předchozí kód HTML definuje často používaný kód na domovské stránce.

1. Vytvořte nový *src/css* adresáře. Jeho účelem je pro uložení projektu *.css* soubory.

1. Vytvoření *src/css/main.css* s následujícím obsahem:

    [!code-css[main.css](signalr-typescript-webpack/sample/src/css/main.css)]

    Předchozí *main.css* styly souboru aplikace.

1. Vytvoření *src/tsconfig.json* s následujícím obsahem:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/src/tsconfig.json)]

    Předchozí kód konfiguruje kompilátor TypeScript vytvoří [ECMAScript](https://wikipedia.org/wiki/ECMAScript) JavaScript 5 kompatibilní.

1. Vytvoření *src/index.ts* s následujícím obsahem:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Předchozí TypeScript načte odkazy na elementy modelu DOM a připojí dvě obslužné rutiny události:

    * `keyup`: Tato událost je vyvoláno, když uživatel zadá něco do textového pole označeno `tbMessage`. `send` Funkce se volá, když uživatel stiskne klávesu **Enter** klíč.
    * `click`: Tato událost se aktivuje, když uživatel klikne **odeslat** tlačítko. `send` Funkce je volána.

## <a name="configure-the-aspnet-core-app"></a>Konfigurace aplikace ASP.NET Core

1. V kódu `Startup.Configure` metoda zobrazí *Hello World!*. Nahradit `app.Run` volání metody s voláními [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) a [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Předchozí kód umožňuje serveru k vyhledání a poskytovat *index.html* souborů, zda uživatel zadá jeho úplnou adresu URL nebo kořenovou adresu URL webové aplikace.

1. Volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) v `Startup.ConfigureServices` metody. Přidá do projektu služby SignalR.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_AddSignalR)]

1. Mapování */hub* směrovat `ChatHub` rozbočovače. Přidejte následující řádky na konci `Startup.Configure` metody:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseSignalR)]

1. Vytvořte nový adresář, s názvem *rozbočovače*, v kořenové složce projektu. Jeho účelem je ukládat rozbočovače SignalR, která je vytvořena v dalším kroku.

1. Vytvoření centra *Hubs/ChatHub.cs* následujícím kódem:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Přidejte následující kód v horní části *Startup.cs* soubor řešení `ChatHub` odkaz:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Povolení komunikace klienta a serveru

Aplikace nyní zobrazí jednoduchý formulář pro odesílání zpráv. Nic se nestane při pokusu provést. Server naslouchá na konkrétní postup, ale neprovede žádnou akci s odeslané zprávy.

1. Spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```console
    npm install @aspnet/signalr
    ```

    Předchozí příkaz nainstaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@aspnet/signalr), což umožňuje klientovi umožní odeslat zprávy na server.

1. Přidejte zvýrazněný kód, který *src/index.ts* souboru:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Předchozí kód podporuje příjem zpráv ze serveru. `HubConnectionBuilder` Třída vytvoří nového tvůrce pro konfiguraci připojení k serveru. `withUrl` Funkce nakonfiguruje adresu URL centra.

    Funkce SignalR umožňuje výměnu zpráv mezi klientem a serverem. Každá zpráva má specifický název. Například můžete mít zprávy s názvem `messageReceived` spouštěných logiku za zobrazení novou zprávu v zóně zprávy. Naslouchání na konkrétní zprávou, můžete to udělat pomocí `on` funkce. Může naslouchat na libovolný počet názvů zpráv. Je také možné předat parametry zpráv, jako je například jméno autora nebo obsah ve zprávě přijaté. Jakmile klient obdrží zprávu, nový `div` element je vytvořena s jméno autora a zpráva obsahu v jeho `innerHTML` atribut. Přidá se do hlavní `div` element zobrazení zprávy.

1. Teď, když klient může obdržet zprávu, nakonfigurujte ji k odesílání zpráv. Přidejte zvýrazněný kód, který *src/index.ts* souboru:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/src/index.ts?highlight=34-35)]

    Odeslání zprávy přes WebSockets připojení vyžaduje volání `send` metody. První parametr metody je název zprávy. Data zprávy se vyskytuje v podoblastech ostatní parametry. V tomto příkladu zprávu identifikována jako `newMessage` je odeslána na server. Zpráva se skládá z uživatelského jména a uživatelský vstup z textového pole. Pokud odesílání funguje, se vymaže hodnotu textového pole.

1. Přidat metodu zvýrazněný `ChatHub` třídy:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/Hubs/ChatHub.cs?highlight=8-11)]

    Předchozí kód vysílá přijaté zprávy na všechny připojené uživatele po server je obdrží. Už není potřeba mít obecný `on` metoda přijímat všechny zprávy. Metodu s názvem po přípony název zprávy.

    V tomto příkladu TypeScript klient odešle zprávu identifikována jako `newMessage`. C# `NewMessage` metoda očekává, že data odeslaná klientem. Je provedeno volání [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) metoda [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all). Přijaté zprávy jsou odeslány na všechny klienty připojené k rozbočovači.

## <a name="test-the-app"></a>Testování aplikace

Potvrďte, že aplikace funguje pomocí následujících kroků.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Spustit Webpacku *release* režimu. Použití **Konzola správce balíčků** okna, spusťte následující příkaz v kořenové složce projektu. Pokud nejste v kořenové složce projektu, zadejte `cd SignalRWebPack` před zadáním příkazu.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Vyberte **ladění** > **spustit bez ladění** pro spuštění aplikace v prohlížeči bez připojení ladicího programu. *Wwwroot/index.html* se použijí pro soubor `http://localhost:<port_number>`.

1. Otevřete jiná instance prohlížeče (libovolného prohlížeče). Vložte adresu URL do adresního řádku.

1. Zvolte buď prohlížeče, zadejte v něco **zpráva** textového pole a klikněte na tlačítko **odeslat** tlačítko. Jedinečné uživatelské jméno a zpráva se zobrazí na obě stránky okamžitě.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spustit Webpacku *release* režimu spuštěním následujícího příkazu v kořenovém adresáři projektu:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Sestavte a spusťte aplikaci spuštěním následujícího příkazu v kořenovém adresáři projektu:

    ```console
    dotnet run
    ```

    Webový server spustí aplikaci a zpřístupňuje je v místním hostiteli.

1. Otevřete prohlížeč na `http://localhost:<port_number>`. *Wwwroot/index.html* soubor je předán. Zkopírujte adresu URL z panelu Adresa.

1. Otevřete jiná instance prohlížeče (libovolného prohlížeče). Vložte adresu URL do adresního řádku.

1. Zvolte buď prohlížeče, zadejte v něco **zpráva** textového pole a klikněte na tlačítko **odeslat** tlačítko. Jedinečné uživatelské jméno a zpráva se zobrazí na obě stránky okamžitě.

---

![zpráva zobrazená v prohlížeči windows](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

## <a name="additional-resources"></a>Další zdroje

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
