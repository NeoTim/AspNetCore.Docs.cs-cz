---
title: Testování webových rozhraní API pomocí protokolu HTTP REPL
author: scottaddie
description: Naučte se používat globální nástroj HTTP REPL .NET Core k procházení a testování ASP.NET Core webového rozhraní API.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/23/2019
uid: web-api/http-repl
ms.openlocfilehash: 29a29b98b31ac8e48545789928a8e83573ca9af5
ms.sourcegitcommit: b1662379b6aeca66ef6c696bd2300c81aa74a514
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68485800"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="d1b92-103">Testování webových rozhraní API pomocí protokolu HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d1b92-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="d1b92-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d1b92-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d1b92-105">Smyčka HTTP Read-Eval-Print (REPL) je:</span><span class="sxs-lookup"><span data-stu-id="d1b92-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="d1b92-106">Podporuje se odlehčený nástroj příkazového řádku pro více platforem, který podporuje všude, kde je podporováno rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d1b92-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="d1b92-107">Slouží k provádění požadavků HTTP na testování ASP.NET Core webových rozhraní API a zobrazení jejich výsledků.</span><span class="sxs-lookup"><span data-stu-id="d1b92-107">Used for making HTTP requests to test ASP.NET Core web APIs and view their results.</span></span>

<span data-ttu-id="d1b92-108">Podporovány jsou následující [Příkazy protokolu HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :</span><span class="sxs-lookup"><span data-stu-id="d1b92-108">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="d1b92-109">DSTRANIT</span><span class="sxs-lookup"><span data-stu-id="d1b92-109">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="d1b92-110">GET</span><span class="sxs-lookup"><span data-stu-id="d1b92-110">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="d1b92-111">ZÁHLAVÍ</span><span class="sxs-lookup"><span data-stu-id="d1b92-111">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="d1b92-112">NASTAVENÍ</span><span class="sxs-lookup"><span data-stu-id="d1b92-112">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="d1b92-113">POUŽITA</span><span class="sxs-lookup"><span data-stu-id="d1b92-113">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="d1b92-114">POST</span><span class="sxs-lookup"><span data-stu-id="d1b92-114">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="d1b92-115">PUT</span><span class="sxs-lookup"><span data-stu-id="d1b92-115">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="d1b92-116">Pokud chcete postup sledovat, [Zobrazte si ukázkové ASP.NET Core webové rozhraní API nebo si ho stáhněte](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d1b92-116">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d1b92-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d1b92-117">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="d1b92-118">Instalace</span><span class="sxs-lookup"><span data-stu-id="d1b92-118">Installation</span></span>

<span data-ttu-id="d1b92-119">Pro instalaci HTTP REPL spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d1b92-119">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="d1b92-120">[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-120">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="d1b92-121">Použití</span><span class="sxs-lookup"><span data-stu-id="d1b92-121">Usage</span></span>

<span data-ttu-id="d1b92-122">Po úspěšné instalaci nástroje spusťte následující příkaz, který spustí HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d1b92-122">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="d1b92-123">Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="d1b92-123">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="d1b92-124">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-124">The following output is displayed:</span></span>

```console
Usage:
  dotnet httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
set swagger    Sets the swagger document to use for information about the current server
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

<span data-ttu-id="d1b92-125">Příkaz HTTP REPL nabízí dokončování příkazů.</span><span class="sxs-lookup"><span data-stu-id="d1b92-125">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="d1b92-126">Stisknutí klávesy <kbd>TAB</kbd> prochází seznam příkazů, které dokončí znaky nebo koncový bod rozhraní API, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="d1b92-126">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="d1b92-127">Následující části popisují dostupné příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d1b92-127">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="d1b92-128">Připojení k webovému rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d1b92-128">Connect to the web API</span></span>

<span data-ttu-id="d1b92-129">Připojte se k webovému rozhraní API spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d1b92-129">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="d1b92-130">`<BASE URI>`je základní identifikátor URI pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d1b92-130">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="d1b92-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-131">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="d1b92-132">Případně spusťte následující příkaz kdykoli, když je spuštěn protokol HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d1b92-132">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="d1b92-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-133">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="d1b92-134">Nasměrování na dokument Swagger pro webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d1b92-134">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="d1b92-135">Pro správné prohlížení webového rozhraní API nastavte relativní identifikátor URI na dokument Swagger pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d1b92-135">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="d1b92-136">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d1b92-136">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="d1b92-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-137">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="d1b92-138">Navigace v rozhraní Web API</span><span class="sxs-lookup"><span data-stu-id="d1b92-138">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="d1b92-139">Zobrazit dostupné koncové body</span><span class="sxs-lookup"><span data-stu-id="d1b92-139">View available endpoints</span></span>

<span data-ttu-id="d1b92-140">Pokud chcete zobrazit seznam různých koncových bodů (řadičů) na aktuální cestě adresy webového rozhraní API, spusťte `ls` příkaz `dir` nebo:</span><span class="sxs-lookup"><span data-stu-id="d1b92-140">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="d1b92-141">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d1b92-141">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="d1b92-142">Předchozí výstup ukazuje, že jsou k dispozici dva řadiče `Fruits` : `People`a.</span><span class="sxs-lookup"><span data-stu-id="d1b92-142">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="d1b92-143">Oba řadiče podporují operace HTTP GET a POST bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="d1b92-143">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="d1b92-144">Přechod na konkrétní kontroler odhalí více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d1b92-144">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="d1b92-145">Například výstup následujícího příkazu ukazuje, že `Fruits` kontroler také podporuje operace HTTP GET, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="d1b92-145">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="d1b92-146">Každá z těchto operací očekává `id` v trase parametr:</span><span class="sxs-lookup"><span data-stu-id="d1b92-146">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="d1b92-147">Případně spusťte `ui` příkaz a otevřete stránku uživatelského rozhraní Swagger webového rozhraní API v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d1b92-147">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="d1b92-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-148">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="d1b92-149">Přejít na koncový bod</span><span class="sxs-lookup"><span data-stu-id="d1b92-149">Navigate to an endpoint</span></span>

<span data-ttu-id="d1b92-150">Pokud chcete přejít na jiný koncový bod webového rozhraní API, spusťte `cd` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d1b92-150">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="d1b92-151">Cesta za `cd` příkazem nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="d1b92-151">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="d1b92-152">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d1b92-152">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="d1b92-153">Přizpůsobení REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="d1b92-153">Customize the HTTP REPL</span></span>

<span data-ttu-id="d1b92-154">Výchozí [barvy](#set-color-preferences) REPL http je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="d1b92-154">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="d1b92-155">Kromě toho lze definovat [výchozí textový editor](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-155">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="d1b92-156">Předvolby HTTP REPL jsou trvale v rámci aktuální relace a jsou v budoucích relacích dodrženy.</span><span class="sxs-lookup"><span data-stu-id="d1b92-156">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="d1b92-157">Po úpravě jsou předvolby uložené v následujícím souboru:</span><span class="sxs-lookup"><span data-stu-id="d1b92-157">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d1b92-158">Linux</span><span class="sxs-lookup"><span data-stu-id="d1b92-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="d1b92-159">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d1b92-159">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="d1b92-160">macOS</span><span class="sxs-lookup"><span data-stu-id="d1b92-160">macOS</span></span>](#tab/macos)

<span data-ttu-id="d1b92-161">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d1b92-161">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="d1b92-162">Windows</span><span class="sxs-lookup"><span data-stu-id="d1b92-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="d1b92-163">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d1b92-163">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="d1b92-164">Soubor *. httpreplprefs* je načten při spuštění a není monitorován pro změny za běhu.</span><span class="sxs-lookup"><span data-stu-id="d1b92-164">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="d1b92-165">Ruční úpravy souboru se projeví až po restartování nástroje.</span><span class="sxs-lookup"><span data-stu-id="d1b92-165">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="d1b92-166">Zobrazit nastavení</span><span class="sxs-lookup"><span data-stu-id="d1b92-166">View the settings</span></span>

<span data-ttu-id="d1b92-167">Dostupná nastavení zobrazíte spuštěním `pref get` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d1b92-167">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="d1b92-168">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-168">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="d1b92-169">Předchozí příkaz zobrazí dostupné páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="d1b92-169">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="d1b92-170">Nastavit předvolby barev</span><span class="sxs-lookup"><span data-stu-id="d1b92-170">Set color preferences</span></span>

<span data-ttu-id="d1b92-171">Vybarvení odpovědi je aktuálně podporováno pouze pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d1b92-171">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="d1b92-172">Chcete-li přizpůsobit výchozí barevné vybarvení nástroje HTTP REPL, vyhledejte klíč odpovídající barvě, který má být změněn.</span><span class="sxs-lookup"><span data-stu-id="d1b92-172">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="d1b92-173">Pokyny k vyhledání klíčů najdete v části [zobrazení nastavení](#view-the-settings) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-173">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="d1b92-174">Například hodnotu `colors.json` klíče můžete změnit z `Green` na `White` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d1b92-174">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="d1b92-175">Mohou být použity pouze [povolené barvy](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-175">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="d1b92-176">Následné požadavky HTTP zobrazují výstup s novými zvýrazněními.</span><span class="sxs-lookup"><span data-stu-id="d1b92-176">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="d1b92-177">Pokud nejsou nastavené určité klíče barev, považují se za obecnější klíče.</span><span class="sxs-lookup"><span data-stu-id="d1b92-177">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="d1b92-178">K předvedení tohoto nouzového chování Vezměte v úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-178">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="d1b92-179">Pokud `colors.json.name` nemá hodnotu, `colors.json.string` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d1b92-179">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="d1b92-180">Pokud `colors.json.string` nemá hodnotu, `colors.json.literal` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d1b92-180">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="d1b92-181">Pokud `colors.json.literal` nemá hodnotu, `colors.json` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d1b92-181">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="d1b92-182">Pokud `colors.json` hodnota nemá hodnotu, použije se výchozí barva textu (`AllowedColors.None`) příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1b92-182">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="d1b92-183">Nastavit velikost odsazení</span><span class="sxs-lookup"><span data-stu-id="d1b92-183">Set indentation size</span></span>

<span data-ttu-id="d1b92-184">Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d1b92-184">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d1b92-185">Výchozí velikost jsou dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="d1b92-185">The default size is two spaces.</span></span> <span data-ttu-id="d1b92-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-186">For example:</span></span>

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

<span data-ttu-id="d1b92-187">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="d1b92-187">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d1b92-188">Například pokud chcete vždy použít čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="d1b92-188">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d1b92-189">Následující odpovědi dodržují nastavení čtyř mezer:</span><span class="sxs-lookup"><span data-stu-id="d1b92-189">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="d1b92-190">Nastavit velikost odsazení</span><span class="sxs-lookup"><span data-stu-id="d1b92-190">Set indentation size</span></span>

<span data-ttu-id="d1b92-191">Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d1b92-191">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d1b92-192">Výchozí velikost jsou dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="d1b92-192">The default size is two spaces.</span></span> <span data-ttu-id="d1b92-193">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-193">For example:</span></span>

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

<span data-ttu-id="d1b92-194">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="d1b92-194">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d1b92-195">Například pokud chcete vždy použít čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="d1b92-195">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d1b92-196">Následující odpovědi dodržují nastavení čtyř mezer:</span><span class="sxs-lookup"><span data-stu-id="d1b92-196">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="d1b92-197">Nastavit výchozí textový editor</span><span class="sxs-lookup"><span data-stu-id="d1b92-197">Set the default text editor</span></span>

<span data-ttu-id="d1b92-198">Ve výchozím nastavení nemá protokol HTTP REPL nakonfigurovaný žádný textový editor pro použití.</span><span class="sxs-lookup"><span data-stu-id="d1b92-198">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="d1b92-199">Chcete-li otestovat metody webového rozhraní API, které vyžadují tělo požadavku HTTP, musí být nastaven výchozí textový editor.</span><span class="sxs-lookup"><span data-stu-id="d1b92-199">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="d1b92-200">Nástroj HTTP REPL spustí nakonfigurovaný textový editor pro výhradní účely vytváření textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="d1b92-200">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="d1b92-201">Spusťte následující příkaz, který nastaví upřednostňovaný textový editor jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="d1b92-201">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="d1b92-202">V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textového editoru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-202">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="d1b92-203">Například spusťte následující příkaz, který nastaví Visual Studio Code jako výchozí textový editor:</span><span class="sxs-lookup"><span data-stu-id="d1b92-203">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d1b92-204">Linux</span><span class="sxs-lookup"><span data-stu-id="d1b92-204">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="d1b92-205">macOS</span><span class="sxs-lookup"><span data-stu-id="d1b92-205">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="d1b92-206">Windows</span><span class="sxs-lookup"><span data-stu-id="d1b92-206">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="d1b92-207">Chcete-li spustit výchozí textový editor s konkrétními argumenty rozhraní příkazového řádku, nastavte `editor.command.default.arguments` klíč.</span><span class="sxs-lookup"><span data-stu-id="d1b92-207">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="d1b92-208">Předpokládejme například, že Visual Studio Code je výchozím textovým editorem a chcete, aby se v nové relaci s vypnutými rozšířeními otevřela Visual Studio Code HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="d1b92-208">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="d1b92-209">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d1b92-209">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="d1b92-210">Testování požadavků HTTP GET</span><span class="sxs-lookup"><span data-stu-id="d1b92-210">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-211">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-211">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-212">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-212">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-213">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-213">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-214">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-214">Options</span></span>

<span data-ttu-id="d1b92-215">Pro `get` příkaz jsou k dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="d1b92-215">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d1b92-216">Příklad</span><span class="sxs-lookup"><span data-stu-id="d1b92-216">Example</span></span>

<span data-ttu-id="d1b92-217">Vystavení požadavku HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="d1b92-217">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="d1b92-218">`get` Spusťte příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d1b92-218">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="d1b92-219">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d1b92-219">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="d1b92-220">Načtěte určitý záznam předáním parametru `get` příkazu:</span><span class="sxs-lookup"><span data-stu-id="d1b92-220">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="d1b92-221">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d1b92-221">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="d1b92-222">Test požadavků HTTP POST</span><span class="sxs-lookup"><span data-stu-id="d1b92-222">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-223">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-223">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-224">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-225">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-226">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-226">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d1b92-227">Příklad</span><span class="sxs-lookup"><span data-stu-id="d1b92-227">Example</span></span>

<span data-ttu-id="d1b92-228">Vystavení požadavku HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="d1b92-228">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="d1b92-229">`post` Spusťte příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d1b92-229">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="d1b92-230">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d1b92-230">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d1b92-231">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1b92-231">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d1b92-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-232">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d1b92-233">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-233">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d1b92-234">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d1b92-234">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="d1b92-235">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d1b92-235">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d1b92-236">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-236">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="d1b92-237">Test požadavků HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="d1b92-237">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-238">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-238">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-239">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-239">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-240">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-240">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-241">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-241">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d1b92-242">Příklad</span><span class="sxs-lookup"><span data-stu-id="d1b92-242">Example</span></span>

<span data-ttu-id="d1b92-243">Vydání požadavku HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="d1b92-243">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="d1b92-244">*Volitelné*: `get` Spusťte příkaz pro zobrazení dat před jeho úpravou:</span><span class="sxs-lookup"><span data-stu-id="d1b92-244">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="d1b92-245">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d1b92-245">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d1b92-246">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1b92-246">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d1b92-247">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-247">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d1b92-248">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d1b92-248">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d1b92-249">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d1b92-249">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="d1b92-250">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d1b92-250">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d1b92-251">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-251">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d1b92-252">*Volitelné*: Vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d1b92-252">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d1b92-253">Například pokud jste v textovém editoru zadali "třešně", `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d1b92-253">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="d1b92-254">Test požadavků HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="d1b92-254">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-255">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-255">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-256">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-256">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-257">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-257">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-258">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-258">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d1b92-259">Příklad</span><span class="sxs-lookup"><span data-stu-id="d1b92-259">Example</span></span>

<span data-ttu-id="d1b92-260">Postup při vystavení žádosti o odstranění protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="d1b92-260">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="d1b92-261">*Volitelné*: `get` Spusťte příkaz pro zobrazení dat před jeho úpravou:</span><span class="sxs-lookup"><span data-stu-id="d1b92-261">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="d1b92-262">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d1b92-262">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d1b92-263">*Volitelné*: Vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d1b92-263">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d1b92-264">V tomto příkladu, `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d1b92-264">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="d1b92-265">Test požadavků na opravy HTTP</span><span class="sxs-lookup"><span data-stu-id="d1b92-265">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-266">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-266">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-267">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-267">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-268">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-268">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-269">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-269">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="d1b92-270">Test požadavků HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="d1b92-270">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-271">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-271">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-272">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-272">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-273">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-273">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-274">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-274">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="d1b92-275">Požadavky na test možností protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="d1b92-275">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d1b92-276">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d1b92-276">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d1b92-277">Arguments</span><span class="sxs-lookup"><span data-stu-id="d1b92-277">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d1b92-278">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-278">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d1b92-279">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-279">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="d1b92-280">Nastavení hlaviček požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="d1b92-280">Set HTTP request headers</span></span>

<span data-ttu-id="d1b92-281">Pokud chcete nastavit hlavičku požadavku HTTP, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d1b92-281">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="d1b92-282">Nastavte inline s požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1b92-282">Set inline with the HTTP request.</span></span> <span data-ttu-id="d1b92-283">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-283">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="d1b92-284">V případě předchozího přístupu vyžaduje každá samostatná Hlavička požadavku HTTP vlastní `-h` možnost.</span><span class="sxs-lookup"><span data-stu-id="d1b92-284">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="d1b92-285">Nastaveno před odesláním požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1b92-285">Set before sending the HTTP request.</span></span> <span data-ttu-id="d1b92-286">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-286">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="d1b92-287">Při nastavování hlavičky před odesláním žádosti zůstane záhlaví nastavené na dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1b92-287">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="d1b92-288">Pokud chcete záhlaví vymazat, zadejte prázdnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d1b92-288">To clear the header, provide an empty value.</span></span> <span data-ttu-id="d1b92-289">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-289">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="d1b92-290">Přepnout zobrazení požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="d1b92-290">Toggle HTTP request display</span></span>

<span data-ttu-id="d1b92-291">Ve výchozím nastavení se zobrazí potlačení požadavku HTTP na odeslání.</span><span class="sxs-lookup"><span data-stu-id="d1b92-291">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="d1b92-292">Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d1b92-292">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="d1b92-293">Povolit zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-293">Enable request display</span></span>

<span data-ttu-id="d1b92-294">Spuštěním `echo on` příkazu Zobrazte požadavek HTTP, který odesíláte.</span><span class="sxs-lookup"><span data-stu-id="d1b92-294">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="d1b92-295">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-295">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="d1b92-296">Následující požadavky HTTP v aktuální relaci zobrazují hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d1b92-296">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="d1b92-297">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-297">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="d1b92-298">Zakázat zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-298">Disable request display</span></span>

<span data-ttu-id="d1b92-299">Potlačit zobrazení požadavku HTTP odesílaného spuštěním `echo off` příkazu</span><span class="sxs-lookup"><span data-stu-id="d1b92-299">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="d1b92-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-300">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="d1b92-301">Spuštění skriptu</span><span class="sxs-lookup"><span data-stu-id="d1b92-301">Run a script</span></span>

<span data-ttu-id="d1b92-302">Pokud často spustíte stejnou sadu příkazů HTTP REPL, zvažte jejich uložení do textového souboru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-302">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="d1b92-303">Příkazy v souboru přebírají stejnou formu, jakou byly provedeny ručně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="d1b92-303">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="d1b92-304">Příkazy lze spustit v dávce způsobem pomocí `run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d1b92-304">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="d1b92-305">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-305">For example:</span></span>

1. <span data-ttu-id="d1b92-306">Vytvoří textový soubor obsahující sadu příkazů s oddělovači na nový řádek.</span><span class="sxs-lookup"><span data-stu-id="d1b92-306">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="d1b92-307">Pro ilustraci zvažte soubor *People-Script. txt* , který obsahuje následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d1b92-307">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="d1b92-308">`run` Spusťte příkaz a předejte cestu k textovému souboru.</span><span class="sxs-lookup"><span data-stu-id="d1b92-308">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="d1b92-309">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d1b92-309">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="d1b92-310">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-310">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="d1b92-311">Vymazat výstup</span><span class="sxs-lookup"><span data-stu-id="d1b92-311">Clear the output</span></span>

<span data-ttu-id="d1b92-312">Pokud chcete odebrat veškerý výstup napsaný do příkazového prostředí nástrojem http REPL, spusťte `clear` příkaz nebo. `cls`</span><span class="sxs-lookup"><span data-stu-id="d1b92-312">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="d1b92-313">K ilustraci si představte, že příkazové prostředí obsahuje následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-313">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="d1b92-314">Výstup vymažete spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d1b92-314">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="d1b92-315">Po spuštění předchozího příkazu obsahuje příkazové prostředí pouze následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d1b92-315">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="d1b92-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d1b92-316">Additional resources</span></span>

* [<span data-ttu-id="d1b92-317">REST API žádosti</span><span class="sxs-lookup"><span data-stu-id="d1b92-317">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="d1b92-318">Úložiště GitHub HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d1b92-318">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
