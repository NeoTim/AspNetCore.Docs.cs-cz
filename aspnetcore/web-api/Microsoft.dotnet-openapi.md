---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 079e36511b63c186ffa7726bdb1e3c3bcbda9d34
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829254"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="6108f-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="6108f-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="6108f-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="6108f-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="6108f-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="6108f-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="6108f-106">Instalace služby</span><span class="sxs-lookup"><span data-stu-id="6108f-106">Installation</span></span>

<span data-ttu-id="6108f-107">Pokud chcete nainstalovat `Microsoft.dotnet-openapi`, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="6108f-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="6108f-108">Přidejte</span><span class="sxs-lookup"><span data-stu-id="6108f-108">Add</span></span>

<span data-ttu-id="6108f-109">Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá prvek `<OpenApiReference />` podobný následujícímu souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="6108f-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="6108f-110">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="6108f-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="6108f-111">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="6108f-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="6108f-112">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6108f-112">Options</span></span>

| <span data-ttu-id="6108f-113">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="6108f-113">Short option</span></span>| <span data-ttu-id="6108f-114">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="6108f-114">Long option</span></span>| <span data-ttu-id="6108f-115">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-115">Description</span></span> | <span data-ttu-id="6108f-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="6108f-117">-p</span><span class="sxs-lookup"><span data-stu-id="6108f-117">-p</span></span>|<span data-ttu-id="6108f-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6108f-118">--updateProject</span></span> | <span data-ttu-id="6108f-119">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="6108f-119">The project to operate on.</span></span> |<span data-ttu-id="6108f-120">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="6108f-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="6108f-121">-c</span><span class="sxs-lookup"><span data-stu-id="6108f-121">-c</span></span>|<span data-ttu-id="6108f-122">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="6108f-122">--code-generator</span></span>| <span data-ttu-id="6108f-123">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="6108f-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="6108f-124">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="6108f-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="6108f-125">Pokud `--code-generator` není zadán, výchozí nastavení nástrojů `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="6108f-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="6108f-126">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="6108f-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="6108f-127">-h</span><span class="sxs-lookup"><span data-stu-id="6108f-127">-h</span></span>|<span data-ttu-id="6108f-128">--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-128">--help</span></span>|<span data-ttu-id="6108f-129">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="6108f-129">Show help information</span></span>|<span data-ttu-id="6108f-130">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="6108f-131">Arguments</span><span class="sxs-lookup"><span data-stu-id="6108f-131">Arguments</span></span>

|  <span data-ttu-id="6108f-132">Argument</span><span class="sxs-lookup"><span data-stu-id="6108f-132">Argument</span></span>  | <span data-ttu-id="6108f-133">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-133">Description</span></span> | <span data-ttu-id="6108f-134">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="6108f-135">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="6108f-135">source-file</span></span> | <span data-ttu-id="6108f-136">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="6108f-136">The source to create a reference from.</span></span> <span data-ttu-id="6108f-137">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="6108f-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="6108f-138">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="6108f-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="6108f-139">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="6108f-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="6108f-140">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6108f-140">Options</span></span>

| <span data-ttu-id="6108f-141">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="6108f-141">Short option</span></span>| <span data-ttu-id="6108f-142">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="6108f-142">Long option</span></span>| <span data-ttu-id="6108f-143">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-143">Description</span></span> | <span data-ttu-id="6108f-144">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="6108f-145">-p</span><span class="sxs-lookup"><span data-stu-id="6108f-145">-p</span></span>|<span data-ttu-id="6108f-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6108f-146">--updateProject</span></span> | <span data-ttu-id="6108f-147">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="6108f-147">The project to operate on.</span></span> |<span data-ttu-id="6108f-148">dotnet openapi Add URL *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6108f-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6108f-149">-o</span><span class="sxs-lookup"><span data-stu-id="6108f-149">-o</span></span>|<span data-ttu-id="6108f-150">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="6108f-150">--output-file</span></span> | <span data-ttu-id="6108f-151">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="6108f-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="6108f-152">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="6108f-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="6108f-153">-c</span><span class="sxs-lookup"><span data-stu-id="6108f-153">-c</span></span>|<span data-ttu-id="6108f-154">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="6108f-154">--code-generator</span></span>| <span data-ttu-id="6108f-155">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="6108f-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="6108f-156">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="6108f-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="6108f-157">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="6108f-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="6108f-158">-h</span><span class="sxs-lookup"><span data-stu-id="6108f-158">-h</span></span>|<span data-ttu-id="6108f-159">--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-159">--help</span></span>|<span data-ttu-id="6108f-160">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="6108f-160">Show help information</span></span>|<span data-ttu-id="6108f-161">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="6108f-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="6108f-162">Arguments</span><span class="sxs-lookup"><span data-stu-id="6108f-162">Arguments</span></span>

|  <span data-ttu-id="6108f-163">Argument</span><span class="sxs-lookup"><span data-stu-id="6108f-163">Argument</span></span>  | <span data-ttu-id="6108f-164">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-164">Description</span></span> | <span data-ttu-id="6108f-165">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="6108f-166">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="6108f-166">source-URL</span></span> | <span data-ttu-id="6108f-167">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="6108f-167">The source to create a reference from.</span></span> <span data-ttu-id="6108f-168">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="6108f-168">Must be a URL.</span></span> |<span data-ttu-id="6108f-169">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6108f-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="6108f-170">Odebrat</span><span class="sxs-lookup"><span data-stu-id="6108f-170">Remove</span></span>

<span data-ttu-id="6108f-171">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="6108f-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="6108f-172">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="6108f-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="6108f-173">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="6108f-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="6108f-174">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6108f-174">Options</span></span>

| <span data-ttu-id="6108f-175">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="6108f-175">Short option</span></span>| <span data-ttu-id="6108f-176">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="6108f-176">Long option</span></span>| <span data-ttu-id="6108f-177">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-177">Description</span></span>| <span data-ttu-id="6108f-178">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="6108f-179">-p</span><span class="sxs-lookup"><span data-stu-id="6108f-179">-p</span></span>|<span data-ttu-id="6108f-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6108f-180">--updateProject</span></span> | <span data-ttu-id="6108f-181">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="6108f-181">The project to operate on.</span></span> |<span data-ttu-id="6108f-182">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="6108f-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="6108f-183">-h</span><span class="sxs-lookup"><span data-stu-id="6108f-183">-h</span></span>|<span data-ttu-id="6108f-184">--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-184">--help</span></span>|<span data-ttu-id="6108f-185">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="6108f-185">Show help information</span></span>|<span data-ttu-id="6108f-186">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="6108f-187">Arguments</span><span class="sxs-lookup"><span data-stu-id="6108f-187">Arguments</span></span>

|  <span data-ttu-id="6108f-188">Argument</span><span class="sxs-lookup"><span data-stu-id="6108f-188">Argument</span></span>  | <span data-ttu-id="6108f-189">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-189">Description</span></span>| <span data-ttu-id="6108f-190">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="6108f-191">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="6108f-191">source-file</span></span> | <span data-ttu-id="6108f-192">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="6108f-192">The source to remove the reference to.</span></span> |<span data-ttu-id="6108f-193">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="6108f-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="6108f-194">Aktualizovat</span><span class="sxs-lookup"><span data-stu-id="6108f-194">Refresh</span></span>

<span data-ttu-id="6108f-195">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="6108f-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="6108f-196">Možnosti</span><span class="sxs-lookup"><span data-stu-id="6108f-196">Options</span></span>

| <span data-ttu-id="6108f-197">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="6108f-197">Short option</span></span>| <span data-ttu-id="6108f-198">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="6108f-198">Long option</span></span>| <span data-ttu-id="6108f-199">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-199">Description</span></span> | <span data-ttu-id="6108f-200">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="6108f-201">-p</span><span class="sxs-lookup"><span data-stu-id="6108f-201">-p</span></span>|<span data-ttu-id="6108f-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="6108f-202">--updateProject</span></span> | <span data-ttu-id="6108f-203">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="6108f-203">The project to operate on.</span></span> | <span data-ttu-id="6108f-204">dotnet openapi Refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6108f-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="6108f-205">-h</span><span class="sxs-lookup"><span data-stu-id="6108f-205">-h</span></span>|<span data-ttu-id="6108f-206">--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-206">--help</span></span>|<span data-ttu-id="6108f-207">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="6108f-207">Show help information</span></span>|<span data-ttu-id="6108f-208">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="6108f-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="6108f-209">Arguments</span><span class="sxs-lookup"><span data-stu-id="6108f-209">Arguments</span></span>

|  <span data-ttu-id="6108f-210">Argument</span><span class="sxs-lookup"><span data-stu-id="6108f-210">Argument</span></span>  | <span data-ttu-id="6108f-211">Popis</span><span class="sxs-lookup"><span data-stu-id="6108f-211">Description</span></span> | <span data-ttu-id="6108f-212">Příklad</span><span class="sxs-lookup"><span data-stu-id="6108f-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="6108f-213">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="6108f-213">source-URL</span></span> | <span data-ttu-id="6108f-214">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="6108f-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="6108f-215">dotnet openapi Refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="6108f-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
