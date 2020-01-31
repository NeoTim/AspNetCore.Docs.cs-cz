---
title: Struktura ASP.NET Core Directory
author: guardrex
description: Další informace o struktuře adresářů publikované aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ba5cb96dfdcdca10034299e3bbe662ce056af791
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870263"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="0730f-103">Struktura ASP.NET Core Directory</span><span class="sxs-lookup"><span data-stu-id="0730f-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="0730f-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0730f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0730f-105">Adresář *Publisher* obsahuje nasaditelné prostředky aplikace vytvořené pomocí příkazu [dotnet Publish](/dotnet/core/tools/dotnet-publish) .</span><span class="sxs-lookup"><span data-stu-id="0730f-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="0730f-106">Adresář obsahuje:</span><span class="sxs-lookup"><span data-stu-id="0730f-106">The directory contains:</span></span>

* <span data-ttu-id="0730f-107">Soubory aplikace</span><span class="sxs-lookup"><span data-stu-id="0730f-107">Application files</span></span>
* <span data-ttu-id="0730f-108">Konfigurační soubory</span><span class="sxs-lookup"><span data-stu-id="0730f-108">Configuration files</span></span>
* <span data-ttu-id="0730f-109">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="0730f-109">Static assets</span></span>
* <span data-ttu-id="0730f-110">Balíčky</span><span class="sxs-lookup"><span data-stu-id="0730f-110">Packages</span></span>
* <span data-ttu-id="0730f-111">Modul runtime (jenom[samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) )</span><span class="sxs-lookup"><span data-stu-id="0730f-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="0730f-112">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="0730f-112">App Type</span></span> | <span data-ttu-id="0730f-113">Adresářová struktura</span><span class="sxs-lookup"><span data-stu-id="0730f-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="0730f-114">Spustitelný soubor závislý na rozhraní (FDE)</span><span class="sxs-lookup"><span data-stu-id="0730f-114">Framework-dependent Executable (FDE)</span></span>](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li><span data-ttu-id="0730f-115">publikování&dagger;</span><span class="sxs-lookup"><span data-stu-id="0730f-115">publish&dagger;</span></span><ul><li><span data-ttu-id="0730f-116">Zobrazení&dagger; aplikace MVC; Pokud zobrazení nejsou předkompilována</span><span class="sxs-lookup"><span data-stu-id="0730f-116">Views&dagger; MVC apps; if views aren't precompiled</span></span></li><li><span data-ttu-id="0730f-117">Stránky&dagger; MVC nebo Razor Pages aplikace, pokud nejsou stránky předkompilovány</span><span class="sxs-lookup"><span data-stu-id="0730f-117">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="0730f-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="0730f-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="0730f-119">*soubory. dll</li><li>{název sestavení}. DEPS. json</li><li>{název sestavení}. dll</li><li>{název sestavení} {. Extension} *. exe* rozšíření v systému Windows, žádné rozšíření na MacOS nebo Linux</li><li>{Assembly Name}. pdb</li><li>{název sestavení}. Views. dll</li><li>{ASSEMBLY NAME}. Zobrazení. pdb</li><li>{název sestavení}. runtimeconfig. JSON</li><li>Web. config (nasazení služby IIS)</li><li>createdump ([Linux createdump Utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>* . (Knihovna sdílených objektů Linux)</span><span class="sxs-lookup"><span data-stu-id="0730f-119">*.dll files</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}{.EXTENSION} *.exe* extension on Windows, no extension on macOS or Linux</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config (IIS deployments)</li><li>createdump ([Linux createdump utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>*.so (Linux shared object library)</span></span></li><li><span data-ttu-id="0730f-120">*. a (archiv MacOS)</li><li>* . DYLIB (MacOS Dynamic Library)</span><span class="sxs-lookup"><span data-stu-id="0730f-120">*.a (macOS archive)</li><li>*.dylib (macOS dynamic library)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="0730f-121">Samostatně uzavřené nasazení (SCD)</span><span class="sxs-lookup"><span data-stu-id="0730f-121">Self-contained Deployment (SCD)</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="0730f-122">publikování&dagger;</span><span class="sxs-lookup"><span data-stu-id="0730f-122">publish&dagger;</span></span><ul><li><span data-ttu-id="0730f-123">Zobrazení&dagger; aplikace MVC, pokud zobrazení nejsou předkompilována</span><span class="sxs-lookup"><span data-stu-id="0730f-123">Views&dagger; MVC apps, if views aren't precompiled</span></span></li><li><span data-ttu-id="0730f-124">Stránky&dagger; MVC nebo Razor Pages aplikace, pokud nejsou stránky předkompilovány</span><span class="sxs-lookup"><span data-stu-id="0730f-124">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="0730f-125">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="0730f-125">wwwroot&dagger;</span></span></li><li><span data-ttu-id="0730f-126">soubory \*. dll</span><span class="sxs-lookup"><span data-stu-id="0730f-126">\*.dll files</span></span></li><li><span data-ttu-id="0730f-127">{ASSEMBLY NAME}. DEPS. JSON</span><span class="sxs-lookup"><span data-stu-id="0730f-127">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="0730f-128">{ASSEMBLY NAME}. dll</span><span class="sxs-lookup"><span data-stu-id="0730f-128">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="0730f-129">{ASSEMBLY NAME}. exe</span><span class="sxs-lookup"><span data-stu-id="0730f-129">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="0730f-130">{NÁZEV sestavení}. pdb</span><span class="sxs-lookup"><span data-stu-id="0730f-130">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="0730f-131">{NÁZEV SESTAVENÍ}. Views. dll</span><span class="sxs-lookup"><span data-stu-id="0730f-131">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="0730f-132">{NÁZEV SESTAVENÍ}. Views. pdb</span><span class="sxs-lookup"><span data-stu-id="0730f-132">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="0730f-133">{ASSEMBLY NAME}. runtimeconfig. JSON</span><span class="sxs-lookup"><span data-stu-id="0730f-133">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="0730f-134">Web. config (nasazení služby IIS)</span><span class="sxs-lookup"><span data-stu-id="0730f-134">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="0730f-135">&dagger;označuje adresář.</span><span class="sxs-lookup"><span data-stu-id="0730f-135">&dagger;Indicates a directory</span></span>

<span data-ttu-id="0730f-136">Adresář *Publisher* představuje *kořenovou cestu obsahu*, která se označuje také jako *základní cesta aplikace*nasazení.</span><span class="sxs-lookup"><span data-stu-id="0730f-136">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="0730f-137">Bez ohledu na název se předává adresář pro *publikování* nasazené aplikace na serveru. jeho umístění slouží jako fyzická cesta serveru k hostované aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0730f-137">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="0730f-138">Adresář *wwwroot* , pokud je k dispozici, obsahuje pouze statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="0730f-138">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0730f-139">Vytvoření složky *logs* je užitečné, když [ASP.NET Core modul rozšířené ladění protokolování](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="0730f-139">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="0730f-140">Složky v cestě zadané `<handlerSetting>` hodnoty se nevytváří modulem automaticky a měly by být v nasazení předběžně existující, aby modul mohl zapisovat do protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="0730f-140">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="0730f-141">Adresář *protokolů* lze pro nasazení vytvořit pomocí jednoho z následujících dvou přístupů:</span><span class="sxs-lookup"><span data-stu-id="0730f-141">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="0730f-142">Do souboru projektu přidejte následující `<Target>` element:</span><span class="sxs-lookup"><span data-stu-id="0730f-142">Add the following `<Target>` element to the project file:</span></span>

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   <span data-ttu-id="0730f-143">Element `<MakeDir>` vytvoří prázdnou složku *logs* v publikovaném výstupu.</span><span class="sxs-lookup"><span data-stu-id="0730f-143">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="0730f-144">Element používá vlastnost `PublishDir` k určení cílového umístění pro vytvoření složky.</span><span class="sxs-lookup"><span data-stu-id="0730f-144">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="0730f-145">Několik metod nasazení, například Nasazení webu, během nasazování Přeskočit prázdné složky.</span><span class="sxs-lookup"><span data-stu-id="0730f-145">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="0730f-146">Element `<WriteLinesToFile>` vygeneruje soubor ve složce *logs* , který garantuje nasazení složky na server.</span><span class="sxs-lookup"><span data-stu-id="0730f-146">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="0730f-147">Vytvoření složky pomocí tohoto přístupu se nepovede, pokud pracovní proces nemá oprávnění k zápisu do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="0730f-147">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="0730f-148">Fyzicky vytvořte adresář *protokolů* na serveru v nasazení.</span><span class="sxs-lookup"><span data-stu-id="0730f-148">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="0730f-149">Adresář nasazení vyžaduje oprávnění ke čtení/spouštění.</span><span class="sxs-lookup"><span data-stu-id="0730f-149">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="0730f-150">Adresář *protokolů* vyžaduje oprávnění ke čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="0730f-150">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="0730f-151">Další adresáře, ve kterých jsou soubory napsány, vyžadují oprávnění ke čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="0730f-151">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0730f-152">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0730f-152">Additional resources</span></span>

* [<span data-ttu-id="0730f-153">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="0730f-153">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="0730f-154">Nasazení aplikace .NET core</span><span class="sxs-lookup"><span data-stu-id="0730f-154">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="0730f-155">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="0730f-155">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="0730f-156">Katalog identifikátorů RID .NET Core</span><span class="sxs-lookup"><span data-stu-id="0730f-156">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
