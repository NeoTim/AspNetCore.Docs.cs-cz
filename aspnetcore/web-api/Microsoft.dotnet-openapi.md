---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 4be2846f0348788102672978a0487e646da434a0
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354756"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="44791-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="44791-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="44791-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="44791-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="44791-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="44791-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="44791-106">Instalace služby</span><span class="sxs-lookup"><span data-stu-id="44791-106">Installation</span></span>

<span data-ttu-id="44791-107">Pokud chcete nainstalovat `Microsoft.dotnet-openapi`, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="44791-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="44791-108">Přidejte</span><span class="sxs-lookup"><span data-stu-id="44791-108">Add</span></span>

<span data-ttu-id="44791-109">Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá prvek `<OpenApiReference />` podobný následujícímu souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="44791-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="44791-110">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="44791-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="44791-111">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="44791-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="44791-112">Možnosti</span><span class="sxs-lookup"><span data-stu-id="44791-112">Options</span></span>

| <span data-ttu-id="44791-113">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="44791-113">Short option</span></span>| <span data-ttu-id="44791-114">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="44791-114">Long option</span></span>| <span data-ttu-id="44791-115">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-115">Description</span></span> | <span data-ttu-id="44791-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="44791-117">-p</span><span class="sxs-lookup"><span data-stu-id="44791-117">-p</span></span>|<span data-ttu-id="44791-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="44791-118">--updateProject</span></span> | <span data-ttu-id="44791-119">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="44791-119">The project to operate on.</span></span> |<span data-ttu-id="44791-120">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="44791-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="44791-121">-c</span><span class="sxs-lookup"><span data-stu-id="44791-121">-c</span></span>|<span data-ttu-id="44791-122">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="44791-122">--code-generator</span></span>| <span data-ttu-id="44791-123">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="44791-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="44791-124">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="44791-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="44791-125">Pokud `--code-generator` není zadán, výchozí nastavení nástrojů `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="44791-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="44791-126">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="44791-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="44791-127">-h</span><span class="sxs-lookup"><span data-stu-id="44791-127">-h</span></span>|<span data-ttu-id="44791-128">--Help</span><span class="sxs-lookup"><span data-stu-id="44791-128">--help</span></span>|<span data-ttu-id="44791-129">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="44791-129">Show help information</span></span>|<span data-ttu-id="44791-130">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="44791-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="44791-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="44791-131">Arguments</span></span>

|  <span data-ttu-id="44791-132">Argument</span><span class="sxs-lookup"><span data-stu-id="44791-132">Argument</span></span>  | <span data-ttu-id="44791-133">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-133">Description</span></span> | <span data-ttu-id="44791-134">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="44791-135">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="44791-135">source-file</span></span> | <span data-ttu-id="44791-136">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="44791-136">The source to create a reference from.</span></span> <span data-ttu-id="44791-137">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="44791-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="44791-138">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="44791-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="44791-139">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="44791-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="44791-140">Možnosti</span><span class="sxs-lookup"><span data-stu-id="44791-140">Options</span></span>

| <span data-ttu-id="44791-141">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="44791-141">Short option</span></span>| <span data-ttu-id="44791-142">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="44791-142">Long option</span></span>| <span data-ttu-id="44791-143">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-143">Description</span></span> | <span data-ttu-id="44791-144">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="44791-145">-p</span><span class="sxs-lookup"><span data-stu-id="44791-145">-p</span></span>|<span data-ttu-id="44791-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="44791-146">--updateProject</span></span> | <span data-ttu-id="44791-147">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="44791-147">The project to operate on.</span></span> |<span data-ttu-id="44791-148">dotnet openapi Add URL *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="44791-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="44791-149">-o</span><span class="sxs-lookup"><span data-stu-id="44791-149">-o</span></span>|<span data-ttu-id="44791-150">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="44791-150">--output-file</span></span> | <span data-ttu-id="44791-151">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="44791-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="44791-152">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="44791-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="44791-153">-c</span><span class="sxs-lookup"><span data-stu-id="44791-153">-c</span></span>|<span data-ttu-id="44791-154">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="44791-154">--code-generator</span></span>| <span data-ttu-id="44791-155">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="44791-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="44791-156">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="44791-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="44791-157">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="44791-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="44791-158">-h</span><span class="sxs-lookup"><span data-stu-id="44791-158">-h</span></span>|<span data-ttu-id="44791-159">--Help</span><span class="sxs-lookup"><span data-stu-id="44791-159">--help</span></span>|<span data-ttu-id="44791-160">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="44791-160">Show help information</span></span>|<span data-ttu-id="44791-161">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="44791-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="44791-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="44791-162">Arguments</span></span>

|  <span data-ttu-id="44791-163">Argument</span><span class="sxs-lookup"><span data-stu-id="44791-163">Argument</span></span>  | <span data-ttu-id="44791-164">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-164">Description</span></span> | <span data-ttu-id="44791-165">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="44791-166">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="44791-166">source-URL</span></span> | <span data-ttu-id="44791-167">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="44791-167">The source to create a reference from.</span></span> <span data-ttu-id="44791-168">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="44791-168">Must be a URL.</span></span> |<span data-ttu-id="44791-169">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="44791-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="44791-170">Odebrat</span><span class="sxs-lookup"><span data-stu-id="44791-170">Remove</span></span>

<span data-ttu-id="44791-171">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="44791-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="44791-172">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="44791-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="44791-173">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="44791-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="44791-174">Možnosti</span><span class="sxs-lookup"><span data-stu-id="44791-174">Options</span></span>

| <span data-ttu-id="44791-175">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="44791-175">Short option</span></span>| <span data-ttu-id="44791-176">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="44791-176">Long option</span></span>| <span data-ttu-id="44791-177">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-177">Description</span></span>| <span data-ttu-id="44791-178">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="44791-179">-p</span><span class="sxs-lookup"><span data-stu-id="44791-179">-p</span></span>|<span data-ttu-id="44791-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="44791-180">--updateProject</span></span> | <span data-ttu-id="44791-181">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="44791-181">The project to operate on.</span></span> |<span data-ttu-id="44791-182">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="44791-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="44791-183">-h</span><span class="sxs-lookup"><span data-stu-id="44791-183">-h</span></span>|<span data-ttu-id="44791-184">--Help</span><span class="sxs-lookup"><span data-stu-id="44791-184">--help</span></span>|<span data-ttu-id="44791-185">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="44791-185">Show help information</span></span>|<span data-ttu-id="44791-186">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="44791-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="44791-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="44791-187">Arguments</span></span>

|  <span data-ttu-id="44791-188">Argument</span><span class="sxs-lookup"><span data-stu-id="44791-188">Argument</span></span>  | <span data-ttu-id="44791-189">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-189">Description</span></span>| <span data-ttu-id="44791-190">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="44791-191">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="44791-191">source-file</span></span> | <span data-ttu-id="44791-192">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="44791-192">The source to remove the reference to.</span></span> |<span data-ttu-id="44791-193">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="44791-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="44791-194">Aktualizovat</span><span class="sxs-lookup"><span data-stu-id="44791-194">Refresh</span></span>

<span data-ttu-id="44791-195">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="44791-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="44791-196">Možnosti</span><span class="sxs-lookup"><span data-stu-id="44791-196">Options</span></span>

| <span data-ttu-id="44791-197">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="44791-197">Short option</span></span>| <span data-ttu-id="44791-198">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="44791-198">Long option</span></span>| <span data-ttu-id="44791-199">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-199">Description</span></span> | <span data-ttu-id="44791-200">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="44791-201">-p</span><span class="sxs-lookup"><span data-stu-id="44791-201">-p</span></span>|<span data-ttu-id="44791-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="44791-202">--updateProject</span></span> | <span data-ttu-id="44791-203">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="44791-203">The project to operate on.</span></span> | <span data-ttu-id="44791-204">dotnet openapi Refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="44791-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="44791-205">-h</span><span class="sxs-lookup"><span data-stu-id="44791-205">-h</span></span>|<span data-ttu-id="44791-206">--Help</span><span class="sxs-lookup"><span data-stu-id="44791-206">--help</span></span>|<span data-ttu-id="44791-207">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="44791-207">Show help information</span></span>|<span data-ttu-id="44791-208">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="44791-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="44791-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="44791-209">Arguments</span></span>

|  <span data-ttu-id="44791-210">Argument</span><span class="sxs-lookup"><span data-stu-id="44791-210">Argument</span></span>  | <span data-ttu-id="44791-211">Popis</span><span class="sxs-lookup"><span data-stu-id="44791-211">Description</span></span> | <span data-ttu-id="44791-212">Příklad</span><span class="sxs-lookup"><span data-stu-id="44791-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="44791-213">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="44791-213">source-URL</span></span> | <span data-ttu-id="44791-214">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="44791-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="44791-215">dotnet openapi Refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="44791-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
