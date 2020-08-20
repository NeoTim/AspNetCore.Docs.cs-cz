---
title: Testování webových rozhraní API pomocí protokolu HTTP REPL
author: scottaddie
description: Naučte se používat globální nástroj HTTP REPL .NET Core k procházení a testování ASP.NET Core webového rozhraní API.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/http-repl
ms.openlocfilehash: e6263f19cdb7f9957fa8360f9e782e622589ea18
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633315"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="d0460-103">Testování webových rozhraní API pomocí protokolu HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d0460-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="d0460-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d0460-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d0460-105">Smyčka HTTP Read-Eval-Print (REPL) je:</span><span class="sxs-lookup"><span data-stu-id="d0460-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="d0460-106">Podporuje se odlehčený nástroj příkazového řádku pro více platforem, který podporuje všude, kde je podporováno rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d0460-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="d0460-107">Slouží k provádění požadavků HTTP na testování ASP.NET Core webových rozhraní API (a webových rozhraní API non-ASP.NET Core) a zobrazení jejich výsledků.</span><span class="sxs-lookup"><span data-stu-id="d0460-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="d0460-108">Je možné testovat webová rozhraní API hostovaná v jakémkoli prostředí, včetně localhost a Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d0460-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="d0460-109">Podporovány jsou následující [Příkazy protokolu HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :</span><span class="sxs-lookup"><span data-stu-id="d0460-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="d0460-110">DSTRANIT</span><span class="sxs-lookup"><span data-stu-id="d0460-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="d0460-111">Čtěte</span><span class="sxs-lookup"><span data-stu-id="d0460-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="d0460-112">HEAD</span><span class="sxs-lookup"><span data-stu-id="d0460-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="d0460-113">NASTAVENÍ</span><span class="sxs-lookup"><span data-stu-id="d0460-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="d0460-114">POUŽITA</span><span class="sxs-lookup"><span data-stu-id="d0460-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="d0460-115">SPUŠTĚNÍ</span><span class="sxs-lookup"><span data-stu-id="d0460-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="d0460-116">PUT</span><span class="sxs-lookup"><span data-stu-id="d0460-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="d0460-117">Pokud chcete postup sledovat, [Zobrazte si ukázkové ASP.NET Core webové rozhraní API nebo si ho stáhněte](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d0460-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d0460-118">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="d0460-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="d0460-119">Instalace</span><span class="sxs-lookup"><span data-stu-id="d0460-119">Installation</span></span>

<span data-ttu-id="d0460-120">Pro instalaci HTTP REPL spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="d0460-121">[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .</span><span class="sxs-lookup"><span data-stu-id="d0460-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="d0460-122">Využití</span><span class="sxs-lookup"><span data-stu-id="d0460-122">Usage</span></span>

<span data-ttu-id="d0460-123">Po úspěšné instalaci nástroje spusťte následující příkaz, který spustí HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d0460-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="d0460-124">Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="d0460-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="d0460-125">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-125">The following output is displayed:</span></span>

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

<span data-ttu-id="d0460-126">Příkaz HTTP REPL nabízí dokončování příkazů.</span><span class="sxs-lookup"><span data-stu-id="d0460-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="d0460-127">Stisknutí klávesy <kbd>TAB</kbd> prochází seznam příkazů, které dokončí znaky nebo koncový bod rozhraní API, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="d0460-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="d0460-128">Následující části popisují dostupné příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d0460-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="d0460-129">Připojení k webovému rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d0460-129">Connect to the web API</span></span>

<span data-ttu-id="d0460-130">Připojte se k webovému rozhraní API spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d0460-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="d0460-131">`<ROOT URI>` je základní identifikátor URI pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d0460-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="d0460-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="d0460-133">Případně spusťte následující příkaz kdykoli, když je spuštěn protokol HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d0460-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="d0460-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="d0460-135">Ruční odkazování na dokument Swagger pro webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d0460-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="d0460-136">Výše uvedený příkaz Connect se pokusí najít dokument Swagger automaticky.</span><span class="sxs-lookup"><span data-stu-id="d0460-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="d0460-137">Pokud z nějakého důvodu to není možné, můžete zadat identifikátor URI dokumentu Swagger pro webové rozhraní API pomocí `--swagger` Možnosti:</span><span class="sxs-lookup"><span data-stu-id="d0460-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="d0460-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="d0460-139">Navigace v rozhraní Web API</span><span class="sxs-lookup"><span data-stu-id="d0460-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="d0460-140">Zobrazit dostupné koncové body</span><span class="sxs-lookup"><span data-stu-id="d0460-140">View available endpoints</span></span>

<span data-ttu-id="d0460-141">Pokud chcete zobrazit seznam různých koncových bodů (řadičů) na aktuální cestě adresy webového rozhraní API, spusťte `ls` `dir` příkaz nebo:</span><span class="sxs-lookup"><span data-stu-id="d0460-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="d0460-142">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d0460-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="d0460-143">Předchozí výstup ukazuje, že jsou k dispozici dva řadiče: `Fruits` a `People` .</span><span class="sxs-lookup"><span data-stu-id="d0460-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="d0460-144">Oba řadiče podporují operace HTTP GET a POST bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="d0460-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="d0460-145">Přechod na konkrétní kontroler odhalí více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d0460-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="d0460-146">Například výstup následujícího příkazu ukazuje, že `Fruits` kontroler také podporuje operace HTTP GET, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="d0460-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="d0460-147">Každá z těchto operací očekává `id` v trase parametr:</span><span class="sxs-lookup"><span data-stu-id="d0460-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="d0460-148">Případně spusťte `ui` příkaz a otevřete stránku uživatelského rozhraní Swagger webového rozhraní API v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d0460-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="d0460-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="d0460-150">Přejít na koncový bod</span><span class="sxs-lookup"><span data-stu-id="d0460-150">Navigate to an endpoint</span></span>

<span data-ttu-id="d0460-151">Pokud chcete přejít na jiný koncový bod webového rozhraní API, spusťte `cd` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="d0460-152">Cesta za `cd` příkazem nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="d0460-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="d0460-153">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d0460-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="d0460-154">Přizpůsobení REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="d0460-155">Výchozí [barvy](#set-color-preferences) REPL http je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="d0460-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="d0460-156">Kromě toho lze definovat [výchozí textový editor](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d0460-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="d0460-157">Předvolby HTTP REPL jsou trvale v rámci aktuální relace a jsou v budoucích relacích dodrženy.</span><span class="sxs-lookup"><span data-stu-id="d0460-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="d0460-158">Po úpravě jsou předvolby uložené v následujícím souboru:</span><span class="sxs-lookup"><span data-stu-id="d0460-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="d0460-159">Linux</span><span class="sxs-lookup"><span data-stu-id="d0460-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="d0460-160">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d0460-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="d0460-161">macOS</span><span class="sxs-lookup"><span data-stu-id="d0460-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="d0460-162">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d0460-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="d0460-163">Windows</span><span class="sxs-lookup"><span data-stu-id="d0460-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="d0460-164">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d0460-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="d0460-165">Soubor *. httpreplprefs* je načten při spuštění a není monitorován pro změny za běhu.</span><span class="sxs-lookup"><span data-stu-id="d0460-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="d0460-166">Ruční úpravy souboru se projeví až po restartování nástroje.</span><span class="sxs-lookup"><span data-stu-id="d0460-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="d0460-167">Zobrazit nastavení</span><span class="sxs-lookup"><span data-stu-id="d0460-167">View the settings</span></span>

<span data-ttu-id="d0460-168">Dostupná nastavení zobrazíte spuštěním `pref get` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d0460-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="d0460-169">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="d0460-170">Předchozí příkaz zobrazí dostupné páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="d0460-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="d0460-171">Nastavit předvolby barev</span><span class="sxs-lookup"><span data-stu-id="d0460-171">Set color preferences</span></span>

<span data-ttu-id="d0460-172">Vybarvení odpovědi je aktuálně podporováno pouze pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d0460-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="d0460-173">Chcete-li přizpůsobit výchozí barevné vybarvení nástroje HTTP REPL, vyhledejte klíč odpovídající barvě, který má být změněn.</span><span class="sxs-lookup"><span data-stu-id="d0460-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="d0460-174">Pokyny k vyhledání klíčů najdete v části [zobrazení nastavení](#view-the-settings) .</span><span class="sxs-lookup"><span data-stu-id="d0460-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="d0460-175">Například `colors.json` hodnotu klíče můžete změnit z na následujícím `Green` `White` způsobem:</span><span class="sxs-lookup"><span data-stu-id="d0460-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="d0460-176">Mohou být použity pouze [povolené barvy](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) .</span><span class="sxs-lookup"><span data-stu-id="d0460-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="d0460-177">Následné požadavky HTTP zobrazují výstup s novými zvýrazněními.</span><span class="sxs-lookup"><span data-stu-id="d0460-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="d0460-178">Pokud nejsou nastavené určité klíče barev, považují se za obecnější klíče.</span><span class="sxs-lookup"><span data-stu-id="d0460-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="d0460-179">K předvedení tohoto nouzového chování Vezměte v úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="d0460-180">Pokud nemá `colors.json.name` hodnotu, `colors.json.string` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0460-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="d0460-181">Pokud nemá `colors.json.string` hodnotu, `colors.json.literal` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0460-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="d0460-182">Pokud nemá `colors.json.literal` hodnotu, `colors.json` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d0460-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="d0460-183">Pokud `colors.json` hodnota nemá hodnotu, použije se výchozí barva textu () příkazového prostředí `AllowedColors.None` .</span><span class="sxs-lookup"><span data-stu-id="d0460-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="d0460-184">Nastavit velikost odsazení</span><span class="sxs-lookup"><span data-stu-id="d0460-184">Set indentation size</span></span>

<span data-ttu-id="d0460-185">Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d0460-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d0460-186">Výchozí velikost jsou dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="d0460-186">The default size is two spaces.</span></span> <span data-ttu-id="d0460-187">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-187">For example:</span></span>

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

<span data-ttu-id="d0460-188">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="d0460-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d0460-189">Například pokud chcete vždy použít čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="d0460-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d0460-190">Následující odpovědi dodržují nastavení čtyř mezer:</span><span class="sxs-lookup"><span data-stu-id="d0460-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="d0460-191">Nastavit výchozí textový editor</span><span class="sxs-lookup"><span data-stu-id="d0460-191">Set the default text editor</span></span>

<span data-ttu-id="d0460-192">Ve výchozím nastavení nemá protokol HTTP REPL nakonfigurovaný žádný textový editor pro použití.</span><span class="sxs-lookup"><span data-stu-id="d0460-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="d0460-193">Chcete-li otestovat metody webového rozhraní API, které vyžadují tělo požadavku HTTP, musí být nastaven výchozí textový editor.</span><span class="sxs-lookup"><span data-stu-id="d0460-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="d0460-194">Nástroj HTTP REPL spustí nakonfigurovaný textový editor pro výhradní účely vytváření textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="d0460-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="d0460-195">Spusťte následující příkaz, který nastaví upřednostňovaný textový editor jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="d0460-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="d0460-196">V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textového editoru.</span><span class="sxs-lookup"><span data-stu-id="d0460-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="d0460-197">Například spusťte následující příkaz, který nastaví Visual Studio Code jako výchozí textový editor:</span><span class="sxs-lookup"><span data-stu-id="d0460-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="d0460-198">Linux</span><span class="sxs-lookup"><span data-stu-id="d0460-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="d0460-199">macOS</span><span class="sxs-lookup"><span data-stu-id="d0460-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="d0460-200">Windows</span><span class="sxs-lookup"><span data-stu-id="d0460-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="d0460-201">Chcete-li spustit výchozí textový editor s konkrétními argumenty rozhraní příkazového řádku, nastavte `editor.command.default.arguments` klíč.</span><span class="sxs-lookup"><span data-stu-id="d0460-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="d0460-202">Předpokládejme například, že Visual Studio Code je výchozím textovým editorem a chcete, aby se v nové relaci s vypnutými rozšířeními otevřela Visual Studio Code HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="d0460-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="d0460-203">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="d0460-204">Nastavení vyhledávacích cest Swagger</span><span class="sxs-lookup"><span data-stu-id="d0460-204">Set the Swagger search paths</span></span>

<span data-ttu-id="d0460-205">Ve výchozím nastavení má REPL HTTP sadu relativních cest, které používá k nalezení dokumentu Swagger při provádění `connect` příkazu bez `--swagger` Možnosti.</span><span class="sxs-lookup"><span data-stu-id="d0460-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="d0460-206">Tyto relativní cesty jsou kombinovány s kořenovou a základní cestou specifikovanou v `connect` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d0460-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="d0460-207">Výchozí relativní cesty jsou:</span><span class="sxs-lookup"><span data-stu-id="d0460-207">The default relative paths are:</span></span>

- <span data-ttu-id="d0460-208">*swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="d0460-208">*swagger.json*</span></span>
- <span data-ttu-id="d0460-209">*Swagger/v1/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="d0460-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="d0460-210">*/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="d0460-210">*/swagger.json*</span></span>
- <span data-ttu-id="d0460-211">*/Swagger/v1/swagger.js*</span><span class="sxs-lookup"><span data-stu-id="d0460-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="d0460-212">Pokud chcete ve svém prostředí použít jinou sadu vyhledávacích cest, nastavte `swagger.searchPaths` Předvolby.</span><span class="sxs-lookup"><span data-stu-id="d0460-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="d0460-213">Hodnota musí být seznam relativních cest oddělených svislým kanálem.</span><span class="sxs-lookup"><span data-stu-id="d0460-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="d0460-214">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="d0460-215">Testování požadavků HTTP GET</span><span class="sxs-lookup"><span data-stu-id="d0460-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-216">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-217">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-218">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-219">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-219">Options</span></span>

<span data-ttu-id="d0460-220">Pro příkaz jsou k dispozici následující možnosti `get` :</span><span class="sxs-lookup"><span data-stu-id="d0460-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d0460-221">Příklad</span><span class="sxs-lookup"><span data-stu-id="d0460-221">Example</span></span>

<span data-ttu-id="d0460-222">Vystavení požadavku HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="d0460-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="d0460-223">Spusťte `get` příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d0460-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="d0460-224">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d0460-224">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="d0460-225">Načtěte určitý záznam předáním parametru `get` příkazu:</span><span class="sxs-lookup"><span data-stu-id="d0460-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="d0460-226">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d0460-226">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="d0460-227">Test požadavků HTTP POST</span><span class="sxs-lookup"><span data-stu-id="d0460-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-228">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-229">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-230">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-231">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d0460-232">Příklad</span><span class="sxs-lookup"><span data-stu-id="d0460-232">Example</span></span>

<span data-ttu-id="d0460-233">Vystavení požadavku HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="d0460-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="d0460-234">Spusťte `post` příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d0460-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="d0460-235">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d0460-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d0460-236">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d0460-237">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d0460-238">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d0460-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d0460-239">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d0460-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="d0460-240">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d0460-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d0460-241">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-241">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="d0460-242">Test požadavků HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="d0460-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-243">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-244">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-245">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-246">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d0460-247">Příklad</span><span class="sxs-lookup"><span data-stu-id="d0460-247">Example</span></span>

<span data-ttu-id="d0460-248">Vydání požadavku HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="d0460-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="d0460-249">*Volitelné*: `get` pro zobrazení dat před úpravou spusťte příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-249">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="d0460-250">Spusťte `put` příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d0460-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="d0460-251">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d0460-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d0460-252">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d0460-253">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d0460-254">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d0460-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d0460-255">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d0460-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="d0460-256">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d0460-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d0460-257">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d0460-258">*Volitelné*: vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d0460-258">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d0460-259">Například pokud jste v textovém editoru zadali "třešně", `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d0460-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="d0460-260">Test požadavků HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="d0460-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-261">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-262">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-263">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-264">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d0460-265">Příklad</span><span class="sxs-lookup"><span data-stu-id="d0460-265">Example</span></span>

<span data-ttu-id="d0460-266">Postup při vystavení žádosti o odstranění protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="d0460-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="d0460-267">*Volitelné*: `get` pro zobrazení dat před úpravou spusťte příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-267">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="d0460-268">Spusťte `delete` příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d0460-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="d0460-269">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d0460-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d0460-270">*Volitelné*: vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d0460-270">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d0460-271">V tomto příkladu, `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d0460-271">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="d0460-272">Test požadavků na opravy HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-273">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-274">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-275">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-276">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="d0460-277">Test požadavků HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="d0460-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-278">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-280">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-281">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="d0460-282">Požadavky na test možností protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d0460-283">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d0460-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d0460-284">Arguments</span><span class="sxs-lookup"><span data-stu-id="d0460-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d0460-285">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d0460-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d0460-286">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d0460-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="d0460-287">Nastavení hlaviček požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-287">Set HTTP request headers</span></span>

<span data-ttu-id="d0460-288">Pokud chcete nastavit hlavičku požadavku HTTP, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d0460-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="d0460-289">Nastavte inline s požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="d0460-290">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="d0460-291">V případě předchozího přístupu vyžaduje každá samostatná Hlavička požadavku HTTP vlastní `-h` možnost.</span><span class="sxs-lookup"><span data-stu-id="d0460-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="d0460-292">Nastaveno před odesláním požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="d0460-293">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="d0460-294">Při nastavování hlavičky před odesláním žádosti zůstane záhlaví nastavené na dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d0460-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="d0460-295">Pokud chcete záhlaví vymazat, zadejte prázdnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d0460-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="d0460-296">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="d0460-297">Test zabezpečených koncových bodů</span><span class="sxs-lookup"><span data-stu-id="d0460-297">Test secured endpoints</span></span>

<span data-ttu-id="d0460-298">HTTP REPL podporuje testování zabezpečených koncových bodů dvěma způsoby: prostřednictvím výchozích přihlašovacích údajů přihlášeného uživatele nebo pomocí hlaviček požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="d0460-299">Výchozí pověření</span><span class="sxs-lookup"><span data-stu-id="d0460-299">Default credentials</span></span>

<span data-ttu-id="d0460-300">Vezměte v úvahu scénář, ve kterém je webové rozhraní API, které testujete, hostované ve službě IIS a zabezpečené s ověřováním systému Windows.</span><span class="sxs-lookup"><span data-stu-id="d0460-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="d0460-301">Chcete, aby přihlašovací údaje uživatele, který spustil nástroj, pokračovaly v testování koncových bodů HTTP.</span><span class="sxs-lookup"><span data-stu-id="d0460-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="d0460-302">Předání výchozích přihlašovacích údajů přihlášeného uživatele:</span><span class="sxs-lookup"><span data-stu-id="d0460-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="d0460-303">Nastavte `httpClient.useDefaultCredentials` Předvolby na `true` :</span><span class="sxs-lookup"><span data-stu-id="d0460-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="d0460-304">Před odesláním dalšího požadavku webovému rozhraní API ukončete nástroj a restartujte ho.</span><span class="sxs-lookup"><span data-stu-id="d0460-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="d0460-305">Hlavičky požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-305">HTTP request headers</span></span>

<span data-ttu-id="d0460-306">Příklady podporovaných schémat ověřování a autorizace zahrnují základní ověřování, tokeny nosiče JWT a ověřování hodnotou hash.</span><span class="sxs-lookup"><span data-stu-id="d0460-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="d0460-307">Například můžete odeslat nosný token do koncového bodu pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d0460-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="d0460-308">Pokud chcete získat přístup ke koncovému bodu hostovanému v Azure nebo použít [Azure REST API](/rest/api/azure/), potřebujete nosný token.</span><span class="sxs-lookup"><span data-stu-id="d0460-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="d0460-309">Pomocí následujícího postupu můžete získat nosný token pro předplatné Azure prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="d0460-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="d0460-310">REPL HTTP nastavuje nosný token v hlavičce požadavku HTTP a načítá seznam Azure App Service Web Apps.</span><span class="sxs-lookup"><span data-stu-id="d0460-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="d0460-311">Přihlaste se k Azure:</span><span class="sxs-lookup"><span data-stu-id="d0460-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="d0460-312">Pomocí následujícího příkazu Získejte ID vašeho předplatného:</span><span class="sxs-lookup"><span data-stu-id="d0460-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="d0460-313">Zkopírujte ID předplatného a spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d0460-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="d0460-314">Získejte token nosiče pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d0460-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="d0460-315">Připojte se k Azure REST API přes REPL HTTP:</span><span class="sxs-lookup"><span data-stu-id="d0460-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="d0460-316">Nastavte `Authorization` hlavičku požadavku http:</span><span class="sxs-lookup"><span data-stu-id="d0460-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="d0460-317">Přejděte k předplatnému:</span><span class="sxs-lookup"><span data-stu-id="d0460-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="d0460-318">Získejte seznam Azure App Service Web Apps vašich předplatných:</span><span class="sxs-lookup"><span data-stu-id="d0460-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="d0460-319">Zobrazí se následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="d0460-319">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="d0460-320">Přepnout zobrazení požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="d0460-320">Toggle HTTP request display</span></span>

<span data-ttu-id="d0460-321">Ve výchozím nastavení se zobrazí potlačení požadavku HTTP na odeslání.</span><span class="sxs-lookup"><span data-stu-id="d0460-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="d0460-322">Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d0460-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="d0460-323">Povolit zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d0460-323">Enable request display</span></span>

<span data-ttu-id="d0460-324">Spuštěním příkazu Zobrazte požadavek HTTP, který odesíláte `echo on` .</span><span class="sxs-lookup"><span data-stu-id="d0460-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="d0460-325">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="d0460-326">Následující požadavky HTTP v aktuální relaci zobrazují hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d0460-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="d0460-327">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-327">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="d0460-328">Zakázat zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d0460-328">Disable request display</span></span>

<span data-ttu-id="d0460-329">Potlačit zobrazení požadavku HTTP odesílaného spuštěním `echo off` příkazu</span><span class="sxs-lookup"><span data-stu-id="d0460-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="d0460-330">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="d0460-331">Spuštění skriptu</span><span class="sxs-lookup"><span data-stu-id="d0460-331">Run a script</span></span>

<span data-ttu-id="d0460-332">Pokud často spustíte stejnou sadu příkazů HTTP REPL, zvažte jejich uložení do textového souboru.</span><span class="sxs-lookup"><span data-stu-id="d0460-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="d0460-333">Příkazy v souboru přebírají stejnou formu, jakou byly provedeny ručně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="d0460-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="d0460-334">Příkazy lze spustit v dávce způsobem pomocí `run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d0460-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="d0460-335">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-335">For example:</span></span>

1. <span data-ttu-id="d0460-336">Vytvoří textový soubor obsahující sadu příkazů s oddělovači na nový řádek.</span><span class="sxs-lookup"><span data-stu-id="d0460-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="d0460-337">K ilustraci zvažte *people-script.txt* soubor obsahující následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d0460-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="d0460-338">Spusťte `run` příkaz a předejte cestu k textovému souboru.</span><span class="sxs-lookup"><span data-stu-id="d0460-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="d0460-339">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d0460-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="d0460-340">Objeví se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-340">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="d0460-341">Vymazat výstup</span><span class="sxs-lookup"><span data-stu-id="d0460-341">Clear the output</span></span>

<span data-ttu-id="d0460-342">Pokud chcete odebrat veškerý výstup napsaný do příkazového prostředí nástrojem HTTP REPL, spusťte `clear` příkaz nebo `cls` .</span><span class="sxs-lookup"><span data-stu-id="d0460-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="d0460-343">K ilustraci si představte, že příkazové prostředí obsahuje následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-343">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="d0460-344">Výstup vymažete spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d0460-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="d0460-345">Po spuštění předchozího příkazu obsahuje příkazové prostředí pouze následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d0460-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="d0460-346">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d0460-346">Additional resources</span></span>

* [<span data-ttu-id="d0460-347">REST API žádosti</span><span class="sxs-lookup"><span data-stu-id="d0460-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="d0460-348">Úložiště GitHub HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d0460-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
