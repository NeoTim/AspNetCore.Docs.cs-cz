---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187458"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="907db-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="907db-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="907db-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="907db-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="907db-105">`Microsoft.dotnet-openapi`je globální nástroj .NET Core pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="907db-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="907db-106">Instalace</span><span class="sxs-lookup"><span data-stu-id="907db-106">Installation</span></span>

<span data-ttu-id="907db-107">K instalaci `Microsoft.dotnet-openapi`spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="907db-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="907db-108">`Microsoft.dotnet-openapi`je [globální nástroj .NET Core](/dotnet/core/tools/global-tools).</span><span class="sxs-lookup"><span data-stu-id="907db-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="907db-109">Přidejte</span><span class="sxs-lookup"><span data-stu-id="907db-109">Add</span></span>

<span data-ttu-id="907db-110">Přidání odkazu openapi pomocí kteréhokoli z příkazů na této stránce přidá `<OpenApiReference />` do souboru *. csproj* prvek podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="907db-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="907db-111">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="907db-111">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="907db-112">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="907db-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="907db-113">Možnosti</span><span class="sxs-lookup"><span data-stu-id="907db-113">Options</span></span>

| <span data-ttu-id="907db-114">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="907db-114">Short option</span></span>| <span data-ttu-id="907db-115">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="907db-115">Long option</span></span>| <span data-ttu-id="907db-116">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-116">Description</span></span> | <span data-ttu-id="907db-117">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="907db-118">-v</span><span class="sxs-lookup"><span data-stu-id="907db-118">-v</span></span>|<span data-ttu-id="907db-119">--verbose</span><span class="sxs-lookup"><span data-stu-id="907db-119">--verbose</span></span> | <span data-ttu-id="907db-120">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="907db-120">Show verbose output.</span></span> |<span data-ttu-id="907db-121">dotnet openapi přidat soubor *-v* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="907db-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="907db-122">-p</span><span class="sxs-lookup"><span data-stu-id="907db-122">-p</span></span>|<span data-ttu-id="907db-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="907db-123">--updateProject</span></span> | <span data-ttu-id="907db-124">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="907db-124">The project to operate on.</span></span> |<span data-ttu-id="907db-125">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="907db-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="907db-126">-c</span><span class="sxs-lookup"><span data-stu-id="907db-126">-c</span></span>|<span data-ttu-id="907db-127">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="907db-127">--code-generator</span></span>| <span data-ttu-id="907db-128">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="907db-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="907db-129">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="907db-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="907db-130">Pokud `--code-generator` parametr není zadán, je `NSwagCSharp`výchozím nastavením nástrojů.</span><span class="sxs-lookup"><span data-stu-id="907db-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="907db-131">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="907db-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="907db-132">-h</span><span class="sxs-lookup"><span data-stu-id="907db-132">-h</span></span>|<span data-ttu-id="907db-133">--Help</span><span class="sxs-lookup"><span data-stu-id="907db-133">--help</span></span>|<span data-ttu-id="907db-134">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="907db-134">Show help information</span></span>|<span data-ttu-id="907db-135">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="907db-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="907db-136">Arguments</span><span class="sxs-lookup"><span data-stu-id="907db-136">Arguments</span></span>

|  <span data-ttu-id="907db-137">Argument</span><span class="sxs-lookup"><span data-stu-id="907db-137">Argument</span></span>  | <span data-ttu-id="907db-138">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-138">Description</span></span> | <span data-ttu-id="907db-139">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="907db-140">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="907db-140">source-file</span></span> | <span data-ttu-id="907db-141">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="907db-141">The source to create a reference from.</span></span> <span data-ttu-id="907db-142">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="907db-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="907db-143">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="907db-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="907db-144">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="907db-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="907db-145">Možnosti</span><span class="sxs-lookup"><span data-stu-id="907db-145">Options</span></span>

| <span data-ttu-id="907db-146">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="907db-146">Short option</span></span>| <span data-ttu-id="907db-147">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="907db-147">Long option</span></span>| <span data-ttu-id="907db-148">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-148">Description</span></span> | <span data-ttu-id="907db-149">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="907db-150">-v</span><span class="sxs-lookup"><span data-stu-id="907db-150">-v</span></span>|<span data-ttu-id="907db-151">--verbose</span><span class="sxs-lookup"><span data-stu-id="907db-151">--verbose</span></span> | <span data-ttu-id="907db-152">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="907db-152">Show verbose output.</span></span> |<span data-ttu-id="907db-153">dotnet openapi přidat URL *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="907db-154">-p</span><span class="sxs-lookup"><span data-stu-id="907db-154">-p</span></span>|<span data-ttu-id="907db-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="907db-155">--updateProject</span></span> | <span data-ttu-id="907db-156">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="907db-156">The project to operate on.</span></span> |<span data-ttu-id="907db-157">dotnet openapi Add URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="907db-158">-o</span><span class="sxs-lookup"><span data-stu-id="907db-158">-o</span></span>|<span data-ttu-id="907db-159">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="907db-159">--output-file</span></span> | <span data-ttu-id="907db-160">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="907db-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="907db-161">dotnet openapi Add URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="907db-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="907db-162">-c</span><span class="sxs-lookup"><span data-stu-id="907db-162">-c</span></span>|<span data-ttu-id="907db-163">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="907db-163">--code-generator</span></span>| <span data-ttu-id="907db-164">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="907db-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="907db-165">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="907db-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="907db-166">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="907db-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="907db-167">-h</span><span class="sxs-lookup"><span data-stu-id="907db-167">-h</span></span>|<span data-ttu-id="907db-168">--Help</span><span class="sxs-lookup"><span data-stu-id="907db-168">--help</span></span>|<span data-ttu-id="907db-169">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="907db-169">Show help information</span></span>|<span data-ttu-id="907db-170">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="907db-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="907db-171">Arguments</span><span class="sxs-lookup"><span data-stu-id="907db-171">Arguments</span></span>

|  <span data-ttu-id="907db-172">Argument</span><span class="sxs-lookup"><span data-stu-id="907db-172">Argument</span></span>  | <span data-ttu-id="907db-173">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-173">Description</span></span> | <span data-ttu-id="907db-174">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="907db-175">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="907db-175">source-URL</span></span> | <span data-ttu-id="907db-176">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="907db-176">The source to create a reference from.</span></span> <span data-ttu-id="907db-177">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="907db-177">Must be a URL.</span></span> |<span data-ttu-id="907db-178">dotnet openapi přidat adresu URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="907db-179">odebrat</span><span class="sxs-lookup"><span data-stu-id="907db-179">Remove</span></span>

<span data-ttu-id="907db-180">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="907db-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="907db-181">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="907db-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="907db-182">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="907db-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="907db-183">Možnosti</span><span class="sxs-lookup"><span data-stu-id="907db-183">Options</span></span>

| <span data-ttu-id="907db-184">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="907db-184">Short option</span></span>| <span data-ttu-id="907db-185">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="907db-185">Long option</span></span>| <span data-ttu-id="907db-186">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-186">Description</span></span>| <span data-ttu-id="907db-187">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="907db-188">-v</span><span class="sxs-lookup"><span data-stu-id="907db-188">-v</span></span>|<span data-ttu-id="907db-189">--verbose</span><span class="sxs-lookup"><span data-stu-id="907db-189">--verbose</span></span> | <span data-ttu-id="907db-190">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="907db-190">Show verbose output.</span></span> |<span data-ttu-id="907db-191">dotnet openapi Remove *-v*</span><span class="sxs-lookup"><span data-stu-id="907db-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="907db-192">-p</span><span class="sxs-lookup"><span data-stu-id="907db-192">-p</span></span>|<span data-ttu-id="907db-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="907db-193">--updateProject</span></span> | <span data-ttu-id="907db-194">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="907db-194">The project to operate on.</span></span> |<span data-ttu-id="907db-195">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="907db-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="907db-196">-h</span><span class="sxs-lookup"><span data-stu-id="907db-196">-h</span></span>|<span data-ttu-id="907db-197">--Help</span><span class="sxs-lookup"><span data-stu-id="907db-197">--help</span></span>|<span data-ttu-id="907db-198">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="907db-198">Show help information</span></span>|<span data-ttu-id="907db-199">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="907db-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="907db-200">Arguments</span><span class="sxs-lookup"><span data-stu-id="907db-200">Arguments</span></span>

|  <span data-ttu-id="907db-201">Argument</span><span class="sxs-lookup"><span data-stu-id="907db-201">Argument</span></span>  | <span data-ttu-id="907db-202">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-202">Description</span></span>| <span data-ttu-id="907db-203">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="907db-204">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="907db-204">source-file</span></span> | <span data-ttu-id="907db-205">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="907db-205">The source to remove the reference to.</span></span> |<span data-ttu-id="907db-206">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="907db-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="907db-207">Aktualizovat</span><span class="sxs-lookup"><span data-stu-id="907db-207">Refresh</span></span>

<span data-ttu-id="907db-208">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="907db-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="907db-209">Možnosti</span><span class="sxs-lookup"><span data-stu-id="907db-209">Options</span></span>

| <span data-ttu-id="907db-210">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="907db-210">Short option</span></span>| <span data-ttu-id="907db-211">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="907db-211">Long option</span></span>| <span data-ttu-id="907db-212">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-212">Description</span></span> | <span data-ttu-id="907db-213">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="907db-214">-v</span><span class="sxs-lookup"><span data-stu-id="907db-214">-v</span></span>|<span data-ttu-id="907db-215">--verbose</span><span class="sxs-lookup"><span data-stu-id="907db-215">--verbose</span></span> | <span data-ttu-id="907db-216">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="907db-216">Show verbose output.</span></span> | <span data-ttu-id="907db-217">dotnet openapi Refresh *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="907db-218">-p</span><span class="sxs-lookup"><span data-stu-id="907db-218">-p</span></span>|<span data-ttu-id="907db-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="907db-219">--updateProject</span></span> | <span data-ttu-id="907db-220">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="907db-220">The project to operate on.</span></span> | <span data-ttu-id="907db-221">dotnet openapi Refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="907db-222">-h</span><span class="sxs-lookup"><span data-stu-id="907db-222">-h</span></span>|<span data-ttu-id="907db-223">--Help</span><span class="sxs-lookup"><span data-stu-id="907db-223">--help</span></span>|<span data-ttu-id="907db-224">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="907db-224">Show help information</span></span>|<span data-ttu-id="907db-225">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="907db-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="907db-226">Arguments</span><span class="sxs-lookup"><span data-stu-id="907db-226">Arguments</span></span>

|  <span data-ttu-id="907db-227">Argument</span><span class="sxs-lookup"><span data-stu-id="907db-227">Argument</span></span>  | <span data-ttu-id="907db-228">Popis</span><span class="sxs-lookup"><span data-stu-id="907db-228">Description</span></span> | <span data-ttu-id="907db-229">Příklad</span><span class="sxs-lookup"><span data-stu-id="907db-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="907db-230">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="907db-230">source-URL</span></span> | <span data-ttu-id="907db-231">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="907db-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="907db-232">dotnet – aktualizace openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="907db-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
