---
title: Hostující sada
author: rick-anderson
description: Naučte se konfigurovat hostující sadu .NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755261"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="df579-103">Hostující sada .NET Core</span><span class="sxs-lookup"><span data-stu-id="df579-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="df579-104">Hostující sada .NET Core je instalační program pro modul runtime .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="df579-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="df579-105">Tato sada umožňuje, aby aplikace ASP.NET Core běžely se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="df579-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="df579-106">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="df579-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="df579-107">Pokud je hostující sada nainstalována před službou IIS, je nutné opravit instalaci sady prostředků.</span><span class="sxs-lookup"><span data-stu-id="df579-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="df579-108">Spusťte znovu instalační program hostující sady po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="df579-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="df579-109">Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="df579-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="df579-110">Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> .</span><span class="sxs-lookup"><span data-stu-id="df579-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="df579-111">Přímé stažení (aktuální verze)</span><span class="sxs-lookup"><span data-stu-id="df579-111">Direct download (current version)</span></span>

<span data-ttu-id="df579-112">Stáhněte instalační program pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="df579-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="df579-113">Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="df579-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="df579-114">Starší verze instalačního programu</span><span class="sxs-lookup"><span data-stu-id="df579-114">Earlier versions of the installer</span></span>

<span data-ttu-id="df579-115">Chcete-li získat starší verzi instalačního programu:</span><span class="sxs-lookup"><span data-stu-id="df579-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="df579-116">Přejděte na stránku [stáhnout jádro .NET Core](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="df579-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="df579-117">Vyberte požadovanou verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df579-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="df579-118">Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df579-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="df579-119">Stáhněte instalační program pomocí odkazu **hostující sada** .</span><span class="sxs-lookup"><span data-stu-id="df579-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="df579-120">Některé instalační programy obsahují verze vydaných verzí, které dosáhly svého konce životnosti (konce řádku) a které už nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="df579-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="df579-121">Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="df579-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="df579-122">Instalace hostitelského balíčku</span><span class="sxs-lookup"><span data-stu-id="df579-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="df579-123">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="df579-123">Run the installer on the server.</span></span> <span data-ttu-id="df579-124">Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:</span><span class="sxs-lookup"><span data-stu-id="df579-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="df579-125">`OPT_NO_ANCM=1`: Přeskočit instalaci modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="df579-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="df579-126">`OPT_NO_RUNTIME=1`: Přeskočí instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="df579-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="df579-127">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="df579-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="df579-128">`OPT_NO_SHAREDFX=1`: Přeskočí instalace sdíleného rozhraní ASP.NET (ASP.NET Runtime).</span><span class="sxs-lookup"><span data-stu-id="df579-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="df579-129">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="df579-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="df579-130">`OPT_NO_X86=1`: Přeskočí instalace běhových prostředí x86.</span><span class="sxs-lookup"><span data-stu-id="df579-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="df579-131">Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="df579-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="df579-132">Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="df579-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="df579-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Pokud je sdílená konfigurace ( `applicationHost.config` ) na stejném počítači jako instalace služby IIS, zakažte kontrolu použití sdílené konfigurace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="df579-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="df579-134">*K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.*</span><span class="sxs-lookup"><span data-stu-id="df579-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="df579-135">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="df579-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="df579-136">Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="df579-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="df579-137">Restartování služby IIS zabere změny v systémové cestě, což je proměnná prostředí vytvořená instalačním programem.</span><span class="sxs-lookup"><span data-stu-id="df579-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="df579-138">ASP.NET Core nesplňuje chování při přeposílání pro vydání oprav pro balíčky sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="df579-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="df579-139">Po upgradu sdíleného rozhraní instalací nové hostitelské sady restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="df579-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="df579-140">Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="df579-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="df579-141">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="df579-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="df579-142">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="df579-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="df579-143">V hostitelském systému přejděte na `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .</span><span class="sxs-lookup"><span data-stu-id="df579-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="df579-144">Vyhledejte `aspnetcorev2.dll` soubor.</span><span class="sxs-lookup"><span data-stu-id="df579-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="df579-145">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="df579-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="df579-146">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="df579-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="df579-147">V umístění se nacházejí protokoly instalačního balíčku pro modul `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="df579-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="df579-148">Soubor je pojmenován `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , kde zástupný symbol `{TIMESTAMP}` je časové razítko souboru.</span><span class="sxs-lookup"><span data-stu-id="df579-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
