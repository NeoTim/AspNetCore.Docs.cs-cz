---
title: Testování webových rozhraní API pomocí protokolu HTTP REPL
author: scottaddie
description: Naučte se používat globální nástroj HTTP REPL .NET Core k procházení a testování ASP.NET Core webového rozhraní API.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 07/25/2019
uid: web-api/http-repl
ms.openlocfilehash: e719d599545810d723840b0800cd6a2b4f96b123
ms.sourcegitcommit: fbc66827e319d28bebed678ea5fd42f582fe3c34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493576"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="d6869-103">Testování webových rozhraní API pomocí protokolu HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d6869-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="d6869-104">[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d6869-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d6869-105">Smyčka HTTP Read-Eval-Print (REPL) je:</span><span class="sxs-lookup"><span data-stu-id="d6869-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="d6869-106">Podporuje se odlehčený nástroj příkazového řádku pro více platforem, který podporuje všude, kde je podporováno rozhraní .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d6869-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="d6869-107">Slouží k provádění požadavků HTTP na testování ASP.NET Core webových rozhraní API (a webových rozhraní API non-ASP.NET Core) a zobrazení jejich výsledků.</span><span class="sxs-lookup"><span data-stu-id="d6869-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="d6869-108">Je možné testovat webová rozhraní API hostovaná v jakémkoli prostředí, včetně localhost a Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="d6869-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="d6869-109">Podporovány jsou následující [Příkazy protokolu HTTP](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :</span><span class="sxs-lookup"><span data-stu-id="d6869-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="d6869-110">DSTRANIT</span><span class="sxs-lookup"><span data-stu-id="d6869-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="d6869-111">GET</span><span class="sxs-lookup"><span data-stu-id="d6869-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="d6869-112">ZÁHLAVÍ</span><span class="sxs-lookup"><span data-stu-id="d6869-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="d6869-113">NASTAVENÍ</span><span class="sxs-lookup"><span data-stu-id="d6869-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="d6869-114">POUŽITA</span><span class="sxs-lookup"><span data-stu-id="d6869-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="d6869-115">POST</span><span class="sxs-lookup"><span data-stu-id="d6869-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="d6869-116">PUT</span><span class="sxs-lookup"><span data-stu-id="d6869-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="d6869-117">Pokud chcete postup sledovat, [Zobrazte si ukázkové ASP.NET Core webové rozhraní API nebo si ho stáhněte](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d6869-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d6869-118">Požadavky</span><span class="sxs-lookup"><span data-stu-id="d6869-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="d6869-119">Instalace</span><span class="sxs-lookup"><span data-stu-id="d6869-119">Installation</span></span>

<span data-ttu-id="d6869-120">Pro instalaci HTTP REPL spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d6869-120">To install the HTTP REPL, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-httprepl --version "3.0.0-*"
```

<span data-ttu-id="d6869-121">[Globální nástroj .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) je nainstalovaný z balíčku NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .</span><span class="sxs-lookup"><span data-stu-id="d6869-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="d6869-122">Použití</span><span class="sxs-lookup"><span data-stu-id="d6869-122">Usage</span></span>

<span data-ttu-id="d6869-123">Po úspěšné instalaci nástroje spusťte následující příkaz, který spustí HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d6869-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
dotnet httprepl
```

<span data-ttu-id="d6869-124">Chcete-li zobrazit dostupné příkazy HTTP REPL, spusťte jeden z následujících příkazů:</span><span class="sxs-lookup"><span data-stu-id="d6869-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
dotnet httprepl -h
```

```console
dotnet httprepl --help
```

<span data-ttu-id="d6869-125">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-125">The following output is displayed:</span></span>

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

<span data-ttu-id="d6869-126">Příkaz HTTP REPL nabízí dokončování příkazů.</span><span class="sxs-lookup"><span data-stu-id="d6869-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="d6869-127">Stisknutí klávesy <kbd>TAB</kbd> prochází seznam příkazů, které dokončí znaky nebo koncový bod rozhraní API, který jste zadali.</span><span class="sxs-lookup"><span data-stu-id="d6869-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="d6869-128">Následující části popisují dostupné příkazy rozhraní příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d6869-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="d6869-129">Připojení k webovému rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d6869-129">Connect to the web API</span></span>

<span data-ttu-id="d6869-130">Připojte se k webovému rozhraní API spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d6869-130">Connect to a web API by running the following command:</span></span>

```console
dotnet httprepl <BASE URI>
```

<span data-ttu-id="d6869-131">`<BASE URI>`je základní identifikátor URI pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d6869-131">`<BASE URI>` is the base URI for the web API.</span></span> <span data-ttu-id="d6869-132">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-132">For example:</span></span>

```console
dotnet httprepl https://localhost:5001
```

<span data-ttu-id="d6869-133">Případně spusťte následující příkaz kdykoli, když je spuštěn protokol HTTP REPL:</span><span class="sxs-lookup"><span data-stu-id="d6869-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
set base <BASE URI>
```

<span data-ttu-id="d6869-134">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-134">For example:</span></span>

```console
(Disconnected)~ set base https://localhost:5001
```

## <a name="point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="d6869-135">Nasměrování na dokument Swagger pro webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="d6869-135">Point to the Swagger document for the web API</span></span>

<span data-ttu-id="d6869-136">Pro správné prohlížení webového rozhraní API nastavte relativní identifikátor URI na dokument Swagger pro webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d6869-136">To properly inspect the web API, set the relative URI to the Swagger document for the web API.</span></span> <span data-ttu-id="d6869-137">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d6869-137">Run the following command:</span></span>

```console
set swagger <RELATIVE URI>
```

<span data-ttu-id="d6869-138">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-138">For example:</span></span>

```console
https://localhost:5001/~ set swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="d6869-139">Navigace v rozhraní Web API</span><span class="sxs-lookup"><span data-stu-id="d6869-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="d6869-140">Zobrazit dostupné koncové body</span><span class="sxs-lookup"><span data-stu-id="d6869-140">View available endpoints</span></span>

<span data-ttu-id="d6869-141">Pokud chcete zobrazit seznam různých koncových bodů (řadičů) na aktuální cestě adresy webového rozhraní API, spusťte `ls` příkaz `dir` nebo:</span><span class="sxs-lookup"><span data-stu-id="d6869-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="d6869-142">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d6869-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="d6869-143">Předchozí výstup ukazuje, že jsou k dispozici dva řadiče `Fruits` : `People`a.</span><span class="sxs-lookup"><span data-stu-id="d6869-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="d6869-144">Oba řadiče podporují operace HTTP GET a POST bez parametrů.</span><span class="sxs-lookup"><span data-stu-id="d6869-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="d6869-145">Přechod na konkrétní kontroler odhalí více podrobností.</span><span class="sxs-lookup"><span data-stu-id="d6869-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="d6869-146">Například výstup následujícího příkazu ukazuje, že `Fruits` kontroler také podporuje operace HTTP GET, PUT a DELETE.</span><span class="sxs-lookup"><span data-stu-id="d6869-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="d6869-147">Každá z těchto operací očekává `id` v trase parametr:</span><span class="sxs-lookup"><span data-stu-id="d6869-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="d6869-148">Případně spusťte `ui` příkaz a otevřete stránku uživatelského rozhraní Swagger webového rozhraní API v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="d6869-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="d6869-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="d6869-150">Přejít na koncový bod</span><span class="sxs-lookup"><span data-stu-id="d6869-150">Navigate to an endpoint</span></span>

<span data-ttu-id="d6869-151">Pokud chcete přejít na jiný koncový bod webového rozhraní API, spusťte `cd` příkaz:</span><span class="sxs-lookup"><span data-stu-id="d6869-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="d6869-152">Cesta za `cd` příkazem nerozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="d6869-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="d6869-153">Zobrazí se následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d6869-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="d6869-154">Přizpůsobení REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="d6869-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="d6869-155">Výchozí [barvy](#set-color-preferences) REPL http je možné přizpůsobit.</span><span class="sxs-lookup"><span data-stu-id="d6869-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="d6869-156">Kromě toho lze definovat [výchozí textový editor](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d6869-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="d6869-157">Předvolby HTTP REPL jsou trvale v rámci aktuální relace a jsou v budoucích relacích dodrženy.</span><span class="sxs-lookup"><span data-stu-id="d6869-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="d6869-158">Po úpravě jsou předvolby uložené v následujícím souboru:</span><span class="sxs-lookup"><span data-stu-id="d6869-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d6869-159">Linux</span><span class="sxs-lookup"><span data-stu-id="d6869-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="d6869-160">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d6869-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="d6869-161">macOS</span><span class="sxs-lookup"><span data-stu-id="d6869-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="d6869-162">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d6869-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="d6869-163">Windows</span><span class="sxs-lookup"><span data-stu-id="d6869-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="d6869-164">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="d6869-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="d6869-165">Soubor *. httpreplprefs* je načten při spuštění a není monitorován pro změny za běhu.</span><span class="sxs-lookup"><span data-stu-id="d6869-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="d6869-166">Ruční úpravy souboru se projeví až po restartování nástroje.</span><span class="sxs-lookup"><span data-stu-id="d6869-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="d6869-167">Zobrazit nastavení</span><span class="sxs-lookup"><span data-stu-id="d6869-167">View the settings</span></span>

<span data-ttu-id="d6869-168">Dostupná nastavení zobrazíte spuštěním `pref get` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d6869-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="d6869-169">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="d6869-170">Předchozí příkaz zobrazí dostupné páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="d6869-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="d6869-171">Nastavit předvolby barev</span><span class="sxs-lookup"><span data-stu-id="d6869-171">Set color preferences</span></span>

<span data-ttu-id="d6869-172">Vybarvení odpovědi je aktuálně podporováno pouze pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d6869-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="d6869-173">Chcete-li přizpůsobit výchozí barevné vybarvení nástroje HTTP REPL, vyhledejte klíč odpovídající barvě, který má být změněn.</span><span class="sxs-lookup"><span data-stu-id="d6869-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="d6869-174">Pokyny k vyhledání klíčů najdete v části [zobrazení nastavení](#view-the-settings) .</span><span class="sxs-lookup"><span data-stu-id="d6869-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="d6869-175">Například hodnotu `colors.json` klíče můžete změnit z `Green` na `White` následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="d6869-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="d6869-176">Mohou být použity pouze [povolené barvy](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) .</span><span class="sxs-lookup"><span data-stu-id="d6869-176">Only the [allowed colors](https://github.com/aspnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="d6869-177">Následné požadavky HTTP zobrazují výstup s novými zvýrazněními.</span><span class="sxs-lookup"><span data-stu-id="d6869-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="d6869-178">Pokud nejsou nastavené určité klíče barev, považují se za obecnější klíče.</span><span class="sxs-lookup"><span data-stu-id="d6869-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="d6869-179">K předvedení tohoto nouzového chování Vezměte v úvahu následující příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="d6869-180">Pokud `colors.json.name` nemá hodnotu, `colors.json.string` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d6869-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="d6869-181">Pokud `colors.json.string` nemá hodnotu, `colors.json.literal` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d6869-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="d6869-182">Pokud `colors.json.literal` nemá hodnotu, `colors.json` je použita hodnota.</span><span class="sxs-lookup"><span data-stu-id="d6869-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="d6869-183">Pokud `colors.json` hodnota nemá hodnotu, použije se výchozí barva textu (`AllowedColors.None`) příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d6869-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="d6869-184">Nastavit velikost odsazení</span><span class="sxs-lookup"><span data-stu-id="d6869-184">Set indentation size</span></span>

<span data-ttu-id="d6869-185">Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d6869-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d6869-186">Výchozí velikost jsou dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="d6869-186">The default size is two spaces.</span></span> <span data-ttu-id="d6869-187">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-187">For example:</span></span>

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

<span data-ttu-id="d6869-188">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="d6869-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d6869-189">Například pokud chcete vždy použít čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="d6869-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d6869-190">Následující odpovědi dodržují nastavení čtyř mezer:</span><span class="sxs-lookup"><span data-stu-id="d6869-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-indentation-size"></a><span data-ttu-id="d6869-191">Nastavit velikost odsazení</span><span class="sxs-lookup"><span data-stu-id="d6869-191">Set indentation size</span></span>

<span data-ttu-id="d6869-192">Přizpůsobení velikosti odsazení odpovědí se v současné době podporuje jenom pro JSON.</span><span class="sxs-lookup"><span data-stu-id="d6869-192">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="d6869-193">Výchozí velikost jsou dvě mezery.</span><span class="sxs-lookup"><span data-stu-id="d6869-193">The default size is two spaces.</span></span> <span data-ttu-id="d6869-194">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-194">For example:</span></span>

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

<span data-ttu-id="d6869-195">Chcete-li změnit výchozí velikost, nastavte `formatting.json.indentSize` klíč.</span><span class="sxs-lookup"><span data-stu-id="d6869-195">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="d6869-196">Například pokud chcete vždy použít čtyři mezery:</span><span class="sxs-lookup"><span data-stu-id="d6869-196">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="d6869-197">Následující odpovědi dodržují nastavení čtyř mezer:</span><span class="sxs-lookup"><span data-stu-id="d6869-197">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="d6869-198">Nastavit výchozí textový editor</span><span class="sxs-lookup"><span data-stu-id="d6869-198">Set the default text editor</span></span>

<span data-ttu-id="d6869-199">Ve výchozím nastavení nemá protokol HTTP REPL nakonfigurovaný žádný textový editor pro použití.</span><span class="sxs-lookup"><span data-stu-id="d6869-199">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="d6869-200">Chcete-li otestovat metody webového rozhraní API, které vyžadují tělo požadavku HTTP, musí být nastaven výchozí textový editor.</span><span class="sxs-lookup"><span data-stu-id="d6869-200">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="d6869-201">Nástroj HTTP REPL spustí nakonfigurovaný textový editor pro výhradní účely vytváření textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="d6869-201">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="d6869-202">Spusťte následující příkaz, který nastaví upřednostňovaný textový editor jako výchozí:</span><span class="sxs-lookup"><span data-stu-id="d6869-202">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="d6869-203">V předchozím příkazu `<EXECUTABLE>` je úplná cesta ke spustitelnému souboru textového editoru.</span><span class="sxs-lookup"><span data-stu-id="d6869-203">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="d6869-204">Například spusťte následující příkaz, který nastaví Visual Studio Code jako výchozí textový editor:</span><span class="sxs-lookup"><span data-stu-id="d6869-204">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="d6869-205">Linux</span><span class="sxs-lookup"><span data-stu-id="d6869-205">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macostabmacos"></a>[<span data-ttu-id="d6869-206">macOS</span><span class="sxs-lookup"><span data-stu-id="d6869-206">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windowstabwindows"></a>[<span data-ttu-id="d6869-207">Windows</span><span class="sxs-lookup"><span data-stu-id="d6869-207">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="d6869-208">Chcete-li spustit výchozí textový editor s konkrétními argumenty rozhraní příkazového řádku, nastavte `editor.command.default.arguments` klíč.</span><span class="sxs-lookup"><span data-stu-id="d6869-208">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="d6869-209">Předpokládejme například, že Visual Studio Code je výchozím textovým editorem a chcete, aby se v nové relaci s vypnutými rozšířeními otevřela Visual Studio Code HTTP REPL.</span><span class="sxs-lookup"><span data-stu-id="d6869-209">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="d6869-210">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="d6869-210">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="d6869-211">Testování požadavků HTTP GET</span><span class="sxs-lookup"><span data-stu-id="d6869-211">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-212">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-212">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-213">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-213">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-214">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-214">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-215">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-215">Options</span></span>

<span data-ttu-id="d6869-216">Pro `get` příkaz jsou k dispozici následující možnosti:</span><span class="sxs-lookup"><span data-stu-id="d6869-216">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d6869-217">Příklad</span><span class="sxs-lookup"><span data-stu-id="d6869-217">Example</span></span>

<span data-ttu-id="d6869-218">Vystavení požadavku HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="d6869-218">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="d6869-219">`get` Spusťte příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d6869-219">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="d6869-220">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d6869-220">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="d6869-221">Načtěte určitý záznam předáním parametru `get` příkazu:</span><span class="sxs-lookup"><span data-stu-id="d6869-221">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="d6869-222">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d6869-222">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="d6869-223">Test požadavků HTTP POST</span><span class="sxs-lookup"><span data-stu-id="d6869-223">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-224">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-224">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-225">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-225">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-226">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-226">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-227">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-227">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d6869-228">Příklad</span><span class="sxs-lookup"><span data-stu-id="d6869-228">Example</span></span>

<span data-ttu-id="d6869-229">Vystavení požadavku HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="d6869-229">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="d6869-230">`post` Spusťte příkaz na koncovém bodu, který ho podporuje:</span><span class="sxs-lookup"><span data-stu-id="d6869-230">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="d6869-231">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d6869-231">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d6869-232">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d6869-232">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d6869-233">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-233">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d6869-234">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d6869-234">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d6869-235">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d6869-235">Modify the JSON template to satisfy model validation requirements:</span></span>

  ```json
  {
    "id": 0,
    "name": "Scott Addie"
  }
  ```

1. <span data-ttu-id="d6869-236">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d6869-236">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d6869-237">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-237">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="d6869-238">Test požadavků HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="d6869-238">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-239">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-239">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-240">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-240">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-241">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-241">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-242">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-242">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="d6869-243">Příklad</span><span class="sxs-lookup"><span data-stu-id="d6869-243">Example</span></span>

<span data-ttu-id="d6869-244">Vydání požadavku HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="d6869-244">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="d6869-245">*Volitelné*: `get` Spusťte příkaz pro zobrazení dat před jeho úpravou:</span><span class="sxs-lookup"><span data-stu-id="d6869-245">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="d6869-246">V předchozím příkazu `Content-Type` je hlavička požadavku HTTP nastavená tak, aby označovala typ média textu požadavku JSON.</span><span class="sxs-lookup"><span data-stu-id="d6869-246">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="d6869-247">Výchozí textový editor otevře soubor *. tmp* se ŠABLONou JSON, která představuje tělo požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d6869-247">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="d6869-248">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-248">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="d6869-249">Chcete-li nastavit výchozí textový editor, přečtěte si část [Nastavení výchozího textového editoru](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="d6869-249">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="d6869-250">Upravte šablonu JSON tak, aby splňovala požadavky na ověření modelu:</span><span class="sxs-lookup"><span data-stu-id="d6869-250">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="d6869-251">Uložte soubor *. tmp* a ukončete textový editor.</span><span class="sxs-lookup"><span data-stu-id="d6869-251">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="d6869-252">V příkazovém prostředí se zobrazí následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-252">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d6869-253">*Volitelné*: Vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d6869-253">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d6869-254">Například pokud jste v textovém editoru zadali "třešně", `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d6869-254">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="d6869-255">Test požadavků HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="d6869-255">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-256">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-256">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-257">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-258">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-259">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="d6869-260">Příklad</span><span class="sxs-lookup"><span data-stu-id="d6869-260">Example</span></span>

<span data-ttu-id="d6869-261">Postup při vystavení žádosti o odstranění protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="d6869-261">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="d6869-262">*Volitelné*: `get` Spusťte příkaz pro zobrazení dat před jeho úpravou:</span><span class="sxs-lookup"><span data-stu-id="d6869-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

    <span data-ttu-id="d6869-263">Předchozí příkaz zobrazí následující výstupní formát:</span><span class="sxs-lookup"><span data-stu-id="d6869-263">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="d6869-264">*Volitelné*: Vydejte `get` příkaz pro zobrazení úprav.</span><span class="sxs-lookup"><span data-stu-id="d6869-264">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="d6869-265">V tomto příkladu, `get` vrátí následující:</span><span class="sxs-lookup"><span data-stu-id="d6869-265">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="d6869-266">Test požadavků na opravy HTTP</span><span class="sxs-lookup"><span data-stu-id="d6869-266">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-267">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-267">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-268">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-268">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-269">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-269">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-270">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-270">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="d6869-271">Test požadavků HTTP HEAD</span><span class="sxs-lookup"><span data-stu-id="d6869-271">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-272">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-272">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-273">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-273">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-274">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-274">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-275">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-275">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="d6869-276">Požadavky na test možností protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="d6869-276">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="d6869-277">Stručný obsah</span><span class="sxs-lookup"><span data-stu-id="d6869-277">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="d6869-278">Arguments</span><span class="sxs-lookup"><span data-stu-id="d6869-278">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="d6869-279">Parametr trasy (pokud existuje), který očekává přidružená metoda akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="d6869-279">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="d6869-280">Možnosti</span><span class="sxs-lookup"><span data-stu-id="d6869-280">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="d6869-281">Nastavení hlaviček požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="d6869-281">Set HTTP request headers</span></span>

<span data-ttu-id="d6869-282">Pokud chcete nastavit hlavičku požadavku HTTP, použijte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d6869-282">To set an HTTP request header, use one of the following approaches:</span></span>

1. <span data-ttu-id="d6869-283">Nastavte inline s požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="d6869-283">Set inline with the HTTP request.</span></span> <span data-ttu-id="d6869-284">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-284">For example:</span></span>

  ```console
  https://localhost:5001/people~ post -h Content-Type=application/json
  ```

  <span data-ttu-id="d6869-285">V případě předchozího přístupu vyžaduje každá samostatná Hlavička požadavku HTTP vlastní `-h` možnost.</span><span class="sxs-lookup"><span data-stu-id="d6869-285">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

1. <span data-ttu-id="d6869-286">Nastaveno před odesláním požadavku HTTP.</span><span class="sxs-lookup"><span data-stu-id="d6869-286">Set before sending the HTTP request.</span></span> <span data-ttu-id="d6869-287">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-287">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type application/json
  ```

  <span data-ttu-id="d6869-288">Při nastavování hlavičky před odesláním žádosti zůstane záhlaví nastavené na dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d6869-288">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="d6869-289">Pokud chcete záhlaví vymazat, zadejte prázdnou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d6869-289">To clear the header, provide an empty value.</span></span> <span data-ttu-id="d6869-290">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-290">For example:</span></span>

  ```console
  https://localhost:5001/people~ set header Content-Type
  ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="d6869-291">Přepnout zobrazení požadavku HTTP</span><span class="sxs-lookup"><span data-stu-id="d6869-291">Toggle HTTP request display</span></span>

<span data-ttu-id="d6869-292">Ve výchozím nastavení se zobrazí potlačení požadavku HTTP na odeslání.</span><span class="sxs-lookup"><span data-stu-id="d6869-292">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="d6869-293">Je možné změnit odpovídající nastavení po dobu trvání relace příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="d6869-293">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="d6869-294">Povolit zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d6869-294">Enable request display</span></span>

<span data-ttu-id="d6869-295">Spuštěním `echo on` příkazu Zobrazte požadavek HTTP, který odesíláte.</span><span class="sxs-lookup"><span data-stu-id="d6869-295">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="d6869-296">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-296">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="d6869-297">Následující požadavky HTTP v aktuální relaci zobrazují hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="d6869-297">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="d6869-298">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-298">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="d6869-299">Zakázat zobrazení žádosti</span><span class="sxs-lookup"><span data-stu-id="d6869-299">Disable request display</span></span>

<span data-ttu-id="d6869-300">Potlačit zobrazení požadavku HTTP odesílaného spuštěním `echo off` příkazu</span><span class="sxs-lookup"><span data-stu-id="d6869-300">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="d6869-301">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-301">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="d6869-302">Spuštění skriptu</span><span class="sxs-lookup"><span data-stu-id="d6869-302">Run a script</span></span>

<span data-ttu-id="d6869-303">Pokud často spustíte stejnou sadu příkazů HTTP REPL, zvažte jejich uložení do textového souboru.</span><span class="sxs-lookup"><span data-stu-id="d6869-303">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="d6869-304">Příkazy v souboru přebírají stejnou formu, jakou byly provedeny ručně na příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="d6869-304">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="d6869-305">Příkazy lze spustit v dávce způsobem pomocí `run` příkazu.</span><span class="sxs-lookup"><span data-stu-id="d6869-305">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="d6869-306">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-306">For example:</span></span>

1. <span data-ttu-id="d6869-307">Vytvoří textový soubor obsahující sadu příkazů s oddělovači na nový řádek.</span><span class="sxs-lookup"><span data-stu-id="d6869-307">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="d6869-308">Pro ilustraci zvažte soubor *People-Script. txt* , který obsahuje následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="d6869-308">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="d6869-309">`run` Spusťte příkaz a předejte cestu k textovému souboru.</span><span class="sxs-lookup"><span data-stu-id="d6869-309">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="d6869-310">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d6869-310">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="d6869-311">Zobrazí se následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-311">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="d6869-312">Vymazat výstup</span><span class="sxs-lookup"><span data-stu-id="d6869-312">Clear the output</span></span>

<span data-ttu-id="d6869-313">Pokud chcete odebrat veškerý výstup napsaný do příkazového prostředí nástrojem http REPL, spusťte `clear` příkaz nebo. `cls`</span><span class="sxs-lookup"><span data-stu-id="d6869-313">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="d6869-314">K ilustraci si představte, že příkazové prostředí obsahuje následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-314">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="d6869-315">Výstup vymažete spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="d6869-315">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="d6869-316">Po spuštění předchozího příkazu obsahuje příkazové prostředí pouze následující výstup:</span><span class="sxs-lookup"><span data-stu-id="d6869-316">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="d6869-317">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d6869-317">Additional resources</span></span>

* [<span data-ttu-id="d6869-318">REST API žádosti</span><span class="sxs-lookup"><span data-stu-id="d6869-318">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="d6869-319">Úložiště GitHub HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="d6869-319">HTTP REPL GitHub repository</span></span>](https://github.com/aspnet/HttpRepl)
