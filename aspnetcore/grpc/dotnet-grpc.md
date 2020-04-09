---
title: Správa odkazů Protobuf pomocí dotnet-grpc
author: juntaoluo
description: Další informace o přidávání, aktualizaci, odebírání a výpisu odkazů Protobuf pomocí globálního nástroje dotnet-grpc.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667333"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="69665-103">Správa odkazů Protobuf pomocí dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="69665-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="69665-104">Podle [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="69665-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="69665-105">`dotnet-grpc`je globální nástroj .NET Core pro správu odkazů [Protobuf (*.proto*)](xref:grpc/basics#proto-file) v rámci projektu .NET gRPC.</span><span class="sxs-lookup"><span data-stu-id="69665-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="69665-106">Nástroj lze použít k přidání, aktualizaci, odebrání a seznamu odkazů Protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="69665-107">Instalace</span><span class="sxs-lookup"><span data-stu-id="69665-107">Installation</span></span>

<span data-ttu-id="69665-108">Chcete-li `dotnet-grpc` nainstalovat [globální nástroj .NET Core](/dotnet/core/tools/global-tools), spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="69665-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="69665-109">Přidání odkazů</span><span class="sxs-lookup"><span data-stu-id="69665-109">Add references</span></span>

<span data-ttu-id="69665-110">`dotnet-grpc`lze přidat odkazy Protobuf jako `<Protobuf />` položky do souboru *.csproj:*</span><span class="sxs-lookup"><span data-stu-id="69665-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="69665-111">Odkazy Protobuf se používají ke generování prostředků klienta c# nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="69665-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="69665-112">Nástroj `dotnet-grpc` může:</span><span class="sxs-lookup"><span data-stu-id="69665-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="69665-113">Vytvořte odkaz Protobuf z místních souborů na disku.</span><span class="sxs-lookup"><span data-stu-id="69665-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="69665-114">Vytvořte odkaz Protobuf ze vzdáleného souboru určeného adresou URL.</span><span class="sxs-lookup"><span data-stu-id="69665-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="69665-115">Ujistěte se, že správné gRPC balíček závislosti jsou přidány do projektu.</span><span class="sxs-lookup"><span data-stu-id="69665-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="69665-116">Balíček se `Grpc.AspNetCore` například přidá do webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="69665-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="69665-117">`Grpc.AspNetCore`obsahuje gRPC server a klientské knihovny a podporu nástrojů.</span><span class="sxs-lookup"><span data-stu-id="69665-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="69665-118">Alternativně `Grpc.Net.Client`, `Grpc.Tools` a `Google.Protobuf` balíčky, které obsahují pouze gRPC klientské knihovny a podporu nástrojů, jsou přidány do aplikace Konzola.</span><span class="sxs-lookup"><span data-stu-id="69665-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="69665-119">Přidat soubor</span><span class="sxs-lookup"><span data-stu-id="69665-119">Add file</span></span>

<span data-ttu-id="69665-120">Příkaz `add-file` se používá k přidání místních souborů na disk jako odkazy Protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="69665-121">Cesty k souboru jsou k dispozici:</span><span class="sxs-lookup"><span data-stu-id="69665-121">The file paths provided:</span></span>

* <span data-ttu-id="69665-122">Může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="69665-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="69665-123">Může obsahovat zástupné znaky pro [globbing](https://wikipedia.org/wiki/Glob_(programming))souborů založených na vzoru .</span><span class="sxs-lookup"><span data-stu-id="69665-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="69665-124">Pokud jsou některé soubory mimo `Link` adresář projektu, je přidán prvek `Protos` pro zobrazení souboru pod složkou v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="69665-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="69665-125">Využití</span><span class="sxs-lookup"><span data-stu-id="69665-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="69665-126">Argumenty</span><span class="sxs-lookup"><span data-stu-id="69665-126">Arguments</span></span>

| <span data-ttu-id="69665-127">Argument</span><span class="sxs-lookup"><span data-stu-id="69665-127">Argument</span></span> | <span data-ttu-id="69665-128">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-128">Description</span></span> |
|-|-|
| <span data-ttu-id="69665-129">files</span><span class="sxs-lookup"><span data-stu-id="69665-129">files</span></span> | <span data-ttu-id="69665-130">Protobuf soubor odkazy.</span><span class="sxs-lookup"><span data-stu-id="69665-130">The protobuf file references.</span></span> <span data-ttu-id="69665-131">Může to být cesta k globu pro místní protobufové soubory.</span><span class="sxs-lookup"><span data-stu-id="69665-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="69665-132">Možnosti</span><span class="sxs-lookup"><span data-stu-id="69665-132">Options</span></span>

| <span data-ttu-id="69665-133">Krátká volba</span><span class="sxs-lookup"><span data-stu-id="69665-133">Short option</span></span> | <span data-ttu-id="69665-134">Dlouhá volba</span><span class="sxs-lookup"><span data-stu-id="69665-134">Long option</span></span> | <span data-ttu-id="69665-135">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="69665-136">-p</span><span class="sxs-lookup"><span data-stu-id="69665-136">-p</span></span> | <span data-ttu-id="69665-137">--projekt</span><span class="sxs-lookup"><span data-stu-id="69665-137">--project</span></span> | <span data-ttu-id="69665-138">Cesta k souboru projektu, na kterém chcete pracovat.</span><span class="sxs-lookup"><span data-stu-id="69665-138">The path to the project file to operate on.</span></span> <span data-ttu-id="69665-139">Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.</span><span class="sxs-lookup"><span data-stu-id="69665-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="69665-140">-s</span><span class="sxs-lookup"><span data-stu-id="69665-140">-s</span></span> | <span data-ttu-id="69665-141">--služby</span><span class="sxs-lookup"><span data-stu-id="69665-141">--services</span></span> | <span data-ttu-id="69665-142">Typ služeb gRPC, které by měly být generovány.</span><span class="sxs-lookup"><span data-stu-id="69665-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="69665-143">Pokud `Default` je `Both` zadán, se používá `Client` pro webové projekty a používá se pro jiné než webové projekty.</span><span class="sxs-lookup"><span data-stu-id="69665-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="69665-144">Přijaté hodnoty `Both` `Client`jsou `Default` `None`, `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="69665-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="69665-145">-i</span><span class="sxs-lookup"><span data-stu-id="69665-145">-i</span></span> | <span data-ttu-id="69665-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="69665-146">--additional-import-dirs</span></span> | <span data-ttu-id="69665-147">Další adresáře, které mají být použity při řešení importů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="69665-148">Toto je středník oddělený seznam cest.</span><span class="sxs-lookup"><span data-stu-id="69665-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="69665-149">--přístup</span><span class="sxs-lookup"><span data-stu-id="69665-149">--access</span></span> | <span data-ttu-id="69665-150">Modifikátor přístupu pro generované třídy Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="69665-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="69665-151">Výchozí hodnota je `Public`.</span><span class="sxs-lookup"><span data-stu-id="69665-151">The default value is `Public`.</span></span> <span data-ttu-id="69665-152">Přijaté hodnoty `Internal` `Public`jsou a .</span><span class="sxs-lookup"><span data-stu-id="69665-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="69665-153">Přidat adresu URL</span><span class="sxs-lookup"><span data-stu-id="69665-153">Add URL</span></span>

<span data-ttu-id="69665-154">Příkaz `add-url` se používá k přidání vzdáleného souboru určeného zdrojovou adresou URL jako odkazu Protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="69665-155">Musí být k dispozici cesta k souboru, která určuje, kam má být vzdálený soubor stažen.</span><span class="sxs-lookup"><span data-stu-id="69665-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="69665-156">Cesta k souboru může být relativní vzhledem k aktuálnímu adresáři nebo absolutní cestě.</span><span class="sxs-lookup"><span data-stu-id="69665-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="69665-157">Pokud je cesta k souboru `Link` mimo adresář projektu, přidá se `Protos` prvek, který zobrazí soubor pod virtuální složkou v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="69665-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="69665-158">Využití</span><span class="sxs-lookup"><span data-stu-id="69665-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="69665-159">Argumenty</span><span class="sxs-lookup"><span data-stu-id="69665-159">Arguments</span></span>

| <span data-ttu-id="69665-160">Argument</span><span class="sxs-lookup"><span data-stu-id="69665-160">Argument</span></span> | <span data-ttu-id="69665-161">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-161">Description</span></span> |
|-|-|
| <span data-ttu-id="69665-162">url</span><span class="sxs-lookup"><span data-stu-id="69665-162">url</span></span> | <span data-ttu-id="69665-163">Adresa URL vzdáleného souboru protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="69665-164">Možnosti</span><span class="sxs-lookup"><span data-stu-id="69665-164">Options</span></span>

| <span data-ttu-id="69665-165">Krátká volba</span><span class="sxs-lookup"><span data-stu-id="69665-165">Short option</span></span> | <span data-ttu-id="69665-166">Dlouhá volba</span><span class="sxs-lookup"><span data-stu-id="69665-166">Long option</span></span> | <span data-ttu-id="69665-167">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="69665-168">-o</span><span class="sxs-lookup"><span data-stu-id="69665-168">-o</span></span> | <span data-ttu-id="69665-169">--output</span><span class="sxs-lookup"><span data-stu-id="69665-169">--output</span></span> | <span data-ttu-id="69665-170">Určuje cestu ke stažení pro vzdálený soubor protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="69665-171">Tato možnost je povinná.</span><span class="sxs-lookup"><span data-stu-id="69665-171">This is a required option.</span></span>
| <span data-ttu-id="69665-172">-p</span><span class="sxs-lookup"><span data-stu-id="69665-172">-p</span></span> | <span data-ttu-id="69665-173">--projekt</span><span class="sxs-lookup"><span data-stu-id="69665-173">--project</span></span> | <span data-ttu-id="69665-174">Cesta k souboru projektu, na kterém chcete pracovat.</span><span class="sxs-lookup"><span data-stu-id="69665-174">The path to the project file to operate on.</span></span> <span data-ttu-id="69665-175">Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.</span><span class="sxs-lookup"><span data-stu-id="69665-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="69665-176">-s</span><span class="sxs-lookup"><span data-stu-id="69665-176">-s</span></span> | <span data-ttu-id="69665-177">--služby</span><span class="sxs-lookup"><span data-stu-id="69665-177">--services</span></span> | <span data-ttu-id="69665-178">Typ služeb gRPC, které by měly být generovány.</span><span class="sxs-lookup"><span data-stu-id="69665-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="69665-179">Pokud `Default` je `Both` zadán, se používá `Client` pro webové projekty a používá se pro jiné než webové projekty.</span><span class="sxs-lookup"><span data-stu-id="69665-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="69665-180">Přijaté hodnoty `Both` `Client`jsou `Default` `None`, `Server`, , , .</span><span class="sxs-lookup"><span data-stu-id="69665-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="69665-181">-i</span><span class="sxs-lookup"><span data-stu-id="69665-181">-i</span></span> | <span data-ttu-id="69665-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="69665-182">--additional-import-dirs</span></span> | <span data-ttu-id="69665-183">Další adresáře, které mají být použity při řešení importů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="69665-184">Toto je středník oddělený seznam cest.</span><span class="sxs-lookup"><span data-stu-id="69665-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="69665-185">--přístup</span><span class="sxs-lookup"><span data-stu-id="69665-185">--access</span></span> | <span data-ttu-id="69665-186">Modifikátor přístupu pro generované třídy Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="69665-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="69665-187">Výchozí hodnota `Public`je .</span><span class="sxs-lookup"><span data-stu-id="69665-187">Default value is `Public`.</span></span> <span data-ttu-id="69665-188">Přijaté hodnoty `Internal` `Public`jsou a .</span><span class="sxs-lookup"><span data-stu-id="69665-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="69665-189">Odebrat</span><span class="sxs-lookup"><span data-stu-id="69665-189">Remove</span></span>

<span data-ttu-id="69665-190">Příkaz `remove` se používá k odebrání odkazů Protobuf ze souboru *.csproj.*</span><span class="sxs-lookup"><span data-stu-id="69665-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="69665-191">Příkaz přijímá argumenty cesty a zdrojové adresy URL jako argumenty.</span><span class="sxs-lookup"><span data-stu-id="69665-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="69665-192">Nástroj:</span><span class="sxs-lookup"><span data-stu-id="69665-192">The tool:</span></span>

* <span data-ttu-id="69665-193">Odebere pouze odkaz Protobuf.</span><span class="sxs-lookup"><span data-stu-id="69665-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="69665-194">Neodstraní soubor *.proto,* i když byl původně stažen ze vzdálené adresy URL.</span><span class="sxs-lookup"><span data-stu-id="69665-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="69665-195">Využití</span><span class="sxs-lookup"><span data-stu-id="69665-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="69665-196">Argumenty</span><span class="sxs-lookup"><span data-stu-id="69665-196">Arguments</span></span>

| <span data-ttu-id="69665-197">Argument</span><span class="sxs-lookup"><span data-stu-id="69665-197">Argument</span></span> | <span data-ttu-id="69665-198">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-198">Description</span></span> |
|-|-|
| <span data-ttu-id="69665-199">odkazy</span><span class="sxs-lookup"><span data-stu-id="69665-199">references</span></span> | <span data-ttu-id="69665-200">Adresy URL nebo cesty k souborům odkazů protobuf odebrat.</span><span class="sxs-lookup"><span data-stu-id="69665-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="69665-201">Možnosti</span><span class="sxs-lookup"><span data-stu-id="69665-201">Options</span></span>

| <span data-ttu-id="69665-202">Krátká volba</span><span class="sxs-lookup"><span data-stu-id="69665-202">Short option</span></span> | <span data-ttu-id="69665-203">Dlouhá volba</span><span class="sxs-lookup"><span data-stu-id="69665-203">Long option</span></span> | <span data-ttu-id="69665-204">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="69665-205">-p</span><span class="sxs-lookup"><span data-stu-id="69665-205">-p</span></span> | <span data-ttu-id="69665-206">--projekt</span><span class="sxs-lookup"><span data-stu-id="69665-206">--project</span></span> | <span data-ttu-id="69665-207">Cesta k souboru projektu, na kterém chcete pracovat.</span><span class="sxs-lookup"><span data-stu-id="69665-207">The path to the project file to operate on.</span></span> <span data-ttu-id="69665-208">Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.</span><span class="sxs-lookup"><span data-stu-id="69665-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="69665-209">Obnovení</span><span class="sxs-lookup"><span data-stu-id="69665-209">Refresh</span></span>

<span data-ttu-id="69665-210">Příkaz `refresh` se používá k aktualizaci vzdáleného odkazu s nejnovějším obsahem ze zdrojové adresy URL.</span><span class="sxs-lookup"><span data-stu-id="69665-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="69665-211">Cestu ke stažení souboru i zdrojovou adresu URL lze použít k určení odkazu, který má být aktualizován.</span><span class="sxs-lookup"><span data-stu-id="69665-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="69665-212">Poznámka:</span><span class="sxs-lookup"><span data-stu-id="69665-212">Note:</span></span>

* <span data-ttu-id="69665-213">Porovnané hodnoty hashe obsahu souboru, aby bylo možné určit, zda má být místní soubor aktualizován.</span><span class="sxs-lookup"><span data-stu-id="69665-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="69665-214">Nejsou porovnány žádné informace o časovém razítku.</span><span class="sxs-lookup"><span data-stu-id="69665-214">No timestamp information is compared.</span></span>

<span data-ttu-id="69665-215">Nástroj vždy nahradí místní soubor vzdáleným souborem, pokud je nutná aktualizace.</span><span class="sxs-lookup"><span data-stu-id="69665-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="69665-216">Využití</span><span class="sxs-lookup"><span data-stu-id="69665-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="69665-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="69665-217">Arguments</span></span>

| <span data-ttu-id="69665-218">Argument</span><span class="sxs-lookup"><span data-stu-id="69665-218">Argument</span></span> | <span data-ttu-id="69665-219">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-219">Description</span></span> |
|-|-|
| <span data-ttu-id="69665-220">odkazy</span><span class="sxs-lookup"><span data-stu-id="69665-220">references</span></span> | <span data-ttu-id="69665-221">Adresy URL nebo cesty k souborům ke vzdáleným protobuf odkazům, které by měly být aktualizovány.</span><span class="sxs-lookup"><span data-stu-id="69665-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="69665-222">Ponechte tento argument prázdný, chcete-li aktualizovat všechny vzdálené odkazy.</span><span class="sxs-lookup"><span data-stu-id="69665-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="69665-223">Možnosti</span><span class="sxs-lookup"><span data-stu-id="69665-223">Options</span></span>

| <span data-ttu-id="69665-224">Krátká volba</span><span class="sxs-lookup"><span data-stu-id="69665-224">Short option</span></span> | <span data-ttu-id="69665-225">Dlouhá volba</span><span class="sxs-lookup"><span data-stu-id="69665-225">Long option</span></span> | <span data-ttu-id="69665-226">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="69665-227">-p</span><span class="sxs-lookup"><span data-stu-id="69665-227">-p</span></span> | <span data-ttu-id="69665-228">--projekt</span><span class="sxs-lookup"><span data-stu-id="69665-228">--project</span></span> | <span data-ttu-id="69665-229">Cesta k souboru projektu, na kterém chcete pracovat.</span><span class="sxs-lookup"><span data-stu-id="69665-229">The path to the project file to operate on.</span></span> <span data-ttu-id="69665-230">Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.</span><span class="sxs-lookup"><span data-stu-id="69665-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="69665-231">--běh na suchu</span><span class="sxs-lookup"><span data-stu-id="69665-231">--dry-run</span></span> | <span data-ttu-id="69665-232">Vypíše seznam souborů, které by byly aktualizovány bez stahování nového obsahu.</span><span class="sxs-lookup"><span data-stu-id="69665-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="69665-233">Seznam</span><span class="sxs-lookup"><span data-stu-id="69665-233">List</span></span>

<span data-ttu-id="69665-234">Příkaz `list` se používá k zobrazení všech odkazů Protobuf v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="69665-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="69665-235">Pokud jsou všechny hodnoty sloupce výchozími hodnotami, může být sloupec vynechán.</span><span class="sxs-lookup"><span data-stu-id="69665-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="69665-236">Využití</span><span class="sxs-lookup"><span data-stu-id="69665-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="69665-237">Možnosti</span><span class="sxs-lookup"><span data-stu-id="69665-237">Options</span></span>

| <span data-ttu-id="69665-238">Krátká volba</span><span class="sxs-lookup"><span data-stu-id="69665-238">Short option</span></span> | <span data-ttu-id="69665-239">Dlouhá volba</span><span class="sxs-lookup"><span data-stu-id="69665-239">Long option</span></span> | <span data-ttu-id="69665-240">Popis</span><span class="sxs-lookup"><span data-stu-id="69665-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="69665-241">-p</span><span class="sxs-lookup"><span data-stu-id="69665-241">-p</span></span> | <span data-ttu-id="69665-242">--projekt</span><span class="sxs-lookup"><span data-stu-id="69665-242">--project</span></span> | <span data-ttu-id="69665-243">Cesta k souboru projektu, na kterém chcete pracovat.</span><span class="sxs-lookup"><span data-stu-id="69665-243">The path to the project file to operate on.</span></span> <span data-ttu-id="69665-244">Pokud soubor není zadán, příkaz prohledá aktuální adresář pro jeden.</span><span class="sxs-lookup"><span data-stu-id="69665-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69665-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="69665-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
