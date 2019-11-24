---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 9f0ad2791e504a0ff46daad07054b6bf909a546a
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634077"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8413d-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8413d-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8413d-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8413d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8413d-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="8413d-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8413d-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8413d-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8413d-107">Azure Key Vault</span></span>
* <span data-ttu-id="8413d-108">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="8413d-108">Azure App Configuration</span></span>
* <span data-ttu-id="8413d-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-109">Command-line arguments</span></span>
* <span data-ttu-id="8413d-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="8413d-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8413d-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="8413d-111">Directory files</span></span>
* <span data-ttu-id="8413d-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-112">Environment variables</span></span>
* <span data-ttu-id="8413d-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="8413d-113">In-memory .NET objects</span></span>
* <span data-ttu-id="8413d-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="8413d-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8413d-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="8413d-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8413d-116">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="8413d-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="8413d-117">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="8413d-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8413d-118">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8413d-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8413d-119">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="8413d-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8413d-120">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8413d-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8413d-121">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8413d-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8413d-122">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="8413d-122">Host versus app configuration</span></span>

<span data-ttu-id="8413d-123">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="8413d-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8413d-124">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="8413d-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8413d-125">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8413d-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8413d-126">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8413d-127">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8413d-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8413d-128">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="8413d-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8413d-129">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8413d-130">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="8413d-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8413d-131">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="8413d-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="8413d-132">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="8413d-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8413d-133">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8413d-134">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8413d-135">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8413d-136">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8413d-137">Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="8413d-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8413d-138">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8413d-139">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8413d-140">Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="8413d-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8413d-141">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="8413d-141">Enable IIS integration.</span></span>
* <span data-ttu-id="8413d-142">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="8413d-143">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-144">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-145">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="8413d-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8413d-146">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-147">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8413d-148">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8413d-149">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="8413d-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8413d-150">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="8413d-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8413d-151">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8413d-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8413d-152">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8413d-153">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8413d-154">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8413d-155">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8413d-156">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="8413d-157">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-158">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-159">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="8413d-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8413d-160">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8413d-161">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8413d-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="8413d-162">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="8413d-162">Security</span></span>

<span data-ttu-id="8413d-163">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="8413d-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8413d-164">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="8413d-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8413d-165">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="8413d-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8413d-166">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="8413d-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8413d-167">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="8413d-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8413d-168"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="8413d-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8413d-169">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="8413d-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8413d-170">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="8413d-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8413d-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8413d-172">Další informace najdete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8413d-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8413d-173">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="8413d-173">Hierarchical configuration data</span></span>

<span data-ttu-id="8413d-174">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="8413d-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8413d-175">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="8413d-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8413d-176">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8413d-177">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="8413d-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8413d-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-178">section0:key0</span></span>
* <span data-ttu-id="8413d-179">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-179">section0:key1</span></span>
* <span data-ttu-id="8413d-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-180">section1:key0</span></span>
* <span data-ttu-id="8413d-181">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-181">section1:key1</span></span>

<span data-ttu-id="8413d-182">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="8413d-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8413d-183">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="8413d-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8413d-184">Konvence</span><span class="sxs-lookup"><span data-stu-id="8413d-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8413d-185">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="8413d-185">Sources and providers</span></span>

<span data-ttu-id="8413d-186">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8413d-187">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="8413d-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8413d-188">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="8413d-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8413d-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8413d-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> pro získání konfigurace pro třídu:</span><span class="sxs-lookup"><span data-stu-id="8413d-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="8413d-191">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="8413d-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8413d-192">Klíče</span><span class="sxs-lookup"><span data-stu-id="8413d-192">Keys</span></span>

<span data-ttu-id="8413d-193">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="8413d-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8413d-194">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="8413d-194">Keys are case-insensitive.</span></span> <span data-ttu-id="8413d-195">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="8413d-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8413d-196">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="8413d-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8413d-197">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="8413d-197">Hierarchical keys</span></span>
  * <span data-ttu-id="8413d-198">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="8413d-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8413d-199">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="8413d-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8413d-200">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="8413d-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8413d-201">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="8413d-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8413d-202">Pokud jsou tajné klíče načteny do konfigurace aplikace, je nutné zadat kód, který nahradí pomlčky dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="8413d-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8413d-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="8413d-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8413d-204">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="8413d-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8413d-205">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="8413d-205">Values</span></span>

<span data-ttu-id="8413d-206">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="8413d-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8413d-207">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="8413d-207">Values are strings.</span></span>
* <span data-ttu-id="8413d-208">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="8413d-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8413d-209">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="8413d-209">Providers</span></span>

<span data-ttu-id="8413d-210">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8413d-211">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="8413d-211">Provider</span></span> | <span data-ttu-id="8413d-212">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="8413d-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8413d-213">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="8413d-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8413d-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8413d-214">Azure Key Vault</span></span> |
| <span data-ttu-id="8413d-215">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="8413d-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8413d-216">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="8413d-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="8413d-217">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8413d-218">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-218">Command-line parameters</span></span> |
| [<span data-ttu-id="8413d-219">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="8413d-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8413d-220">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="8413d-220">Custom source</span></span> |
| [<span data-ttu-id="8413d-221">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8413d-222">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-222">Environment variables</span></span> |
| [<span data-ttu-id="8413d-223">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="8413d-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8413d-224">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="8413d-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8413d-225">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="8413d-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8413d-226">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="8413d-226">Directory files</span></span> |
| [<span data-ttu-id="8413d-227">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="8413d-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8413d-228">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="8413d-228">In-memory collections</span></span> |
| <span data-ttu-id="8413d-229">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="8413d-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8413d-230">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="8413d-230">File in the user profile directory</span></span> |

<span data-ttu-id="8413d-231">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8413d-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8413d-232">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, ve kterém je váš kód může uspořádat.</span><span class="sxs-lookup"><span data-stu-id="8413d-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="8413d-233">Seřazení zprostředkovatelů konfigurace v kódu tak, aby vyhovoval vašim prioritám pro základní zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="8413d-234">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="8413d-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8413d-235">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="8413d-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8413d-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8413d-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8413d-237">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="8413d-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8413d-238">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-238">Environment variables</span></span>
1. <span data-ttu-id="8413d-239">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-239">Command-line arguments</span></span>

<span data-ttu-id="8413d-240">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="8413d-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8413d-241">Předchozí sekvence zprostředkovatelů se používá, když inicializujete nového tvůrce hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8413d-242">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="8413d-243">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="8413d-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="8413d-244">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="8413d-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="8413d-245">`ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="8413d-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="8413d-246">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="8413d-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8413d-247">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="8413d-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8413d-248">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="8413d-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="8413d-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8413d-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8413d-250">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="8413d-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8413d-251">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8413d-252">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="8413d-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8413d-253">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="8413d-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8413d-254">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="8413d-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8413d-255">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="8413d-255">Consume configuration during app startup</span></span>

<span data-ttu-id="8413d-256">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8413d-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8413d-257">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="8413d-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8413d-258">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="8413d-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="8413d-259"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="8413d-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8413d-260">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="8413d-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8413d-261">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="8413d-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8413d-262">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8413d-263">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="8413d-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8413d-264">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8413d-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8413d-265">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8413d-266">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-266">Environment variables.</span></span>

<span data-ttu-id="8413d-267">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="8413d-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8413d-268">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="8413d-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8413d-269">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8413d-270">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="8413d-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8413d-271">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8413d-272">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="8413d-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8413d-273">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="8413d-273">**Example**</span></span>

<span data-ttu-id="8413d-274">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="8413d-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8413d-275">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="8413d-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8413d-276">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="8413d-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8413d-277">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8413d-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8413d-278">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="8413d-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8413d-279">Argumenty</span><span class="sxs-lookup"><span data-stu-id="8413d-279">Arguments</span></span>

<span data-ttu-id="8413d-280">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="8413d-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8413d-281">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="8413d-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8413d-282">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="8413d-282">Key prefix</span></span>               | <span data-ttu-id="8413d-283">Příklad</span><span class="sxs-lookup"><span data-stu-id="8413d-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8413d-284">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="8413d-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8413d-285">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="8413d-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="8413d-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8413d-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8413d-287">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="8413d-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="8413d-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8413d-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8413d-289">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="8413d-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8413d-290">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="8413d-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8413d-291">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="8413d-291">Switch mappings</span></span>

<span data-ttu-id="8413d-292">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="8413d-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8413d-293">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>můžete do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> zadat slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="8413d-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8413d-294">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8413d-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8413d-295">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8413d-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8413d-296">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="8413d-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8413d-297">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="8413d-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8413d-298">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="8413d-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8413d-299">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="8413d-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8413d-300">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="8413d-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8413d-301">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="8413d-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8413d-302">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="8413d-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8413d-303">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="8413d-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8413d-304">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8413d-305">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="8413d-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8413d-306">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8413d-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8413d-307">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-307">Key</span></span>       | <span data-ttu-id="8413d-308">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8413d-309">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="8413d-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8413d-310">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8413d-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8413d-311">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-311">Key</span></span>               | <span data-ttu-id="8413d-312">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8413d-313">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8413d-314"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="8413d-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8413d-315">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8413d-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8413d-317">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8413d-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8413d-318">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8413d-319">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8413d-320">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8413d-321">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="8413d-322">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="8413d-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8413d-323">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="8413d-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8413d-324">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8413d-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8413d-325">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8413d-326">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8413d-326">Command-line arguments.</span></span>

<span data-ttu-id="8413d-327">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="8413d-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8413d-328">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="8413d-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8413d-329">Pokud potřebujete zadat konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="8413d-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="8413d-330">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="8413d-330">**Example**</span></span>

<span data-ttu-id="8413d-331">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="8413d-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8413d-332">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-332">Run the sample app.</span></span> <span data-ttu-id="8413d-333">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8413d-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8413d-334">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="8413d-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8413d-335">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="8413d-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8413d-336">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8413d-337">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8413d-338">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="8413d-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8413d-339">Předpony</span><span class="sxs-lookup"><span data-stu-id="8413d-339">Prefixes</span></span>

<span data-ttu-id="8413d-340">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="8413d-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8413d-341">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8413d-342">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="8413d-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8413d-343">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8413d-344">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8413d-345">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="8413d-345">**Connection string prefixes**</span></span>

<span data-ttu-id="8413d-346">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8413d-347">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="8413d-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8413d-348">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="8413d-348">Connection string prefix</span></span> | <span data-ttu-id="8413d-349">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="8413d-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8413d-350">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="8413d-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8413d-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="8413d-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8413d-352">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="8413d-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8413d-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8413d-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8413d-354">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="8413d-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8413d-355">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="8413d-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8413d-356">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="8413d-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8413d-357">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="8413d-357">Environment variable key</span></span> | <span data-ttu-id="8413d-358">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="8413d-358">Converted configuration key</span></span> | <span data-ttu-id="8413d-359">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="8413d-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8413d-360">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="8413d-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8413d-361">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8413d-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8413d-362">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8413d-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8413d-363">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8413d-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8413d-364">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8413d-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8413d-365">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8413d-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8413d-366">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8413d-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="8413d-367">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="8413d-367">File Configuration Provider</span></span>

<span data-ttu-id="8413d-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="8413d-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8413d-369">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="8413d-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8413d-370">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="8413d-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8413d-371">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="8413d-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8413d-372">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="8413d-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8413d-373">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="8413d-373">INI Configuration Provider</span></span>

<span data-ttu-id="8413d-374"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="8413d-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8413d-375">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8413d-376">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="8413d-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8413d-377">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="8413d-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="8413d-378">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="8413d-378">Whether the file is optional.</span></span>
* <span data-ttu-id="8413d-379">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8413d-380"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8413d-381">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8413d-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8413d-382">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="8413d-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8413d-383">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="8413d-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8413d-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-384">section0:key0</span></span>
* <span data-ttu-id="8413d-385">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-385">section0:key1</span></span>
* <span data-ttu-id="8413d-386">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-386">section1:subsection:key</span></span>
* <span data-ttu-id="8413d-387">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-387">section2:subsection0:key</span></span>
* <span data-ttu-id="8413d-388">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8413d-389">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="8413d-389">JSON Configuration Provider</span></span>

<span data-ttu-id="8413d-390"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="8413d-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8413d-391">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8413d-392">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="8413d-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="8413d-393">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="8413d-393">Whether the file is optional.</span></span>
* <span data-ttu-id="8413d-394">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8413d-395"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8413d-396">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového tvůrce hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8413d-397">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="8413d-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8413d-398">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="8413d-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="8413d-399">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="8413d-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8413d-400">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="8413d-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8413d-401">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="8413d-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8413d-402">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="8413d-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8413d-403">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-403">Environment variables.</span></span>
* <span data-ttu-id="8413d-404">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8413d-405">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="8413d-405">Command-line arguments.</span></span>

<span data-ttu-id="8413d-406">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="8413d-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8413d-407">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="8413d-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8413d-408">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="8413d-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8413d-409">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="8413d-409">**Example**</span></span>

<span data-ttu-id="8413d-410">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje dvě volání `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="8413d-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="8413d-411">Konfigurace je načtena z souboru *appSettings. JSON* a *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="8413d-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="8413d-412">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-412">Run the sample app.</span></span> <span data-ttu-id="8413d-413">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8413d-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8413d-414">Všimněte si, že výstup obsahuje páry klíč-hodnota pro konfiguraci uvedenou v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="8413d-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="8413d-415">Konfigurační klíče protokolování používají dvojtečku (`:`) jako hierarchický oddělovač.</span><span class="sxs-lookup"><span data-stu-id="8413d-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="8413d-416">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-416">Key</span></span>                        | <span data-ttu-id="8413d-417">Hodnota vývoje</span><span class="sxs-lookup"><span data-stu-id="8413d-417">Development Value</span></span> | <span data-ttu-id="8413d-418">Produkční hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="8413d-419">Protokolování: LogLevel: systém</span><span class="sxs-lookup"><span data-stu-id="8413d-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="8413d-420">Information</span><span class="sxs-lookup"><span data-stu-id="8413d-420">Information</span></span>       | <span data-ttu-id="8413d-421">Information</span><span class="sxs-lookup"><span data-stu-id="8413d-421">Information</span></span>      |
| <span data-ttu-id="8413d-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="8413d-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="8413d-423">Information</span><span class="sxs-lookup"><span data-stu-id="8413d-423">Information</span></span>       | <span data-ttu-id="8413d-424">Information</span><span class="sxs-lookup"><span data-stu-id="8413d-424">Information</span></span>      |
| <span data-ttu-id="8413d-425">Protokolování: LogLevel: výchozí</span><span class="sxs-lookup"><span data-stu-id="8413d-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="8413d-426">Ladit</span><span class="sxs-lookup"><span data-stu-id="8413d-426">Debug</span></span>             | <span data-ttu-id="8413d-427">Chyba</span><span class="sxs-lookup"><span data-stu-id="8413d-427">Error</span></span>            |
| <span data-ttu-id="8413d-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="8413d-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="8413d-429">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="8413d-429">XML Configuration Provider</span></span>

<span data-ttu-id="8413d-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="8413d-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8413d-431">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8413d-432">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="8413d-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="8413d-433">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="8413d-433">Whether the file is optional.</span></span>
* <span data-ttu-id="8413d-434">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8413d-435"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8413d-436">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8413d-437">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8413d-438">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8413d-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8413d-439">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="8413d-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8413d-440">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="8413d-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8413d-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-441">section0:key0</span></span>
* <span data-ttu-id="8413d-442">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-442">section0:key1</span></span>
* <span data-ttu-id="8413d-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-443">section1:key0</span></span>
* <span data-ttu-id="8413d-444">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-444">section1:key1</span></span>

<span data-ttu-id="8413d-445">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="8413d-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8413d-446">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="8413d-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8413d-447">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-447">section:section0:key:key0</span></span>
* <span data-ttu-id="8413d-448">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8413d-448">section:section0:key:key1</span></span>
* <span data-ttu-id="8413d-449">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-449">section:section1:key:key0</span></span>
* <span data-ttu-id="8413d-450">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-450">section:section1:key:key1</span></span>

<span data-ttu-id="8413d-451">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="8413d-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8413d-452">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="8413d-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8413d-453">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="8413d-453">key:attribute</span></span>
* <span data-ttu-id="8413d-454">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="8413d-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8413d-455">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="8413d-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8413d-456"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8413d-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8413d-457">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-457">The key is the file name.</span></span> <span data-ttu-id="8413d-458">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="8413d-458">The value contains the file's contents.</span></span> <span data-ttu-id="8413d-459">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="8413d-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8413d-460">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8413d-461">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="8413d-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8413d-462">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="8413d-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="8413d-463">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="8413d-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8413d-464">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="8413d-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8413d-465">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="8413d-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8413d-466">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8413d-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8413d-467">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8413d-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8413d-468">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="8413d-468">Memory Configuration Provider</span></span>

<span data-ttu-id="8413d-469"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="8413d-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8413d-470">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="8413d-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8413d-471">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="8413d-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8413d-472">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="8413d-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8413d-473">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="8413d-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8413d-474">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8413d-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="8413d-475">GetValue</span></span>

<span data-ttu-id="8413d-476">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="8413d-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8413d-477">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8413d-477">An overload accepts a default value.</span></span>

<span data-ttu-id="8413d-478">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="8413d-478">The following example:</span></span>

* <span data-ttu-id="8413d-479">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="8413d-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8413d-480">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="8413d-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8413d-481">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="8413d-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="8413d-482">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="8413d-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8413d-483">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="8413d-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8413d-484">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="8413d-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8413d-485">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="8413d-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8413d-486">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8413d-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-487">section0:key0</span></span>
* <span data-ttu-id="8413d-488">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-488">section0:key1</span></span>
* <span data-ttu-id="8413d-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-489">section1:key0</span></span>
* <span data-ttu-id="8413d-490">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-490">section1:key1</span></span>
* <span data-ttu-id="8413d-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="8413d-492">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="8413d-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8413d-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="8413d-494">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="8413d-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8413d-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="8413d-495">GetSection</span></span>

<span data-ttu-id="8413d-496">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="8413d-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8413d-497">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="8413d-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8413d-498">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="8413d-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8413d-499">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="8413d-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8413d-500">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="8413d-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8413d-501">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="8413d-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8413d-502">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="8413d-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8413d-503">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="8413d-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8413d-504">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="8413d-504">GetChildren</span></span>

<span data-ttu-id="8413d-505">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8413d-506">Existuje</span><span class="sxs-lookup"><span data-stu-id="8413d-506">Exists</span></span>

<span data-ttu-id="8413d-507">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="8413d-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8413d-508">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="8413d-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="8413d-509">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="8413d-509">Bind to a class</span></span>

<span data-ttu-id="8413d-510">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="8413d-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="8413d-511">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8413d-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8413d-512">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="8413d-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="8413d-513">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="8413d-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8413d-514">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="8413d-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="8413d-515">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="8413d-516">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-516">Key</span></span>                   | <span data-ttu-id="8413d-517">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="8413d-518">Starship: název</span><span class="sxs-lookup"><span data-stu-id="8413d-518">starship:name</span></span>         | <span data-ttu-id="8413d-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="8413d-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="8413d-520">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="8413d-520">starship:registry</span></span>     | <span data-ttu-id="8413d-521">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="8413d-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="8413d-522">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="8413d-522">starship:class</span></span>        | <span data-ttu-id="8413d-523">Založen</span><span class="sxs-lookup"><span data-stu-id="8413d-523">Constitution</span></span>                                      |
| <span data-ttu-id="8413d-524">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="8413d-524">starship:length</span></span>       | <span data-ttu-id="8413d-525">304,8</span><span class="sxs-lookup"><span data-stu-id="8413d-525">304.8</span></span>                                             |
| <span data-ttu-id="8413d-526">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="8413d-526">starship:commissioned</span></span> | <span data-ttu-id="8413d-527">Nepravda</span><span class="sxs-lookup"><span data-stu-id="8413d-527">False</span></span>                                             |
| <span data-ttu-id="8413d-528">Patka</span><span class="sxs-lookup"><span data-stu-id="8413d-528">trademark</span></span>             | <span data-ttu-id="8413d-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="8413d-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="8413d-530">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="8413d-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="8413d-531">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="8413d-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="8413d-532">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="8413d-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="8413d-533">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="8413d-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8413d-534">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="8413d-534">Bind to an object graph</span></span>

<span data-ttu-id="8413d-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="8413d-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="8413d-536">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="8413d-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8413d-537">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="8413d-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="8413d-538">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8413d-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8413d-539">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="8413d-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8413d-540">[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="8413d-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8413d-541">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8413d-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8413d-542">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="8413d-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8413d-543">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="8413d-543">Bind an array to a class</span></span>

<span data-ttu-id="8413d-544">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="8413d-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8413d-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="8413d-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8413d-546">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="8413d-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8413d-547">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="8413d-547">Binding is provided by convention.</span></span> <span data-ttu-id="8413d-548">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="8413d-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8413d-549">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="8413d-549">**In-memory array processing**</span></span>

<span data-ttu-id="8413d-550">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="8413d-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8413d-551">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-551">Key</span></span>             | <span data-ttu-id="8413d-552">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8413d-553">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="8413d-553">array:entries:0</span></span> | <span data-ttu-id="8413d-554">value0</span><span class="sxs-lookup"><span data-stu-id="8413d-554">value0</span></span> |
| <span data-ttu-id="8413d-555">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="8413d-555">array:entries:1</span></span> | <span data-ttu-id="8413d-556">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="8413d-556">value1</span></span> |
| <span data-ttu-id="8413d-557">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="8413d-557">array:entries:2</span></span> | <span data-ttu-id="8413d-558">Argument</span><span class="sxs-lookup"><span data-stu-id="8413d-558">value2</span></span> |
| <span data-ttu-id="8413d-559">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="8413d-559">array:entries:4</span></span> | <span data-ttu-id="8413d-560">value4</span><span class="sxs-lookup"><span data-stu-id="8413d-560">value4</span></span> |
| <span data-ttu-id="8413d-561">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="8413d-561">array:entries:5</span></span> | <span data-ttu-id="8413d-562">value5</span><span class="sxs-lookup"><span data-stu-id="8413d-562">value5</span></span> |

<span data-ttu-id="8413d-563">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="8413d-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="8413d-564">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="8413d-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8413d-565">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="8413d-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8413d-566">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="8413d-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8413d-567">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="8413d-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8413d-568">[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="8413d-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="8413d-569">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8413d-570">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8413d-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8413d-571">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8413d-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8413d-572">0</span><span class="sxs-lookup"><span data-stu-id="8413d-572">0</span></span>                            | <span data-ttu-id="8413d-573">value0</span><span class="sxs-lookup"><span data-stu-id="8413d-573">value0</span></span>                       |
| <span data-ttu-id="8413d-574">1</span><span class="sxs-lookup"><span data-stu-id="8413d-574">1</span></span>                            | <span data-ttu-id="8413d-575">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="8413d-575">value1</span></span>                       |
| <span data-ttu-id="8413d-576">2</span><span class="sxs-lookup"><span data-stu-id="8413d-576">2</span></span>                            | <span data-ttu-id="8413d-577">Argument</span><span class="sxs-lookup"><span data-stu-id="8413d-577">value2</span></span>                       |
| <span data-ttu-id="8413d-578">3</span><span class="sxs-lookup"><span data-stu-id="8413d-578">3</span></span>                            | <span data-ttu-id="8413d-579">value4</span><span class="sxs-lookup"><span data-stu-id="8413d-579">value4</span></span>                       |
| <span data-ttu-id="8413d-580">4</span><span class="sxs-lookup"><span data-stu-id="8413d-580">4</span></span>                            | <span data-ttu-id="8413d-581">value5</span><span class="sxs-lookup"><span data-stu-id="8413d-581">value5</span></span>                       |

<span data-ttu-id="8413d-582">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="8413d-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8413d-583">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="8413d-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8413d-584">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="8413d-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8413d-585">Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="8413d-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8413d-586">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="8413d-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8413d-587">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8413d-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8413d-588">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8413d-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8413d-589">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8413d-590">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-590">Key</span></span>             | <span data-ttu-id="8413d-591">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8413d-592">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="8413d-592">array:entries:3</span></span> | <span data-ttu-id="8413d-593">hodnota3</span><span class="sxs-lookup"><span data-stu-id="8413d-593">value3</span></span> |

<span data-ttu-id="8413d-594">Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="8413d-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8413d-595">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8413d-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8413d-596">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="8413d-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8413d-597">0</span><span class="sxs-lookup"><span data-stu-id="8413d-597">0</span></span>                            | <span data-ttu-id="8413d-598">value0</span><span class="sxs-lookup"><span data-stu-id="8413d-598">value0</span></span>                       |
| <span data-ttu-id="8413d-599">1</span><span class="sxs-lookup"><span data-stu-id="8413d-599">1</span></span>                            | <span data-ttu-id="8413d-600">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="8413d-600">value1</span></span>                       |
| <span data-ttu-id="8413d-601">2</span><span class="sxs-lookup"><span data-stu-id="8413d-601">2</span></span>                            | <span data-ttu-id="8413d-602">Argument</span><span class="sxs-lookup"><span data-stu-id="8413d-602">value2</span></span>                       |
| <span data-ttu-id="8413d-603">3</span><span class="sxs-lookup"><span data-stu-id="8413d-603">3</span></span>                            | <span data-ttu-id="8413d-604">hodnota3</span><span class="sxs-lookup"><span data-stu-id="8413d-604">value3</span></span>                       |
| <span data-ttu-id="8413d-605">4</span><span class="sxs-lookup"><span data-stu-id="8413d-605">4</span></span>                            | <span data-ttu-id="8413d-606">value4</span><span class="sxs-lookup"><span data-stu-id="8413d-606">value4</span></span>                       |
| <span data-ttu-id="8413d-607">5</span><span class="sxs-lookup"><span data-stu-id="8413d-607">5</span></span>                            | <span data-ttu-id="8413d-608">value5</span><span class="sxs-lookup"><span data-stu-id="8413d-608">value5</span></span>                       |

<span data-ttu-id="8413d-609">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="8413d-609">**JSON array processing**</span></span>

<span data-ttu-id="8413d-610">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="8413d-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8413d-611">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="8413d-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="8413d-612">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="8413d-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8413d-613">Klíč</span><span class="sxs-lookup"><span data-stu-id="8413d-613">Key</span></span>                     | <span data-ttu-id="8413d-614">Hodnota</span><span class="sxs-lookup"><span data-stu-id="8413d-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8413d-615">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8413d-615">json_array:key</span></span>          | <span data-ttu-id="8413d-616">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="8413d-616">valueA</span></span> |
| <span data-ttu-id="8413d-617">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="8413d-617">json_array:subsection:0</span></span> | <span data-ttu-id="8413d-618">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="8413d-618">valueB</span></span> |
| <span data-ttu-id="8413d-619">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8413d-619">json_array:subsection:1</span></span> | <span data-ttu-id="8413d-620">valueC</span><span class="sxs-lookup"><span data-stu-id="8413d-620">valueC</span></span> |
| <span data-ttu-id="8413d-621">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8413d-621">json_array:subsection:2</span></span> | <span data-ttu-id="8413d-622">s</span><span class="sxs-lookup"><span data-stu-id="8413d-622">valueD</span></span> |

<span data-ttu-id="8413d-623">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="8413d-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8413d-624">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="8413d-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8413d-625">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="8413d-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8413d-626">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="8413d-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8413d-627">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="8413d-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8413d-628">0</span><span class="sxs-lookup"><span data-stu-id="8413d-628">0</span></span>                                   | <span data-ttu-id="8413d-629">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="8413d-629">valueB</span></span>                              |
| <span data-ttu-id="8413d-630">1</span><span class="sxs-lookup"><span data-stu-id="8413d-630">1</span></span>                                   | <span data-ttu-id="8413d-631">valueC</span><span class="sxs-lookup"><span data-stu-id="8413d-631">valueC</span></span>                              |
| <span data-ttu-id="8413d-632">2</span><span class="sxs-lookup"><span data-stu-id="8413d-632">2</span></span>                                   | <span data-ttu-id="8413d-633">s</span><span class="sxs-lookup"><span data-stu-id="8413d-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8413d-634">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="8413d-634">Custom configuration provider</span></span>

<span data-ttu-id="8413d-635">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="8413d-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8413d-636">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="8413d-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8413d-637">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="8413d-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8413d-638">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="8413d-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8413d-639">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="8413d-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8413d-640">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="8413d-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8413d-641">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8413d-642">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="8413d-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8413d-643">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8413d-644">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="8413d-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8413d-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8413d-646">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="8413d-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8413d-647">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8413d-648">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8413d-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8413d-649">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="8413d-649">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8413d-650">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="8413d-650">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8413d-651">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8413d-652">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-652">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8413d-653">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-653">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8413d-654">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-654">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8413d-655">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-655">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8413d-656">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="8413d-656">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8413d-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8413d-658">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="8413d-658">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8413d-659">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8413d-660">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8413d-660">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8413d-661">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="8413d-661">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8413d-662">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8413d-663">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="8413d-663">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8413d-664">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-664">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8413d-665">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8413d-665">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8413d-666">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="8413d-666">Access configuration during startup</span></span>

<span data-ttu-id="8413d-667">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8413d-667">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8413d-668">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="8413d-668">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8413d-669">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8413d-669">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8413d-670">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="8413d-670">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8413d-671">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8413d-671">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8413d-672">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="8413d-672">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8413d-673">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="8413d-673">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8413d-674">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="8413d-674">Add configuration from an external assembly</span></span>

<span data-ttu-id="8413d-675">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace.</span><span class="sxs-lookup"><span data-stu-id="8413d-675">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8413d-676">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8413d-676">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8413d-677">Další zdroje informací:</span><span class="sxs-lookup"><span data-stu-id="8413d-677">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
