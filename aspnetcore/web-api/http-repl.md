---
title: Test webové rozhraní API pomocí protokolu HTTP REPL
author: scottaddie
description: Další informace o použití protokolu HTTP REPL nástroje rozhraní .NET Core globální procházení a otestovat webovému rozhraní API ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/12/2019
uid: web-api/http-repl
ms.openlocfilehash: 920561fc86ed90eef64af49fa5936a080a096de2
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67919261"
---
# <a name="test-web-apis-with-the-http-repl"></a>Test webové rozhraní API pomocí protokolu HTTP REPL

Podle [Scott Addie](https://twitter.com/Scott_Addie)

Je smyčka čtení protokolu HTTP-Eval-Print (REPL):

* Zjednodušené, multiplatformní nástroje příkazového řádku, který všude, kde se podporuje .NET Core je podporována.
* Používá se pro uskutečňování požadavků HTTP pro testování webového rozhraní API ASP.NET Core a zobrazit jejich výsledky.

Následující [příkazy HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) jsou podporovány:

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HLAVNÍ](#test-http-head-requests)
* [MOŽNOSTI](#test-http-options-requests)
* [PATCH](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

Pokud chcete postup sledovat, [zobrazení nebo stažení ukázkové webové rozhraní API ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([stažení](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Požadavky

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Instalace

K instalaci HTTP REPL, spusťte následující příkaz:

```console
dotnet tool install -g dotnet-httprepl
    --version 2.2.0-*
    --add-source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
```

A [globální nástroje .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) se instaluje z [dotnet httprepl](https://dotnet.myget.org/feed/dotnet-core/package/nuget/dotnet-httprepl
) balíček NuGet hostovaným na MyGet.

## <a name="usage"></a>Použití

Po úspěšné instalaci nástroje, spuštěním následujícího příkazu spusťte HTTP REPL:

```console
dotnet httprepl
```

Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

Zobrazí se následující výstup:

```console
Usage: dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  --help - Show help information.

REPL Commands:

HTTP Commands:
Use these commands to execute requests against your application.

GET            Issues a GET request.
POST           Issues a POST request.
PUT            Issues a PUT request.
DELETE         Issues a DELETE request.
PATCH          Issues a PATCH request.
HEAD           Issues a HEAD request.
OPTIONS        Issues an OPTIONS request.

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`


Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Set the URI, relative to your base if set, of the Swagger document for this API. e.g. `set swagger /swagger/v1/swagger.json`
ls             Show all endpoints for the current path.
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`.

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell.
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands.
exit           Exit the shell.

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\Program Files\Microsoft VS Code\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line.
ui             Displays the Swagger UI page, if available, in the default browser.

Use help <COMMAND> to learn more details about individual commands. e.g. `help get`
```

HTTP REPL nabízí dokončení příkazu. Stisknutím klávesy <kbd>kartu</kbd> klíč Iteruje přes seznam příkazů, které dokončení znaky nebo koncový bod rozhraní API, který jste zadali. Následující oddíly popisují dostupné příkazy rozhraní příkazového řádku.

## <a name="connect-to-the-web-api"></a>Připojení k webovému rozhraní API

Připojení k webovému rozhraní API spuštěním následujícího příkazu:

```console
dotnet httprepl <BASE URI>
```

`<BASE URI>` je základní identifikátor URI pro webové rozhraní API. Příklad:

```console
dotnet httprepl https://localhost:5001
```

Alternativně spusťte následující příkaz kdykoli během HTTP REPL:

```console
set base <BASE URI>
```

Příklad:

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a>Přejděte k dokumentu Swagger pro webové rozhraní API

Chcete-li prověřit správně webového rozhraní API, nastavte relativní identifikátor URI k dokumentu Swagger pro webové rozhraní API. Spusťte následující příkaz:

```console
set swagger <RELATIVE URI>
```

Příklad:

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Přejděte webového rozhraní API

### <a name="view-available-endpoints"></a>Zobrazit dostupné koncové body

Chcete-li seznam různých koncových bodů (kontrolery) v aktuální cestě z webové adresy rozhraní API, spusťte `ls` nebo `dir` příkaz:

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

Tento výstup označuje, že jsou k dispozici dva řadiče: `Fruits` a `People`. Oba kontrolery podporují konstruktor bez parametrů operace HTTP GET a POST.

Přejděte do konkrétní ovladač odhalí podrobněji. Například následující příkaz zobrazí výstup `Fruits` řadič také podporuje operace HTTP GET, PUT a DELETE. Každá z těchto operací očekává, že `id` parametr trasa:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

Další možností je spustit `ui` příkaz pro otevření stránky uživatelské rozhraní Swagger webového rozhraní API v prohlížeči. Příklad:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Přejděte na koncový bod

Chcete-li přejít k jinému koncovému bodu na webové rozhraní API, spusťte `cd` příkaz:

```console
https://localhost:5001/~ cd people
```

Následující cesta `cd` příkaz velká a malá písmena. Zobrazí se následující výstupní formát:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Přizpůsobení HTTP REPL

Výchozí HTTP REPL [barvy](#set-color-preferences) lze přizpůsobit. Kromě toho [výchozí text editor](#set-the-default-text-editor) lze definovat. Předvolby protokolu HTTP REPL se zachová pro aktuální relaci a jsou respektovat v budoucích relacích. Jakmile upravit, předvolby jsou uložené v následujícím souboru:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macostabmacos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

*.Httpreplprefs* je soubor načten při spuštění a nemonitorováno pro změny za běhu. Ruční změny do souboru se projeví až po restartování nástroj.

### <a name="view-the-settings"></a>Zobrazit nastavení

Pokud chcete zobrazit dostupná nastavení, spusťte `pref get` příkazu. Příklad:

```console
https://localhost:5001/~ pref get
```

Předchozí příkaz zobrazí dostupné páry klíč hodnota:

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

### <a name="set-color-preferences"></a>Nastavení barev předvoleb

Zabarvení odpovědi se aktuálně podporuje pro formát JSON pouze. Přizpůsobení výchozí nástroj HTTP REPL barevné zvýrazňování, vyhledejte klíč odpovídající barvu, která se změnit. Pokyny o tom, jak najít klíčů najdete v tématu [zobrazit nastavení](#view-the-settings) oddílu. Například změnit `colors.json` hodnotu z klíče `Green` k `White` následujícím způsobem:

```console
https://localhost:5001/people~ pref set colors.json White
```

Pouze [povolené barvy](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) mohou být použity. Zobrazit výstup s nové barevné zvýraznění žádostí následné HTTP.

Pokud nejsou nastavené určitá barva klíče, jsou považovány za obecnější klíče. Abychom si předvedli tuto náhradní chování, zvažte následující příklad:

* Pokud `colors.json.name` nemá žádnou hodnotu, `colors.json.string` se používá.
* Pokud `colors.json.string` nemá žádnou hodnotu, `colors.json.literal` se používá.
* Pokud `colors.json.literal` nemá žádnou hodnotu, `colors.json` se používá. 
* Pokud `colors.json` nemá žádnou hodnotu, výchozí barva textu příkazového okna (`AllowedColors.None`) se používá.

### <a name="set-indentation-size"></a>Velikost odsazení sady

Přizpůsobení velikosti odpovědi odsazení aktuálně podporuje pro formát JSON pouze. Výchozí velikost je dvě mezery. Příklad:

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

Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč. Například chcete vždy používal čtyři mezery:

```console
pref set formatting.json.indentSize 4
```

Odpovědi na následné respektovat nastavení čtyři mezery:

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

### <a name="set-indentation-size"></a>Velikost odsazení sady

Přizpůsobení velikosti odpovědi odsazení aktuálně podporuje pro formát JSON pouze. Výchozí velikost je dvě mezery. Příklad:

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

Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč. Například chcete vždy používal čtyři mezery:

```console
pref set formatting.json.indentSize 4
```

Odpovědi na následné respektovat nastavení čtyři mezery:

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

Ve výchozím nastavení HTTP REPL nemá žádný textový editor, který je nakonfigurován pro použití. K otestování webové rozhraní API metody vyžadující textu požadavku HTTP, musí být nastavena výchozí textového editoru. K jedinému účelu – sestavování textu žádosti se spustí nástroj HTTP REPL nakonfigurované textového editoru. Spusťte následující příkaz pro nastavení upřednostňovaném textovém editoru jako výchozí:

```console
pref set editor.command.default "<EXECUTABLE>"
```

V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textový editor. Například spusťte následující příkaz pro nastavení jako výchozí text editor Visual Studio Code:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Chcete-li spustit výchozí text editor s konkrétní argumenty příkazového řádku, nastavte `editor.command.default.arguments` klíč. Předpokládejme například, Visual Studio Code je výchozí textový editor a mají vždy REPL HTTP otevřete Visual Studio Code v nové relaci s příponami zakázán. Spusťte následující příkaz:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a>Testování požadavky HTTP GET

### <a name="synopsis"></a>Souhrn

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

Jsou k dispozici pro následující možnosti `get` příkaz:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Příklad

Vydání požadavku HTTP GET:

1. Spustit `get` příkaz na koncový bod, který to podporuje:

    ```console
    https://localhost:5001/people~ get
    ```

    Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:

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

1. Vyhledat konkrétní záznam tím, že předáte parametr, který se `get` příkaz:

    ```console
    https://localhost:5001/people~ get 2
    ```

    Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:

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

## <a name="test-http-post-requests"></a>Testování požadavky HTTP POST

### <a name="synopsis"></a>Souhrn

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Příklad

Vydání požadavku HTTP POST:

1. Spustit `post` příkaz na koncový bod, který to podporuje:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    V předchozím příkazu `Content-Type` hlavičku požadavku HTTP je nastaven označující typ média textu požadavku JSON. Otevře se editor výchozí text *tmp* soubor pomocí šablony JSON představující obsahu žádosti HTTP. Příklad:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Pokud chcete nastavit výchozí textového editoru, najdete v článku [nastavit výchozí text editor](#set-the-default-text-editor) části.

1. Upravte šablonu JSON, aby byly splněny požadavky na ověření modelu:

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. Uložit *tmp* soubor a zavřete textového editoru. V příkazovém prostředí se zobrazí následující výstup:

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

## <a name="test-http-put-requests"></a>Testovací žádosti HTTP PUT

### <a name="synopsis"></a>Souhrn

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Příklad

Vydání požadavku HTTP PUT:

1. *Volitelné*: Spustit `get` příkazu zobrazíte data před její změny:

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

1. Run the `put` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    V předchozím příkazu `Content-Type` hlavičku požadavku HTTP je nastaven označující typ média textu požadavku JSON. Otevře se editor výchozí text *tmp* soubor pomocí šablony JSON představující obsahu žádosti HTTP. Příklad:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Pokud chcete nastavit výchozí textového editoru, najdete v článku [nastavit výchozí text editor](#set-the-default-text-editor) části.

1. Upravte šablonu JSON, aby byly splněny požadavky na ověření modelu:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Uložit *tmp* soubor a zavřete textového editoru. V příkazovém prostředí se zobrazí následující výstup:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Volitelné*: Problém `get` příkazu zobrazte změny. Pokud jste zadali "Výběru určitých položek" v textovém editoru, například `get` vrátí následující:

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

## <a name="test-http-delete-requests"></a>Testovací žádosti HTTP DELETE

### <a name="synopsis"></a>Souhrn

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Příklad

Vydání požadavku HTTP DELETE:

1. *Volitelné*: Spustit `get` příkazu zobrazíte data před její změny:

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

1. Run the `delete` command on an endpoint that supports it:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Volitelné*: Problém `get` příkazu zobrazte změny. V tomto příkladu `get` vrátí následující:

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

## <a name="test-http-patch-requests"></a>Testovací žádosti HTTP PATCH

### <a name="synopsis"></a>Souhrn

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testování požadavku HTTP HEAD

### <a name="synopsis"></a>Souhrn

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testovací žádosti HTTP OPTIONS

### <a name="synopsis"></a>Souhrn

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Arguments

`PARAMETER`

Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.

### <a name="options"></a>Možnosti

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Nastavit hlavičku žádosti HTTP

Pokud chcete nastavit hlavičku žádosti HTTP, použijte jednu z následujících postupů:

1. Nastavit pomocí vložených s požadavkem HTTP. Příklad:

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  S předchozím přístup každý jedinečných hlavičku požadavku HTTP vyžaduje svou vlastní `-h` možnost.

1. Nastavit před odesláním požadavku HTTP. Příklad:

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  Při nastavení záhlaví před odesláním požadavku, zůstane po dobu trvání relace příkazového prostředí nastavte záhlaví. Pokud chcete vymazat záhlaví, zadejte prázdnou hodnotu. Příklad:

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a>Přepnout zobrazení požadavku HTTP

Ve výchozím nastavení je potlačeno zobrazit odeslání požadavku HTTP. Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.

### <a name="enable-request-display"></a>Povolit žádosti o zobrazení

Zobrazit spuštěním odeslání požadavku HTTP `echo on` příkazu. Příklad:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

Následné žádosti protokolu HTTP v aktuální relaci zobrazit záhlaví požadavku. Příklad:

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

Potlačit zobrazení spuštěním odeslání požadavku HTTP `echo off` příkazu. Příklad:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Spuštění skriptu

Pokud často spustit stejnou sadu příkazů HTTP REPL, zvažte jejich ukládáním do textového souboru. Příkazy v souboru stejnou formu jako ručně provést na příkazovém řádku. Příkazy mohou být provedeny v dávkové způsobem pomocí `run` příkazu. Příklad:

1. Vytvořte textový soubor obsahující sadu příkazů oddělených znaku nového řádku. Pro ilustraci, vezměte v úvahu *lidé skript.txt* soubor, který obsahuje následující příkazy:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Spustit `run` příkazu předávání v cestě k souboru text. Příklad:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    Zobrazí se následující výstup:

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

Chcete-li odebrat všechny výstupu zapsaného do příkazového okna nástroje HTTP REPL, spusťte `clear` nebo `cls` příkazu. Pro ilustraci si představte, že příkazový řádek nástroje obsahuje následující výstup:

```console
dotnet httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Spusťte následující příkaz k vymazání výstup:

```console
https://localhost:5001/~ clear
```

Příkazové okno po spuštění předchozího příkazu, obsahuje pouze následující výstup:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Další zdroje

* [Požadavky rozhraní REST API](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Úložiště HTTP REPL GitHub](https://github.com/aspnet/HttpRepl)
