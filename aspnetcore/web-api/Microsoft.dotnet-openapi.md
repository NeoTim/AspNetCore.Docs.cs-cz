---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317776"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="7d6f6-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="7d6f6-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="7d6f6-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="7d6f6-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="7d6f6-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="7d6f6-106">Instalace</span><span class="sxs-lookup"><span data-stu-id="7d6f6-106">Installation</span></span>

<span data-ttu-id="7d6f6-107">K instalaci `Microsoft.dotnet-openapi`spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7d6f6-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="7d6f6-108">Přidat</span><span class="sxs-lookup"><span data-stu-id="7d6f6-108">Add</span></span>

<span data-ttu-id="7d6f6-109">Přidání odkazu openapi pomocí kteréhokoli z příkazů na této stránce přidá `<OpenApiReference />` do souboru *. csproj* prvek podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="7d6f6-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="7d6f6-110">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="7d6f6-111">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="7d6f6-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="7d6f6-112">Možnosti</span><span class="sxs-lookup"><span data-stu-id="7d6f6-112">Options</span></span>

| <span data-ttu-id="7d6f6-113">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="7d6f6-113">Short option</span></span>| <span data-ttu-id="7d6f6-114">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="7d6f6-114">Long option</span></span>| <span data-ttu-id="7d6f6-115">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-115">Description</span></span> | <span data-ttu-id="7d6f6-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="7d6f6-117">-v</span><span class="sxs-lookup"><span data-stu-id="7d6f6-117">-v</span></span>|<span data-ttu-id="7d6f6-118">--verbose</span><span class="sxs-lookup"><span data-stu-id="7d6f6-118">--verbose</span></span> | <span data-ttu-id="7d6f6-119">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-119">Show verbose output.</span></span> |<span data-ttu-id="7d6f6-120">dotnet openapi přidat soubor *-v* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="7d6f6-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="7d6f6-121">-p</span><span class="sxs-lookup"><span data-stu-id="7d6f6-121">-p</span></span>|<span data-ttu-id="7d6f6-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="7d6f6-122">--updateProject</span></span> | <span data-ttu-id="7d6f6-123">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-123">The project to operate on.</span></span> |<span data-ttu-id="7d6f6-124">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="7d6f6-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="7d6f6-125">-c</span><span class="sxs-lookup"><span data-stu-id="7d6f6-125">-c</span></span>|<span data-ttu-id="7d6f6-126">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="7d6f6-126">--code-generator</span></span>| <span data-ttu-id="7d6f6-127">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="7d6f6-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="7d6f6-128">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="7d6f6-129">Pokud `--code-generator` parametr není zadán, je `NSwagCSharp`výchozím nastavením nástrojů.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="7d6f6-130">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="7d6f6-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="7d6f6-131">-h</span><span class="sxs-lookup"><span data-stu-id="7d6f6-131">-h</span></span>|<span data-ttu-id="7d6f6-132">--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-132">--help</span></span>|<span data-ttu-id="7d6f6-133">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="7d6f6-133">Show help information</span></span>|<span data-ttu-id="7d6f6-134">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="7d6f6-135">Argumenty</span><span class="sxs-lookup"><span data-stu-id="7d6f6-135">Arguments</span></span>

|  <span data-ttu-id="7d6f6-136">Argument</span><span class="sxs-lookup"><span data-stu-id="7d6f6-136">Argument</span></span>  | <span data-ttu-id="7d6f6-137">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-137">Description</span></span> | <span data-ttu-id="7d6f6-138">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="7d6f6-139">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="7d6f6-139">source-file</span></span> | <span data-ttu-id="7d6f6-140">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-140">The source to create a reference from.</span></span> <span data-ttu-id="7d6f6-141">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="7d6f6-142">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="7d6f6-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="7d6f6-143">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="7d6f6-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="7d6f6-144">Možnosti</span><span class="sxs-lookup"><span data-stu-id="7d6f6-144">Options</span></span>

| <span data-ttu-id="7d6f6-145">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="7d6f6-145">Short option</span></span>| <span data-ttu-id="7d6f6-146">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="7d6f6-146">Long option</span></span>| <span data-ttu-id="7d6f6-147">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-147">Description</span></span> | <span data-ttu-id="7d6f6-148">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="7d6f6-149">-v</span><span class="sxs-lookup"><span data-stu-id="7d6f6-149">-v</span></span>|<span data-ttu-id="7d6f6-150">--verbose</span><span class="sxs-lookup"><span data-stu-id="7d6f6-150">--verbose</span></span> | <span data-ttu-id="7d6f6-151">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-151">Show verbose output.</span></span> |<span data-ttu-id="7d6f6-152">dotnet openapi přidat URL *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="7d6f6-153">-p</span><span class="sxs-lookup"><span data-stu-id="7d6f6-153">-p</span></span>|<span data-ttu-id="7d6f6-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="7d6f6-154">--updateProject</span></span> | <span data-ttu-id="7d6f6-155">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-155">The project to operate on.</span></span> |<span data-ttu-id="7d6f6-156">dotnet openapi Add URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="7d6f6-157">-o</span><span class="sxs-lookup"><span data-stu-id="7d6f6-157">-o</span></span>|<span data-ttu-id="7d6f6-158">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="7d6f6-158">--output-file</span></span> | <span data-ttu-id="7d6f6-159">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="7d6f6-160">dotnet openapi Add URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="7d6f6-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="7d6f6-161">-c</span><span class="sxs-lookup"><span data-stu-id="7d6f6-161">-c</span></span>|<span data-ttu-id="7d6f6-162">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="7d6f6-162">--code-generator</span></span>| <span data-ttu-id="7d6f6-163">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="7d6f6-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="7d6f6-164">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="7d6f6-165">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="7d6f6-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="7d6f6-166">-h</span><span class="sxs-lookup"><span data-stu-id="7d6f6-166">-h</span></span>|<span data-ttu-id="7d6f6-167">--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-167">--help</span></span>|<span data-ttu-id="7d6f6-168">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="7d6f6-168">Show help information</span></span>|<span data-ttu-id="7d6f6-169">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="7d6f6-170">Argumenty</span><span class="sxs-lookup"><span data-stu-id="7d6f6-170">Arguments</span></span>

|  <span data-ttu-id="7d6f6-171">Argument</span><span class="sxs-lookup"><span data-stu-id="7d6f6-171">Argument</span></span>  | <span data-ttu-id="7d6f6-172">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-172">Description</span></span> | <span data-ttu-id="7d6f6-173">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="7d6f6-174">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="7d6f6-174">source-URL</span></span> | <span data-ttu-id="7d6f6-175">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-175">The source to create a reference from.</span></span> <span data-ttu-id="7d6f6-176">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-176">Must be a URL.</span></span> |<span data-ttu-id="7d6f6-177">dotnet openapi přidat adresu URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="7d6f6-178">odebrat</span><span class="sxs-lookup"><span data-stu-id="7d6f6-178">Remove</span></span>

<span data-ttu-id="7d6f6-179">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="7d6f6-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="7d6f6-180">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="7d6f6-181">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="7d6f6-182">Možnosti</span><span class="sxs-lookup"><span data-stu-id="7d6f6-182">Options</span></span>

| <span data-ttu-id="7d6f6-183">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="7d6f6-183">Short option</span></span>| <span data-ttu-id="7d6f6-184">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="7d6f6-184">Long option</span></span>| <span data-ttu-id="7d6f6-185">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-185">Description</span></span>| <span data-ttu-id="7d6f6-186">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="7d6f6-187">-v</span><span class="sxs-lookup"><span data-stu-id="7d6f6-187">-v</span></span>|<span data-ttu-id="7d6f6-188">--verbose</span><span class="sxs-lookup"><span data-stu-id="7d6f6-188">--verbose</span></span> | <span data-ttu-id="7d6f6-189">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-189">Show verbose output.</span></span> |<span data-ttu-id="7d6f6-190">dotnet openapi Remove *-v*</span><span class="sxs-lookup"><span data-stu-id="7d6f6-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="7d6f6-191">-p</span><span class="sxs-lookup"><span data-stu-id="7d6f6-191">-p</span></span>|<span data-ttu-id="7d6f6-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="7d6f6-192">--updateProject</span></span> | <span data-ttu-id="7d6f6-193">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-193">The project to operate on.</span></span> |<span data-ttu-id="7d6f6-194">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="7d6f6-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="7d6f6-195">-h</span><span class="sxs-lookup"><span data-stu-id="7d6f6-195">-h</span></span>|<span data-ttu-id="7d6f6-196">--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-196">--help</span></span>|<span data-ttu-id="7d6f6-197">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="7d6f6-197">Show help information</span></span>|<span data-ttu-id="7d6f6-198">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="7d6f6-199">Argumenty</span><span class="sxs-lookup"><span data-stu-id="7d6f6-199">Arguments</span></span>

|  <span data-ttu-id="7d6f6-200">Argument</span><span class="sxs-lookup"><span data-stu-id="7d6f6-200">Argument</span></span>  | <span data-ttu-id="7d6f6-201">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-201">Description</span></span>| <span data-ttu-id="7d6f6-202">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="7d6f6-203">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="7d6f6-203">source-file</span></span> | <span data-ttu-id="7d6f6-204">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="7d6f6-204">The source to remove the reference to.</span></span> |<span data-ttu-id="7d6f6-205">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="7d6f6-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="7d6f6-206">Aktualizovat</span><span class="sxs-lookup"><span data-stu-id="7d6f6-206">Refresh</span></span>

<span data-ttu-id="7d6f6-207">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="7d6f6-208">Možnosti</span><span class="sxs-lookup"><span data-stu-id="7d6f6-208">Options</span></span>

| <span data-ttu-id="7d6f6-209">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="7d6f6-209">Short option</span></span>| <span data-ttu-id="7d6f6-210">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="7d6f6-210">Long option</span></span>| <span data-ttu-id="7d6f6-211">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-211">Description</span></span> | <span data-ttu-id="7d6f6-212">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="7d6f6-213">-v</span><span class="sxs-lookup"><span data-stu-id="7d6f6-213">-v</span></span>|<span data-ttu-id="7d6f6-214">--verbose</span><span class="sxs-lookup"><span data-stu-id="7d6f6-214">--verbose</span></span> | <span data-ttu-id="7d6f6-215">Zobrazit podrobný výstup.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-215">Show verbose output.</span></span> | <span data-ttu-id="7d6f6-216">dotnet openapi Refresh *-v*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="7d6f6-217">-p</span><span class="sxs-lookup"><span data-stu-id="7d6f6-217">-p</span></span>|<span data-ttu-id="7d6f6-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="7d6f6-218">--updateProject</span></span> | <span data-ttu-id="7d6f6-219">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="7d6f6-219">The project to operate on.</span></span> | <span data-ttu-id="7d6f6-220">dotnet openapi Refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="7d6f6-221">-h</span><span class="sxs-lookup"><span data-stu-id="7d6f6-221">-h</span></span>|<span data-ttu-id="7d6f6-222">--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-222">--help</span></span>|<span data-ttu-id="7d6f6-223">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="7d6f6-223">Show help information</span></span>|<span data-ttu-id="7d6f6-224">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="7d6f6-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="7d6f6-225">Argumenty</span><span class="sxs-lookup"><span data-stu-id="7d6f6-225">Arguments</span></span>

|  <span data-ttu-id="7d6f6-226">Argument</span><span class="sxs-lookup"><span data-stu-id="7d6f6-226">Argument</span></span>  | <span data-ttu-id="7d6f6-227">Popis</span><span class="sxs-lookup"><span data-stu-id="7d6f6-227">Description</span></span> | <span data-ttu-id="7d6f6-228">Příklad</span><span class="sxs-lookup"><span data-stu-id="7d6f6-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="7d6f6-229">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="7d6f6-229">source-URL</span></span> | <span data-ttu-id="7d6f6-230">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="7d6f6-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="7d6f6-231">dotnet – aktualizace openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="7d6f6-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
