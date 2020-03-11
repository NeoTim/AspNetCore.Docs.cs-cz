---
title: Vývoj aplikací pro ASP.NET Core pomocí OpenAPI
author: ryanbrandenburg
description: Ukazuje, jak pomocí nástroje Microsoft. dotnet-openapi přidat odkazy na soubory OpenAPI.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 079e36511b63c186ffa7726bdb1e3c3bcbda9d34
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655265"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="0d395-103">Vývoj aplikací pro ASP.NET Core pomocí nástrojů pro OpenAPI</span><span class="sxs-lookup"><span data-stu-id="0d395-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="0d395-104">Služba Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="0d395-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="0d395-105">[Microsoft. dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) je [globální nástroj .NET Core](/dotnet/core/tools/global-tools) pro správu odkazů [openapi](https://github.com/OAI/OpenAPI-Specification) v rámci projektu.</span><span class="sxs-lookup"><span data-stu-id="0d395-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="0d395-106">Instalace</span><span class="sxs-lookup"><span data-stu-id="0d395-106">Installation</span></span>

<span data-ttu-id="0d395-107">Pokud chcete nainstalovat `Microsoft.dotnet-openapi`, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="0d395-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="0d395-108">Přidat</span><span class="sxs-lookup"><span data-stu-id="0d395-108">Add</span></span>

<span data-ttu-id="0d395-109">Přidání odkazu OpenAPI pomocí kteréhokoli z příkazů na této stránce přidá prvek `<OpenApiReference />` podobný následujícímu souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="0d395-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="0d395-110">Předchozí odkaz je vyžadován, aby aplikace volala generovaný kód klienta.</span><span class="sxs-lookup"><span data-stu-id="0d395-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="0d395-111">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="0d395-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="0d395-112">Možnosti</span><span class="sxs-lookup"><span data-stu-id="0d395-112">Options</span></span>

| <span data-ttu-id="0d395-113">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="0d395-113">Short option</span></span>| <span data-ttu-id="0d395-114">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="0d395-114">Long option</span></span>| <span data-ttu-id="0d395-115">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-115">Description</span></span> | <span data-ttu-id="0d395-116">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="0d395-117">-p</span><span class="sxs-lookup"><span data-stu-id="0d395-117">-p</span></span>|<span data-ttu-id="0d395-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="0d395-118">--updateProject</span></span> | <span data-ttu-id="0d395-119">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="0d395-119">The project to operate on.</span></span> |<span data-ttu-id="0d395-120">dotnet openapi přidat soubor *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="0d395-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="0d395-121">-c</span><span class="sxs-lookup"><span data-stu-id="0d395-121">-c</span></span>|<span data-ttu-id="0d395-122">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="0d395-122">--code-generator</span></span>| <span data-ttu-id="0d395-123">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="0d395-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="0d395-124">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="0d395-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="0d395-125">Pokud `--code-generator` není zadán, výchozí nastavení nástrojů `NSwagCSharp`.</span><span class="sxs-lookup"><span data-stu-id="0d395-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="0d395-126">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="0d395-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="0d395-127">-h</span><span class="sxs-lookup"><span data-stu-id="0d395-127">-h</span></span>|<span data-ttu-id="0d395-128">--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-128">--help</span></span>|<span data-ttu-id="0d395-129">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="0d395-129">Show help information</span></span>|<span data-ttu-id="0d395-130">dotnet openapi přidat soubor--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="0d395-131">Argumenty</span><span class="sxs-lookup"><span data-stu-id="0d395-131">Arguments</span></span>

|  <span data-ttu-id="0d395-132">Argument</span><span class="sxs-lookup"><span data-stu-id="0d395-132">Argument</span></span>  | <span data-ttu-id="0d395-133">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-133">Description</span></span> | <span data-ttu-id="0d395-134">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="0d395-135">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="0d395-135">source-file</span></span> | <span data-ttu-id="0d395-136">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="0d395-136">The source to create a reference from.</span></span> <span data-ttu-id="0d395-137">Musí se jednat o soubor OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="0d395-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="0d395-138">dotnet openapi přidat soubor *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="0d395-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="0d395-139">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="0d395-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="0d395-140">Možnosti</span><span class="sxs-lookup"><span data-stu-id="0d395-140">Options</span></span>

| <span data-ttu-id="0d395-141">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="0d395-141">Short option</span></span>| <span data-ttu-id="0d395-142">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="0d395-142">Long option</span></span>| <span data-ttu-id="0d395-143">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-143">Description</span></span> | <span data-ttu-id="0d395-144">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="0d395-145">-p</span><span class="sxs-lookup"><span data-stu-id="0d395-145">-p</span></span>|<span data-ttu-id="0d395-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="0d395-146">--updateProject</span></span> | <span data-ttu-id="0d395-147">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="0d395-147">The project to operate on.</span></span> |<span data-ttu-id="0d395-148">dotnet openapi Add URL *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="0d395-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="0d395-149">-o</span><span class="sxs-lookup"><span data-stu-id="0d395-149">-o</span></span>|<span data-ttu-id="0d395-150">--výstupní soubor</span><span class="sxs-lookup"><span data-stu-id="0d395-150">--output-file</span></span> | <span data-ttu-id="0d395-151">Kam umístit místní kopii souboru OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="0d395-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="0d395-152">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json` *--Output-File MyClient. JSON*</span><span class="sxs-lookup"><span data-stu-id="0d395-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="0d395-153">-c</span><span class="sxs-lookup"><span data-stu-id="0d395-153">-c</span></span>|<span data-ttu-id="0d395-154">--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="0d395-154">--code-generator</span></span>| <span data-ttu-id="0d395-155">Generátor kódu, který se má použít na odkaz</span><span class="sxs-lookup"><span data-stu-id="0d395-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="0d395-156">Možnosti jsou `NSwagCSharp` a `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="0d395-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="0d395-157">dotnet openapi přidat soubor .\OpenApi.json--generátor kódu</span><span class="sxs-lookup"><span data-stu-id="0d395-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="0d395-158">-h</span><span class="sxs-lookup"><span data-stu-id="0d395-158">-h</span></span>|<span data-ttu-id="0d395-159">--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-159">--help</span></span>|<span data-ttu-id="0d395-160">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="0d395-160">Show help information</span></span>|<span data-ttu-id="0d395-161">dotnet openapi přidat URL – Help</span><span class="sxs-lookup"><span data-stu-id="0d395-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="0d395-162">Argumenty</span><span class="sxs-lookup"><span data-stu-id="0d395-162">Arguments</span></span>

|  <span data-ttu-id="0d395-163">Argument</span><span class="sxs-lookup"><span data-stu-id="0d395-163">Argument</span></span>  | <span data-ttu-id="0d395-164">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-164">Description</span></span> | <span data-ttu-id="0d395-165">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="0d395-166">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="0d395-166">source-URL</span></span> | <span data-ttu-id="0d395-167">Zdroj, ze kterého má být vytvořen odkaz.</span><span class="sxs-lookup"><span data-stu-id="0d395-167">The source to create a reference from.</span></span> <span data-ttu-id="0d395-168">Musí se jednat o adresu URL.</span><span class="sxs-lookup"><span data-stu-id="0d395-168">Must be a URL.</span></span> |<span data-ttu-id="0d395-169">dotnet openapi přidání adresy URL `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="0d395-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="0d395-170">Odebrat</span><span class="sxs-lookup"><span data-stu-id="0d395-170">Remove</span></span>

<span data-ttu-id="0d395-171">Odebere odkaz OpenAPI shodný se zadaným názvem souboru ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="0d395-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="0d395-172">Po odebrání odkazu na OpenAPI se nebudou generovat klienti.</span><span class="sxs-lookup"><span data-stu-id="0d395-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="0d395-173">Soubory Local *. JSON* a *. yaml* jsou odstraněny.</span><span class="sxs-lookup"><span data-stu-id="0d395-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="0d395-174">Možnosti</span><span class="sxs-lookup"><span data-stu-id="0d395-174">Options</span></span>

| <span data-ttu-id="0d395-175">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="0d395-175">Short option</span></span>| <span data-ttu-id="0d395-176">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="0d395-176">Long option</span></span>| <span data-ttu-id="0d395-177">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-177">Description</span></span>| <span data-ttu-id="0d395-178">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="0d395-179">-p</span><span class="sxs-lookup"><span data-stu-id="0d395-179">-p</span></span>|<span data-ttu-id="0d395-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="0d395-180">--updateProject</span></span> | <span data-ttu-id="0d395-181">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="0d395-181">The project to operate on.</span></span> |<span data-ttu-id="0d395-182">dotnet openapi Remove *--updateProject .\Ref.csproj* .\OpenAPI.JSON</span><span class="sxs-lookup"><span data-stu-id="0d395-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="0d395-183">-h</span><span class="sxs-lookup"><span data-stu-id="0d395-183">-h</span></span>|<span data-ttu-id="0d395-184">--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-184">--help</span></span>|<span data-ttu-id="0d395-185">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="0d395-185">Show help information</span></span>|<span data-ttu-id="0d395-186">dotnet openapi Remove--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="0d395-187">Argumenty</span><span class="sxs-lookup"><span data-stu-id="0d395-187">Arguments</span></span>

|  <span data-ttu-id="0d395-188">Argument</span><span class="sxs-lookup"><span data-stu-id="0d395-188">Argument</span></span>  | <span data-ttu-id="0d395-189">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-189">Description</span></span>| <span data-ttu-id="0d395-190">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="0d395-191">zdrojový soubor</span><span class="sxs-lookup"><span data-stu-id="0d395-191">source-file</span></span> | <span data-ttu-id="0d395-192">Zdroj, na který se má odebrat odkaz</span><span class="sxs-lookup"><span data-stu-id="0d395-192">The source to remove the reference to.</span></span> |<span data-ttu-id="0d395-193">dotnet openapi odebrat *.\OpenAPI.JSON*</span><span class="sxs-lookup"><span data-stu-id="0d395-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="0d395-194">Obnovení</span><span class="sxs-lookup"><span data-stu-id="0d395-194">Refresh</span></span>

<span data-ttu-id="0d395-195">Aktualizuje místní verzi souboru, který byl stažen pomocí nejnovějšího obsahu z adresy URL pro stahování.</span><span class="sxs-lookup"><span data-stu-id="0d395-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="0d395-196">Možnosti</span><span class="sxs-lookup"><span data-stu-id="0d395-196">Options</span></span>

| <span data-ttu-id="0d395-197">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="0d395-197">Short option</span></span>| <span data-ttu-id="0d395-198">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="0d395-198">Long option</span></span>| <span data-ttu-id="0d395-199">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-199">Description</span></span> | <span data-ttu-id="0d395-200">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="0d395-201">-p</span><span class="sxs-lookup"><span data-stu-id="0d395-201">-p</span></span>|<span data-ttu-id="0d395-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="0d395-202">--updateProject</span></span> | <span data-ttu-id="0d395-203">Projekt, na kterém má být provozován.</span><span class="sxs-lookup"><span data-stu-id="0d395-203">The project to operate on.</span></span> | <span data-ttu-id="0d395-204">dotnet openapi Refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="0d395-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="0d395-205">-h</span><span class="sxs-lookup"><span data-stu-id="0d395-205">-h</span></span>|<span data-ttu-id="0d395-206">--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-206">--help</span></span>|<span data-ttu-id="0d395-207">Zobrazit informace o nápovědě</span><span class="sxs-lookup"><span data-stu-id="0d395-207">Show help information</span></span>|<span data-ttu-id="0d395-208">dotnet openapi Refresh--Help</span><span class="sxs-lookup"><span data-stu-id="0d395-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="0d395-209">Argumenty</span><span class="sxs-lookup"><span data-stu-id="0d395-209">Arguments</span></span>

|  <span data-ttu-id="0d395-210">Argument</span><span class="sxs-lookup"><span data-stu-id="0d395-210">Argument</span></span>  | <span data-ttu-id="0d395-211">Popis</span><span class="sxs-lookup"><span data-stu-id="0d395-211">Description</span></span> | <span data-ttu-id="0d395-212">Příklad</span><span class="sxs-lookup"><span data-stu-id="0d395-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="0d395-213">Adresa URL zdroje</span><span class="sxs-lookup"><span data-stu-id="0d395-213">source-URL</span></span> | <span data-ttu-id="0d395-214">Adresa URL, ze které se má aktualizovat odkaz</span><span class="sxs-lookup"><span data-stu-id="0d395-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="0d395-215">dotnet openapi Refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="0d395-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
