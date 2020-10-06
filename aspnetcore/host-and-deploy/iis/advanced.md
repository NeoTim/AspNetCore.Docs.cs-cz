---
title: Pokročilá konfigurace
author: rick-anderson
description: Pokročilá konfigurace s modulem ASP.NET Core a Internetová informační služba (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755242"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="1f619-103">Pokročilá konfigurace modulu ASP.NET Core a služby IIS</span><span class="sxs-lookup"><span data-stu-id="1f619-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="1f619-104">Tento článek se zabývá pokročilými možnostmi konfigurace a scénáři pro modul ASP.NET Core a službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="1f619-105">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="1f619-105">Modify the stack size</span></span>

<span data-ttu-id="1f619-106">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="1f619-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="1f619-107">Nakonfigurujte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech v `web.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="1f619-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="1f619-108">Výchozí velikost je 1 048 576 bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="1f619-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="1f619-109">Následující příklad změní velikost zásobníku na 2 MB (2 097 152 bajtů):</span><span class="sxs-lookup"><span data-stu-id="1f619-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="1f619-110">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="1f619-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="1f619-111">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="1f619-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="1f619-112">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="1f619-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="1f619-113">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="1f619-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="1f619-114">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="1f619-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="1f619-115">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="1f619-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="1f619-116">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="1f619-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="1f619-117">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1f619-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="1f619-118">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="1f619-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="1f619-119">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="1f619-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="1f619-120">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="1f619-121">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="1f619-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="1f619-122">Instalační služba modulu ASP.NET Core se spouští s oprávněními `TrustedInstaller` účtu.</span><span class="sxs-lookup"><span data-stu-id="1f619-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="1f619-123">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v `applicationHost.config` souboru ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="1f619-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="1f619-124">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="1f619-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="1f619-125">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="1f619-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="1f619-126">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="1f619-127">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="1f619-127">Run the installer.</span></span>
1. <span data-ttu-id="1f619-128">Exportujte aktualizovaný `applicationHost.config` soubor do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="1f619-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="1f619-129">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="1f619-130">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="1f619-130">Data protection</span></span>

<span data-ttu-id="1f619-131">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování.</span><span class="sxs-lookup"><span data-stu-id="1f619-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="1f619-132">I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="1f619-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="1f619-133">Pokud ochrana dat není nakonfigurovaná, klíče se uchovávají v paměti a při restartování aplikace se zahodí.</span><span class="sxs-lookup"><span data-stu-id="1f619-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="1f619-134">Pokud je datový prstenec ochrany dat uložený v paměti při restartu aplikace:</span><span class="sxs-lookup"><span data-stu-id="1f619-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="1f619-135">U cookie tokenů ověřování na základě jsou neověřené.</span><span class="sxs-lookup"><span data-stu-id="1f619-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="1f619-136">Uživatelé se musí znovu přihlásit na svůj další požadavek.</span><span class="sxs-lookup"><span data-stu-id="1f619-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="1f619-137">Data chráněná pomocí Key ringu už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="1f619-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="1f619-138">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="1f619-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="1f619-139">Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1f619-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="1f619-140">**Vytvoření klíčů registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="1f619-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="1f619-141">Klíče ochrany dat, které používá aplikace ASP.NET Core, jsou uložené v registru externě pro aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="1f619-142">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="1f619-143">Pro samostatnou instalaci služby IIS, která není typu webfarma, se dá [skript Provision-AutoGenKeys.ps1 PowerShellu pro ochranu dat](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1f619-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="1f619-144">Tento skript vytvoří klíč registru v registru HKLM, který je přístupný jenom pro účet pracovního procesu fondu aplikací aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="1f619-145">Klíče jsou v klidovém stavu zašifrované pomocí rozhraní DPAPI s klíčem celého počítače.</span><span class="sxs-lookup"><span data-stu-id="1f619-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="1f619-146">Ve scénářích webové farmy může být aplikace nakonfigurovaná tak, aby používala cestu UNC k uložení svého prstence s klíčem pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="1f619-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="1f619-147">Ve výchozím nastavení klíče nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="1f619-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="1f619-148">Ujistěte se, že oprávnění k souborům pro sdílenou síťovou složku jsou omezená na účet systému Windows, pod kterým je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="1f619-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="1f619-149">K ochraně neaktivních klíčů je možné použít certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="1f619-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="1f619-150">Vezměte v úvahu mechanismus, který uživatelům umožní nahrávat certifikáty.</span><span class="sxs-lookup"><span data-stu-id="1f619-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="1f619-151">Dejte certifikáty do důvěryhodného úložiště certifikátů uživatele a ujistěte se, že jsou k dispozici na všech počítačích, na kterých běží aplikace uživatele.</span><span class="sxs-lookup"><span data-stu-id="1f619-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="1f619-152">Další informace naleznete v tématu <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="1f619-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="1f619-153">**Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**</span><span class="sxs-lookup"><span data-stu-id="1f619-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="1f619-154">Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="1f619-155">Nastavte **načíst profil uživatele** na `True` .</span><span class="sxs-lookup"><span data-stu-id="1f619-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="1f619-156">Pokud je nastaveno na `True` , klíče jsou uloženy v adresáři profilu uživatele a chráněny pomocí rozhraní DPAPI s klíčem specifickým pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="1f619-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="1f619-157">Klíče jsou uchovány do `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` složky.</span><span class="sxs-lookup"><span data-stu-id="1f619-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="1f619-158">Musí být povolen i [ `setProfileEnvironment` atribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) ve fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="1f619-159">Výchozí hodnota `setProfileEnvironment` je `true` .</span><span class="sxs-lookup"><span data-stu-id="1f619-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="1f619-160">V některých scénářích (například operační systém Windows) `setProfileEnvironment` je nastavena na `false` .</span><span class="sxs-lookup"><span data-stu-id="1f619-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="1f619-161">Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="1f619-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="1f619-162">Přejděte do složky `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="1f619-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="1f619-163">Otevřete soubor `applicationHost.config`.</span><span class="sxs-lookup"><span data-stu-id="1f619-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="1f619-164">Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="1f619-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="1f619-165">Potvrďte, že `setProfileEnvironment` atribut není přítomen, přičemž výchozí hodnota je `true` , nebo explicitně nastavte hodnotu atributu na `true` .</span><span class="sxs-lookup"><span data-stu-id="1f619-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="1f619-166">**Použití systému souborů jako úložiště pro Key Ring**</span><span class="sxs-lookup"><span data-stu-id="1f619-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="1f619-167">Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="1f619-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="1f619-168">K ochraně služby Key Ring použijte certifikát x509 a ujistěte se, že certifikát je důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="1f619-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="1f619-169">Pokud je certifikát podepsaný svým držitelem, umístěte certifikát do důvěryhodného kořenového úložiště.</span><span class="sxs-lookup"><span data-stu-id="1f619-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="1f619-170">Při použití služby IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="1f619-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="1f619-171">Použijte sdílenou složku, ke které mají přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="1f619-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="1f619-172">Nasaďte do každého počítače certifikát x509.</span><span class="sxs-lookup"><span data-stu-id="1f619-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="1f619-173">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="1f619-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="1f619-174">**Nastavení zásad pro ochranu dat na úrovni počítače**</span><span class="sxs-lookup"><span data-stu-id="1f619-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="1f619-175">Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="1f619-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="1f619-176">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="1f619-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="1f619-177">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="1f619-177">IIS configuration</span></span>

<span data-ttu-id="1f619-178">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="1f619-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="1f619-179">Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.</span><span class="sxs-lookup"><span data-stu-id="1f619-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="1f619-180">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="1f619-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="1f619-181">V kroku **role serveru** zaškrtněte políčko **webový server (IIS)**.</span><span class="sxs-lookup"><span data-stu-id="1f619-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku vybrat role serveru je vybraná role Webový server IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="1f619-183">Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="1f619-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="1f619-184">Vyberte požadované služby role IIS nebo přijměte výchozí poskytnuté služby rolí.</span><span class="sxs-lookup"><span data-stu-id="1f619-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí se vyberou v kroku vybrat služby rolí.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="1f619-186">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="1f619-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="1f619-187">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webového serveru**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="1f619-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="1f619-188">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="1f619-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="1f619-189">Další informace najdete v tématu [ověřování `<windowsAuthentication>` systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="1f619-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="1f619-190">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="1f619-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="1f619-191">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="1f619-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="1f619-192">Chcete-li povolit objekty WebSockets, rozbalte následující **Web Server**uzly:  >  **vývoj aplikací**webového serveru.</span><span class="sxs-lookup"><span data-stu-id="1f619-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="1f619-193">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="1f619-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="1f619-194">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="1f619-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="1f619-195">Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** .</span><span class="sxs-lookup"><span data-stu-id="1f619-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="1f619-196">Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="1f619-197">**Desktopové operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="1f619-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="1f619-198">Povolte **konzolu pro správu služby IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="1f619-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="1f619-199">Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="1f619-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="1f619-200">Otevřete uzel **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="1f619-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="1f619-201">Otevřete uzel **Nástroje webové správy** .</span><span class="sxs-lookup"><span data-stu-id="1f619-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="1f619-202">Zaškrtněte políčko pro **konzolu pro správu služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="1f619-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="1f619-203">Zaškrtněte políčko u **webových služeb**.</span><span class="sxs-lookup"><span data-stu-id="1f619-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="1f619-204">Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1f619-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="1f619-205">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="1f619-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="1f619-206">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: zabezpečení **webové služby**  >  **Security**.</span><span class="sxs-lookup"><span data-stu-id="1f619-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="1f619-207">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="1f619-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="1f619-208">Další informace najdete v tématu [ověřování `<windowsAuthentication>` systému Windows](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="1f619-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="1f619-209">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="1f619-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="1f619-210">Objekty WebSocket jsou podporované ASP.NET Core 1,1 nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="1f619-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="1f619-211">Chcete-li povolit objekty WebSockets, rozbalte následující uzly: funkce pro vývoj aplikací v **rámci webové služby**  >  **Application Development Features**.</span><span class="sxs-lookup"><span data-stu-id="1f619-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="1f619-212">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="1f619-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="1f619-213">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="1f619-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="1f619-214">Pokud instalace služby IIS vyžaduje restart, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="1f619-214">If the IIS installation requires a restart, restart the system.</span></span>

![V rámci funkcí systému Windows jsou vybrána Konzola pro správu služby IIS a webové služby.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="1f619-216">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="1f619-216">Virtual Directories</span></span>

<span data-ttu-id="1f619-217">Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) .</span><span class="sxs-lookup"><span data-stu-id="1f619-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="1f619-218">Aplikace může být hostována jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="1f619-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="1f619-219">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="1f619-219">Sub-applications</span></span>

<span data-ttu-id="1f619-220">Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="1f619-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="1f619-221">Cesta k dílčí aplikaci se stala součástí adresy URL kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="1f619-222">Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku ( `~/` ).</span><span class="sxs-lookup"><span data-stu-id="1f619-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="1f619-223">Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="1f619-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="1f619-224">V případě dílčí aplikace na `/subapp_path` je obrázek propojený s objektem `src="~/image.png"` vykreslen jako `src="/subapp_path/image.png"` .</span><span class="sxs-lookup"><span data-stu-id="1f619-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="1f619-225">Middleware statických souborů kořenové aplikace nezpracovává požadavek na statický soubor.</span><span class="sxs-lookup"><span data-stu-id="1f619-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="1f619-226">Požadavek zpracovává middleware statických souborů v dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f619-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="1f619-227">Pokud je atribut statického prostředku `src` nastavený na absolutní cestu (například `src="/image.png"` ), odkaz se vykreslí bez pathbase dílčí aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="1f619-228">Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="1f619-229">Hostování aplikace ASP.NET Core jako dílčí aplikace v jiné aplikaci ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1f619-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="1f619-230">Vytvořte fond aplikací pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f619-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="1f619-231">Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="1f619-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="1f619-232">Přidejte kořenovou lokalitu ve Správci služby IIS pomocí dílčí aplikace do složky v kořenové lokalitě.</span><span class="sxs-lookup"><span data-stu-id="1f619-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="1f619-233">Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="1f619-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="1f619-234">V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f619-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="1f619-235">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f619-235">Select **OK**.</span></span>

<span data-ttu-id="1f619-236">Přiřazení samostatného fondu aplikací k dílčí aplikaci je požadavek při použití modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="1f619-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="1f619-237">Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="1f619-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="1f619-238">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="1f619-238">Application Pools</span></span>

<span data-ttu-id="1f619-239">Izolaci fondu aplikací určuje model hostování:</span><span class="sxs-lookup"><span data-stu-id="1f619-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="1f619-240">Hostování v procesu: aplikace se vyžadují ke spuštění v samostatných fondech aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="1f619-241">Hostování mimo procesy: aplikace doporučujeme izolovat od sebe navzájem tak, že spustíte každou aplikaci ve svém vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="1f619-242">Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1f619-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="1f619-243">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="1f619-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="1f619-244">Po přidání webu se vytvoří nový fond aplikací s názvem lokality.</span><span class="sxs-lookup"><span data-stu-id="1f619-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="1f619-245">Fond aplikací Identity</span><span class="sxs-lookup"><span data-stu-id="1f619-245">Application Pool Identity</span></span>

<span data-ttu-id="1f619-246">Účet identity fondu aplikací umožňuje aplikaci běžet v rámci jedinečného účtu, aniž by bylo nutné vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="1f619-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="1f619-247">Ve službě IIS 8,0 nebo novější služba pracovní proces správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a ve výchozím nastavení spustí pracovní procesy fondu aplikací v rámci tohoto účtu.</span><span class="sxs-lookup"><span data-stu-id="1f619-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="1f619-248">V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byl **Identity** nastaven na hodnotu použít `ApplicationPoolIdentity` :</span><span class="sxs-lookup"><span data-stu-id="1f619-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Dialogové okno Upřesnit nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="1f619-250">Proces správy služby IIS vytvoří v systému zabezpečení systému Windows zabezpečený identifikátor s názvem fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="1f619-251">Prostředky je možné zabezpečit pomocí této identity.</span><span class="sxs-lookup"><span data-stu-id="1f619-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="1f619-252">Tato identita ale není skutečným uživatelským účtem a nezobrazuje se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="1f619-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="1f619-253">Pokud pracovní proces služby IIS vyžaduje zvýšený přístup k aplikaci, upravte seznam Access Control (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="1f619-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="1f619-254">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="1f619-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="1f619-255">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="1f619-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="1f619-256">Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="1f619-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="1f619-257">Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.</span><span class="sxs-lookup"><span data-stu-id="1f619-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="1f619-258">Zadejte `IIS AppPool\{APP POOL NAME}` formát, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací, v **Zadejte názvy objektů k výběru** oblasti.</span><span class="sxs-lookup"><span data-stu-id="1f619-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="1f619-259">Vyberte tlačítko pro **kontrolu názvů** .</span><span class="sxs-lookup"><span data-stu-id="1f619-259">Select the **Check Names** button.</span></span> <span data-ttu-id="1f619-260">U pole *DefaultAppPool* ověřte názvy pomocí `IIS AppPool\DefaultAppPool` .</span><span class="sxs-lookup"><span data-stu-id="1f619-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="1f619-261">Když je vybráno tlačítko pro **kontrolu názvů** , hodnota `DefaultAppPool` je uvedena v oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="1f619-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="1f619-262">Není možné zadat název fondu aplikací přímo do oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="1f619-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="1f619-263">`IIS AppPool\{APP POOL NAME}`Při kontrole názvu objektu použijte formát, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool" \" v oblasti názvy objektů před výběrem "kontrolovat jména".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="1f619-265">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f619-265">Select **OK**.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: po výběru možnosti kontrolovat názvy se v oblasti názvy objektů zobrazí název objektu DefaultAppPool.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="1f619-267">&amp;Ve výchozím nastavení by měla být udělena oprávnění ke čtení.</span><span class="sxs-lookup"><span data-stu-id="1f619-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="1f619-268">V případě potřeby zadejte další oprávnění.</span><span class="sxs-lookup"><span data-stu-id="1f619-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="1f619-269">Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** .</span><span class="sxs-lookup"><span data-stu-id="1f619-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="1f619-270">Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="1f619-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="1f619-271">Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="1f619-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="1f619-272">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="1f619-272">HTTP/2 support</span></span>

<span data-ttu-id="1f619-273">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="1f619-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="1f619-274">V procesu</span><span class="sxs-lookup"><span data-stu-id="1f619-274">In-process</span></span>
  * <span data-ttu-id="1f619-275">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1f619-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1f619-276">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1f619-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="1f619-277">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="1f619-277">Out-of-process</span></span>
  * <span data-ttu-id="1f619-278">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1f619-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="1f619-279">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1f619-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="1f619-280">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="1f619-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="1f619-281">Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="1f619-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="1f619-282">Pro nasazení mimo procesy, když je navázáno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="1f619-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="1f619-283">Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="1f619-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="1f619-284">HTTP/2 je ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="1f619-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="1f619-285">Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="1f619-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="1f619-286">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="1f619-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="1f619-287">Požadavky na kontrolu před výstupem CORS</span><span class="sxs-lookup"><span data-stu-id="1f619-287">CORS preflight requests</span></span>

<span data-ttu-id="1f619-288">*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="1f619-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="1f619-289">Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="1f619-290">Informace o tom, jak nakonfigurovat obslužné rutiny služby IIS v aplikaci `web.config` k předání požadavků na možnosti, najdete v tématu [Povolení žádostí mezi zdroji v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="1f619-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="1f619-291">Modul inicializace aplikace a časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="1f619-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="1f619-292">Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:</span><span class="sxs-lookup"><span data-stu-id="1f619-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="1f619-293">[Modul inicializace aplikace](#application-initialization-module): hostovaný [v procesu](xref:host-and-deploy/iis/in-process-hosting) nebo [mimoprocesové](xref:host-and-deploy/iis/out-of-process-hosting) aplikaci aplikace je možné nakonfigurovat tak, aby se automaticky spouštěla v pracovním procesu nebo na restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="1f619-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="1f619-294">[Časový limit nečinnosti](#idle-timeout): hostovaný [v procesu](xref:host-and-deploy/iis/in-process-hosting) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="1f619-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="1f619-295">Modul inicializace aplikace</span><span class="sxs-lookup"><span data-stu-id="1f619-295">Application Initialization Module</span></span>

<span data-ttu-id="1f619-296">*Platí pro aplikace hostované v procesu a mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="1f619-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="1f619-297">[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="1f619-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="1f619-298">Požadavek spustí spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1f619-298">The request triggers the app to start.</span></span> <span data-ttu-id="1f619-299">Služba IIS ve výchozím nastavení vydá požadavek na kořenovou adresu URL aplikace ( `/` ) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).</span><span class="sxs-lookup"><span data-stu-id="1f619-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="1f619-300">Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:</span><span class="sxs-lookup"><span data-stu-id="1f619-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="1f619-301">V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:</span><span class="sxs-lookup"><span data-stu-id="1f619-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="1f619-302">Přejděte na **Ovládací panely**  >  **programy**programy  >  **a funkce**  >  **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="1f619-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="1f619-303">Otevřete **Internetová informační služba**  >  **World Wide Web Services**  >  **funkce pro vývoj aplikací**webové služby.</span><span class="sxs-lookup"><span data-stu-id="1f619-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="1f619-304">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1f619-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="1f619-305">V systému Windows Server 2008 R2 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="1f619-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="1f619-306">Otevřete **Průvodce přidáním rolí a funkcí**.</span><span class="sxs-lookup"><span data-stu-id="1f619-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="1f619-307">Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .</span><span class="sxs-lookup"><span data-stu-id="1f619-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="1f619-308">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="1f619-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="1f619-309">Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:</span><span class="sxs-lookup"><span data-stu-id="1f619-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="1f619-310">Pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="1f619-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="1f619-311">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="1f619-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="1f619-312">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="1f619-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="1f619-313">Výchozí **režim spuštění** je `OnDemand` .</span><span class="sxs-lookup"><span data-stu-id="1f619-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="1f619-314">Nastavte **režim spuštění** na `AlwaysRunning` .</span><span class="sxs-lookup"><span data-stu-id="1f619-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="1f619-315">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f619-315">Select **OK**.</span></span>
  1. <span data-ttu-id="1f619-316">Otevřete uzel **lokality** na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="1f619-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="1f619-317">Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat**  >  **Rozšířená nastavení**webu.</span><span class="sxs-lookup"><span data-stu-id="1f619-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="1f619-318">Výchozí nastavení pro **Povolení přednačtení** je `False` .</span><span class="sxs-lookup"><span data-stu-id="1f619-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="1f619-319">Nastavte **přednačtení povoleno** na `True` .</span><span class="sxs-lookup"><span data-stu-id="1f619-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="1f619-320">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f619-320">Select **OK**.</span></span>

* <span data-ttu-id="1f619-321">Pomocí `web.config` přidejte `<applicationInitialization>` element s nastavením do `doAppInitAfterRestart` `true` `<system.webServer>` prvků v `web.config` souboru aplikace:</span><span class="sxs-lookup"><span data-stu-id="1f619-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="1f619-322">Časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="1f619-322">Idle Timeout</span></span>

<span data-ttu-id="1f619-323">*Platí jenom pro aplikace hostované v procesu.*</span><span class="sxs-lookup"><span data-stu-id="1f619-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="1f619-324">Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="1f619-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="1f619-325">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="1f619-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="1f619-326">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="1f619-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="1f619-327">Výchozí **časový limit nečinnosti (minuty)** je `20` minuty.</span><span class="sxs-lookup"><span data-stu-id="1f619-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="1f619-328">Nastavte **časový limit nečinnosti (v minutách)** na `0` (nula).</span><span class="sxs-lookup"><span data-stu-id="1f619-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="1f619-329">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="1f619-329">Select **OK**.</span></span>
1. <span data-ttu-id="1f619-330">Recyklujte pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="1f619-330">Recycle the worker process.</span></span>

<span data-ttu-id="1f619-331">Chcete-li zabránit aplikacím hostovaným v [procesu](xref:host-and-deploy/iis/out-of-process-hosting) vypršení časového limitu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1f619-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="1f619-332">Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.</span><span class="sxs-lookup"><span data-stu-id="1f619-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="1f619-333">Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="1f619-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="1f619-334">Modul inicializace aplikace a další prostředky časového limitu nečinnosti</span><span class="sxs-lookup"><span data-stu-id="1f619-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="1f619-335">Inicializace aplikace IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="1f619-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="1f619-336">[Inicializace `<applicationInitialization>` aplikace ](/iis/configuration/system.webserver/applicationinitialization/)</span><span class="sxs-lookup"><span data-stu-id="1f619-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="1f619-337">[Nastavení modelu procesu pro `<processModel>` fond aplikací ](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="1f619-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="1f619-338">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="1f619-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="1f619-339">Modul</span><span class="sxs-lookup"><span data-stu-id="1f619-339">Module</span></span>

<span data-ttu-id="1f619-340">**Služba IIS (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="1f619-340">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="1f619-341">**IIS Express (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="1f619-341">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="1f619-342">Schéma</span><span class="sxs-lookup"><span data-stu-id="1f619-342">Schema</span></span>

<span data-ttu-id="1f619-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1f619-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="1f619-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1f619-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="1f619-345">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1f619-345">Configuration</span></span>

<span data-ttu-id="1f619-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="1f619-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="1f619-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="1f619-347">**IIS Express**</span></span>

* <span data-ttu-id="1f619-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="1f619-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="1f619-349">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="1f619-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="1f619-350">Soubory lze najít hledáním `aspnetcore` v `applicationHost.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="1f619-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
