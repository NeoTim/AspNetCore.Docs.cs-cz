---
title: Správa odkazů Protobuf pomocí dotnet-grpc
author: juntaoluo
description: Přečtěte si, jak přidávat, aktualizovat, odebírat a zobrazovat odkazy na Protobuf s globálním nástrojem dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667333"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="ba00d-103">Správa odkazů Protobuf pomocí dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="ba00d-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="ba00d-104">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ba00d-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ba00d-105">`dotnet-grpc` je globální nástroj .NET Core pro správu [Protobuf ( *.* )](xref:grpc/basics#proto-file) v rámci projektu .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="ba00d-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="ba00d-106">Nástroj lze použít k přidání, aktualizaci, odebrání a výpisu odkazů Protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="ba00d-107">Instalace</span><span class="sxs-lookup"><span data-stu-id="ba00d-107">Installation</span></span>

<span data-ttu-id="ba00d-108">Pokud chcete nainstalovat `dotnet-grpc` [globální nástroj .NET Core](/dotnet/core/tools/global-tools), spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ba00d-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="ba00d-109">Přidat odkazy</span><span class="sxs-lookup"><span data-stu-id="ba00d-109">Add references</span></span>

<span data-ttu-id="ba00d-110">`dotnet-grpc` lze použít k přidání odkazů Protobuf jako `<Protobuf />` položek do souboru *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="ba00d-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="ba00d-111">Odkazy na Protobuf se používají ke generování prostředků C# klienta a/nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="ba00d-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="ba00d-112">Nástroj `dotnet-grpc` může:</span><span class="sxs-lookup"><span data-stu-id="ba00d-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="ba00d-113">Vytvořte odkaz Protobuf z místních souborů na disku.</span><span class="sxs-lookup"><span data-stu-id="ba00d-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="ba00d-114">Vytvoří odkaz Protobuf ze vzdáleného souboru zadaného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="ba00d-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="ba00d-115">Zajistěte, aby se do projektu přidaly správné závislosti balíčků gRPC.</span><span class="sxs-lookup"><span data-stu-id="ba00d-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="ba00d-116">Například balíček `Grpc.AspNetCore` se přidá do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="ba00d-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="ba00d-117">`Grpc.AspNetCore` obsahuje gRPC Server a klientské knihovny a podporu nástrojů.</span><span class="sxs-lookup"><span data-stu-id="ba00d-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="ba00d-118">Alternativně se do konzolové aplikace přidají i balíčky `Grpc.Net.Client`, `Grpc.Tools` a `Google.Protobuf`, které obsahují jenom klientské knihovny gRPC a podpora nástrojů.</span><span class="sxs-lookup"><span data-stu-id="ba00d-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="ba00d-119">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="ba00d-119">Add file</span></span>

<span data-ttu-id="ba00d-120">Příkaz `add-file` slouží k přidání místních souborů na disk jako odkazů Protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="ba00d-121">Zadané cesty k souborům:</span><span class="sxs-lookup"><span data-stu-id="ba00d-121">The file paths provided:</span></span>

* <span data-ttu-id="ba00d-122">Může být relativní vzhledem k aktuálnímu adresáři nebo absolutním cestám.</span><span class="sxs-lookup"><span data-stu-id="ba00d-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="ba00d-123">Může obsahovat zástupné znaky pro [expanzi](https://wikipedia.org/wiki/Glob_(programming))souborů založené na vzorcích.</span><span class="sxs-lookup"><span data-stu-id="ba00d-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="ba00d-124">Pokud jsou některé soubory mimo adresář projektu, je přidán `Link` element pro zobrazení souboru pod `Protos` složky v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ba00d-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="ba00d-125">Využití</span><span class="sxs-lookup"><span data-stu-id="ba00d-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="ba00d-126">Argumenty</span><span class="sxs-lookup"><span data-stu-id="ba00d-126">Arguments</span></span>

| <span data-ttu-id="ba00d-127">Argument</span><span class="sxs-lookup"><span data-stu-id="ba00d-127">Argument</span></span> | <span data-ttu-id="ba00d-128">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-128">Description</span></span> |
|-|-|
| <span data-ttu-id="ba00d-129">files</span><span class="sxs-lookup"><span data-stu-id="ba00d-129">files</span></span> | <span data-ttu-id="ba00d-130">Odkazy na soubor protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-130">The protobuf file references.</span></span> <span data-ttu-id="ba00d-131">Může se jednat o cestu k glob pro místní soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="ba00d-132">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ba00d-132">Options</span></span>

| <span data-ttu-id="ba00d-133">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="ba00d-133">Short option</span></span> | <span data-ttu-id="ba00d-134">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="ba00d-134">Long option</span></span> | <span data-ttu-id="ba00d-135">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="ba00d-136">-p</span><span class="sxs-lookup"><span data-stu-id="ba00d-136">-p</span></span> | <span data-ttu-id="ba00d-137">--Project</span><span class="sxs-lookup"><span data-stu-id="ba00d-137">--project</span></span> | <span data-ttu-id="ba00d-138">Cesta k souboru projektu, na kterém má být provozována.</span><span class="sxs-lookup"><span data-stu-id="ba00d-138">The path to the project file to operate on.</span></span> <span data-ttu-id="ba00d-139">Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.</span><span class="sxs-lookup"><span data-stu-id="ba00d-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="ba00d-140">-s</span><span class="sxs-lookup"><span data-stu-id="ba00d-140">-s</span></span> | <span data-ttu-id="ba00d-141">– služby</span><span class="sxs-lookup"><span data-stu-id="ba00d-141">--services</span></span> | <span data-ttu-id="ba00d-142">Typ gRPC služeb, které se mají vygenerovat</span><span class="sxs-lookup"><span data-stu-id="ba00d-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="ba00d-143">Je-li zadán `Default`, `Both` se používá pro webové projekty a `Client` se používá pro newebové projekty.</span><span class="sxs-lookup"><span data-stu-id="ba00d-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="ba00d-144">Přijaté hodnoty jsou `Both`, `Client`, `Default`, `None``Server`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="ba00d-145">-i</span><span class="sxs-lookup"><span data-stu-id="ba00d-145">-i</span></span> | <span data-ttu-id="ba00d-146">--Další-import-adresářů</span><span class="sxs-lookup"><span data-stu-id="ba00d-146">--additional-import-dirs</span></span> | <span data-ttu-id="ba00d-147">Další adresáře, které se mají použít při překladu importu souborů protobuf</span><span class="sxs-lookup"><span data-stu-id="ba00d-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="ba00d-148">Toto je seznam cest oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="ba00d-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="ba00d-149">– přístup</span><span class="sxs-lookup"><span data-stu-id="ba00d-149">--access</span></span> | <span data-ttu-id="ba00d-150">Modifikátor přístupu, který má být použit pro C# vygenerované třídy.</span><span class="sxs-lookup"><span data-stu-id="ba00d-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="ba00d-151">Výchozí hodnota je `Public`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-151">The default value is `Public`.</span></span> <span data-ttu-id="ba00d-152">Přijaté hodnoty jsou `Internal` a `Public`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="ba00d-153">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="ba00d-153">Add URL</span></span>

<span data-ttu-id="ba00d-154">Příkaz `add-url` se používá k přidání vzdáleného souboru zadaného zdrojovou adresou URL jako odkaz na Protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="ba00d-155">Aby bylo možné určit, kam Stáhnout vzdálený soubor, je nutné zadat cestu k souboru.</span><span class="sxs-lookup"><span data-stu-id="ba00d-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="ba00d-156">Cesta k souboru může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cestě.</span><span class="sxs-lookup"><span data-stu-id="ba00d-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="ba00d-157">Pokud je cesta k souboru mimo adresář projektu, je přidán `Link` element pro zobrazení souboru ve virtuální složce `Protos` v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ba00d-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="ba00d-158">Využití</span><span class="sxs-lookup"><span data-stu-id="ba00d-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="ba00d-159">Argumenty</span><span class="sxs-lookup"><span data-stu-id="ba00d-159">Arguments</span></span>

| <span data-ttu-id="ba00d-160">Argument</span><span class="sxs-lookup"><span data-stu-id="ba00d-160">Argument</span></span> | <span data-ttu-id="ba00d-161">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-161">Description</span></span> |
|-|-|
| <span data-ttu-id="ba00d-162">url</span><span class="sxs-lookup"><span data-stu-id="ba00d-162">url</span></span> | <span data-ttu-id="ba00d-163">Adresa URL vzdáleného souboru protobuf</span><span class="sxs-lookup"><span data-stu-id="ba00d-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="ba00d-164">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ba00d-164">Options</span></span>

| <span data-ttu-id="ba00d-165">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="ba00d-165">Short option</span></span> | <span data-ttu-id="ba00d-166">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="ba00d-166">Long option</span></span> | <span data-ttu-id="ba00d-167">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="ba00d-168">-o</span><span class="sxs-lookup"><span data-stu-id="ba00d-168">-o</span></span> | <span data-ttu-id="ba00d-169">--output</span><span class="sxs-lookup"><span data-stu-id="ba00d-169">--output</span></span> | <span data-ttu-id="ba00d-170">Určuje cestu pro stažení vzdáleného souboru protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="ba00d-171">Tato možnost je povinná.</span><span class="sxs-lookup"><span data-stu-id="ba00d-171">This is a required option.</span></span>
| <span data-ttu-id="ba00d-172">-p</span><span class="sxs-lookup"><span data-stu-id="ba00d-172">-p</span></span> | <span data-ttu-id="ba00d-173">--Project</span><span class="sxs-lookup"><span data-stu-id="ba00d-173">--project</span></span> | <span data-ttu-id="ba00d-174">Cesta k souboru projektu, na kterém má být provozována.</span><span class="sxs-lookup"><span data-stu-id="ba00d-174">The path to the project file to operate on.</span></span> <span data-ttu-id="ba00d-175">Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.</span><span class="sxs-lookup"><span data-stu-id="ba00d-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="ba00d-176">-s</span><span class="sxs-lookup"><span data-stu-id="ba00d-176">-s</span></span> | <span data-ttu-id="ba00d-177">– služby</span><span class="sxs-lookup"><span data-stu-id="ba00d-177">--services</span></span> | <span data-ttu-id="ba00d-178">Typ gRPC služeb, které se mají vygenerovat</span><span class="sxs-lookup"><span data-stu-id="ba00d-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="ba00d-179">Je-li zadán `Default`, `Both` se používá pro webové projekty a `Client` se používá pro newebové projekty.</span><span class="sxs-lookup"><span data-stu-id="ba00d-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="ba00d-180">Přijaté hodnoty jsou `Both`, `Client`, `Default`, `None``Server`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="ba00d-181">-i</span><span class="sxs-lookup"><span data-stu-id="ba00d-181">-i</span></span> | <span data-ttu-id="ba00d-182">--Další-import-adresářů</span><span class="sxs-lookup"><span data-stu-id="ba00d-182">--additional-import-dirs</span></span> | <span data-ttu-id="ba00d-183">Další adresáře, které se mají použít při překladu importu souborů protobuf</span><span class="sxs-lookup"><span data-stu-id="ba00d-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="ba00d-184">Toto je seznam cest oddělených středníky.</span><span class="sxs-lookup"><span data-stu-id="ba00d-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="ba00d-185">– přístup</span><span class="sxs-lookup"><span data-stu-id="ba00d-185">--access</span></span> | <span data-ttu-id="ba00d-186">Modifikátor přístupu, který má být použit pro C# vygenerované třídy.</span><span class="sxs-lookup"><span data-stu-id="ba00d-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="ba00d-187">Výchozí hodnota je `Public`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-187">Default value is `Public`.</span></span> <span data-ttu-id="ba00d-188">Přijaté hodnoty jsou `Internal` a `Public`.</span><span class="sxs-lookup"><span data-stu-id="ba00d-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="ba00d-189">Odebrat</span><span class="sxs-lookup"><span data-stu-id="ba00d-189">Remove</span></span>

<span data-ttu-id="ba00d-190">Příkaz `remove` slouží k odebrání odkazů Protobuf ze souboru *. csproj* .</span><span class="sxs-lookup"><span data-stu-id="ba00d-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="ba00d-191">Příkaz přijímá argumenty cesty a zdrojové adresy URL jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="ba00d-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="ba00d-192">Nástroj:</span><span class="sxs-lookup"><span data-stu-id="ba00d-192">The tool:</span></span>

* <span data-ttu-id="ba00d-193">Odebere pouze odkaz Protobuf.</span><span class="sxs-lookup"><span data-stu-id="ba00d-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="ba00d-194">Neodstraní soubor *...* i v případě, že byl původně stažen ze vzdálené adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ba00d-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="ba00d-195">Využití</span><span class="sxs-lookup"><span data-stu-id="ba00d-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="ba00d-196">Argumenty</span><span class="sxs-lookup"><span data-stu-id="ba00d-196">Arguments</span></span>

| <span data-ttu-id="ba00d-197">Argument</span><span class="sxs-lookup"><span data-stu-id="ba00d-197">Argument</span></span> | <span data-ttu-id="ba00d-198">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-198">Description</span></span> |
|-|-|
| <span data-ttu-id="ba00d-199">odkazy</span><span class="sxs-lookup"><span data-stu-id="ba00d-199">references</span></span> | <span data-ttu-id="ba00d-200">Adresy URL nebo cesty k souborům protobuf odkazů, které se mají odebrat.</span><span class="sxs-lookup"><span data-stu-id="ba00d-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="ba00d-201">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ba00d-201">Options</span></span>

| <span data-ttu-id="ba00d-202">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="ba00d-202">Short option</span></span> | <span data-ttu-id="ba00d-203">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="ba00d-203">Long option</span></span> | <span data-ttu-id="ba00d-204">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="ba00d-205">-p</span><span class="sxs-lookup"><span data-stu-id="ba00d-205">-p</span></span> | <span data-ttu-id="ba00d-206">--Project</span><span class="sxs-lookup"><span data-stu-id="ba00d-206">--project</span></span> | <span data-ttu-id="ba00d-207">Cesta k souboru projektu, na kterém má být provozována.</span><span class="sxs-lookup"><span data-stu-id="ba00d-207">The path to the project file to operate on.</span></span> <span data-ttu-id="ba00d-208">Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.</span><span class="sxs-lookup"><span data-stu-id="ba00d-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="ba00d-209">Obnovení</span><span class="sxs-lookup"><span data-stu-id="ba00d-209">Refresh</span></span>

<span data-ttu-id="ba00d-210">Příkaz `refresh` slouží k aktualizaci vzdáleného odkazu s nejnovějším obsahem ze zdrojové adresy URL.</span><span class="sxs-lookup"><span data-stu-id="ba00d-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="ba00d-211">K určení odkazu, který se má aktualizovat, se dá použít jak cestu souboru ke stažení, tak i zdrojová adresa URL.</span><span class="sxs-lookup"><span data-stu-id="ba00d-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="ba00d-212">Poznámka:</span><span class="sxs-lookup"><span data-stu-id="ba00d-212">Note:</span></span>

* <span data-ttu-id="ba00d-213">Hodnoty hash obsahu souboru se porovnávají s cílem určit, jestli se má místní soubor aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="ba00d-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="ba00d-214">Nesrovnávají se žádné informace o časovém razítku.</span><span class="sxs-lookup"><span data-stu-id="ba00d-214">No timestamp information is compared.</span></span>

<span data-ttu-id="ba00d-215">Nástroj vždy nahradí místní soubor vzdáleným souborem, pokud je požadována aktualizace.</span><span class="sxs-lookup"><span data-stu-id="ba00d-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="ba00d-216">Využití</span><span class="sxs-lookup"><span data-stu-id="ba00d-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="ba00d-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="ba00d-217">Arguments</span></span>

| <span data-ttu-id="ba00d-218">Argument</span><span class="sxs-lookup"><span data-stu-id="ba00d-218">Argument</span></span> | <span data-ttu-id="ba00d-219">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-219">Description</span></span> |
|-|-|
| <span data-ttu-id="ba00d-220">odkazy</span><span class="sxs-lookup"><span data-stu-id="ba00d-220">references</span></span> | <span data-ttu-id="ba00d-221">Adresy URL nebo cesty k souborům pro vzdálené protobuf odkazují na odkazy, které by se měly aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="ba00d-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="ba00d-222">Pokud chcete aktualizovat všechny vzdálené odkazy, ponechte tento argument prázdný.</span><span class="sxs-lookup"><span data-stu-id="ba00d-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="ba00d-223">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ba00d-223">Options</span></span>

| <span data-ttu-id="ba00d-224">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="ba00d-224">Short option</span></span> | <span data-ttu-id="ba00d-225">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="ba00d-225">Long option</span></span> | <span data-ttu-id="ba00d-226">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="ba00d-227">-p</span><span class="sxs-lookup"><span data-stu-id="ba00d-227">-p</span></span> | <span data-ttu-id="ba00d-228">--Project</span><span class="sxs-lookup"><span data-stu-id="ba00d-228">--project</span></span> | <span data-ttu-id="ba00d-229">Cesta k souboru projektu, na kterém má být provozována.</span><span class="sxs-lookup"><span data-stu-id="ba00d-229">The path to the project file to operate on.</span></span> <span data-ttu-id="ba00d-230">Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.</span><span class="sxs-lookup"><span data-stu-id="ba00d-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="ba00d-231">--suché-běh</span><span class="sxs-lookup"><span data-stu-id="ba00d-231">--dry-run</span></span> | <span data-ttu-id="ba00d-232">Vytvoří výstup seznamu souborů, které by se aktualizovaly bez stažení nového obsahu.</span><span class="sxs-lookup"><span data-stu-id="ba00d-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="ba00d-233">Seznam</span><span class="sxs-lookup"><span data-stu-id="ba00d-233">List</span></span>

<span data-ttu-id="ba00d-234">Příkaz `list` slouží k zobrazení všech odkazů Protobuf v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="ba00d-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="ba00d-235">Pokud jsou všechny hodnoty sloupce výchozí hodnoty, sloupec může být vynechán.</span><span class="sxs-lookup"><span data-stu-id="ba00d-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="ba00d-236">Využití</span><span class="sxs-lookup"><span data-stu-id="ba00d-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="ba00d-237">Možnosti</span><span class="sxs-lookup"><span data-stu-id="ba00d-237">Options</span></span>

| <span data-ttu-id="ba00d-238">Krátká možnost</span><span class="sxs-lookup"><span data-stu-id="ba00d-238">Short option</span></span> | <span data-ttu-id="ba00d-239">Možnost Long</span><span class="sxs-lookup"><span data-stu-id="ba00d-239">Long option</span></span> | <span data-ttu-id="ba00d-240">Popis</span><span class="sxs-lookup"><span data-stu-id="ba00d-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="ba00d-241">-p</span><span class="sxs-lookup"><span data-stu-id="ba00d-241">-p</span></span> | <span data-ttu-id="ba00d-242">--Project</span><span class="sxs-lookup"><span data-stu-id="ba00d-242">--project</span></span> | <span data-ttu-id="ba00d-243">Cesta k souboru projektu, na kterém má být provozována.</span><span class="sxs-lookup"><span data-stu-id="ba00d-243">The path to the project file to operate on.</span></span> <span data-ttu-id="ba00d-244">Pokud soubor není zadán, příkaz vyhledá v aktuálním adresáři.</span><span class="sxs-lookup"><span data-stu-id="ba00d-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba00d-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ba00d-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
