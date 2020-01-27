---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 141ae5cda7672159032013cbda1ef4bfa7c142dd
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726983"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="10d14-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10d14-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="10d14-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10d14-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="10d14-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="10d14-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="10d14-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="10d14-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="10d14-107">Azure Key Vault</span></span>
* <span data-ttu-id="10d14-108">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="10d14-108">Azure App Configuration</span></span>
* <span data-ttu-id="10d14-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-109">Command-line arguments</span></span>
* <span data-ttu-id="10d14-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="10d14-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="10d14-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="10d14-111">Directory files</span></span>
* <span data-ttu-id="10d14-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-112">Environment variables</span></span>
* <span data-ttu-id="10d14-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="10d14-113">In-memory .NET objects</span></span>
* <span data-ttu-id="10d14-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="10d14-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10d14-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="10d14-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10d14-116">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="10d14-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="10d14-117">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="10d14-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="10d14-118">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="10d14-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="10d14-119">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="10d14-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="10d14-120">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="10d14-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="10d14-121">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="10d14-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="10d14-122">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="10d14-122">Host versus app configuration</span></span>

<span data-ttu-id="10d14-123">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="10d14-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="10d14-124">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="10d14-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="10d14-125">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="10d14-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="10d14-126">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="10d14-127">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="10d14-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="10d14-128">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="10d14-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10d14-129">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="10d14-130">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="10d14-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="10d14-131">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="10d14-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="10d14-132">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="10d14-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="10d14-133">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="10d14-134">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="10d14-135">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="10d14-136">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="10d14-137">Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="10d14-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="10d14-138">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="10d14-139">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="10d14-140">Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="10d14-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="10d14-141">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="10d14-141">Enable IIS integration.</span></span>
* <span data-ttu-id="10d14-142">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="10d14-143">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-144">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-145">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="10d14-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="10d14-146">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-147">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10d14-148">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="10d14-149">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="10d14-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="10d14-150">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="10d14-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="10d14-151">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="10d14-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="10d14-152">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="10d14-153">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="10d14-154">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="10d14-155">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="10d14-156">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="10d14-157">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-158">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-159">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="10d14-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="10d14-160">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="10d14-161">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="10d14-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="10d14-162">Zabezpečení –</span><span class="sxs-lookup"><span data-stu-id="10d14-162">Security</span></span>

<span data-ttu-id="10d14-163">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="10d14-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="10d14-164">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="10d14-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="10d14-165">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="10d14-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="10d14-166">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="10d14-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="10d14-167">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="10d14-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="10d14-168"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="10d14-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="10d14-169">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="10d14-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="10d14-170">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="10d14-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="10d14-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="10d14-172">Další informace najdete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="10d14-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="10d14-173">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="10d14-173">Hierarchical configuration data</span></span>

<span data-ttu-id="10d14-174">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="10d14-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="10d14-175">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="10d14-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="10d14-176">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="10d14-177">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="10d14-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="10d14-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-178">section0:key0</span></span>
* <span data-ttu-id="10d14-179">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-179">section0:key1</span></span>
* <span data-ttu-id="10d14-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-180">section1:key0</span></span>
* <span data-ttu-id="10d14-181">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-181">section1:key1</span></span>

<span data-ttu-id="10d14-182">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="10d14-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="10d14-183">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="10d14-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="10d14-184">Konvence</span><span class="sxs-lookup"><span data-stu-id="10d14-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="10d14-185">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="10d14-185">Sources and providers</span></span>

<span data-ttu-id="10d14-186">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="10d14-187">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="10d14-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="10d14-188">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="10d14-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="10d14-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="10d14-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="10d14-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="10d14-191">V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:</span><span class="sxs-lookup"><span data-stu-id="10d14-191">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="10d14-192">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="10d14-192">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="10d14-193">Klíče</span><span class="sxs-lookup"><span data-stu-id="10d14-193">Keys</span></span>

<span data-ttu-id="10d14-194">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="10d14-194">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="10d14-195">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="10d14-195">Keys are case-insensitive.</span></span> <span data-ttu-id="10d14-196">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="10d14-196">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="10d14-197">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="10d14-197">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="10d14-198">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="10d14-198">Hierarchical keys</span></span>
  * <span data-ttu-id="10d14-199">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="10d14-199">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="10d14-200">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="10d14-200">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="10d14-201">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="10d14-201">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="10d14-202">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="10d14-202">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="10d14-203">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-203">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="10d14-204"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="10d14-204">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="10d14-205">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="10d14-205">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="10d14-206">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="10d14-206">Values</span></span>

<span data-ttu-id="10d14-207">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="10d14-207">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="10d14-208">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="10d14-208">Values are strings.</span></span>
* <span data-ttu-id="10d14-209">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="10d14-209">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="10d14-210">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="10d14-210">Providers</span></span>

<span data-ttu-id="10d14-211">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-211">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="10d14-212">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="10d14-212">Provider</span></span> | <span data-ttu-id="10d14-213">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="10d14-213">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="10d14-214">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="10d14-214">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="10d14-215">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="10d14-215">Azure Key Vault</span></span> |
| <span data-ttu-id="10d14-216">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="10d14-216">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="10d14-217">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="10d14-217">Azure App Configuration</span></span> |
| [<span data-ttu-id="10d14-218">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-218">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="10d14-219">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-219">Command-line parameters</span></span> |
| [<span data-ttu-id="10d14-220">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="10d14-220">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="10d14-221">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="10d14-221">Custom source</span></span> |
| [<span data-ttu-id="10d14-222">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-222">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="10d14-223">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-223">Environment variables</span></span> |
| [<span data-ttu-id="10d14-224">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="10d14-224">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="10d14-225">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="10d14-225">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="10d14-226">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="10d14-226">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="10d14-227">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="10d14-227">Directory files</span></span> |
| [<span data-ttu-id="10d14-228">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="10d14-228">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="10d14-229">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="10d14-229">In-memory collections</span></span> |
| <span data-ttu-id="10d14-230">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="10d14-230">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="10d14-231">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="10d14-231">File in the user profile directory</span></span> |

<span data-ttu-id="10d14-232">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="10d14-232">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="10d14-233">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="10d14-233">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="10d14-234">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="10d14-234">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="10d14-235">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="10d14-235">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="10d14-236">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="10d14-236">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="10d14-237">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="10d14-237">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="10d14-238">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="10d14-238">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="10d14-239">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-239">Environment variables</span></span>
1. <span data-ttu-id="10d14-240">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-240">Command-line arguments</span></span>

<span data-ttu-id="10d14-241">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="10d14-241">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="10d14-242">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-242">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="10d14-243">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-243">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="10d14-244">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="10d14-244">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="10d14-245">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="10d14-245">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="10d14-246">`ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="10d14-246">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="10d14-247">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="10d14-247">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="10d14-248">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="10d14-248">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="10d14-249">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="10d14-249">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="10d14-250">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="10d14-250">ConfigureAppConfiguration</span></span>

<span data-ttu-id="10d14-251">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="10d14-251">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="10d14-252">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-252">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="10d14-253">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="10d14-253">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="10d14-254">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="10d14-254">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="10d14-255">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="10d14-255">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="10d14-256">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="10d14-256">Consume configuration during app startup</span></span>

<span data-ttu-id="10d14-257">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10d14-257">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10d14-258">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="10d14-258">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="10d14-259">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="10d14-259">Command-line Configuration Provider</span></span>

<span data-ttu-id="10d14-260"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="10d14-260">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="10d14-261">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="10d14-261">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="10d14-262">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="10d14-262">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="10d14-263">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-263">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="10d14-264">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="10d14-264">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="10d14-265">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="10d14-265">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="10d14-266">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-266">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="10d14-267">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-267">Environment variables.</span></span>

<span data-ttu-id="10d14-268">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="10d14-268">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="10d14-269">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="10d14-269">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="10d14-270">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-270">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="10d14-271">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="10d14-271">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="10d14-272">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-272">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="10d14-273">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="10d14-273">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="10d14-274">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="10d14-274">**Example**</span></span>

<span data-ttu-id="10d14-275">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="10d14-275">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="10d14-276">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="10d14-276">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="10d14-277">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="10d14-277">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="10d14-278">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="10d14-278">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="10d14-279">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="10d14-279">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="10d14-280">Arguments</span><span class="sxs-lookup"><span data-stu-id="10d14-280">Arguments</span></span>

<span data-ttu-id="10d14-281">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="10d14-281">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="10d14-282">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="10d14-282">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="10d14-283">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="10d14-283">Key prefix</span></span>               | <span data-ttu-id="10d14-284">Příklad</span><span class="sxs-lookup"><span data-stu-id="10d14-284">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="10d14-285">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="10d14-285">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="10d14-286">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="10d14-286">Two dashes (`--`)</span></span>        | <span data-ttu-id="10d14-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="10d14-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="10d14-288">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="10d14-288">Forward slash (`/`)</span></span>      | <span data-ttu-id="10d14-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="10d14-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="10d14-290">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="10d14-290">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="10d14-291">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="10d14-291">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="10d14-292">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="10d14-292">Switch mappings</span></span>

<span data-ttu-id="10d14-293">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="10d14-293">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="10d14-294">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="10d14-294">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="10d14-295">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="10d14-295">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="10d14-296">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="10d14-296">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="10d14-297">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="10d14-297">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="10d14-298">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="10d14-298">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="10d14-299">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="10d14-299">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="10d14-300">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="10d14-300">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="10d14-301">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="10d14-301">Create a switch mappings dictionary.</span></span> <span data-ttu-id="10d14-302">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="10d14-302">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="10d14-303">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="10d14-303">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="10d14-304">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="10d14-304">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="10d14-305">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-305">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="10d14-306">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="10d14-306">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="10d14-307">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="10d14-307">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="10d14-308">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-308">Key</span></span>       | <span data-ttu-id="10d14-309">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-309">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="10d14-310">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="10d14-310">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="10d14-311">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="10d14-311">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="10d14-312">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-312">Key</span></span>               | <span data-ttu-id="10d14-313">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-313">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="10d14-314">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-314">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="10d14-315"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="10d14-315">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="10d14-316">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-316">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="10d14-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="10d14-318">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="10d14-318">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10d14-319">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-319">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="10d14-320">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-320">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10d14-321">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-321">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="10d14-322">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-322">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="10d14-323">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="10d14-323">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="10d14-324">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="10d14-324">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="10d14-325">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="10d14-325">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="10d14-326">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-326">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="10d14-327">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="10d14-327">Command-line arguments.</span></span>

<span data-ttu-id="10d14-328">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="10d14-328">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="10d14-329">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="10d14-329">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="10d14-330">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:</span><span class="sxs-lookup"><span data-stu-id="10d14-330">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="10d14-331">Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.</span><span class="sxs-lookup"><span data-stu-id="10d14-331">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="10d14-332">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="10d14-332">**Example**</span></span>

<span data-ttu-id="10d14-333">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="10d14-333">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="10d14-334">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-334">Run the sample app.</span></span> <span data-ttu-id="10d14-335">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="10d14-335">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="10d14-336">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="10d14-336">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="10d14-337">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="10d14-337">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="10d14-338">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-338">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="10d14-339">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-339">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="10d14-340">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="10d14-340">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="10d14-341">Předpony</span><span class="sxs-lookup"><span data-stu-id="10d14-341">Prefixes</span></span>

<span data-ttu-id="10d14-342">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="10d14-342">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="10d14-343">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-343">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="10d14-344">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="10d14-344">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="10d14-345">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-345">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="10d14-346">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-346">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="10d14-347">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="10d14-347">**Connection string prefixes**</span></span>

<span data-ttu-id="10d14-348">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-348">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="10d14-349">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="10d14-349">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="10d14-350">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="10d14-350">Connection string prefix</span></span> | <span data-ttu-id="10d14-351">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="10d14-351">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="10d14-352">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="10d14-352">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="10d14-353">MySQL</span><span class="sxs-lookup"><span data-stu-id="10d14-353">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="10d14-354">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="10d14-354">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="10d14-355">SQL Server</span><span class="sxs-lookup"><span data-stu-id="10d14-355">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="10d14-356">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="10d14-356">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="10d14-357">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="10d14-357">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="10d14-358">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="10d14-358">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="10d14-359">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="10d14-359">Environment variable key</span></span> | <span data-ttu-id="10d14-360">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="10d14-360">Converted configuration key</span></span> | <span data-ttu-id="10d14-361">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="10d14-361">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="10d14-362">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="10d14-362">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="10d14-363">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="10d14-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="10d14-364">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="10d14-364">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="10d14-365">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="10d14-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="10d14-366">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="10d14-366">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="10d14-367">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="10d14-367">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="10d14-368">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="10d14-368">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="10d14-369">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="10d14-369">File Configuration Provider</span></span>

<span data-ttu-id="10d14-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="10d14-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="10d14-371">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="10d14-371">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="10d14-372">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="10d14-372">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="10d14-373">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="10d14-373">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="10d14-374">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="10d14-374">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="10d14-375">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="10d14-375">INI Configuration Provider</span></span>

<span data-ttu-id="10d14-376"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="10d14-376">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="10d14-377">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-377">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="10d14-378">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="10d14-378">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="10d14-379">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="10d14-379">Overloads permit specifying:</span></span>

* <span data-ttu-id="10d14-380">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="10d14-380">Whether the file is optional.</span></span>
* <span data-ttu-id="10d14-381">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-381">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="10d14-382"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-382">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="10d14-383">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="10d14-383">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="10d14-384">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="10d14-384">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="10d14-385">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="10d14-385">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="10d14-386">section0:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-386">section0:key0</span></span>
* <span data-ttu-id="10d14-387">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-387">section0:key1</span></span>
* <span data-ttu-id="10d14-388">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="10d14-388">section1:subsection:key</span></span>
* <span data-ttu-id="10d14-389">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="10d14-389">section2:subsection0:key</span></span>
* <span data-ttu-id="10d14-390">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="10d14-390">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="10d14-391">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="10d14-391">JSON Configuration Provider</span></span>

<span data-ttu-id="10d14-392"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="10d14-392">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="10d14-393">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-393">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="10d14-394">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="10d14-394">Overloads permit specifying:</span></span>

* <span data-ttu-id="10d14-395">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="10d14-395">Whether the file is optional.</span></span>
* <span data-ttu-id="10d14-396">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-396">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="10d14-397"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-397">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="10d14-398">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-398">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="10d14-399">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="10d14-399">The method is called to load configuration from:</span></span>

* <span data-ttu-id="10d14-400">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="10d14-400">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="10d14-401">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="10d14-401">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="10d14-402">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="10d14-402">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="10d14-403">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="10d14-403">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="10d14-404">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="10d14-404">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="10d14-405">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-405">Environment variables.</span></span>
* <span data-ttu-id="10d14-406">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-406">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="10d14-407">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="10d14-407">Command-line arguments.</span></span>

<span data-ttu-id="10d14-408">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="10d14-408">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="10d14-409">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="10d14-409">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="10d14-410">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="10d14-410">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="10d14-411">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="10d14-411">**Example**</span></span>

<span data-ttu-id="10d14-412">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="10d14-412">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="10d14-413">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="10d14-413">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="10d14-414">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="10d14-414">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="10d14-415">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="10d14-415">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="10d14-416">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-416">Run the sample app.</span></span> <span data-ttu-id="10d14-417">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="10d14-417">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="10d14-418">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-418">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="10d14-419">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-419">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="10d14-420">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="10d14-420">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="10d14-421">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="10d14-421">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="10d14-422">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="10d14-422">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="10d14-423">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-423">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="10d14-424">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="10d14-424">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="10d14-425">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Information`.</span><span class="sxs-lookup"><span data-stu-id="10d14-425">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="10d14-426">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="10d14-426">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="10d14-427">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="10d14-427">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="10d14-428">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="10d14-428">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="10d14-429">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-429">Run the sample app.</span></span> <span data-ttu-id="10d14-430">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="10d14-430">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="10d14-431">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-431">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="10d14-432">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-432">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="10d14-433">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="10d14-433">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="10d14-434">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="10d14-434">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="10d14-435">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="10d14-435">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="10d14-436">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="10d14-436">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="10d14-437">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="10d14-437">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="10d14-438">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Warning`.</span><span class="sxs-lookup"><span data-stu-id="10d14-438">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="10d14-439">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="10d14-439">XML Configuration Provider</span></span>

<span data-ttu-id="10d14-440"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="10d14-440">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="10d14-441">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-441">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="10d14-442">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="10d14-442">Overloads permit specifying:</span></span>

* <span data-ttu-id="10d14-443">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="10d14-443">Whether the file is optional.</span></span>
* <span data-ttu-id="10d14-444">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-444">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="10d14-445"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-445">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="10d14-446">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-446">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="10d14-447">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-447">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="10d14-448">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="10d14-448">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="10d14-449">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="10d14-449">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="10d14-450">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="10d14-450">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="10d14-451">section0:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-451">section0:key0</span></span>
* <span data-ttu-id="10d14-452">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-452">section0:key1</span></span>
* <span data-ttu-id="10d14-453">section1:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-453">section1:key0</span></span>
* <span data-ttu-id="10d14-454">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-454">section1:key1</span></span>

<span data-ttu-id="10d14-455">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="10d14-455">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="10d14-456">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="10d14-456">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="10d14-457">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-457">section:section0:key:key0</span></span>
* <span data-ttu-id="10d14-458">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="10d14-458">section:section0:key:key1</span></span>
* <span data-ttu-id="10d14-459">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-459">section:section1:key:key0</span></span>
* <span data-ttu-id="10d14-460">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-460">section:section1:key:key1</span></span>

<span data-ttu-id="10d14-461">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="10d14-461">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="10d14-462">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="10d14-462">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="10d14-463">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="10d14-463">key:attribute</span></span>
* <span data-ttu-id="10d14-464">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="10d14-464">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="10d14-465">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="10d14-465">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="10d14-466"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="10d14-466">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="10d14-467">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-467">The key is the file name.</span></span> <span data-ttu-id="10d14-468">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="10d14-468">The value contains the file's contents.</span></span> <span data-ttu-id="10d14-469">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="10d14-469">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="10d14-470">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-470">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="10d14-471">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="10d14-471">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="10d14-472">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="10d14-472">Overloads permit specifying:</span></span>

* <span data-ttu-id="10d14-473">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="10d14-473">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="10d14-474">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="10d14-474">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="10d14-475">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="10d14-475">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="10d14-476">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="10d14-476">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="10d14-477">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="10d14-477">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="10d14-478">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="10d14-478">Memory Configuration Provider</span></span>

<span data-ttu-id="10d14-479"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="10d14-479">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="10d14-480">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="10d14-480">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="10d14-481">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="10d14-481">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="10d14-482">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="10d14-482">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="10d14-483">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="10d14-483">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="10d14-484">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-484">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="10d14-485">GetValue</span><span class="sxs-lookup"><span data-stu-id="10d14-485">GetValue</span></span>

<span data-ttu-id="10d14-486">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="10d14-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="10d14-487">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="10d14-487">An overload accepts a default value.</span></span>

<span data-ttu-id="10d14-488">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="10d14-488">The following example:</span></span>

* <span data-ttu-id="10d14-489">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="10d14-489">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="10d14-490">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="10d14-490">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="10d14-491">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="10d14-491">Types the value as an `int`.</span></span>
* <span data-ttu-id="10d14-492">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="10d14-492">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="10d14-493">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="10d14-493">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="10d14-494">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="10d14-494">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="10d14-495">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="10d14-495">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="10d14-496">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-496">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="10d14-497">section0:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-497">section0:key0</span></span>
* <span data-ttu-id="10d14-498">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-498">section0:key1</span></span>
* <span data-ttu-id="10d14-499">section1:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-499">section1:key0</span></span>
* <span data-ttu-id="10d14-500">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-500">section1:key1</span></span>
* <span data-ttu-id="10d14-501">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-501">section2:subsection0:key0</span></span>
* <span data-ttu-id="10d14-502">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-502">section2:subsection0:key1</span></span>
* <span data-ttu-id="10d14-503">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="10d14-503">section2:subsection1:key0</span></span>
* <span data-ttu-id="10d14-504">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="10d14-504">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="10d14-505">GetSection</span><span class="sxs-lookup"><span data-stu-id="10d14-505">GetSection</span></span>

<span data-ttu-id="10d14-506">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="10d14-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="10d14-507">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="10d14-507">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="10d14-508">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="10d14-508">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="10d14-509">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="10d14-509">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="10d14-510">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="10d14-510">`GetSection` never returns `null`.</span></span> <span data-ttu-id="10d14-511">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="10d14-511">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="10d14-512">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="10d14-512">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="10d14-513">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="10d14-513">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="10d14-514">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="10d14-514">GetChildren</span></span>

<span data-ttu-id="10d14-515">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-515">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="10d14-516">Existuje</span><span class="sxs-lookup"><span data-stu-id="10d14-516">Exists</span></span>

<span data-ttu-id="10d14-517">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="10d14-517">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="10d14-518">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="10d14-518">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="10d14-519">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="10d14-519">Bind to a class</span></span>

<span data-ttu-id="10d14-520">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="10d14-520">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="10d14-521">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="10d14-521">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="10d14-522">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="10d14-522">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="10d14-523">Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="10d14-523">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="10d14-524">Pole nejsou **svázána** .</span><span class="sxs-lookup"><span data-stu-id="10d14-524">Fields are **not** bound.</span></span>

<span data-ttu-id="10d14-525">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="10d14-525">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="10d14-526">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="10d14-526">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="10d14-527">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-527">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="10d14-528">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-528">Key</span></span>                   | <span data-ttu-id="10d14-529">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-529">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="10d14-530">Starship: název</span><span class="sxs-lookup"><span data-stu-id="10d14-530">starship:name</span></span>         | <span data-ttu-id="10d14-531">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="10d14-531">USS Enterprise</span></span>                                    |
| <span data-ttu-id="10d14-532">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="10d14-532">starship:registry</span></span>     | <span data-ttu-id="10d14-533">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="10d14-533">NCC-1701</span></span>                                          |
| <span data-ttu-id="10d14-534">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="10d14-534">starship:class</span></span>        | <span data-ttu-id="10d14-535">Založen</span><span class="sxs-lookup"><span data-stu-id="10d14-535">Constitution</span></span>                                      |
| <span data-ttu-id="10d14-536">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="10d14-536">starship:length</span></span>       | <span data-ttu-id="10d14-537">304,8</span><span class="sxs-lookup"><span data-stu-id="10d14-537">304.8</span></span>                                             |
| <span data-ttu-id="10d14-538">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="10d14-538">starship:commissioned</span></span> | <span data-ttu-id="10d14-539">Nepravda</span><span class="sxs-lookup"><span data-stu-id="10d14-539">False</span></span>                                             |
| <span data-ttu-id="10d14-540">Patka</span><span class="sxs-lookup"><span data-stu-id="10d14-540">trademark</span></span>             | <span data-ttu-id="10d14-541">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="10d14-541">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="10d14-542">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="10d14-542">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="10d14-543">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="10d14-543">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="10d14-544">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="10d14-544">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="10d14-545">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="10d14-545">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="10d14-546">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="10d14-546">Bind to an object graph</span></span>

<span data-ttu-id="10d14-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="10d14-547"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="10d14-548">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="10d14-548">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="10d14-549">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="10d14-549">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="10d14-550">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="10d14-550">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="10d14-551">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="10d14-551">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="10d14-552">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="10d14-552">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="10d14-553">[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="10d14-553">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="10d14-554">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="10d14-554">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="10d14-555">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="10d14-555">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="10d14-556">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="10d14-556">Bind an array to a class</span></span>

<span data-ttu-id="10d14-557">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="10d14-557">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="10d14-558"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="10d14-558">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="10d14-559">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="10d14-559">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="10d14-560">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="10d14-560">Binding is provided by convention.</span></span> <span data-ttu-id="10d14-561">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="10d14-561">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="10d14-562">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="10d14-562">**In-memory array processing**</span></span>

<span data-ttu-id="10d14-563">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="10d14-563">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="10d14-564">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-564">Key</span></span>             | <span data-ttu-id="10d14-565">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-565">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="10d14-566">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="10d14-566">array:entries:0</span></span> | <span data-ttu-id="10d14-567">value0</span><span class="sxs-lookup"><span data-stu-id="10d14-567">value0</span></span> |
| <span data-ttu-id="10d14-568">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="10d14-568">array:entries:1</span></span> | <span data-ttu-id="10d14-569">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="10d14-569">value1</span></span> |
| <span data-ttu-id="10d14-570">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="10d14-570">array:entries:2</span></span> | <span data-ttu-id="10d14-571">Argument</span><span class="sxs-lookup"><span data-stu-id="10d14-571">value2</span></span> |
| <span data-ttu-id="10d14-572">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="10d14-572">array:entries:4</span></span> | <span data-ttu-id="10d14-573">value4</span><span class="sxs-lookup"><span data-stu-id="10d14-573">value4</span></span> |
| <span data-ttu-id="10d14-574">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="10d14-574">array:entries:5</span></span> | <span data-ttu-id="10d14-575">value5</span><span class="sxs-lookup"><span data-stu-id="10d14-575">value5</span></span> |

<span data-ttu-id="10d14-576">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="10d14-576">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="10d14-577">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="10d14-577">The array skips a value for index &num;3.</span></span> <span data-ttu-id="10d14-578">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="10d14-578">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="10d14-579">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="10d14-579">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="10d14-580">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="10d14-580">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="10d14-581">[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="10d14-581">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="10d14-582">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-582">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="10d14-583">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="10d14-583">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="10d14-584">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="10d14-584">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="10d14-585">0</span><span class="sxs-lookup"><span data-stu-id="10d14-585">0</span></span>                            | <span data-ttu-id="10d14-586">value0</span><span class="sxs-lookup"><span data-stu-id="10d14-586">value0</span></span>                       |
| <span data-ttu-id="10d14-587">1</span><span class="sxs-lookup"><span data-stu-id="10d14-587">1</span></span>                            | <span data-ttu-id="10d14-588">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="10d14-588">value1</span></span>                       |
| <span data-ttu-id="10d14-589">2</span><span class="sxs-lookup"><span data-stu-id="10d14-589">2</span></span>                            | <span data-ttu-id="10d14-590">Argument</span><span class="sxs-lookup"><span data-stu-id="10d14-590">value2</span></span>                       |
| <span data-ttu-id="10d14-591">3</span><span class="sxs-lookup"><span data-stu-id="10d14-591">3</span></span>                            | <span data-ttu-id="10d14-592">value4</span><span class="sxs-lookup"><span data-stu-id="10d14-592">value4</span></span>                       |
| <span data-ttu-id="10d14-593">4</span><span class="sxs-lookup"><span data-stu-id="10d14-593">4</span></span>                            | <span data-ttu-id="10d14-594">value5</span><span class="sxs-lookup"><span data-stu-id="10d14-594">value5</span></span>                       |

<span data-ttu-id="10d14-595">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="10d14-595">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="10d14-596">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="10d14-596">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="10d14-597">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="10d14-597">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="10d14-598">Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="10d14-598">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="10d14-599">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="10d14-599">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="10d14-600">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="10d14-600">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="10d14-601">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="10d14-601">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="10d14-602">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-602">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="10d14-603">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-603">Key</span></span>             | <span data-ttu-id="10d14-604">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-604">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="10d14-605">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="10d14-605">array:entries:3</span></span> | <span data-ttu-id="10d14-606">hodnota3</span><span class="sxs-lookup"><span data-stu-id="10d14-606">value3</span></span> |

<span data-ttu-id="10d14-607">Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="10d14-607">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="10d14-608">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="10d14-608">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="10d14-609">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="10d14-609">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="10d14-610">0</span><span class="sxs-lookup"><span data-stu-id="10d14-610">0</span></span>                            | <span data-ttu-id="10d14-611">value0</span><span class="sxs-lookup"><span data-stu-id="10d14-611">value0</span></span>                       |
| <span data-ttu-id="10d14-612">1</span><span class="sxs-lookup"><span data-stu-id="10d14-612">1</span></span>                            | <span data-ttu-id="10d14-613">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="10d14-613">value1</span></span>                       |
| <span data-ttu-id="10d14-614">2</span><span class="sxs-lookup"><span data-stu-id="10d14-614">2</span></span>                            | <span data-ttu-id="10d14-615">Argument</span><span class="sxs-lookup"><span data-stu-id="10d14-615">value2</span></span>                       |
| <span data-ttu-id="10d14-616">3</span><span class="sxs-lookup"><span data-stu-id="10d14-616">3</span></span>                            | <span data-ttu-id="10d14-617">hodnota3</span><span class="sxs-lookup"><span data-stu-id="10d14-617">value3</span></span>                       |
| <span data-ttu-id="10d14-618">4</span><span class="sxs-lookup"><span data-stu-id="10d14-618">4</span></span>                            | <span data-ttu-id="10d14-619">value4</span><span class="sxs-lookup"><span data-stu-id="10d14-619">value4</span></span>                       |
| <span data-ttu-id="10d14-620">5</span><span class="sxs-lookup"><span data-stu-id="10d14-620">5</span></span>                            | <span data-ttu-id="10d14-621">value5</span><span class="sxs-lookup"><span data-stu-id="10d14-621">value5</span></span>                       |

<span data-ttu-id="10d14-622">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="10d14-622">**JSON array processing**</span></span>

<span data-ttu-id="10d14-623">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="10d14-623">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="10d14-624">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="10d14-624">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="10d14-625">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="10d14-625">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="10d14-626">Key</span><span class="sxs-lookup"><span data-stu-id="10d14-626">Key</span></span>                     | <span data-ttu-id="10d14-627">Hodnota</span><span class="sxs-lookup"><span data-stu-id="10d14-627">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="10d14-628">json_array:key</span><span class="sxs-lookup"><span data-stu-id="10d14-628">json_array:key</span></span>          | <span data-ttu-id="10d14-629">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="10d14-629">valueA</span></span> |
| <span data-ttu-id="10d14-630">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="10d14-630">json_array:subsection:0</span></span> | <span data-ttu-id="10d14-631">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="10d14-631">valueB</span></span> |
| <span data-ttu-id="10d14-632">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="10d14-632">json_array:subsection:1</span></span> | <span data-ttu-id="10d14-633">valueC</span><span class="sxs-lookup"><span data-stu-id="10d14-633">valueC</span></span> |
| <span data-ttu-id="10d14-634">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="10d14-634">json_array:subsection:2</span></span> | <span data-ttu-id="10d14-635">s</span><span class="sxs-lookup"><span data-stu-id="10d14-635">valueD</span></span> |

<span data-ttu-id="10d14-636">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="10d14-636">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="10d14-637">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="10d14-637">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="10d14-638">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="10d14-638">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="10d14-639">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="10d14-639">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="10d14-640">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="10d14-640">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="10d14-641">0</span><span class="sxs-lookup"><span data-stu-id="10d14-641">0</span></span>                                   | <span data-ttu-id="10d14-642">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="10d14-642">valueB</span></span>                              |
| <span data-ttu-id="10d14-643">1</span><span class="sxs-lookup"><span data-stu-id="10d14-643">1</span></span>                                   | <span data-ttu-id="10d14-644">valueC</span><span class="sxs-lookup"><span data-stu-id="10d14-644">valueC</span></span>                              |
| <span data-ttu-id="10d14-645">2</span><span class="sxs-lookup"><span data-stu-id="10d14-645">2</span></span>                                   | <span data-ttu-id="10d14-646">s</span><span class="sxs-lookup"><span data-stu-id="10d14-646">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="10d14-647">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="10d14-647">Custom configuration provider</span></span>

<span data-ttu-id="10d14-648">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="10d14-648">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="10d14-649">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="10d14-649">The provider has the following characteristics:</span></span>

* <span data-ttu-id="10d14-650">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="10d14-650">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="10d14-651">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10d14-651">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="10d14-652">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="10d14-652">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="10d14-653">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="10d14-653">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="10d14-654">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-654">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="10d14-655">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="10d14-655">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="10d14-656">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-656">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="10d14-657">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="10d14-657">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="10d14-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-658">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="10d14-659">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="10d14-659">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="10d14-660">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-660">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="10d14-661">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="10d14-661">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="10d14-662">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="10d14-662">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="10d14-663">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="10d14-663">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="10d14-664">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-664">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="10d14-665">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-665">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="10d14-666">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-666">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="10d14-667">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-667">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="10d14-668">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-668">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="10d14-669">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="10d14-669">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="10d14-670">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-670">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="10d14-671">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="10d14-671">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="10d14-672">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-672">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="10d14-673">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="10d14-673">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="10d14-674">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="10d14-674">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="10d14-675">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-675">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="10d14-676">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="10d14-676">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="10d14-677">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-677">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="10d14-678">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="10d14-678">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="10d14-679">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="10d14-679">Access configuration during startup</span></span>

<span data-ttu-id="10d14-680">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="10d14-680">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="10d14-681">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="10d14-681">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="10d14-682">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="10d14-682">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="10d14-683">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="10d14-683">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="10d14-684">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="10d14-684">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="10d14-685">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="10d14-685">In a Razor Pages page:</span></span>

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

<span data-ttu-id="10d14-686">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="10d14-686">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="10d14-687">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="10d14-687">Add configuration from an external assembly</span></span>

<span data-ttu-id="10d14-688">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace.</span><span class="sxs-lookup"><span data-stu-id="10d14-688">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="10d14-689">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="10d14-689">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10d14-690">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="10d14-690">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
