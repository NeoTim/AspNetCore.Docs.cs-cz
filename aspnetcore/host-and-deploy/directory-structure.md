---
title: Struktura adresářů ASP.NET Core
author: guardrex
description: Další informace o struktuře adresářů publikované aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: host-and-deploy/directory-structure
ms.openlocfilehash: f1df047decc7a0a6b7dcee57a690c55eea428b05
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166971"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="b9539-103">Struktura adresářů ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9539-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="b9539-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b9539-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b9539-105">*Publikovat* adresář obsahuje nasaditelné prostředky aplikace vytvořené metodou [dotnet publikovat](/dotnet/core/tools/dotnet-publish) příkazu.</span><span class="sxs-lookup"><span data-stu-id="b9539-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b9539-106">Adresář obsahuje:</span><span class="sxs-lookup"><span data-stu-id="b9539-106">The directory contains:</span></span>

* <span data-ttu-id="b9539-107">Soubory aplikace</span><span class="sxs-lookup"><span data-stu-id="b9539-107">Application files</span></span>
* <span data-ttu-id="b9539-108">Konfigurační soubory</span><span class="sxs-lookup"><span data-stu-id="b9539-108">Configuration files</span></span>
* <span data-ttu-id="b9539-109">Statické prostředky</span><span class="sxs-lookup"><span data-stu-id="b9539-109">Static assets</span></span>
* <span data-ttu-id="b9539-110">Balíčky</span><span class="sxs-lookup"><span data-stu-id="b9539-110">Packages</span></span>
* <span data-ttu-id="b9539-111">Modul runtime ([samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) jenom)</span><span class="sxs-lookup"><span data-stu-id="b9539-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="b9539-112">Typ aplikace</span><span class="sxs-lookup"><span data-stu-id="b9539-112">App Type</span></span> | <span data-ttu-id="b9539-113">Adresářová struktura</span><span class="sxs-lookup"><span data-stu-id="b9539-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="b9539-114">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="b9539-114">Framework-dependent Deployment</span></span>](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li><span data-ttu-id="b9539-115">Publikování&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9539-115">publish&dagger;</span></span><ul><li><span data-ttu-id="b9539-116">Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</span><span class="sxs-lookup"><span data-stu-id="b9539-116">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="b9539-117">Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</span><span class="sxs-lookup"><span data-stu-id="b9539-117">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="b9539-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9539-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="b9539-119">\*\.soubory knihoven DLL</span><span class="sxs-lookup"><span data-stu-id="b9539-119">\*\.dll files</span></span></li><li><span data-ttu-id="b9539-120">{NÁZEV sestavení} deps.JSON</span><span class="sxs-lookup"><span data-stu-id="b9539-120">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="b9539-121">{NÁZEV sestavení} .dll</span><span class="sxs-lookup"><span data-stu-id="b9539-121">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="b9539-122">{NÁZEV sestavení} .pdb</span><span class="sxs-lookup"><span data-stu-id="b9539-122">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="b9539-123">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="b9539-123">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="b9539-124">{NÁZEV SESTAVENÍ}. Views.pdb</span><span class="sxs-lookup"><span data-stu-id="b9539-124">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="b9539-125">{NÁZEV sestavení}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="b9539-125">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="b9539-126">soubor Web.config (nasazení služby IIS)</span><span class="sxs-lookup"><span data-stu-id="b9539-126">web.config (IIS deployments)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="b9539-127">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="b9539-127">Self-contained Deployment</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="b9539-128">Publikování&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9539-128">publish&dagger;</span></span><ul><li><span data-ttu-id="b9539-129">Zobrazení&dagger; (aplikace MVC; Pokud nejsou předkompilované zobrazení)</span><span class="sxs-lookup"><span data-stu-id="b9539-129">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="b9539-130">Stránky&dagger; (MVC Razor Pages aplikace nebo; pokud nejsou předkompilované stránky)</span><span class="sxs-lookup"><span data-stu-id="b9539-130">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="b9539-131">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="b9539-131">wwwroot&dagger;</span></span></li><li><span data-ttu-id="b9539-132">\*knihovny DLL</span><span class="sxs-lookup"><span data-stu-id="b9539-132">\*.dll files</span></span></li><li><span data-ttu-id="b9539-133">{NÁZEV sestavení} deps.JSON</span><span class="sxs-lookup"><span data-stu-id="b9539-133">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="b9539-134">{NÁZEV sestavení} .dll</span><span class="sxs-lookup"><span data-stu-id="b9539-134">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="b9539-135">{NÁZEV sestavení} .exe</span><span class="sxs-lookup"><span data-stu-id="b9539-135">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="b9539-136">{NÁZEV sestavení} .pdb</span><span class="sxs-lookup"><span data-stu-id="b9539-136">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="b9539-137">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="b9539-137">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="b9539-138">{NÁZEV SESTAVENÍ}. Views.pdb</span><span class="sxs-lookup"><span data-stu-id="b9539-138">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="b9539-139">{NÁZEV sestavení}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="b9539-139">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="b9539-140">soubor Web.config (nasazení služby IIS)</span><span class="sxs-lookup"><span data-stu-id="b9539-140">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="b9539-141">&dagger;Určuje adresář</span><span class="sxs-lookup"><span data-stu-id="b9539-141">&dagger;Indicates a directory</span></span>

<span data-ttu-id="b9539-142">*Publikovat* adresář představuje *obsahu kořenová cesta*, označované také jako *základní cesta aplikace*, nasazení.</span><span class="sxs-lookup"><span data-stu-id="b9539-142">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="b9539-143">Název je uveden *publikovat* adresáře nasazené aplikace na serveru, její umístění slouží jako fyzická cesta serveru k hostované aplikace.</span><span class="sxs-lookup"><span data-stu-id="b9539-143">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="b9539-144">*Wwwroot* adresář, pokud jsou k dispozici, obsahuje pouze statické prostředky.</span><span class="sxs-lookup"><span data-stu-id="b9539-144">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b9539-145">Vytváření *protokoly* složka je užitečné pro [modul ASP.NET Core rozšířené protokolování ladění](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span><span class="sxs-lookup"><span data-stu-id="b9539-145">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="b9539-146">Složky v cestě k dispozici na `<handlerSetting>` hodnoty nevytvoří modulem automaticky a měla by existovat předem v nasazení umožňuje modulu pro zápis protokolů ladění.</span><span class="sxs-lookup"><span data-stu-id="b9539-146">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="b9539-147">A *protokoly* lze vytvořit adresář pro nasazení pomocí jedné z následujících dvou přístupů:</span><span class="sxs-lookup"><span data-stu-id="b9539-147">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="b9539-148">Přidejte následující `<Target>` element do souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="b9539-148">Add the following `<Target>` element to the project file:</span></span>

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

   <span data-ttu-id="b9539-149">`<MakeDir>` Element vytvoří prázdnou *protokoly* složky v publikované výstup.</span><span class="sxs-lookup"><span data-stu-id="b9539-149">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="b9539-150">Element používá `PublishDir` a určí cílové umístění pro vytvoření složky.</span><span class="sxs-lookup"><span data-stu-id="b9539-150">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="b9539-151">Několik metod nasazení, jako je nasazení webu, přeskočte prázdné složky během nasazení.</span><span class="sxs-lookup"><span data-stu-id="b9539-151">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="b9539-152">`<WriteLinesToFile>` Element generuje soubor v *protokoly* složky, která zaručuje nasazení složky na serveru.</span><span class="sxs-lookup"><span data-stu-id="b9539-152">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="b9539-153">Vytvoření složky Tento přístup selže, pokud pracovní proces nemá oprávnění k zápisu do cílové složky.</span><span class="sxs-lookup"><span data-stu-id="b9539-153">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="b9539-154">Fyzicky vytvořit *protokoly* adresáře na serveru v nasazení.</span><span class="sxs-lookup"><span data-stu-id="b9539-154">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="b9539-155">Adresář nasazení vyžaduje oprávnění ke čtení a spouštění.</span><span class="sxs-lookup"><span data-stu-id="b9539-155">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="b9539-156">*Protokoly* directory vyžaduje oprávnění ke čtení/zápisu.</span><span class="sxs-lookup"><span data-stu-id="b9539-156">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="b9539-157">Další adresáře, kde soubory jsou zapsány vyžadují oprávnění pro čtení a zápis.</span><span class="sxs-lookup"><span data-stu-id="b9539-157">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b9539-158">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b9539-158">Additional resources</span></span>

* [<span data-ttu-id="b9539-159">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="b9539-159">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="b9539-160">Nasazení aplikace .NET core</span><span class="sxs-lookup"><span data-stu-id="b9539-160">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="b9539-161">Cílové architektury</span><span class="sxs-lookup"><span data-stu-id="b9539-161">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="b9539-162">Katalog identifikátorů RID .NET core</span><span class="sxs-lookup"><span data-stu-id="b9539-162">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
