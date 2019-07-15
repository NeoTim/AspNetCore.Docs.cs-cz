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
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="6edbc-103">Test webové rozhraní API pomocí protokolu HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="6edbc-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="6edbc-104">Podle [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6edbc-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6edbc-105">Je smyčka čtení protokolu HTTP-Eval-Print (REPL):</span><span class="sxs-lookup"><span data-stu-id="6edbc-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="6edbc-106">Zjednodušené, multiplatformní nástroje příkazového řádku, který všude, kde se podporuje .NET Core je podporována.</span><span class="sxs-lookup"><span data-stu-id="6edbc-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="6edbc-107">Používá se pro uskutečňování požadavků HTTP pro testování webového rozhraní API ASP.NET Core a zobrazit jejich výsledky.</span><span class="sxs-lookup"><span data-stu-id="6edbc-107">Used for making HTTP requests to test ASP.NET Core web APIs and view their results.</span></span>

<span data-ttu-id="6edbc-108">Následující [příkazy HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) jsou podporovány:</span><span class="sxs-lookup"><span data-stu-id="6edbc-108">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="6edbc-109">DELETE</span><span class="sxs-lookup"><span data-stu-id="6edbc-109">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="6edbc-110">GET</span><span class="sxs-lookup"><span data-stu-id="6edbc-110">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="6edbc-111">HLAVNÍ</span><span class="sxs-lookup"><span data-stu-id="6edbc-111">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="6edbc-112">MOŽNOSTI</span><span class="sxs-lookup"><span data-stu-id="6edbc-112">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="6edbc-113">PATCH</span><span class="sxs-lookup"><span data-stu-id="6edbc-113">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="6edbc-114">POST</span><span class="sxs-lookup"><span data-stu-id="6edbc-114">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="6edbc-115">PUT</span><span class="sxs-lookup"><span data-stu-id="6edbc-115">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="6edbc-116">Pokud chcete postup sledovat, [zobrazení nebo stažení ukázkové webové rozhraní API ASP.NET Core](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6edbc-116">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6edbc-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="6edbc-117">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="6edbc-118">Instalace</span><span class="sxs-lookup"><span data-stu-id="6edbc-118">Installation</span></span>

<span data-ttu-id="6edbc-119">K instalaci HTTP REPL, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-119">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g dotnet-httprepl
    --version 2.2.0-*
    --add-source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
```

<span data-ttu-id="6edbc-120">A [globální nástroje .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) se instaluje z [dotnet httprepl](https://dotnet.myget.org/feed/dotnet-core/package/nuget/dotnet-httprepl
) balíček NuGet hostovaným na MyGet.</span><span class="sxs-lookup"><span data-stu-id="6edbc-120">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [dotnet-httprepl](https://dotnet.myget.org/feed/dotnet-core/package/nuget/dotnet-httprepl
) NuGet package hosted on MyGet.</span></span>

## <a name="usage"></a><span data-ttu-id="6edbc-121">Použití</span><span class="sxs-lookup"><span data-stu-id="6edbc-121">Usage</span></span>

<span data-ttu-id="6edbc-122">Po úspěšné instalaci nástroje, spuštěním následujícího příkazu spusťte HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="6edbc-122">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="6edbc-123">Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="6edbc-123">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="6edbc-124">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-124">The following output is displayed:</span></span>

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

<span data-ttu-id="6edbc-125">HTTP REPL nabízí dokončení příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-125">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="6edbc-126">Stisknutím klávesy <kbd>kartu</kbd> klíč Iteruje přes seznam příkazů, které dokončení znaky nebo koncový bod rozhraní API, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="6edbc-126">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="6edbc-127">Následující oddíly popisují dostupné příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6edbc-127">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="6edbc-128">Připojení k webovému rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6edbc-128">Connect to the web API</span></span>

<span data-ttu-id="6edbc-129">Připojení k webovému rozhraní API spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="6edbc-129">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="6edbc-130">`<BASE URI>` je základní identifikátor URI pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6edbc-130">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="6edbc-131">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-131">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="6edbc-132">Alternativně spusťte následující příkaz kdykoli během HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="6edbc-132">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="6edbc-133">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-133">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="6edbc-134">Přejděte k dokumentu Swagger pro webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6edbc-134">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="6edbc-135">Chcete-li prověřit správně webového rozhraní API, nastavte relativní identifikátor URI k dokumentu Swagger pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6edbc-135">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="6edbc-136">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-136">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="6edbc-137">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-137">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="6edbc-138">Přejděte webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="6edbc-138">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="6edbc-139">Zobrazit dostupné koncové body</span><span class="sxs-lookup"><span data-stu-id="6edbc-139">View available endpoints</span></span>

<span data-ttu-id="6edbc-140">Chcete-li seznam různých koncových bodů (kontrolery) v aktuální cestě z webové adresy rozhraní API, spusťte `ls` nebo `dir` příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-140">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="6edbc-141">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="6edbc-141">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="6edbc-142">Tento výstup označuje, že jsou k dispozici dva řadiče: `Fruits` a `People`.</span><span class="sxs-lookup"><span data-stu-id="6edbc-142">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="6edbc-143">Oba kontrolery podporují konstruktor bez parametrů operace HTTP GET a POST.</span><span class="sxs-lookup"><span data-stu-id="6edbc-143">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="6edbc-144">Přejděte do konkrétní ovladač odhalí podrobněji.</span><span class="sxs-lookup"><span data-stu-id="6edbc-144">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="6edbc-145">Například následující příkaz zobrazí výstup `Fruits` řadič také podporuje operace HTTP GET, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="6edbc-145">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="6edbc-146">Každá z těchto operací očekává, že `id` parametr trasa:</span><span class="sxs-lookup"><span data-stu-id="6edbc-146">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="6edbc-147">Další možností je spustit `ui` příkaz pro otevření stránky uživatelské rozhraní Swagger webového rozhraní API v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6edbc-147">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="6edbc-148">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-148">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="6edbc-149">Přejděte na koncový bod</span><span class="sxs-lookup"><span data-stu-id="6edbc-149">Navigate to an endpoint</span></span>

<span data-ttu-id="6edbc-150">Chcete-li přejít k jinému koncovému bodu na webové rozhraní API, spusťte `cd` příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-150">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="6edbc-151">Následující cesta `cd` příkaz velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="6edbc-151">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="6edbc-152">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="6edbc-152">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="6edbc-153">Přizpůsobení HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="6edbc-153">Customize the HTTP REPL</span></span>

<span data-ttu-id="6edbc-154">Výchozí HTTP REPL [barvy](#set-color-preferences) lze přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="6edbc-154">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="6edbc-155">Kromě toho [výchozí text editor](#set-the-default-text-editor) lze definovat.</span><span class="sxs-lookup"><span data-stu-id="6edbc-155">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="6edbc-156">Předvolby protokolu HTTP REPL se zachová pro aktuální relaci a jsou respektovat v budoucích relacích.</span><span class="sxs-lookup"><span data-stu-id="6edbc-156">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="6edbc-157">Jakmile upravit, předvolby jsou uložené v následujícím souboru:</span><span class="sxs-lookup"><span data-stu-id="6edbc-157">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="6edbc-158">Linux</span><span class="sxs-lookup"><span data-stu-id="6edbc-158">Linux</span></span>](#tab/linux)

<span data-ttu-id="6edbc-159">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="6edbc-159">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="6edbc-160">macOS</span><span class="sxs-lookup"><span data-stu-id="6edbc-160">macOS</span></span>](#tab/macos)

<span data-ttu-id="6edbc-161">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="6edbc-161">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="6edbc-162">Windows</span><span class="sxs-lookup"><span data-stu-id="6edbc-162">Windows</span></span>](#tab/windows)

<span data-ttu-id="6edbc-163">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="6edbc-163">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="6edbc-164">*.Httpreplprefs* je soubor načten při spuštění a nemonitorováno pro změny za běhu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-164">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="6edbc-165">Ruční změny do souboru se projeví až po restartování nástroj.</span><span class="sxs-lookup"><span data-stu-id="6edbc-165">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="6edbc-166">Zobrazit nastavení</span><span class="sxs-lookup"><span data-stu-id="6edbc-166">View the settings</span></span>

<span data-ttu-id="6edbc-167">Pokud chcete zobrazit dostupná nastavení, spusťte `pref get` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-167">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="6edbc-168">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-168">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="6edbc-169">Předchozí příkaz zobrazí dostupné páry klíč hodnota:</span><span class="sxs-lookup"><span data-stu-id="6edbc-169">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="6edbc-170">Nastavení barev předvoleb</span><span class="sxs-lookup"><span data-stu-id="6edbc-170">Set color preferences</span></span>

<span data-ttu-id="6edbc-171">Zabarvení odpovědi se aktuálně podporuje pro formát JSON pouze.</span><span class="sxs-lookup"><span data-stu-id="6edbc-171">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="6edbc-172">Přizpůsobení výchozí nástroj HTTP REPL barevné zvýrazňování, vyhledejte klíč odpovídající barvu, která se změnit.</span><span class="sxs-lookup"><span data-stu-id="6edbc-172">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="6edbc-173">Pokyny o tom, jak najít klíčů najdete v tématu [zobrazit nastavení](#view-the-settings) oddílu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-173">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="6edbc-174">Například změnit `colors.json` hodnotu z klíče `Green` k `White` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="6edbc-174">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="6edbc-175">Pouze [povolené barvy](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) mohou být použity.</span><span class="sxs-lookup"><span data-stu-id="6edbc-175">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="6edbc-176">Zobrazit výstup s nové barevné zvýraznění žádostí následné HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-176">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="6edbc-177">Pokud nejsou nastavené určitá barva klíče, jsou považovány za obecnější klíče.</span><span class="sxs-lookup"><span data-stu-id="6edbc-177">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="6edbc-178">Abychom si předvedli tuto náhradní chování, zvažte následující příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-178">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="6edbc-179">Pokud `colors.json.name` nemá žádnou hodnotu, `colors.json.string` se používá.</span><span class="sxs-lookup"><span data-stu-id="6edbc-179">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="6edbc-180">Pokud `colors.json.string` nemá žádnou hodnotu, `colors.json.literal` se používá.</span><span class="sxs-lookup"><span data-stu-id="6edbc-180">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="6edbc-181">Pokud `colors.json.literal` nemá žádnou hodnotu, `colors.json` se používá.</span><span class="sxs-lookup"><span data-stu-id="6edbc-181">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="6edbc-182">Pokud `colors.json` nemá žádnou hodnotu, výchozí barva textu příkazového okna (`AllowedColors.None`) se používá.</span><span class="sxs-lookup"><span data-stu-id="6edbc-182">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="6edbc-183">Velikost odsazení sady</span><span class="sxs-lookup"><span data-stu-id="6edbc-183">Set indentation size</span></span>

<span data-ttu-id="6edbc-184">Přizpůsobení velikosti odpovědi odsazení aktuálně podporuje pro formát JSON pouze.</span><span class="sxs-lookup"><span data-stu-id="6edbc-184">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="6edbc-185">Výchozí velikost je dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="6edbc-185">The default size is two spaces.</span></span> <span data-ttu-id="6edbc-186">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-186">For example:</span></span>

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

<span data-ttu-id="6edbc-187">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="6edbc-187">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="6edbc-188">Například chcete vždy používal čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="6edbc-188">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="6edbc-189">Odpovědi na následné respektovat nastavení čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="6edbc-189">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="6edbc-190">Velikost odsazení sady</span><span class="sxs-lookup"><span data-stu-id="6edbc-190">Set indentation size</span></span>

<span data-ttu-id="6edbc-191">Přizpůsobení velikosti odpovědi odsazení aktuálně podporuje pro formát JSON pouze.</span><span class="sxs-lookup"><span data-stu-id="6edbc-191">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="6edbc-192">Výchozí velikost je dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="6edbc-192">The default size is two spaces.</span></span> <span data-ttu-id="6edbc-193">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-193">For example:</span></span>

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

<span data-ttu-id="6edbc-194">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="6edbc-194">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="6edbc-195">Například chcete vždy používal čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="6edbc-195">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="6edbc-196">Odpovědi na následné respektovat nastavení čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="6edbc-196">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="6edbc-197">Nastavit výchozí textový editor</span><span class="sxs-lookup"><span data-stu-id="6edbc-197">Set the default text editor</span></span>

<span data-ttu-id="6edbc-198">Ve výchozím nastavení HTTP REPL nemá žádný textový editor, který je nakonfigurován pro použití.</span><span class="sxs-lookup"><span data-stu-id="6edbc-198">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="6edbc-199">K otestování webové rozhraní API metody vyžadující textu požadavku HTTP, musí být nastavena výchozí textového editoru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-199">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="6edbc-200">K jedinému účelu – sestavování textu žádosti se spustí nástroj HTTP REPL nakonfigurované textového editoru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-200">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="6edbc-201">Spusťte následující příkaz pro nastavení upřednostňovaném textovém editoru jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="6edbc-201">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="6edbc-202">V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textový editor.</span><span class="sxs-lookup"><span data-stu-id="6edbc-202">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="6edbc-203">Například spusťte následující příkaz pro nastavení jako výchozí text editor Visual Studio Code:</span><span class="sxs-lookup"><span data-stu-id="6edbc-203">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="6edbc-204">Linux</span><span class="sxs-lookup"><span data-stu-id="6edbc-204">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="6edbc-205">macOS</span><span class="sxs-lookup"><span data-stu-id="6edbc-205">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="6edbc-206">Windows</span><span class="sxs-lookup"><span data-stu-id="6edbc-206">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="6edbc-207">Chcete-li spustit výchozí text editor s konkrétní argumenty příkazového řádku, nastavte `editor.command.default.arguments` klíč.</span><span class="sxs-lookup"><span data-stu-id="6edbc-207">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="6edbc-208">Předpokládejme například, Visual Studio Code je výchozí textový editor a mají vždy REPL HTTP otevřete Visual Studio Code v nové relaci s příponami zakázán.</span><span class="sxs-lookup"><span data-stu-id="6edbc-208">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="6edbc-209">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-209">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="6edbc-210">Testování požadavky HTTP GET</span><span class="sxs-lookup"><span data-stu-id="6edbc-210">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-211">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-211">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-212">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-212">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-213">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-213">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-214">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-214">Options</span></span>

<span data-ttu-id="6edbc-215">Jsou k dispozici pro následující možnosti `get` příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-215">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="6edbc-216">Příklad</span><span class="sxs-lookup"><span data-stu-id="6edbc-216">Example</span></span>

<span data-ttu-id="6edbc-217">Vydání požadavku HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="6edbc-217">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="6edbc-218">Spustit `get` příkaz na koncový bod, který to podporuje:</span><span class="sxs-lookup"><span data-stu-id="6edbc-218">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="6edbc-219">Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="6edbc-219">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="6edbc-220">Vyhledat konkrétní záznam tím, že předáte parametr, který se `get` příkaz:</span><span class="sxs-lookup"><span data-stu-id="6edbc-220">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="6edbc-221">Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="6edbc-221">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="6edbc-222">Testování požadavky HTTP POST</span><span class="sxs-lookup"><span data-stu-id="6edbc-222">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-223">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-223">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-224">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-224">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-225">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-225">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-226">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-226">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="6edbc-227">Příklad</span><span class="sxs-lookup"><span data-stu-id="6edbc-227">Example</span></span>

<span data-ttu-id="6edbc-228">Vydání požadavku HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="6edbc-228">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="6edbc-229">Spustit `post` příkaz na koncový bod, který to podporuje:</span><span class="sxs-lookup"><span data-stu-id="6edbc-229">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="6edbc-230">V předchozím příkazu `Content-Type` hlavičku požadavku HTTP je nastaven označující typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="6edbc-230">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="6edbc-231">Otevře se editor výchozí text *tmp* soubor pomocí šablony JSON představující obsahu žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-231">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="6edbc-232">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-232">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="6edbc-233">Pokud chcete nastavit výchozí textového editoru, najdete v článku [nastavit výchozí text editor](#set-the-default-text-editor) části.</span><span class="sxs-lookup"><span data-stu-id="6edbc-233">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="6edbc-234">Upravte šablonu JSON, aby byly splněny požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="6edbc-234">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="6edbc-235">Uložit *tmp* soubor a zavřete textového editoru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-235">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="6edbc-236">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-236">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="6edbc-237">Testovací žádosti HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="6edbc-237">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-238">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-238">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-239">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-239">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-240">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-240">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-241">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-241">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="6edbc-242">Příklad</span><span class="sxs-lookup"><span data-stu-id="6edbc-242">Example</span></span>

<span data-ttu-id="6edbc-243">Vydání požadavku HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="6edbc-243">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="6edbc-244">*Volitelné*: Spustit `get` příkazu zobrazíte data před její změny:</span><span class="sxs-lookup"><span data-stu-id="6edbc-244">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="6edbc-245">V předchozím příkazu `Content-Type` hlavičku požadavku HTTP je nastaven označující typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="6edbc-245">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="6edbc-246">Otevře se editor výchozí text *tmp* soubor pomocí šablony JSON představující obsahu žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-246">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="6edbc-247">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-247">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="6edbc-248">Pokud chcete nastavit výchozí textového editoru, najdete v článku [nastavit výchozí text editor](#set-the-default-text-editor) části.</span><span class="sxs-lookup"><span data-stu-id="6edbc-248">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="6edbc-249">Upravte šablonu JSON, aby byly splněny požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="6edbc-249">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="6edbc-250">Uložit *tmp* soubor a zavřete textového editoru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-250">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="6edbc-251">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-251">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="6edbc-252">*Volitelné*: Problém `get` příkazu zobrazte změny.</span><span class="sxs-lookup"><span data-stu-id="6edbc-252">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="6edbc-253">Pokud jste zadali "Výběru určitých položek" v textovém editoru, například `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="6edbc-253">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="6edbc-254">Testovací žádosti HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="6edbc-254">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-255">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-255">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-256">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-256">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-257">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-257">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-258">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-258">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="6edbc-259">Příklad</span><span class="sxs-lookup"><span data-stu-id="6edbc-259">Example</span></span>

<span data-ttu-id="6edbc-260">Vydání požadavku HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="6edbc-260">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="6edbc-261">*Volitelné*: Spustit `get` příkazu zobrazíte data před její změny:</span><span class="sxs-lookup"><span data-stu-id="6edbc-261">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="6edbc-262">Ve výstupu předchozího příkazu se zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="6edbc-262">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="6edbc-263">*Volitelné*: Problém `get` příkazu zobrazte změny.</span><span class="sxs-lookup"><span data-stu-id="6edbc-263">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="6edbc-264">V tomto příkladu `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="6edbc-264">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="6edbc-265">Testovací žádosti HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="6edbc-265">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-266">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-266">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-267">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-267">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-268">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-268">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-269">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-269">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="6edbc-270">Testování požadavku HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="6edbc-270">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-271">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-271">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-272">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-272">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-273">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-273">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-274">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-274">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="6edbc-275">Testovací žádosti HTTP OPTIONS</span><span class="sxs-lookup"><span data-stu-id="6edbc-275">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="6edbc-276">Souhrn</span><span class="sxs-lookup"><span data-stu-id="6edbc-276">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="6edbc-277">Arguments</span><span class="sxs-lookup"><span data-stu-id="6edbc-277">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="6edbc-278">Parametr trasy, pokud existuje, očekává metody akce k přidruženému kontroleru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-278">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="6edbc-279">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-279">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="6edbc-280">Nastavit hlavičku žádosti HTTP</span><span class="sxs-lookup"><span data-stu-id="6edbc-280">Set HTTP request headers</span></span>

<span data-ttu-id="6edbc-281">Pokud chcete nastavit hlavičku žádosti HTTP, použijte jednu z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="6edbc-281">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="6edbc-282">Nastavit pomocí vložených s požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-282">Set inline with the HTTP request.</span></span> <span data-ttu-id="6edbc-283">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-283">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="6edbc-284">S předchozím přístup každý jedinečných hlavičku požadavku HTTP vyžaduje svou vlastní `-h` možnost.</span><span class="sxs-lookup"><span data-stu-id="6edbc-284">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="6edbc-285">Nastavit před odesláním požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-285">Set before sending the HTTP request.</span></span> <span data-ttu-id="6edbc-286">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-286">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="6edbc-287">Při nastavení záhlaví před odesláním požadavku, zůstane po dobu trvání relace příkazového prostředí nastavte záhlaví.</span><span class="sxs-lookup"><span data-stu-id="6edbc-287">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="6edbc-288">Pokud chcete vymazat záhlaví, zadejte prázdnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-288">To clear the header, provide an empty value.</span></span> <span data-ttu-id="6edbc-289">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-289">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="6edbc-290">Přepnout zobrazení požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="6edbc-290">Toggle HTTP request display</span></span>

<span data-ttu-id="6edbc-291">Ve výchozím nastavení je potlačeno zobrazit odeslání požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="6edbc-291">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="6edbc-292">Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="6edbc-292">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="6edbc-293">Povolit žádosti o zobrazení</span><span class="sxs-lookup"><span data-stu-id="6edbc-293">Enable request display</span></span>

<span data-ttu-id="6edbc-294">Zobrazit spuštěním odeslání požadavku HTTP `echo on` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-294">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="6edbc-295">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-295">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="6edbc-296">Následné žádosti protokolu HTTP v aktuální relaci zobrazit záhlaví požadavku.</span><span class="sxs-lookup"><span data-stu-id="6edbc-296">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="6edbc-297">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-297">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="6edbc-298">Zakázat zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="6edbc-298">Disable request display</span></span>

<span data-ttu-id="6edbc-299">Potlačit zobrazení spuštěním odeslání požadavku HTTP `echo off` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-299">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="6edbc-300">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-300">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="6edbc-301">Spuštění skriptu</span><span class="sxs-lookup"><span data-stu-id="6edbc-301">Run a script</span></span>

<span data-ttu-id="6edbc-302">Pokud často spustit stejnou sadu příkazů HTTP REPL, zvažte jejich ukládáním do textového souboru.</span><span class="sxs-lookup"><span data-stu-id="6edbc-302">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="6edbc-303">Příkazy v souboru stejnou formu jako ručně provést na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="6edbc-303">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="6edbc-304">Příkazy mohou být provedeny v dávkové způsobem pomocí `run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-304">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="6edbc-305">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-305">For example:</span></span>

1. <span data-ttu-id="6edbc-306">Vytvořte textový soubor obsahující sadu příkazů oddělených znaku nového řádku.</span><span class="sxs-lookup"><span data-stu-id="6edbc-306">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="6edbc-307">Pro ilustraci, vezměte v úvahu *lidé skript.txt* soubor, který obsahuje následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="6edbc-307">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="6edbc-308">Spustit `run` příkazu předávání v cestě k souboru text.</span><span class="sxs-lookup"><span data-stu-id="6edbc-308">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="6edbc-309">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6edbc-309">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="6edbc-310">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-310">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="6edbc-311">Vymazat výstup</span><span class="sxs-lookup"><span data-stu-id="6edbc-311">Clear the output</span></span>

<span data-ttu-id="6edbc-312">Chcete-li odebrat všechny výstupu zapsaného do příkazového okna nástroje HTTP REPL, spusťte `clear` nebo `cls` příkazu.</span><span class="sxs-lookup"><span data-stu-id="6edbc-312">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="6edbc-313">Pro ilustraci si představte, že příkazový řádek nástroje obsahuje následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-313">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="6edbc-314">Spusťte následující příkaz k vymazání výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-314">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="6edbc-315">Příkazové okno po spuštění předchozího příkazu, obsahuje pouze následující výstup:</span><span class="sxs-lookup"><span data-stu-id="6edbc-315">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="6edbc-316">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6edbc-316">Additional resources</span></span>

* [<span data-ttu-id="6edbc-317">Požadavky rozhraní REST API</span><span class="sxs-lookup"><span data-stu-id="6edbc-317">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="6edbc-318">Úložiště HTTP REPL GitHub</span><span class="sxs-lookup"><span data-stu-id="6edbc-318">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
