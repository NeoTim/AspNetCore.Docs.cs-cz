---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 5723295c70f8d893f758ca5dc87180c6b707f493
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994180"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="73474-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73474-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="73474-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="73474-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="73474-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="73474-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="73474-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="73474-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="73474-107">Azure Key Vault</span></span>
* <span data-ttu-id="73474-108">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="73474-108">Azure App Configuration</span></span>
* <span data-ttu-id="73474-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-109">Command-line arguments</span></span>
* <span data-ttu-id="73474-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="73474-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="73474-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="73474-111">Directory files</span></span>
* <span data-ttu-id="73474-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-112">Environment variables</span></span>
* <span data-ttu-id="73474-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="73474-113">In-memory .NET objects</span></span>
* <span data-ttu-id="73474-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="73474-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73474-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="73474-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73474-116">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="73474-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="73474-117">Příklady kódu, které následují a v ukázkové aplikaci používají <xref:Microsoft.Extensions.Configuration> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="73474-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="73474-118">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="73474-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="73474-119">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="73474-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="73474-120">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="73474-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="73474-121">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="73474-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="73474-122">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="73474-122">Host versus app configuration</span></span>

<span data-ttu-id="73474-123">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="73474-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="73474-124">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="73474-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="73474-125">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="73474-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="73474-126">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="73474-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="73474-127">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele <xref:fundamentals/index#host>, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="73474-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="73474-128">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="73474-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73474-129">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="73474-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="73474-130">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="73474-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="73474-131">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="73474-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="73474-132">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="73474-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="73474-133">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="73474-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="73474-134">Proměnné prostředí s `DOTNET_` předponou ( `DOTNET_ENVIRONMENT`například) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="73474-135">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="73474-136">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="73474-137">Je navázána výchozí konfigurace webového`ConfigureWebHostDefaults`hostitele ():</span><span class="sxs-lookup"><span data-stu-id="73474-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="73474-138">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="73474-139">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="73474-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="73474-140">Přidejte middleware předávaných hlaviček `ASPNETCORE_FORWARDEDHEADERS_ENABLED` , pokud je proměnná prostředí `true`nastavena na.</span><span class="sxs-lookup"><span data-stu-id="73474-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="73474-141">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="73474-141">Enable IIS integration.</span></span>
* <span data-ttu-id="73474-142">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="73474-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="73474-143">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73474-144">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73474-145">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="73474-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="73474-146">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="73474-147">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73474-148">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="73474-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="73474-149">`CreateDefaultBuilder`poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="73474-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="73474-150">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="73474-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="73474-151">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="73474-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="73474-152">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="73474-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="73474-153">Proměnné prostředí s `ASPNETCORE_` předponou ( `ASPNETCORE_ENVIRONMENT`například) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="73474-154">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="73474-155">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="73474-156">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="73474-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="73474-157">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73474-158">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="73474-159">[Správce tajných klíčů](xref:security/app-secrets) , když aplikace běží `Development` v prostředí pomocí položky sestavení</span><span class="sxs-lookup"><span data-stu-id="73474-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="73474-160">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="73474-161">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="73474-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="73474-162">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="73474-162">Security</span></span>

<span data-ttu-id="73474-163">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="73474-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="73474-164">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="73474-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="73474-165">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="73474-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="73474-166">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="73474-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="73474-167">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="73474-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="73474-168"><xref:security/app-secrets>&ndash; Obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="73474-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="73474-169">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="73474-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="73474-170">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="73474-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="73474-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="73474-172">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73474-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="73474-173">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="73474-173">Hierarchical configuration data</span></span>

<span data-ttu-id="73474-174">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="73474-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="73474-175">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="73474-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="73474-176">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="73474-177">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) pro zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="73474-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="73474-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73474-178">section0:key0</span></span>
* <span data-ttu-id="73474-179">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-179">section0:key1</span></span>
* <span data-ttu-id="73474-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73474-180">section1:key0</span></span>
* <span data-ttu-id="73474-181">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-181">section1:key1</span></span>

<span data-ttu-id="73474-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*>metody <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> a jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="73474-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="73474-183">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="73474-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="73474-184">Konvence</span><span class="sxs-lookup"><span data-stu-id="73474-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="73474-185">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="73474-185">Sources and providers</span></span>

<span data-ttu-id="73474-186">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="73474-187">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="73474-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="73474-188">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="73474-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="73474-189"><xref:Microsoft.Extensions.Configuration.IConfiguration>je k dispozici v kontejneru [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="73474-190"><xref:Microsoft.Extensions.Configuration.IConfiguration>lze vložit do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> a získat tak konfiguraci pro třídu:</span><span class="sxs-lookup"><span data-stu-id="73474-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="73474-191">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="73474-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="73474-192">Klíče</span><span class="sxs-lookup"><span data-stu-id="73474-192">Keys</span></span>

<span data-ttu-id="73474-193">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="73474-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="73474-194">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="73474-194">Keys are case-insensitive.</span></span> <span data-ttu-id="73474-195">Například `ConnectionString` a`connectionstring` jsou považovány za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="73474-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="73474-196">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="73474-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="73474-197">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="73474-197">Hierarchical keys</span></span>
  * <span data-ttu-id="73474-198">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky`:`() na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="73474-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="73474-199">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="73474-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="73474-200">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="73474-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="73474-201">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="73474-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="73474-202">Pokud jsou tajné klíče načteny do konfigurace aplikace, je nutné zadat kód, který nahradí pomlčky dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="73474-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="73474-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="73474-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73474-204">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="73474-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="73474-205">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="73474-205">Values</span></span>

<span data-ttu-id="73474-206">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="73474-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="73474-207">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="73474-207">Values are strings.</span></span>
* <span data-ttu-id="73474-208">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="73474-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="73474-209">Dodavateli</span><span class="sxs-lookup"><span data-stu-id="73474-209">Providers</span></span>

<span data-ttu-id="73474-210">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="73474-211">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="73474-211">Provider</span></span> | <span data-ttu-id="73474-212">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="73474-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="73474-213">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (Témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="73474-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="73474-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="73474-214">Azure Key Vault</span></span> |
| <span data-ttu-id="73474-215">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Dokumentace k Azure)</span><span class="sxs-lookup"><span data-stu-id="73474-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="73474-216">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="73474-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="73474-217">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="73474-218">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-218">Command-line parameters</span></span> |
| [<span data-ttu-id="73474-219">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="73474-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="73474-220">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="73474-220">Custom source</span></span> |
| [<span data-ttu-id="73474-221">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="73474-222">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-222">Environment variables</span></span> |
| [<span data-ttu-id="73474-223">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="73474-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="73474-224">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="73474-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="73474-225">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="73474-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="73474-226">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="73474-226">Directory files</span></span> |
| [<span data-ttu-id="73474-227">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="73474-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="73474-228">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="73474-228">In-memory collections</span></span> |
| <span data-ttu-id="73474-229">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (Témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="73474-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="73474-230">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="73474-230">File in the user profile directory</span></span> |

<span data-ttu-id="73474-231">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="73474-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="73474-232">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, ve kterém je váš kód může uspořádat.</span><span class="sxs-lookup"><span data-stu-id="73474-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="73474-233">Seřazení zprostředkovatelů konfigurace v kódu tak, aby vyhovoval vašim prioritám pro základní zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="73474-234">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="73474-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="73474-235">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace</span><span class="sxs-lookup"><span data-stu-id="73474-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="73474-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="73474-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="73474-237">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (Jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="73474-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="73474-238">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-238">Environment variables</span></span>
1. <span data-ttu-id="73474-239">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-239">Command-line arguments</span></span>

<span data-ttu-id="73474-240">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="73474-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="73474-241">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele v `CreateDefaultBuilder`nástroji.</span><span class="sxs-lookup"><span data-stu-id="73474-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73474-242">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="73474-243">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="73474-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="73474-244">Chcete-li nakonfigurovat tvůrce hostitele, <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> zavolejte a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="73474-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="73474-245">`ConfigureHostConfiguration`slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="73474-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="73474-246">`ConfigureHostConfiguration`dá se volat víckrát s výsledky doplňkových výsledků.</span><span class="sxs-lookup"><span data-stu-id="73474-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration((hostingContext, config) =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="73474-247">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="73474-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="73474-248">Chcete-li nakonfigurovat tvůrce hostitele, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> zavolejte na tvůrce hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="73474-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="73474-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="73474-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="73474-250">Volání `ConfigureAppConfiguration` při sestavování hostitele k určení poskytovatelů konfigurace aplikace společně s `CreateDefaultBuilder`automaticky přidanými uživateli:</span><span class="sxs-lookup"><span data-stu-id="73474-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="73474-251">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="73474-252">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku `AddCommandLine` , zavolejte jako poslední:</span><span class="sxs-lookup"><span data-stu-id="73474-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="73474-253">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="73474-253">Consume configuration during app startup</span></span>

<span data-ttu-id="73474-254">Konfigurace dodaná do aplikace v `ConfigureAppConfiguration` nástroji je k dispozici během spouštění aplikace, `Startup.ConfigureServices`včetně.</span><span class="sxs-lookup"><span data-stu-id="73474-254">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="73474-255">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="73474-255">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="73474-256">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="73474-256">Command-line Configuration Provider</span></span>

<span data-ttu-id="73474-257"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> Načte konfiguraci z párů klíč-hodnota argumentu v příkazovém řádku za běhu.</span><span class="sxs-lookup"><span data-stu-id="73474-257">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="73474-258">Chcete-li aktivovat konfiguraci příkazového řádku <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> , je metoda rozšíření volána na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-258">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73474-259">`AddCommandLine`je automaticky volána, `CreateDefaultBuilder(string [])` když je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="73474-259">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="73474-260">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-260">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73474-261">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="73474-261">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73474-262">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="73474-262">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="73474-263">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-263">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73474-264">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-264">Environment variables.</span></span>

<span data-ttu-id="73474-265">`CreateDefaultBuilder`přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="73474-265">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="73474-266">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="73474-266">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="73474-267">`CreateDefaultBuilder`funguje, když je hostitel vytvořen.</span><span class="sxs-lookup"><span data-stu-id="73474-267">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="73474-268">Proto může konfigurace z příkazového řádku aktivované `CreateDefaultBuilder` nástrojem ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="73474-268">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="73474-269">Pro aplikace založené na šablonách `AddCommandLine` ASP.NET Core již byly volány pomocí. `CreateDefaultBuilder`</span><span class="sxs-lookup"><span data-stu-id="73474-269">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73474-270">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto poskytovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` na poslední.</span><span class="sxs-lookup"><span data-stu-id="73474-270">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="73474-271">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="73474-271">**Example**</span></span>

<span data-ttu-id="73474-272">Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>volání.</span><span class="sxs-lookup"><span data-stu-id="73474-272">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="73474-273">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="73474-273">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="73474-274">Zadejte do `dotnet run` příkazu argument příkazového řádku, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="73474-274">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="73474-275">Po spuštění aplikace otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="73474-275">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73474-276">Všimněte si, že výstup obsahuje pár klíč-hodnota pro argument konfiguračního řádku konfigurace, který je k `dotnet run`dispozici pro příkaz.</span><span class="sxs-lookup"><span data-stu-id="73474-276">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="73474-277">Arguments</span><span class="sxs-lookup"><span data-stu-id="73474-277">Arguments</span></span>

<span data-ttu-id="73474-278">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="73474-278">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="73474-279">Hodnota není povinná, `CommandLineKey=`Pokud se používá znaménko rovná se (například).</span><span class="sxs-lookup"><span data-stu-id="73474-279">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="73474-280">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="73474-280">Key prefix</span></span>               | <span data-ttu-id="73474-281">Příklad</span><span class="sxs-lookup"><span data-stu-id="73474-281">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="73474-282">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="73474-282">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="73474-283">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="73474-283">Two dashes (`--`)</span></span>        | <span data-ttu-id="73474-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="73474-284">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="73474-285">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="73474-285">Forward slash (`/`)</span></span>      | <span data-ttu-id="73474-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="73474-286">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="73474-287">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="73474-287">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="73474-288">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="73474-288">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="73474-289">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="73474-289">Switch mappings</span></span>

<span data-ttu-id="73474-290">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="73474-290">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="73474-291">Při ručním sestavení konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>nástroje můžete zadat slovník přepínačů <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> pro vložení do metody.</span><span class="sxs-lookup"><span data-stu-id="73474-291">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="73474-292">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="73474-292">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="73474-293">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="73474-293">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="73474-294">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jednou pomlčkou (`-`).</span><span class="sxs-lookup"><span data-stu-id="73474-294">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="73474-295">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="73474-295">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="73474-296">Přepínače musí začínat spojovníkem`-`() nebo dvojitou čárkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="73474-296">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="73474-297">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="73474-297">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="73474-298">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="73474-298">Create a switch mappings dictionary.</span></span> <span data-ttu-id="73474-299">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="73474-299">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="73474-300">Po sestavení hostitele zavolejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="73474-300">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="73474-301">Pro aplikace, které používají mapování přepínačů, by `CreateDefaultBuilder` volání nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="73474-301">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="73474-302">Volání metody nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předat slovníku mapování přepínačů na `CreateDefaultBuilder`. `CreateDefaultBuilder` `AddCommandLine`</span><span class="sxs-lookup"><span data-stu-id="73474-302">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73474-303">Řešení nebude předávat argumenty `CreateDefaultBuilder` , ale místo toho `ConfigurationBuilder` , aby `AddCommandLine` metoda metody mohla zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="73474-303">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="73474-304">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="73474-304">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="73474-305">Key</span><span class="sxs-lookup"><span data-stu-id="73474-305">Key</span></span>       | <span data-ttu-id="73474-306">Value</span><span class="sxs-lookup"><span data-stu-id="73474-306">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="73474-307">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="73474-307">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="73474-308">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="73474-308">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="73474-309">Key</span><span class="sxs-lookup"><span data-stu-id="73474-309">Key</span></span>               | <span data-ttu-id="73474-310">Value</span><span class="sxs-lookup"><span data-stu-id="73474-310">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="73474-311">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-311">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="73474-312"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> Načítá konfiguraci z proměnných prostředí. páry klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="73474-312">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="73474-313">Chcete-li aktivovat konfiguraci proměnných prostředí, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-313">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="73474-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-314">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="73474-315">Další informace najdete v tématu [aplikace Azure: Přepište konfiguraci aplikace pomocí webu Azure](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)Portal.</span><span class="sxs-lookup"><span data-stu-id="73474-315">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73474-316">`AddEnvironmentVariables`slouží k načtení proměnných prostředí `DOTNET_` s předponou pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem s obecným [hostitelem](xref:fundamentals/host/generic-host) a `CreateDefaultBuilder` který je volán.</span><span class="sxs-lookup"><span data-stu-id="73474-316">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="73474-317">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-317">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73474-318">`AddEnvironmentVariables`slouží k načtení proměnných prostředí `ASPNETCORE_` s předponou pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele, který je hostitelem [webového hostitele](xref:fundamentals/host/web-host) a `CreateDefaultBuilder` je zavolán.</span><span class="sxs-lookup"><span data-stu-id="73474-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="73474-319">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="73474-320">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="73474-320">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73474-321">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="73474-321">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="73474-322">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="73474-322">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="73474-323">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-323">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73474-324">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="73474-324">Command-line arguments.</span></span>

<span data-ttu-id="73474-325">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="73474-325">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="73474-326">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="73474-326">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="73474-327">Pokud potřebujete zadat konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` předponu.</span><span class="sxs-lookup"><span data-stu-id="73474-327">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="73474-328">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="73474-328">**Example**</span></span>

<span data-ttu-id="73474-329">Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje `AddEnvironmentVariables`volání.</span><span class="sxs-lookup"><span data-stu-id="73474-329">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="73474-330">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-330">Run the sample app.</span></span> <span data-ttu-id="73474-331">Otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="73474-331">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73474-332">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou `ENVIRONMENT`prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-332">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="73474-333">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při spuštění místně.</span><span class="sxs-lookup"><span data-stu-id="73474-333">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="73474-334">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-334">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="73474-335">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-335">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="73474-336">Pokud chcete zpřístupnit všechny proměnné prostředí, které jsou k dispozici pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* následující:</span><span class="sxs-lookup"><span data-stu-id="73474-336">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="73474-337">Předpony</span><span class="sxs-lookup"><span data-stu-id="73474-337">Prefixes</span></span>

<span data-ttu-id="73474-338">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony `AddEnvironmentVariables` metody.</span><span class="sxs-lookup"><span data-stu-id="73474-338">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="73474-339">Chcete-li například filtrovat proměnné prostředí v předponě `CUSTOM_`, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-339">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="73474-340">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="73474-340">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="73474-341">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-341">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="73474-342">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-342">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73474-343">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="73474-343">**Connection string prefixes**</span></span>

<span data-ttu-id="73474-344">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-344">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="73474-345">Proměnné prostředí s předponami zobrazenými v tabulce jsou načteny do aplikace, pokud není zadána `AddEnvironmentVariables`žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="73474-345">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="73474-346">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="73474-346">Connection string prefix</span></span> | <span data-ttu-id="73474-347">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="73474-347">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="73474-348">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="73474-348">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="73474-349">MySQL</span><span class="sxs-lookup"><span data-stu-id="73474-349">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="73474-350">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="73474-350">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="73474-351">SQL Server</span><span class="sxs-lookup"><span data-stu-id="73474-351">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="73474-352">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="73474-352">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="73474-353">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="73474-353">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="73474-354">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="73474-354">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="73474-355">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="73474-355">Environment variable key</span></span> | <span data-ttu-id="73474-356">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="73474-356">Converted configuration key</span></span> | <span data-ttu-id="73474-357">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="73474-357">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="73474-358">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="73474-358">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="73474-359">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73474-359">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="73474-360">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="73474-360">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="73474-361">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73474-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="73474-362">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73474-362">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="73474-363">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="73474-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="73474-364">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="73474-364">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="73474-365">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="73474-365">File Configuration Provider</span></span>

<span data-ttu-id="73474-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>je základní třídou pro načtení konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="73474-366"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="73474-367">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="73474-367">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="73474-368">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="73474-368">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="73474-369">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="73474-369">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="73474-370">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="73474-370">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="73474-371">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="73474-371">INI Configuration Provider</span></span>

<span data-ttu-id="73474-372"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="73474-372">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="73474-373">Chcete-li aktivovat konfiguraci souboru INI, <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-373">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73474-374">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="73474-374">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="73474-375">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="73474-375">Overloads permit specifying:</span></span>

* <span data-ttu-id="73474-376">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="73474-376">Whether the file is optional.</span></span>
* <span data-ttu-id="73474-377">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-377">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73474-378"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-378">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73474-379">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="73474-379">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73474-380">Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="73474-380">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="73474-381">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="73474-381">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="73474-382">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="73474-382">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73474-383">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73474-383">section0:key0</span></span>
* <span data-ttu-id="73474-384">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-384">section0:key1</span></span>
* <span data-ttu-id="73474-385">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="73474-385">section1:subsection:key</span></span>
* <span data-ttu-id="73474-386">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="73474-386">section2:subsection0:key</span></span>
* <span data-ttu-id="73474-387">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="73474-387">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="73474-388">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="73474-388">JSON Configuration Provider</span></span>

<span data-ttu-id="73474-389"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="73474-389">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="73474-390">Chcete-li aktivovat konfiguraci souboru JSON, <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-390">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73474-391">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="73474-391">Overloads permit specifying:</span></span>

* <span data-ttu-id="73474-392">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="73474-392">Whether the file is optional.</span></span>
* <span data-ttu-id="73474-393">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-393">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73474-394"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-394">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73474-395">`AddJsonFile`je automaticky volána dvakrát při inicializaci nového tvůrce hostitele pomocí `CreateDefaultBuilder`nástroje.</span><span class="sxs-lookup"><span data-stu-id="73474-395">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="73474-396">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="73474-396">The method is called to load configuration from:</span></span>

* <span data-ttu-id="73474-397">*appSettings. JSON* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="73474-397">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="73474-398">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="73474-398">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="73474-399">*appSettings. {Environment}. JSON* &ndash; verze souboru je načtená na základě [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="73474-399">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="73474-400">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-400">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="73474-401">`CreateDefaultBuilder`také načte:</span><span class="sxs-lookup"><span data-stu-id="73474-401">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="73474-402">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-402">Environment variables.</span></span>
* <span data-ttu-id="73474-403">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-403">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="73474-404">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="73474-404">Command-line arguments.</span></span>

<span data-ttu-id="73474-405">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="73474-405">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="73474-406">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="73474-406">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="73474-407">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="73474-407">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73474-408">Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="73474-408">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="73474-409">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="73474-409">**Example**</span></span>

<span data-ttu-id="73474-410">Ukázková aplikace využívá metodu `CreateDefaultBuilder` statického usnadnění k sestavení hostitele, který obsahuje dvě `AddJsonFile`volání.</span><span class="sxs-lookup"><span data-stu-id="73474-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="73474-411">Konfigurace je načtena z souboru *appSettings. JSON* a *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="73474-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="73474-412">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-412">Run the sample app.</span></span> <span data-ttu-id="73474-413">Otevřete v `http://localhost:5000`aplikaci prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="73474-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="73474-414">Všimněte si, že výstup obsahuje páry klíč-hodnota pro konfiguraci uvedenou v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="73474-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="73474-415">Klíče konfigurace protokolování používají jako hierarchický oddělovač`:`dvojtečku ().</span><span class="sxs-lookup"><span data-stu-id="73474-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="73474-416">Key</span><span class="sxs-lookup"><span data-stu-id="73474-416">Key</span></span>                        | <span data-ttu-id="73474-417">Hodnota vývoje</span><span class="sxs-lookup"><span data-stu-id="73474-417">Development Value</span></span> | <span data-ttu-id="73474-418">Produkční hodnota</span><span class="sxs-lookup"><span data-stu-id="73474-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="73474-419">Protokolování: LogLevel: systém</span><span class="sxs-lookup"><span data-stu-id="73474-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="73474-420">Informace o</span><span class="sxs-lookup"><span data-stu-id="73474-420">Information</span></span>       | <span data-ttu-id="73474-421">Informace o</span><span class="sxs-lookup"><span data-stu-id="73474-421">Information</span></span>      |
| <span data-ttu-id="73474-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="73474-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="73474-423">Informace o</span><span class="sxs-lookup"><span data-stu-id="73474-423">Information</span></span>       | <span data-ttu-id="73474-424">Informace o</span><span class="sxs-lookup"><span data-stu-id="73474-424">Information</span></span>      |
| <span data-ttu-id="73474-425">Protokolování: LogLevel: výchozí</span><span class="sxs-lookup"><span data-stu-id="73474-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="73474-426">Ladění</span><span class="sxs-lookup"><span data-stu-id="73474-426">Debug</span></span>             | <span data-ttu-id="73474-427">Chyba</span><span class="sxs-lookup"><span data-stu-id="73474-427">Error</span></span>            |
| <span data-ttu-id="73474-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="73474-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="73474-429">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="73474-429">XML Configuration Provider</span></span>

<span data-ttu-id="73474-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> Načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="73474-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="73474-431">Chcete-li aktivovat konfiguraci souboru XML, <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> zavolejte metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73474-432">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="73474-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="73474-433">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="73474-433">Whether the file is optional.</span></span>
* <span data-ttu-id="73474-434">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="73474-435"><xref:Microsoft.Extensions.FileProviders.IFileProvider> Slouží k přístupu k souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="73474-436">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="73474-437">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="73474-438">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="73474-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="73474-439">Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="73474-439">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

<span data-ttu-id="73474-440">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="73474-440">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="73474-441">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="73474-441">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73474-442">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73474-442">section0:key0</span></span>
* <span data-ttu-id="73474-443">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-443">section0:key1</span></span>
* <span data-ttu-id="73474-444">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73474-444">section1:key0</span></span>
* <span data-ttu-id="73474-445">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-445">section1:key1</span></span>

<span data-ttu-id="73474-446">Opakující se prvky, které používají stejný název elementu fungují, `name` Pokud je atribut použit k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="73474-446">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="73474-447">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="73474-447">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73474-448">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="73474-448">section:section0:key:key0</span></span>
* <span data-ttu-id="73474-449">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="73474-449">section:section0:key:key1</span></span>
* <span data-ttu-id="73474-450">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="73474-450">section:section1:key:key0</span></span>
* <span data-ttu-id="73474-451">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-451">section:section1:key:key1</span></span>

<span data-ttu-id="73474-452">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="73474-452">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="73474-453">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="73474-453">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="73474-454">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="73474-454">key:attribute</span></span>
* <span data-ttu-id="73474-455">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="73474-455">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="73474-456">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="73474-456">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="73474-457"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> Používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="73474-457">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="73474-458">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-458">The key is the file name.</span></span> <span data-ttu-id="73474-459">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="73474-459">The value contains the file's contents.</span></span> <span data-ttu-id="73474-460">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="73474-460">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="73474-461">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-461">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="73474-462">`directoryPath` Do souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="73474-462">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="73474-463">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="73474-463">Overloads permit specifying:</span></span>

* <span data-ttu-id="73474-464">`Action<KeyPerFileConfigurationSource>` Delegát, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="73474-464">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="73474-465">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="73474-465">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="73474-466">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="73474-466">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="73474-467">Například název `Logging__LogLevel__System` souboru vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="73474-467">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="73474-468">Zavolat `ConfigureAppConfiguration` při sestavování hostitele k určení konfigurace aplikace:</span><span class="sxs-lookup"><span data-stu-id="73474-468">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.SetBasePath(Directory.GetCurrentDirectory());
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<span data-ttu-id="73474-469">Základní cesta je nastavena s použitím <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span><span class="sxs-lookup"><span data-stu-id="73474-469">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span>

## <a name="memory-configuration-provider"></a><span data-ttu-id="73474-470">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="73474-470">Memory Configuration Provider</span></span>

<span data-ttu-id="73474-471"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> Používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="73474-471">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="73474-472">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> metodu rozšíření na <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>instanci.</span><span class="sxs-lookup"><span data-stu-id="73474-472">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="73474-473">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="73474-473">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="73474-474">Zavolejte `ConfigureAppConfiguration` při sestavování hostitele, aby se určila konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-474">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="73474-475">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="73474-475">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="73474-476">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-476">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="73474-477">GetValue</span><span class="sxs-lookup"><span data-stu-id="73474-477">GetValue</span></span>

<span data-ttu-id="73474-478">[ConfigurationBinder. GetValue\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="73474-478">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="73474-479">Přetížení umožňuje zadat výchozí hodnotu, pokud se klíč nenajde.</span><span class="sxs-lookup"><span data-stu-id="73474-479">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="73474-480">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="73474-480">The following example:</span></span>

* <span data-ttu-id="73474-481">Extrahuje řetězcovou hodnotu z konfigurace s klíčem `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="73474-481">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="73474-482">Pokud `NumberKey` se v konfiguračních klíčích nenajde, použije se výchozí `99` hodnota.</span><span class="sxs-lookup"><span data-stu-id="73474-482">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="73474-483">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="73474-483">Types the value as an `int`.</span></span>
* <span data-ttu-id="73474-484">Ukládá hodnotu `NumberConfig` vlastnosti pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="73474-484">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="73474-485">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="73474-485">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="73474-486">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="73474-486">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="73474-487">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="73474-487">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="73474-488">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-488">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="73474-489">section0:key0</span><span class="sxs-lookup"><span data-stu-id="73474-489">section0:key0</span></span>
* <span data-ttu-id="73474-490">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-490">section0:key1</span></span>
* <span data-ttu-id="73474-491">section1:key0</span><span class="sxs-lookup"><span data-stu-id="73474-491">section1:key0</span></span>
* <span data-ttu-id="73474-492">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-492">section1:key1</span></span>
* <span data-ttu-id="73474-493">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="73474-493">section2:subsection0:key0</span></span>
* <span data-ttu-id="73474-494">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-494">section2:subsection0:key1</span></span>
* <span data-ttu-id="73474-495">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="73474-495">section2:subsection1:key0</span></span>
* <span data-ttu-id="73474-496">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="73474-496">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="73474-497">GetSection</span><span class="sxs-lookup"><span data-stu-id="73474-497">GetSection</span></span>

<span data-ttu-id="73474-498">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="73474-498">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="73474-499">Chcete-li <xref:Microsoft.Extensions.Configuration.IConfigurationSection> vrátit obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="73474-499">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="73474-500">Hodnota `configSection` neobsahuje pouze klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="73474-500">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="73474-501">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="73474-501">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="73474-502">`GetSection`nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="73474-502">`GetSection` never returns `null`.</span></span> <span data-ttu-id="73474-503">Pokud se nenalezne shodný oddíl, vrátí `IConfigurationSection` se prázdný řetězec.</span><span class="sxs-lookup"><span data-stu-id="73474-503">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="73474-504">Při `GetSection` vrácení odpovídajícího <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> oddílu není naplněna hodnota.</span><span class="sxs-lookup"><span data-stu-id="73474-504">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="73474-505"><xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> A<xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> se vrátí, když oddíl existuje.</span><span class="sxs-lookup"><span data-stu-id="73474-505">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="73474-506">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="73474-506">GetChildren</span></span>

<span data-ttu-id="73474-507">Volání [IConfiguration.](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) getchildrens pro `section2` získá výjimku `IEnumerable<IConfigurationSection>` , která zahrnuje:</span><span class="sxs-lookup"><span data-stu-id="73474-507">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="73474-508">Existuje</span><span class="sxs-lookup"><span data-stu-id="73474-508">Exists</span></span>

<span data-ttu-id="73474-509">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="73474-509">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="73474-510">Vzhledem k ukázkovým datům `sectionExists` je `false` to `section2:subsection2` proto, že v konfiguračních datech není oddíl.</span><span class="sxs-lookup"><span data-stu-id="73474-510">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="73474-511">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="73474-511">Bind to a class</span></span>

<span data-ttu-id="73474-512">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="73474-512">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="73474-513">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="73474-513">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="73474-514">Konfigurační hodnoty jsou vraceny jako řetězce, ale <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> volání umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="73474-514">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="73474-515">Ukázková aplikace obsahuje `Starship` model (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="73474-515">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="73474-516">V `starship` části souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace k načtení konfigurace používá poskytovatele konfigurace JSON:</span><span class="sxs-lookup"><span data-stu-id="73474-516">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="73474-517">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-517">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="73474-518">Key</span><span class="sxs-lookup"><span data-stu-id="73474-518">Key</span></span>                   | <span data-ttu-id="73474-519">Value</span><span class="sxs-lookup"><span data-stu-id="73474-519">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="73474-520">Starship: název</span><span class="sxs-lookup"><span data-stu-id="73474-520">starship:name</span></span>         | <span data-ttu-id="73474-521">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="73474-521">USS Enterprise</span></span>                                    |
| <span data-ttu-id="73474-522">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="73474-522">starship:registry</span></span>     | <span data-ttu-id="73474-523">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="73474-523">NCC-1701</span></span>                                          |
| <span data-ttu-id="73474-524">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="73474-524">starship:class</span></span>        | <span data-ttu-id="73474-525">Založen</span><span class="sxs-lookup"><span data-stu-id="73474-525">Constitution</span></span>                                      |
| <span data-ttu-id="73474-526">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="73474-526">starship:length</span></span>       | <span data-ttu-id="73474-527">304,8</span><span class="sxs-lookup"><span data-stu-id="73474-527">304.8</span></span>                                             |
| <span data-ttu-id="73474-528">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="73474-528">starship:commissioned</span></span> | <span data-ttu-id="73474-529">False</span><span class="sxs-lookup"><span data-stu-id="73474-529">False</span></span>                                             |
| <span data-ttu-id="73474-530">Patka</span><span class="sxs-lookup"><span data-stu-id="73474-530">trademark</span></span>             | <span data-ttu-id="73474-531">Nejdůležitější obrázky Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="73474-531">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="73474-532">Ukázková aplikace volá `GetSection` `starship` klíč.</span><span class="sxs-lookup"><span data-stu-id="73474-532">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="73474-533">Páry `starship` klíč-hodnota jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="73474-533">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="73474-534">Metoda je volána pro dílčí oddíl procházející v instanci `Starship` třídy. `Bind`</span><span class="sxs-lookup"><span data-stu-id="73474-534">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="73474-535">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="73474-535">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="73474-536">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="73474-536">Bind to an object graph</span></span>

<span data-ttu-id="73474-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="73474-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="73474-538">Ukázka obsahuje `TvShow` model, jehož objekt Graph zahrnuje `Metadata` a `Actors` třídy (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="73474-538">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="73474-539">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="73474-539">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="73474-540">Konfigurace je svázána `TvShow` `Bind` s celým grafem objektů pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="73474-540">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="73474-541">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="73474-541">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="73474-542">[ConfigurationBinder. Get\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="73474-542">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="73474-543">`Get<T>`je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="73474-543">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="73474-544">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="73474-544">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="73474-545">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="73474-545">Bind an array to a class</span></span>

<span data-ttu-id="73474-546">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="73474-546">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="73474-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> Podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="73474-547">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="73474-548">Libovolný formát pole, který zveřejňuje numerický klíčový segment`:0:`( `:1:`, &hellip; , `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="73474-548">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="73474-549">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="73474-549">Binding is provided by convention.</span></span> <span data-ttu-id="73474-550">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="73474-550">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="73474-551">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="73474-551">**In-memory array processing**</span></span>

<span data-ttu-id="73474-552">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="73474-552">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="73474-553">Key</span><span class="sxs-lookup"><span data-stu-id="73474-553">Key</span></span>             | <span data-ttu-id="73474-554">Value</span><span class="sxs-lookup"><span data-stu-id="73474-554">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="73474-555">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="73474-555">array:entries:0</span></span> | <span data-ttu-id="73474-556">value0</span><span class="sxs-lookup"><span data-stu-id="73474-556">value0</span></span> |
| <span data-ttu-id="73474-557">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="73474-557">array:entries:1</span></span> | <span data-ttu-id="73474-558">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="73474-558">value1</span></span> |
| <span data-ttu-id="73474-559">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="73474-559">array:entries:2</span></span> | <span data-ttu-id="73474-560">Argument</span><span class="sxs-lookup"><span data-stu-id="73474-560">value2</span></span> |
| <span data-ttu-id="73474-561">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="73474-561">array:entries:4</span></span> | <span data-ttu-id="73474-562">value4</span><span class="sxs-lookup"><span data-stu-id="73474-562">value4</span></span> |
| <span data-ttu-id="73474-563">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="73474-563">array:entries:5</span></span> | <span data-ttu-id="73474-564">value5</span><span class="sxs-lookup"><span data-stu-id="73474-564">value5</span></span> |

<span data-ttu-id="73474-565">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="73474-565">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,23)]

::: moniker-end

<span data-ttu-id="73474-566">Pole přeskočí hodnotu indexu &num;3.</span><span class="sxs-lookup"><span data-stu-id="73474-566">The array skips a value for index &num;3.</span></span> <span data-ttu-id="73474-567">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="73474-567">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="73474-568">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="73474-568">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="73474-569">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="73474-569">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="73474-570">Lze také použít syntaxi [ConfigurationBinder. Get\<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) , což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="73474-570">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="73474-571">Vázaný objekt, instance `ArrayExample`, přijímá data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-571">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="73474-572">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="73474-572">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="73474-573">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="73474-573">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="73474-574">0</span><span class="sxs-lookup"><span data-stu-id="73474-574">0</span></span>                            | <span data-ttu-id="73474-575">value0</span><span class="sxs-lookup"><span data-stu-id="73474-575">value0</span></span>                       |
| <span data-ttu-id="73474-576">1</span><span class="sxs-lookup"><span data-stu-id="73474-576">1</span></span>                            | <span data-ttu-id="73474-577">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="73474-577">value1</span></span>                       |
| <span data-ttu-id="73474-578">2</span><span class="sxs-lookup"><span data-stu-id="73474-578">2</span></span>                            | <span data-ttu-id="73474-579">Argument</span><span class="sxs-lookup"><span data-stu-id="73474-579">value2</span></span>                       |
| <span data-ttu-id="73474-580">3</span><span class="sxs-lookup"><span data-stu-id="73474-580">3</span></span>                            | <span data-ttu-id="73474-581">value4</span><span class="sxs-lookup"><span data-stu-id="73474-581">value4</span></span>                       |
| <span data-ttu-id="73474-582">4</span><span class="sxs-lookup"><span data-stu-id="73474-582">4</span></span>                            | <span data-ttu-id="73474-583">value5</span><span class="sxs-lookup"><span data-stu-id="73474-583">value5</span></span>                       |

<span data-ttu-id="73474-584">Index &num;3 ve vázaném objektu obsahuje konfigurační data `array:4` pro konfigurační `value4`klíč a jeho hodnotu.</span><span class="sxs-lookup"><span data-stu-id="73474-584">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="73474-585">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="73474-585">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="73474-586">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="73474-586">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="73474-587">Chybějící položka konfigurace pro index &num;3 se dá zadat předtím, než se vazba `ArrayExample` na instanci doplní jakýmkoli poskytovatelem konfigurace, který vytvoří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="73474-587">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="73474-588">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá celému poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="73474-588">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="73474-589">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="73474-589">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="73474-590">V `ConfigureAppConfiguration`nástroji:</span><span class="sxs-lookup"><span data-stu-id="73474-590">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="73474-591">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-591">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="73474-592">Key</span><span class="sxs-lookup"><span data-stu-id="73474-592">Key</span></span>             | <span data-ttu-id="73474-593">Value</span><span class="sxs-lookup"><span data-stu-id="73474-593">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="73474-594">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="73474-594">array:entries:3</span></span> | <span data-ttu-id="73474-595">hodnota3</span><span class="sxs-lookup"><span data-stu-id="73474-595">value3</span></span> |

<span data-ttu-id="73474-596">Je-li instance &num; `ArrayExample.Entries` třídy svázána poté, co zprostředkovatel konfigurace JSON obsahuje položku pro index 3, pole obsahuje hodnotu. `ArrayExample`</span><span class="sxs-lookup"><span data-stu-id="73474-596">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="73474-597">`ArrayExample.Entries`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="73474-597">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="73474-598">`ArrayExample.Entries`Osa</span><span class="sxs-lookup"><span data-stu-id="73474-598">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="73474-599">0</span><span class="sxs-lookup"><span data-stu-id="73474-599">0</span></span>                            | <span data-ttu-id="73474-600">value0</span><span class="sxs-lookup"><span data-stu-id="73474-600">value0</span></span>                       |
| <span data-ttu-id="73474-601">1</span><span class="sxs-lookup"><span data-stu-id="73474-601">1</span></span>                            | <span data-ttu-id="73474-602">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="73474-602">value1</span></span>                       |
| <span data-ttu-id="73474-603">2</span><span class="sxs-lookup"><span data-stu-id="73474-603">2</span></span>                            | <span data-ttu-id="73474-604">Argument</span><span class="sxs-lookup"><span data-stu-id="73474-604">value2</span></span>                       |
| <span data-ttu-id="73474-605">3</span><span class="sxs-lookup"><span data-stu-id="73474-605">3</span></span>                            | <span data-ttu-id="73474-606">hodnota3</span><span class="sxs-lookup"><span data-stu-id="73474-606">value3</span></span>                       |
| <span data-ttu-id="73474-607">4</span><span class="sxs-lookup"><span data-stu-id="73474-607">4</span></span>                            | <span data-ttu-id="73474-608">value4</span><span class="sxs-lookup"><span data-stu-id="73474-608">value4</span></span>                       |
| <span data-ttu-id="73474-609">5</span><span class="sxs-lookup"><span data-stu-id="73474-609">5</span></span>                            | <span data-ttu-id="73474-610">value5</span><span class="sxs-lookup"><span data-stu-id="73474-610">value5</span></span>                       |

<span data-ttu-id="73474-611">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="73474-611">**JSON array processing**</span></span>

<span data-ttu-id="73474-612">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="73474-612">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="73474-613">V následujícím konfiguračním souboru `subsection` je pole:</span><span class="sxs-lookup"><span data-stu-id="73474-613">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="73474-614">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="73474-614">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="73474-615">Key</span><span class="sxs-lookup"><span data-stu-id="73474-615">Key</span></span>                     | <span data-ttu-id="73474-616">Value</span><span class="sxs-lookup"><span data-stu-id="73474-616">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="73474-617">json_array:key</span><span class="sxs-lookup"><span data-stu-id="73474-617">json_array:key</span></span>          | <span data-ttu-id="73474-618">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="73474-618">valueA</span></span> |
| <span data-ttu-id="73474-619">json_array: dílčí oddíl: 0</span><span class="sxs-lookup"><span data-stu-id="73474-619">json_array:subsection:0</span></span> | <span data-ttu-id="73474-620">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="73474-620">valueB</span></span> |
| <span data-ttu-id="73474-621">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="73474-621">json_array:subsection:1</span></span> | <span data-ttu-id="73474-622">valueC</span><span class="sxs-lookup"><span data-stu-id="73474-622">valueC</span></span> |
| <span data-ttu-id="73474-623">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="73474-623">json_array:subsection:2</span></span> | <span data-ttu-id="73474-624">s</span><span class="sxs-lookup"><span data-stu-id="73474-624">valueD</span></span> |

<span data-ttu-id="73474-625">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="73474-625">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="73474-626">Po vytvoření vazby `JsonArrayExample.Key` obsahuje hodnotu. `valueA`</span><span class="sxs-lookup"><span data-stu-id="73474-626">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="73474-627">Hodnoty pododdílu jsou uloženy ve vlastnosti `Subsection`pole POCO.</span><span class="sxs-lookup"><span data-stu-id="73474-627">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="73474-628">`JsonArrayExample.Subsection`Indexovacím</span><span class="sxs-lookup"><span data-stu-id="73474-628">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="73474-629">`JsonArrayExample.Subsection`Osa</span><span class="sxs-lookup"><span data-stu-id="73474-629">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="73474-630">0</span><span class="sxs-lookup"><span data-stu-id="73474-630">0</span></span>                                   | <span data-ttu-id="73474-631">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="73474-631">valueB</span></span>                              |
| <span data-ttu-id="73474-632">1</span><span class="sxs-lookup"><span data-stu-id="73474-632">1</span></span>                                   | <span data-ttu-id="73474-633">valueC</span><span class="sxs-lookup"><span data-stu-id="73474-633">valueC</span></span>                              |
| <span data-ttu-id="73474-634">2</span><span class="sxs-lookup"><span data-stu-id="73474-634">2</span></span>                                   | <span data-ttu-id="73474-635">s</span><span class="sxs-lookup"><span data-stu-id="73474-635">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="73474-636">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="73474-636">Custom configuration provider</span></span>

<span data-ttu-id="73474-637">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="73474-637">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="73474-638">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="73474-638">The provider has the following characteristics:</span></span>

* <span data-ttu-id="73474-639">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="73474-639">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="73474-640">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte `ConfigurationBuilder` sekundární pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="73474-640">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="73474-641">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="73474-641">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="73474-642">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="73474-642">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="73474-643">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-643">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="73474-644">`EFConfigurationValue` Definujte entitu pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="73474-644">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="73474-645">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-645">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="73474-646">`EFConfigurationContext` Přidejte do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="73474-646">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="73474-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-647">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="73474-648">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="73474-648">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="73474-649">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-649">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="73474-650">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="73474-650">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="73474-651">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="73474-651">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="73474-652">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-652">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="73474-653">Metoda rozšíření umožňuje přidat zdroj konfigurace `ConfigurationBuilder`do. `AddEFConfiguration`</span><span class="sxs-lookup"><span data-stu-id="73474-653">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="73474-654">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-654">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="73474-655">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-655">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="73474-656">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="73474-657">`EFConfigurationContext` Přidejte do úložiště a získejte přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="73474-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="73474-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="73474-659">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="73474-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="73474-660">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="73474-661">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="73474-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="73474-662">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="73474-662">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="73474-663">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-663">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="73474-664">Metoda rozšíření umožňuje přidat zdroj konfigurace `ConfigurationBuilder`do. `AddEFConfiguration`</span><span class="sxs-lookup"><span data-stu-id="73474-664">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="73474-665">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-665">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="73474-666">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="73474-666">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=30-31)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="73474-667">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="73474-667">Access configuration during startup</span></span>

<span data-ttu-id="73474-668">Vložení `IConfiguration` `Startup.ConfigureServices`do konstruktorupropřístupkhodnotámkonfiguracev.`Startup`</span><span class="sxs-lookup"><span data-stu-id="73474-668">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="73474-669">Chcete-li získat `Startup.Configure`přístup ke konfiguraci `IConfiguration` v, buď přímo do metody, nebo použijte instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="73474-669">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="73474-670">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete [v tématu spuštění aplikace: Praktické metody](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="73474-670">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="73474-671">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="73474-671">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="73474-672">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> ji do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="73474-672">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="73474-673">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="73474-673">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="73474-674">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="73474-674">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="73474-675">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="73474-675">Add configuration from an external assembly</span></span>

<span data-ttu-id="73474-676">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace.</span><span class="sxs-lookup"><span data-stu-id="73474-676">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="73474-677">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="73474-677">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73474-678">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="73474-678">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
