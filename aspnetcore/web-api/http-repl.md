---
title: Testování webových rozhraní API pomocí protokolu HTTP REPL
author: scottaddie
description: Naučte se používat globální nástroj HTTP REPL .NET Core k procházení a testování ASP.NET Core webového rozhraní API.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/http-repl
ms.openlocfilehash: ead745ae8843173bb25b94672005cc6ce295db2e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403375"
---
# <a name="test-web-apis-with-the-http-repl"></a>Testování webových rozhraní API pomocí protokolu HTTP REPL

[Scott Addie](https://twitter.com/Scott_Addie)

Smyčka HTTP Read-Eval-Print (REPL) je:

* Podporuje se odlehčený nástroj příkazového řádku pro více platforem, který podporuje všude, kde je podporováno rozhraní .NET Core.
* Slouží k provádění požadavků HTTP na testování ASP.NET Core webových rozhraní API (a webových rozhraní API non-ASP.NET Core) a zobrazení jejich výsledků.
* Je možné testovat webová rozhraní API hostovaná v jakémkoli prostředí, včetně localhost a Azure App Service.

Podporovány jsou následující [Příkazy protokolu HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :

* [DSTRANIT](#test-http-delete-requests)
* [Čtěte](#test-http-get-requests)
* [ZÁHLAVÍ](#test-http-head-requests)
* [NASTAVENÍ](#test-http-options-requests)
* [POUŽITA](#test-http-patch-requests)
* [SPUŠTĚNÍ](#test-http-post-requests)
* [PŘEVÉST](#test-http-put-requests)

Pokud chcete postup sledovat, [Zobrazte si ukázkové ASP.NET Core webové rozhraní API nebo si ho stáhněte](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Instalace

Pro instalaci HTTP REPL spusťte následující příkaz:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .

## <a name="usage"></a>Využití

Po úspěšné instalaci nástroje spusťte následující příkaz, který spustí HTTP REPL:

```console
httprepl
```

Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:

```console
httprepl -h
```

```console
httprepl --help
```

Zobrazí se následující výstup:

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

Příkaz HTTP REPL nabízí dokončování příkazů. Stisknutí klávesy <kbd>TAB</kbd> prochází seznam příkazů, které dokončí znaky nebo koncový bod rozhraní API, který jste zadali. Následující části popisují dostupné příkazy rozhraní příkazového řádku.

## <a name="connect-to-the-web-api"></a>Připojení k webovému rozhraní API

Připojte se k webovému rozhraní API spuštěním následujícího příkazu:

```console
httprepl <ROOT URI>
```

`<ROOT URI>`je základní identifikátor URI pro webové rozhraní API. Například:

```console
httprepl https://localhost:5001
```

Případně spusťte následující příkaz kdykoli, když je spuštěn protokol HTTP REPL:

```console
connect <ROOT URI>
```

Například:

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a>Ruční odkazování na dokument Swagger pro webové rozhraní API

Výše uvedený příkaz Connect se pokusí najít dokument Swagger automaticky. Pokud z nějakého důvodu to není možné, můžete zadat identifikátor URI dokumentu Swagger pro webové rozhraní API pomocí `--swagger` Možnosti:

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

Například:

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Navigace v rozhraní Web API

### <a name="view-available-endpoints"></a>Zobrazit dostupné koncové body

Pokud chcete zobrazit seznam různých koncových bodů (řadičů) na aktuální cestě adresy webového rozhraní API, spusťte `ls` `dir` příkaz nebo:

```console
https://localhot:5001/~ ls
```

Zobrazí se následující výstupní formát:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Předchozí výstup ukazuje, že jsou k dispozici dva řadiče: `Fruits` a `People` . Oba řadiče podporují operace HTTP GET a POST bez parametrů.

Přechod na konkrétní kontroler odhalí více podrobností. Například výstup následujícího příkazu ukazuje, že `Fruits` kontroler také podporuje operace HTTP GET, PUT a DELETE. Každá z těchto operací očekává `id` v trase parametr:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

Případně spusťte `ui` příkaz a otevřete stránku uživatelského rozhraní Swagger webového rozhraní API v prohlížeči. Například:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Přejít na koncový bod

Pokud chcete přejít na jiný koncový bod webového rozhraní API, spusťte `cd` příkaz:

```console
https://localhost:5001/~ cd people
```

Cesta za `cd` příkazem nerozlišuje velká a malá písmena. Zobrazí se následující výstupní formát:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Přizpůsobení REPL HTTP

Výchozí [barvy](#set-color-preferences) REPL http je možné přizpůsobit. Kromě toho lze definovat [výchozí textový editor](#set-the-default-text-editor) . Předvolby HTTP REPL jsou trvale v rámci aktuální relace a jsou v budoucích relacích dodrženy. Po úpravě jsou předvolby uložené v následujícím souboru:

# <a name="linux"></a>[Linux](#tab/linux)

*% HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*% HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*% USERPROFILE% \\ . httpreplprefs*

---

Soubor *. httpreplprefs* je načten při spuštění a není monitorován pro změny za běhu. Ruční úpravy souboru se projeví až po restartování nástroje.

### <a name="view-the-settings"></a>Zobrazit nastavení

Dostupná nastavení zobrazíte spuštěním `pref get` příkazu. Například:

```console
https://localhost:5001/~ pref get
```

Předchozí příkaz zobrazí dostupné páry klíč-hodnota:

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Nastavit předvolby barev

Vybarvení odpovědi je aktuálně podporováno pouze pro JSON. Chcete-li přizpůsobit výchozí barevné vybarvení nástroje HTTP REPL, vyhledejte klíč odpovídající barvě, který má být změněn. Pokyny k vyhledání klíčů najdete v části [zobrazení nastavení](#view-the-settings) . Například `colors.json` hodnotu klíče můžete změnit z na následujícím `Green` `White` způsobem:

```console
https://localhost:5001/people~ pref set colors.json White
```

Mohou být použity pouze [povolené barvy](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) . Následné požadavky HTTP zobrazují výstup s novými zvýrazněními.

Pokud nejsou nastavené určité klíče barev, považují se za obecnější klíče. K předvedení tohoto nouzového chování Vezměte v úvahu následující příklad:

* Pokud nemá `colors.json.name` hodnotu, `colors.json.string` je použita hodnota.
* Pokud nemá `colors.json.string` hodnotu, `colors.json.literal` je použita hodnota.
* Pokud nemá `colors.json.literal` hodnotu, `colors.json` je použita hodnota. 
* Pokud `colors.json` hodnota nemá hodnotu, použije se výchozí barva textu () příkazového prostředí `AllowedColors.None` .

### <a name="set-indentation-size"></a>Nastavit velikost odsazení

Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON. Výchozí velikost jsou dvě mezery. Například:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč. Například pokud chcete vždy použít čtyři mezery:

```console
pref set formatting.json.indentSize 4
```

Následující odpovědi dodržují nastavení čtyř mezer:

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Nastavit výchozí textový editor

Ve výchozím nastavení nemá protokol HTTP REPL nakonfigurovaný žádný textový editor pro použití. Chcete-li otestovat metody webového rozhraní API, které vyžadují tělo požadavku HTTP, musí být nastaven výchozí textový editor. Nástroj HTTP REPL spustí nakonfigurovaný textový editor pro výhradní účely vytváření textu žádosti. Spusťte následující příkaz, který nastaví upřednostňovaný textový editor jako výchozí:

```console
pref set editor.command.default "<EXECUTABLE>"
```

V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textového editoru. Například spusťte následující příkaz, který nastaví Visual Studio Code jako výchozí textový editor:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Chcete-li spustit výchozí textový editor s konkrétními argumenty rozhraní příkazového řádku, nastavte `editor.command.default.arguments` klíč. Předpokládejme například, že Visual Studio Code je výchozím textovým editorem a chcete, aby se v nové relaci s vypnutými rozšířeními otevřela Visual Studio Code HTTP REPL. Spusťte následující příkaz:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a>Nastavení vyhledávacích cest Swagger

Ve výchozím nastavení má REPL HTTP sadu relativních cest, které používá k nalezení dokumentu Swagger při provádění `connect` příkazu bez `--swagger` Možnosti. Tyto relativní cesty jsou kombinovány s kořenovou a základní cestou specifikovanou v `connect` příkazu. Výchozí relativní cesty jsou:

- *swagger.jsna*
- *Swagger/v1/swagger.jsna*
- */swagger.jsna*
- */Swagger/v1/swagger.js*

Pokud chcete ve svém prostředí použít jinou sadu vyhledávacích cest, nastavte `swagger.searchPaths` Předvolby. Hodnota musí být seznam relativních cest oddělených svislým kanálem. Například:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a>Testování požadavků HTTP GET

### <a name="synopsis"></a>Stručný obsah

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

Pro příkaz jsou k dispozici následující možnosti `get` :

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Příklad

Vystavení požadavku HTTP GET:

1. Spusťte `get` příkaz na koncovém bodu, který ho podporuje:

    ```console
    https://localhost:5001/people~ get
    ```

    Předchozí příkaz zobrazí následující výstupní formát:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. Načtěte určitý záznam předáním parametru `get` příkazu:

    ```console
    https://localhost:5001/people~ get 2
    ```

    Předchozí příkaz zobrazí následující výstupní formát:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a>Test požadavků HTTP POST

### <a name="synopsis"></a>Stručný obsah

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Příklad

Vystavení požadavku HTTP POST:

1. Spusťte `post` příkaz na koncovém bodu, který ho podporuje:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON. Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP. Například:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .

1. Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Uložte soubor *. tmp* a ukončete textový editor. V příkazovém prostředí se zobrazí následující výstup:

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a>Test požadavků HTTP PUT

### <a name="synopsis"></a>Stručný obsah

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Příklad

Vydání požadavku HTTP PUT:

1. *Volitelné*: `get` pro zobrazení dat před úpravou spusťte příkaz:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Spusťte `put` příkaz na koncovém bodu, který ho podporuje:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON. Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP. Například:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .

1. Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Uložte soubor *. tmp* a ukončete textový editor. V příkazovém prostředí se zobrazí následující výstup:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Volitelné*: vydejte `get` příkaz pro zobrazení úprav. Například pokud jste v textovém editoru zadali "třešně", `get` vrátí následující:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a>Test požadavků HTTP DELETE

### <a name="synopsis"></a>Stručný obsah

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Příklad

Postup při vystavení žádosti o odstranění protokolu HTTP:

1. *Volitelné*: `get` pro zobrazení dat před úpravou spusťte příkaz:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Spusťte `delete` příkaz na koncovém bodu, který ho podporuje:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    Předchozí příkaz zobrazí následující výstupní formát:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Volitelné*: vydejte `get` příkaz pro zobrazení úprav. V tomto příkladu, `get` vrátí následující:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a>Test požadavků na opravy HTTP

### <a name="synopsis"></a>Stručný obsah

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Test požadavků HTTP HEAD

### <a name="synopsis"></a>Stručný obsah

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Požadavky na test možností protokolu HTTP

### <a name="synopsis"></a>Stručný obsah

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Nastavení hlaviček požadavků HTTP

Pokud chcete nastavit hlavičku požadavku HTTP, použijte jeden z následujících přístupů:

* Nastavte inline s požadavkem HTTP. Například:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    V případě předchozího přístupu vyžaduje každá samostatná Hlavička požadavku HTTP vlastní `-h` možnost.

* Nastaveno před odesláním požadavku HTTP. Například:

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    Při nastavování hlavičky před odesláním žádosti zůstane záhlaví nastavené na dobu trvání relace příkazového prostředí. Pokud chcete záhlaví vymazat, zadejte prázdnou hodnotu. Například:
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Test zabezpečených koncových bodů

HTTP REPL podporuje testování zabezpečených koncových bodů dvěma způsoby: prostřednictvím výchozích přihlašovacích údajů přihlášeného uživatele nebo pomocí hlaviček požadavků HTTP. 

### <a name="default-credentials"></a>Výchozí pověření

Vezměte v úvahu scénář, ve kterém je webové rozhraní API, které testujete, hostované ve službě IIS a zabezpečené s ověřováním systému Windows. Chcete, aby přihlašovací údaje uživatele, který spustil nástroj, pokračovaly v testování koncových bodů HTTP. Předání výchozích přihlašovacích údajů přihlášeného uživatele:

1. Nastavte `httpClient.useDefaultCredentials` Předvolby na `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Před odesláním dalšího požadavku webovému rozhraní API ukončete nástroj a restartujte ho.

### <a name="http-request-headers"></a>Hlavičky požadavku HTTP

Příklady podporovaných schémat ověřování a autorizace zahrnují základní ověřování, tokeny nosiče JWT a ověřování hodnotou hash. Například můžete odeslat nosný token do koncového bodu pomocí následujícího příkazu:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Pokud chcete získat přístup ke koncovému bodu hostovanému v Azure nebo použít [Azure REST API](/rest/api/azure/), potřebujete nosný token. Pomocí následujícího postupu můžete získat nosný token pro předplatné Azure prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/). REPL HTTP nastavuje nosný token v hlavičce požadavku HTTP a načítá seznam Azure App Service Web Apps.

1. Přihlaste se k Azure:

    ```azurecli
    az login
    ```

1. Pomocí následujícího příkazu Získejte ID vašeho předplatného:

    ```azurecli
    az account show --query id
    ```

1. Zkopírujte ID předplatného a spusťte následující příkaz:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Získejte token nosiče pomocí následujícího příkazu:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Připojte se k Azure REST API přes REPL HTTP:

    ```console
    httprepl https://management.azure.com
    ```

1. Nastavte `Authorization` hlavičku požadavku http:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Přejděte k předplatnému:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Získejte seznam Azure App Service Web Apps vašich předplatných:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    Zobrazí se následující odpověď:

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>Přepnout zobrazení požadavku HTTP

Ve výchozím nastavení se zobrazí potlačení požadavku HTTP na odeslání. Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.

### <a name="enable-request-display"></a>Povolit zobrazení žádosti

Spuštěním příkazu Zobrazte požadavek HTTP, který odesíláte `echo on` . Například:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

Následující požadavky HTTP v aktuální relaci zobrazují hlavičky žádosti. Například:

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a>Zakázat zobrazení žádosti

Potlačit zobrazení požadavku HTTP odesílaného spuštěním `echo off` příkazu Například:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Spuštění skriptu

Pokud často spustíte stejnou sadu příkazů HTTP REPL, zvažte jejich uložení do textového souboru. Příkazy v souboru přebírají stejnou formu, jakou byly provedeny ručně na příkazovém řádku. Příkazy lze spustit v dávce způsobem pomocí `run` příkazu. Například:

1. Vytvoří textový soubor obsahující sadu příkazů s oddělovači na nový řádek. K ilustraci zvažte *people-script.txt* soubor obsahující následující příkazy:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Spusťte `run` příkaz a předejte cestu k textovému souboru. Například:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    Zobrazí se výstup:

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a>Vymazat výstup

Pokud chcete odebrat veškerý výstup napsaný do příkazového prostředí nástrojem HTTP REPL, spusťte `clear` příkaz nebo `cls` . K ilustraci si představte, že příkazové prostředí obsahuje následující výstup:

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Výstup vymažete spuštěním následujícího příkazu:

```console
https://localhost:5001/~ clear
```

Po spuštění předchozího příkazu obsahuje příkazové prostředí pouze následující výstup:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Další zdroje

* [REST API žádosti](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Úložiště GitHub HTTP REPL](https://github.com/dotnet/HttpRepl)
