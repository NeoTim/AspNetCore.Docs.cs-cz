---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 1924fb8ee5ac1ba8dc31d2175a336c8333c81fb2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775710"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="bd0f6-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="bd0f6-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="bd0f6-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="bd0f6-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="bd0f6-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="bd0f6-106">Instalace</span><span class="sxs-lookup"><span data-stu-id="bd0f6-106">Installation</span></span>

<span data-ttu-id="bd0f6-107">K instalaci `Microsoft.dotnet-openapi`spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="bd0f6-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="bd0f6-108">Přidat</span><span class="sxs-lookup"><span data-stu-id="bd0f6-108">Add</span></span>

<span data-ttu-id="bd0f6-109">Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá do souboru `<OpenApiReference />` *. csproj* prvek podobný následujícímu:</span><span class="sxs-lookup"><span data-stu-id="bd0f6-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="bd0f6-110">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="bd0f6-111">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="bd0f6-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="bd0f6-112">Možnosti</span><span class="sxs-lookup"><span data-stu-id="bd0f6-112">Options</span></span>

| <span data-ttu-id="bd0f6-113">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="bd0f6-113">Short option</span></span>| <span data-ttu-id="bd0f6-114">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="bd0f6-114">Long option</span></span>| <span data-ttu-id="bd0f6-115">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-115">Description</span></span> | <span data-ttu-id="bd0f6-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="bd0f6-117">-p</span><span class="sxs-lookup"><span data-stu-id="bd0f6-117">-p</span></span>|<span data-ttu-id="bd0f6-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bd0f6-118">--updateProject</span></span> | <span data-ttu-id="bd0f6-119">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-119">The project to operate on.</span></span> |<span data-ttu-id="bd0f6-120">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="bd0f6-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="bd0f6-121">-c</span><span class="sxs-lookup"><span data-stu-id="bd0f6-121">-c</span></span>|<span data-ttu-id="bd0f6-122">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="bd0f6-122">--code-generator</span></span>| <span data-ttu-id="bd0f6-123">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="bd0f6-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="bd0f6-124">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="bd0f6-125">Pokud `--code-generator` parametr není zadán, je výchozím nastavením nástrojů `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="bd0f6-126">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="bd0f6-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="bd0f6-127">-h</span><span class="sxs-lookup"><span data-stu-id="bd0f6-127">-h</span></span>|<span data-ttu-id="bd0f6-128">--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-128">--help</span></span>|<span data-ttu-id="bd0f6-129">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="bd0f6-129">Show help information</span></span>|<span data-ttu-id="bd0f6-130">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="bd0f6-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="bd0f6-131">Arguments</span></span>

|  <span data-ttu-id="bd0f6-132">Argument</span><span class="sxs-lookup"><span data-stu-id="bd0f6-132">Argument</span></span>  | <span data-ttu-id="bd0f6-133">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-133">Description</span></span> | <span data-ttu-id="bd0f6-134">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="bd0f6-135">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="bd0f6-135">source-file</span></span> | <span data-ttu-id="bd0f6-136">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-136">The source to create a reference from.</span></span> <span data-ttu-id="bd0f6-137">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="bd0f6-138">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="bd0f6-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="bd0f6-139">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="bd0f6-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="bd0f6-140">Možnosti</span><span class="sxs-lookup"><span data-stu-id="bd0f6-140">Options</span></span>

| <span data-ttu-id="bd0f6-141">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="bd0f6-141">Short option</span></span>| <span data-ttu-id="bd0f6-142">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="bd0f6-142">Long option</span></span>| <span data-ttu-id="bd0f6-143">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-143">Description</span></span> | <span data-ttu-id="bd0f6-144">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="bd0f6-145">-p</span><span class="sxs-lookup"><span data-stu-id="bd0f6-145">-p</span></span>|<span data-ttu-id="bd0f6-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bd0f6-146">--updateProject</span></span> | <span data-ttu-id="bd0f6-147">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-147">The project to operate on.</span></span> |<span data-ttu-id="bd0f6-148">dotnet openapi Add URL *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bd0f6-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bd0f6-149">-o</span><span class="sxs-lookup"><span data-stu-id="bd0f6-149">-o</span></span>|<span data-ttu-id="bd0f6-150">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="bd0f6-150">--output-file</span></span> | <span data-ttu-id="bd0f6-151">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="bd0f6-152">dotnet openapi Add URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="bd0f6-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="bd0f6-153">-c</span><span class="sxs-lookup"><span data-stu-id="bd0f6-153">-c</span></span>|<span data-ttu-id="bd0f6-154">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="bd0f6-154">--code-generator</span></span>| <span data-ttu-id="bd0f6-155">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="bd0f6-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="bd0f6-156">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="bd0f6-157">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="bd0f6-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="bd0f6-158">-h</span><span class="sxs-lookup"><span data-stu-id="bd0f6-158">-h</span></span>|<span data-ttu-id="bd0f6-159">--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-159">--help</span></span>|<span data-ttu-id="bd0f6-160">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="bd0f6-160">Show help information</span></span>|<span data-ttu-id="bd0f6-161">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="bd0f6-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="bd0f6-162">Arguments</span></span>

|  <span data-ttu-id="bd0f6-163">Argument</span><span class="sxs-lookup"><span data-stu-id="bd0f6-163">Argument</span></span>  | <span data-ttu-id="bd0f6-164">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-164">Description</span></span> | <span data-ttu-id="bd0f6-165">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="bd0f6-166">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="bd0f6-166">source-URL</span></span> | <span data-ttu-id="bd0f6-167">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-167">The source to create a reference from.</span></span> <span data-ttu-id="bd0f6-168">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-168">Must be a URL.</span></span> |<span data-ttu-id="bd0f6-169">dotnet openapi přidat adresu URL`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bd0f6-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="bd0f6-170">Odebrat</span><span class="sxs-lookup"><span data-stu-id="bd0f6-170">Remove</span></span>

<span data-ttu-id="bd0f6-171">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="bd0f6-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="bd0f6-172">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="bd0f6-173">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="bd0f6-174">Možnosti</span><span class="sxs-lookup"><span data-stu-id="bd0f6-174">Options</span></span>

| <span data-ttu-id="bd0f6-175">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="bd0f6-175">Short option</span></span>| <span data-ttu-id="bd0f6-176">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="bd0f6-176">Long option</span></span>| <span data-ttu-id="bd0f6-177">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-177">Description</span></span>| <span data-ttu-id="bd0f6-178">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="bd0f6-179">-p</span><span class="sxs-lookup"><span data-stu-id="bd0f6-179">-p</span></span>|<span data-ttu-id="bd0f6-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bd0f6-180">--updateProject</span></span> | <span data-ttu-id="bd0f6-181">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-181">The project to operate on.</span></span> |<span data-ttu-id="bd0f6-182">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="bd0f6-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="bd0f6-183">-h</span><span class="sxs-lookup"><span data-stu-id="bd0f6-183">-h</span></span>|<span data-ttu-id="bd0f6-184">--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-184">--help</span></span>|<span data-ttu-id="bd0f6-185">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="bd0f6-185">Show help information</span></span>|<span data-ttu-id="bd0f6-186">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="bd0f6-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="bd0f6-187">Arguments</span></span>

|  <span data-ttu-id="bd0f6-188">Argument</span><span class="sxs-lookup"><span data-stu-id="bd0f6-188">Argument</span></span>  | <span data-ttu-id="bd0f6-189">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-189">Description</span></span>| <span data-ttu-id="bd0f6-190">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="bd0f6-191">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="bd0f6-191">source-file</span></span> | <span data-ttu-id="bd0f6-192">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="bd0f6-192">The source to remove the reference to.</span></span> |<span data-ttu-id="bd0f6-193">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="bd0f6-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="bd0f6-194">Obnovení</span><span class="sxs-lookup"><span data-stu-id="bd0f6-194">Refresh</span></span>

<span data-ttu-id="bd0f6-195">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="bd0f6-196">Možnosti</span><span class="sxs-lookup"><span data-stu-id="bd0f6-196">Options</span></span>

| <span data-ttu-id="bd0f6-197">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="bd0f6-197">Short option</span></span>| <span data-ttu-id="bd0f6-198">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="bd0f6-198">Long option</span></span>| <span data-ttu-id="bd0f6-199">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-199">Description</span></span> | <span data-ttu-id="bd0f6-200">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="bd0f6-201">-p</span><span class="sxs-lookup"><span data-stu-id="bd0f6-201">-p</span></span>|<span data-ttu-id="bd0f6-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="bd0f6-202">--updateProject</span></span> | <span data-ttu-id="bd0f6-203">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="bd0f6-203">The project to operate on.</span></span> | <span data-ttu-id="bd0f6-204">dotnet openapi Refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bd0f6-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="bd0f6-205">-h</span><span class="sxs-lookup"><span data-stu-id="bd0f6-205">-h</span></span>|<span data-ttu-id="bd0f6-206">--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-206">--help</span></span>|<span data-ttu-id="bd0f6-207">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="bd0f6-207">Show help information</span></span>|<span data-ttu-id="bd0f6-208">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="bd0f6-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="bd0f6-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="bd0f6-209">Arguments</span></span>

|  <span data-ttu-id="bd0f6-210">Argument</span><span class="sxs-lookup"><span data-stu-id="bd0f6-210">Argument</span></span>  | <span data-ttu-id="bd0f6-211">Popis</span><span class="sxs-lookup"><span data-stu-id="bd0f6-211">Description</span></span> | <span data-ttu-id="bd0f6-212">Příklad</span><span class="sxs-lookup"><span data-stu-id="bd0f6-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="bd0f6-213">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="bd0f6-213">source-URL</span></span> | <span data-ttu-id="bd0f6-214">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="bd0f6-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="bd0f6-215">dotnet – aktualizace openapi`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="bd0f6-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
