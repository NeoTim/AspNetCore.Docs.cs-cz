---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: c63609cfb91a1668b8e125c54fcfecf5f4ec259b
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143358"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="cd240-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd240-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="cd240-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cd240-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cd240-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="cd240-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="cd240-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="cd240-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cd240-107">Azure Key Vault</span></span>
* <span data-ttu-id="cd240-108">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="cd240-108">Azure App Configuration</span></span>
* <span data-ttu-id="cd240-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-109">Command-line arguments</span></span>
* <span data-ttu-id="cd240-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="cd240-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="cd240-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="cd240-111">Directory files</span></span>
* <span data-ttu-id="cd240-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-112">Environment variables</span></span>
* <span data-ttu-id="cd240-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="cd240-113">In-memory .NET objects</span></span>
* <span data-ttu-id="cd240-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="cd240-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd240-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cd240-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd240-116">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="cd240-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="cd240-117">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="cd240-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="cd240-118">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="cd240-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="cd240-119">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="cd240-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="cd240-120">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="cd240-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="cd240-121">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd240-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="cd240-122">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="cd240-122">Host versus app configuration</span></span>

<span data-ttu-id="cd240-123">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="cd240-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="cd240-124">Hostitel zodpovídá za spuštění aplikace a správu životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="cd240-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="cd240-125">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="cd240-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="cd240-126">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="cd240-127">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="cd240-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="cd240-128">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd240-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd240-129">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="cd240-130">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="cd240-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="cd240-131">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="cd240-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="cd240-132">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="cd240-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="cd240-133">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="cd240-134">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="cd240-135">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="cd240-136">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="cd240-137">Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="cd240-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="cd240-138">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="cd240-139">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="cd240-140">Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="cd240-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="cd240-141">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="cd240-141">Enable IIS integration.</span></span>
* <span data-ttu-id="cd240-142">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="cd240-143">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-144">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-145">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="cd240-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="cd240-146">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-147">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd240-148">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="cd240-149">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="cd240-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="cd240-150">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="cd240-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="cd240-151">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="cd240-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="cd240-152">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="cd240-153">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="cd240-154">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="cd240-155">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="cd240-156">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="cd240-157">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-158">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-159">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="cd240-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="cd240-160">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="cd240-161">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="cd240-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="cd240-162">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="cd240-162">Security</span></span>

<span data-ttu-id="cd240-163">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="cd240-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="cd240-164">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="cd240-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="cd240-165">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="cd240-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="cd240-166">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="cd240-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="cd240-167">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="cd240-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="cd240-168"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="cd240-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="cd240-169">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="cd240-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="cd240-170">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="cd240-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="cd240-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="cd240-172">Další informace najdete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cd240-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="cd240-173">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="cd240-173">Hierarchical configuration data</span></span>

<span data-ttu-id="cd240-174">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="cd240-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="cd240-175">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="cd240-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="cd240-176">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="cd240-177">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="cd240-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="cd240-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-178">section0:key0</span></span>
* <span data-ttu-id="cd240-179">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-179">section0:key1</span></span>
* <span data-ttu-id="cd240-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-180">section1:key0</span></span>
* <span data-ttu-id="cd240-181">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-181">section1:key1</span></span>

<span data-ttu-id="cd240-182">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="cd240-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="cd240-183">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="cd240-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="cd240-184">Konvence</span><span class="sxs-lookup"><span data-stu-id="cd240-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="cd240-185">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="cd240-185">Sources and providers</span></span>

<span data-ttu-id="cd240-186">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="cd240-187">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="cd240-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="cd240-188">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="cd240-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="cd240-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="cd240-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> pro získání konfigurace pro třídu:</span><span class="sxs-lookup"><span data-stu-id="cd240-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

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

<span data-ttu-id="cd240-191">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="cd240-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="cd240-192">Klíče</span><span class="sxs-lookup"><span data-stu-id="cd240-192">Keys</span></span>

<span data-ttu-id="cd240-193">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="cd240-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="cd240-194">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="cd240-194">Keys are case-insensitive.</span></span> <span data-ttu-id="cd240-195">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="cd240-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="cd240-196">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="cd240-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="cd240-197">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="cd240-197">Hierarchical keys</span></span>
  * <span data-ttu-id="cd240-198">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="cd240-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="cd240-199">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="cd240-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="cd240-200">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="cd240-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="cd240-201">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="cd240-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="cd240-202">Pokud jsou tajné klíče načteny do konfigurace aplikace, je nutné zadat kód, který nahradí pomlčky dvojtečkou.</span><span class="sxs-lookup"><span data-stu-id="cd240-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="cd240-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="cd240-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="cd240-204">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="cd240-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="cd240-205">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="cd240-205">Values</span></span>

<span data-ttu-id="cd240-206">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="cd240-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="cd240-207">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="cd240-207">Values are strings.</span></span>
* <span data-ttu-id="cd240-208">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="cd240-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="cd240-209">Dodavateli</span><span class="sxs-lookup"><span data-stu-id="cd240-209">Providers</span></span>

<span data-ttu-id="cd240-210">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="cd240-211">Zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="cd240-211">Provider</span></span> | <span data-ttu-id="cd240-212">Poskytuje konfiguraci z &hellip;</span><span class="sxs-lookup"><span data-stu-id="cd240-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="cd240-213">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="cd240-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="cd240-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cd240-214">Azure Key Vault</span></span> |
| <span data-ttu-id="cd240-215">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="cd240-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="cd240-216">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="cd240-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="cd240-217">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="cd240-218">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-218">Command-line parameters</span></span> |
| [<span data-ttu-id="cd240-219">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd240-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="cd240-220">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="cd240-220">Custom source</span></span> |
| [<span data-ttu-id="cd240-221">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="cd240-222">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-222">Environment variables</span></span> |
| [<span data-ttu-id="cd240-223">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="cd240-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="cd240-224">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="cd240-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="cd240-225">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="cd240-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="cd240-226">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="cd240-226">Directory files</span></span> |
| [<span data-ttu-id="cd240-227">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="cd240-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="cd240-228">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="cd240-228">In-memory collections</span></span> |
| <span data-ttu-id="cd240-229">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="cd240-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="cd240-230">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="cd240-230">File in the user profile directory</span></span> |

<span data-ttu-id="cd240-231">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd240-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="cd240-232">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, ve kterém je váš kód může uspořádat.</span><span class="sxs-lookup"><span data-stu-id="cd240-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="cd240-233">Seřazení zprostředkovatelů konfigurace v kódu tak, aby vyhovoval vašim prioritám pro základní zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="cd240-234">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="cd240-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="cd240-235">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="cd240-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="cd240-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="cd240-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="cd240-237">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="cd240-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="cd240-238">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-238">Environment variables</span></span>
1. <span data-ttu-id="cd240-239">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-239">Command-line arguments</span></span>

<span data-ttu-id="cd240-240">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="cd240-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="cd240-241">Předchozí sekvence zprostředkovatelů se používá, když inicializujete nového tvůrce hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cd240-242">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="cd240-243">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="cd240-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="cd240-244">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="cd240-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="cd240-245">`ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="cd240-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="cd240-246">`ConfigureHostConfiguration` se dá volat víckrát s přičítáním výsledků.</span><span class="sxs-lookup"><span data-stu-id="cd240-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="cd240-247">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="cd240-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="cd240-248">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="cd240-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="cd240-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="cd240-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="cd240-250">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="cd240-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="cd240-251">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="cd240-252">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="cd240-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="cd240-253">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="cd240-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="cd240-254">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="cd240-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="cd240-255">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="cd240-255">Consume configuration during app startup</span></span>

<span data-ttu-id="cd240-256">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd240-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd240-257">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="cd240-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="cd240-258">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="cd240-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="cd240-259"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="cd240-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="cd240-260">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="cd240-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="cd240-261">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="cd240-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="cd240-262">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="cd240-263">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="cd240-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="cd240-264">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cd240-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="cd240-265">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="cd240-266">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-266">Environment variables.</span></span>

<span data-ttu-id="cd240-267">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="cd240-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="cd240-268">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="cd240-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="cd240-269">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="cd240-270">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="cd240-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="cd240-271">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cd240-272">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="cd240-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="cd240-273">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="cd240-273">**Example**</span></span>

<span data-ttu-id="cd240-274">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="cd240-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="cd240-275">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="cd240-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="cd240-276">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="cd240-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="cd240-277">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="cd240-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="cd240-278">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="cd240-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="cd240-279">Arguments</span><span class="sxs-lookup"><span data-stu-id="cd240-279">Arguments</span></span>

<span data-ttu-id="cd240-280">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="cd240-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="cd240-281">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="cd240-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="cd240-282">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="cd240-282">Key prefix</span></span>               | <span data-ttu-id="cd240-283">Příklad</span><span class="sxs-lookup"><span data-stu-id="cd240-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="cd240-284">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="cd240-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="cd240-285">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="cd240-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="cd240-286">`--CommandLineKey2=value2` `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="cd240-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="cd240-287">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="cd240-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="cd240-288">`/CommandLineKey3=value3` `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="cd240-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="cd240-289">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="cd240-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="cd240-290">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="cd240-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="cd240-291">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="cd240-291">Switch mappings</span></span>

<span data-ttu-id="cd240-292">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="cd240-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="cd240-293">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> můžete do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> zadat slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="cd240-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="cd240-294">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="cd240-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="cd240-295">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="cd240-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="cd240-296">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="cd240-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="cd240-297">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="cd240-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="cd240-298">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="cd240-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="cd240-299">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="cd240-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="cd240-300">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="cd240-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="cd240-301">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="cd240-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="cd240-302">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="cd240-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="cd240-303">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="cd240-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="cd240-304">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cd240-305">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="cd240-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="cd240-306">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="cd240-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="cd240-307">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-307">Key</span></span>       | <span data-ttu-id="cd240-308">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="cd240-309">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="cd240-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="cd240-310">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="cd240-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="cd240-311">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-311">Key</span></span>               | <span data-ttu-id="cd240-312">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="cd240-313">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="cd240-314"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="cd240-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="cd240-315">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="cd240-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) umožňuje nastavit proměnné prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="cd240-317">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="cd240-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd240-318">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="cd240-319">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd240-320">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="cd240-321">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="cd240-322">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="cd240-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="cd240-323">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="cd240-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="cd240-324">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cd240-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="cd240-325">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="cd240-326">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="cd240-326">Command-line arguments.</span></span>

<span data-ttu-id="cd240-327">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="cd240-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="cd240-328">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="cd240-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="cd240-329">Pokud potřebujete zadat konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou.</span><span class="sxs-lookup"><span data-stu-id="cd240-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

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

<span data-ttu-id="cd240-330">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="cd240-330">**Example**</span></span>

<span data-ttu-id="cd240-331">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="cd240-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="cd240-332">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd240-332">Run the sample app.</span></span> <span data-ttu-id="cd240-333">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="cd240-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="cd240-334">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="cd240-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="cd240-335">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd240-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="cd240-336">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="cd240-337">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="cd240-338">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="cd240-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="cd240-339">Předpony</span><span class="sxs-lookup"><span data-stu-id="cd240-339">Prefixes</span></span>

<span data-ttu-id="cd240-340">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="cd240-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="cd240-341">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="cd240-342">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="cd240-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="cd240-343">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="cd240-344">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="cd240-345">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="cd240-345">**Connection string prefixes**</span></span>

<span data-ttu-id="cd240-346">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="cd240-347">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables` zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="cd240-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="cd240-348">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="cd240-348">Connection string prefix</span></span> | <span data-ttu-id="cd240-349">Zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="cd240-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="cd240-350">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="cd240-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="cd240-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="cd240-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="cd240-352">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="cd240-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="cd240-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="cd240-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="cd240-354">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="cd240-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="cd240-355">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="cd240-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="cd240-356">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="cd240-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="cd240-357">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="cd240-357">Environment variable key</span></span> | <span data-ttu-id="cd240-358">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="cd240-358">Converted configuration key</span></span> | <span data-ttu-id="cd240-359">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="cd240-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="cd240-360">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="cd240-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="cd240-361">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="cd240-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="cd240-362">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="cd240-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="cd240-363">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="cd240-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="cd240-364">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="cd240-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="cd240-365">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="cd240-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="cd240-366">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="cd240-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="cd240-367">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="cd240-367">File Configuration Provider</span></span>

<span data-ttu-id="cd240-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="cd240-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="cd240-369">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="cd240-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="cd240-370">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="cd240-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="cd240-371">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="cd240-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="cd240-372">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="cd240-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="cd240-373">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="cd240-373">INI Configuration Provider</span></span>

<span data-ttu-id="cd240-374"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="cd240-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="cd240-375">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="cd240-376">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="cd240-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="cd240-377">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="cd240-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="cd240-378">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="cd240-378">Whether the file is optional.</span></span>
* <span data-ttu-id="cd240-379">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="cd240-380"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="cd240-381">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="cd240-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="cd240-382">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="cd240-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="cd240-383">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="cd240-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="cd240-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-384">section0:key0</span></span>
* <span data-ttu-id="cd240-385">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-385">section0:key1</span></span>
* <span data-ttu-id="cd240-386">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="cd240-386">section1:subsection:key</span></span>
* <span data-ttu-id="cd240-387">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="cd240-387">section2:subsection0:key</span></span>
* <span data-ttu-id="cd240-388">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="cd240-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="cd240-389">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="cd240-389">JSON Configuration Provider</span></span>

<span data-ttu-id="cd240-390"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="cd240-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="cd240-391">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="cd240-392">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="cd240-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="cd240-393">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="cd240-393">Whether the file is optional.</span></span>
* <span data-ttu-id="cd240-394">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="cd240-395"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="cd240-396">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového tvůrce hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="cd240-397">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="cd240-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="cd240-398">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="cd240-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="cd240-399">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="cd240-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="cd240-400">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="cd240-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="cd240-401">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="cd240-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="cd240-402">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="cd240-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="cd240-403">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-403">Environment variables.</span></span>
* <span data-ttu-id="cd240-404">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="cd240-405">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="cd240-405">Command-line arguments.</span></span>

<span data-ttu-id="cd240-406">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="cd240-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="cd240-407">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="cd240-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="cd240-408">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cd240-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="cd240-409">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="cd240-409">**Example**</span></span>

<span data-ttu-id="cd240-410">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje dvě volání `AddJsonFile`.</span><span class="sxs-lookup"><span data-stu-id="cd240-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="cd240-411">Konfigurace je načtena z souboru *appSettings. JSON* a *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="cd240-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="cd240-412">Spusťte ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cd240-412">Run the sample app.</span></span> <span data-ttu-id="cd240-413">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="cd240-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="cd240-414">Všimněte si, že výstup obsahuje páry klíč-hodnota pro konfiguraci uvedenou v tabulce v závislosti na prostředí.</span><span class="sxs-lookup"><span data-stu-id="cd240-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="cd240-415">Konfigurační klíče protokolování používají dvojtečku (`:`) jako hierarchický oddělovač.</span><span class="sxs-lookup"><span data-stu-id="cd240-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="cd240-416">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-416">Key</span></span>                        | <span data-ttu-id="cd240-417">Hodnota vývoje</span><span class="sxs-lookup"><span data-stu-id="cd240-417">Development Value</span></span> | <span data-ttu-id="cd240-418">Produkční hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="cd240-419">Protokolování: LogLevel: systém</span><span class="sxs-lookup"><span data-stu-id="cd240-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="cd240-420">Informace o</span><span class="sxs-lookup"><span data-stu-id="cd240-420">Information</span></span>       | <span data-ttu-id="cd240-421">Informace o</span><span class="sxs-lookup"><span data-stu-id="cd240-421">Information</span></span>      |
| <span data-ttu-id="cd240-422">Protokolování: LogLevel: Microsoft</span><span class="sxs-lookup"><span data-stu-id="cd240-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="cd240-423">Informace o</span><span class="sxs-lookup"><span data-stu-id="cd240-423">Information</span></span>       | <span data-ttu-id="cd240-424">Informace o</span><span class="sxs-lookup"><span data-stu-id="cd240-424">Information</span></span>      |
| <span data-ttu-id="cd240-425">Protokolování: LogLevel: výchozí</span><span class="sxs-lookup"><span data-stu-id="cd240-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="cd240-426">Ladit</span><span class="sxs-lookup"><span data-stu-id="cd240-426">Debug</span></span>             | <span data-ttu-id="cd240-427">Chyba</span><span class="sxs-lookup"><span data-stu-id="cd240-427">Error</span></span>            |
| <span data-ttu-id="cd240-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="cd240-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="cd240-429">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="cd240-429">XML Configuration Provider</span></span>

<span data-ttu-id="cd240-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="cd240-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="cd240-431">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="cd240-432">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="cd240-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="cd240-433">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="cd240-433">Whether the file is optional.</span></span>
* <span data-ttu-id="cd240-434">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="cd240-435"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="cd240-436">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="cd240-437">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="cd240-438">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="cd240-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="cd240-439">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="cd240-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="cd240-440">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="cd240-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="cd240-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-441">section0:key0</span></span>
* <span data-ttu-id="cd240-442">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-442">section0:key1</span></span>
* <span data-ttu-id="cd240-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-443">section1:key0</span></span>
* <span data-ttu-id="cd240-444">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-444">section1:key1</span></span>

<span data-ttu-id="cd240-445">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="cd240-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="cd240-446">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="cd240-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="cd240-447">oddíl: section0: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="cd240-447">section:section0:key:key0</span></span>
* <span data-ttu-id="cd240-448">oddíl: section0: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-448">section:section0:key:key1</span></span>
* <span data-ttu-id="cd240-449">oddíl: Section1: Key: Key0</span><span class="sxs-lookup"><span data-stu-id="cd240-449">section:section1:key:key0</span></span>
* <span data-ttu-id="cd240-450">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-450">section:section1:key:key1</span></span>

<span data-ttu-id="cd240-451">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="cd240-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="cd240-452">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="cd240-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="cd240-453">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="cd240-453">key:attribute</span></span>
* <span data-ttu-id="cd240-454">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="cd240-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="cd240-455">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="cd240-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="cd240-456"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="cd240-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="cd240-457">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-457">The key is the file name.</span></span> <span data-ttu-id="cd240-458">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="cd240-458">The value contains the file's contents.</span></span> <span data-ttu-id="cd240-459">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="cd240-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="cd240-460">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="cd240-461">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="cd240-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="cd240-462">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="cd240-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="cd240-463">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="cd240-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="cd240-464">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="cd240-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="cd240-465">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="cd240-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="cd240-466">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="cd240-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="cd240-467">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="cd240-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="cd240-468">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="cd240-468">Memory Configuration Provider</span></span>

<span data-ttu-id="cd240-469"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="cd240-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="cd240-470">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="cd240-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="cd240-471">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="cd240-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="cd240-472">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="cd240-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="cd240-473">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="cd240-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="cd240-474">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="cd240-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="cd240-475">GetValue</span></span>

<span data-ttu-id="cd240-476">[ConfigurationBinder. GetValue \<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="cd240-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="cd240-477">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="cd240-477">An overload accepts a default value.</span></span>

<span data-ttu-id="cd240-478">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="cd240-478">The following example:</span></span>

* <span data-ttu-id="cd240-479">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="cd240-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="cd240-480">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="cd240-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="cd240-481">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="cd240-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="cd240-482">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="cd240-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="cd240-483">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="cd240-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="cd240-484">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="cd240-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="cd240-485">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="cd240-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="cd240-486">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="cd240-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-487">section0:key0</span></span>
* <span data-ttu-id="cd240-488">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-488">section0:key1</span></span>
* <span data-ttu-id="cd240-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-489">section1:key0</span></span>
* <span data-ttu-id="cd240-490">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-490">section1:key1</span></span>
* <span data-ttu-id="cd240-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="cd240-492">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="cd240-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="cd240-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="cd240-494">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="cd240-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="cd240-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="cd240-495">GetSection</span></span>

<span data-ttu-id="cd240-496">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="cd240-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="cd240-497">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="cd240-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="cd240-498">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="cd240-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="cd240-499">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="cd240-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="cd240-500">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="cd240-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="cd240-501">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="cd240-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="cd240-502">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="cd240-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="cd240-503">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="cd240-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="cd240-504">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="cd240-504">GetChildren</span></span>

<span data-ttu-id="cd240-505">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="cd240-506">Neexistuje</span><span class="sxs-lookup"><span data-stu-id="cd240-506">Exists</span></span>

<span data-ttu-id="cd240-507">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="cd240-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="cd240-508">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="cd240-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="cd240-509">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="cd240-509">Bind to a class</span></span>

<span data-ttu-id="cd240-510">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="cd240-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="cd240-511">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="cd240-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="cd240-512">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="cd240-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="cd240-513">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="cd240-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="cd240-514">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="cd240-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="cd240-515">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="cd240-516">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-516">Key</span></span>                   | <span data-ttu-id="cd240-517">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="cd240-518">Starship: název</span><span class="sxs-lookup"><span data-stu-id="cd240-518">starship:name</span></span>         | <span data-ttu-id="cd240-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="cd240-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="cd240-520">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="cd240-520">starship:registry</span></span>     | <span data-ttu-id="cd240-521">1701</span><span class="sxs-lookup"><span data-stu-id="cd240-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="cd240-522">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="cd240-522">starship:class</span></span>        | <span data-ttu-id="cd240-523">Založen</span><span class="sxs-lookup"><span data-stu-id="cd240-523">Constitution</span></span>                                      |
| <span data-ttu-id="cd240-524">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="cd240-524">starship:length</span></span>       | <span data-ttu-id="cd240-525">304,8</span><span class="sxs-lookup"><span data-stu-id="cd240-525">304.8</span></span>                                             |
| <span data-ttu-id="cd240-526">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="cd240-526">starship:commissioned</span></span> | <span data-ttu-id="cd240-527">False</span><span class="sxs-lookup"><span data-stu-id="cd240-527">False</span></span>                                             |
| <span data-ttu-id="cd240-528">Patka</span><span class="sxs-lookup"><span data-stu-id="cd240-528">trademark</span></span>             | <span data-ttu-id="cd240-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="cd240-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="cd240-530">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="cd240-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="cd240-531">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="cd240-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="cd240-532">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="cd240-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="cd240-533">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="cd240-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="cd240-534">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="cd240-534">Bind to an object graph</span></span>

<span data-ttu-id="cd240-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="cd240-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="cd240-536">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="cd240-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="cd240-537">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="cd240-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="cd240-538">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="cd240-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="cd240-539">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="cd240-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="cd240-540">[ConfigurationBinder. Get \<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) váže a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="cd240-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="cd240-541">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="cd240-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="cd240-542">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="cd240-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="cd240-543">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="cd240-543">Bind an array to a class</span></span>

<span data-ttu-id="cd240-544">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="cd240-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="cd240-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="cd240-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="cd240-546">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="cd240-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="cd240-547">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="cd240-547">Binding is provided by convention.</span></span> <span data-ttu-id="cd240-548">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="cd240-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="cd240-549">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="cd240-549">**In-memory array processing**</span></span>

<span data-ttu-id="cd240-550">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="cd240-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="cd240-551">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-551">Key</span></span>             | <span data-ttu-id="cd240-552">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="cd240-553">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="cd240-553">array:entries:0</span></span> | <span data-ttu-id="cd240-554">value0</span><span class="sxs-lookup"><span data-stu-id="cd240-554">value0</span></span> |
| <span data-ttu-id="cd240-555">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="cd240-555">array:entries:1</span></span> | <span data-ttu-id="cd240-556">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="cd240-556">value1</span></span> |
| <span data-ttu-id="cd240-557">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="cd240-557">array:entries:2</span></span> | <span data-ttu-id="cd240-558">Argument</span><span class="sxs-lookup"><span data-stu-id="cd240-558">value2</span></span> |
| <span data-ttu-id="cd240-559">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="cd240-559">array:entries:4</span></span> | <span data-ttu-id="cd240-560">value4</span><span class="sxs-lookup"><span data-stu-id="cd240-560">value4</span></span> |
| <span data-ttu-id="cd240-561">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="cd240-561">array:entries:5</span></span> | <span data-ttu-id="cd240-562">value5</span><span class="sxs-lookup"><span data-stu-id="cd240-562">value5</span></span> |

<span data-ttu-id="cd240-563">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="cd240-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="cd240-564">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="cd240-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="cd240-565">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="cd240-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="cd240-566">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="cd240-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="cd240-567">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="cd240-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="cd240-568">Lze také použít syntaxi [ConfigurationBinder. Get \<T >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) , která má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="cd240-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="cd240-569">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="cd240-570">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="cd240-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="cd240-571">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="cd240-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="cd240-572">0,8</span><span class="sxs-lookup"><span data-stu-id="cd240-572">0</span></span>                            | <span data-ttu-id="cd240-573">value0</span><span class="sxs-lookup"><span data-stu-id="cd240-573">value0</span></span>                       |
| <span data-ttu-id="cd240-574">první</span><span class="sxs-lookup"><span data-stu-id="cd240-574">1</span></span>                            | <span data-ttu-id="cd240-575">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="cd240-575">value1</span></span>                       |
| <span data-ttu-id="cd240-576">odst</span><span class="sxs-lookup"><span data-stu-id="cd240-576">2</span></span>                            | <span data-ttu-id="cd240-577">Argument</span><span class="sxs-lookup"><span data-stu-id="cd240-577">value2</span></span>                       |
| <span data-ttu-id="cd240-578">3</span><span class="sxs-lookup"><span data-stu-id="cd240-578">3</span></span>                            | <span data-ttu-id="cd240-579">value4</span><span class="sxs-lookup"><span data-stu-id="cd240-579">value4</span></span>                       |
| <span data-ttu-id="cd240-580">4</span><span class="sxs-lookup"><span data-stu-id="cd240-580">4</span></span>                            | <span data-ttu-id="cd240-581">value5</span><span class="sxs-lookup"><span data-stu-id="cd240-581">value5</span></span>                       |

<span data-ttu-id="cd240-582">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="cd240-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="cd240-583">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="cd240-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="cd240-584">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="cd240-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="cd240-585">Chybějící položka konfigurace pro index &num;3 lze zadat před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="cd240-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="cd240-586">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="cd240-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="cd240-587">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="cd240-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="cd240-588">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="cd240-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="cd240-589">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="cd240-590">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-590">Key</span></span>             | <span data-ttu-id="cd240-591">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="cd240-592">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="cd240-592">array:entries:3</span></span> | <span data-ttu-id="cd240-593">hodnota3</span><span class="sxs-lookup"><span data-stu-id="cd240-593">value3</span></span> |

<span data-ttu-id="cd240-594">Je-li instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON zahrne položku pro indexovou &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="cd240-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="cd240-595">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="cd240-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="cd240-596">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="cd240-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="cd240-597">0,8</span><span class="sxs-lookup"><span data-stu-id="cd240-597">0</span></span>                            | <span data-ttu-id="cd240-598">value0</span><span class="sxs-lookup"><span data-stu-id="cd240-598">value0</span></span>                       |
| <span data-ttu-id="cd240-599">první</span><span class="sxs-lookup"><span data-stu-id="cd240-599">1</span></span>                            | <span data-ttu-id="cd240-600">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="cd240-600">value1</span></span>                       |
| <span data-ttu-id="cd240-601">odst</span><span class="sxs-lookup"><span data-stu-id="cd240-601">2</span></span>                            | <span data-ttu-id="cd240-602">Argument</span><span class="sxs-lookup"><span data-stu-id="cd240-602">value2</span></span>                       |
| <span data-ttu-id="cd240-603">3</span><span class="sxs-lookup"><span data-stu-id="cd240-603">3</span></span>                            | <span data-ttu-id="cd240-604">hodnota3</span><span class="sxs-lookup"><span data-stu-id="cd240-604">value3</span></span>                       |
| <span data-ttu-id="cd240-605">4</span><span class="sxs-lookup"><span data-stu-id="cd240-605">4</span></span>                            | <span data-ttu-id="cd240-606">value4</span><span class="sxs-lookup"><span data-stu-id="cd240-606">value4</span></span>                       |
| <span data-ttu-id="cd240-607">5</span><span class="sxs-lookup"><span data-stu-id="cd240-607">5</span></span>                            | <span data-ttu-id="cd240-608">value5</span><span class="sxs-lookup"><span data-stu-id="cd240-608">value5</span></span>                       |

<span data-ttu-id="cd240-609">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="cd240-609">**JSON array processing**</span></span>

<span data-ttu-id="cd240-610">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="cd240-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="cd240-611">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="cd240-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="cd240-612">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="cd240-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="cd240-613">Key</span><span class="sxs-lookup"><span data-stu-id="cd240-613">Key</span></span>                     | <span data-ttu-id="cd240-614">Hodnota</span><span class="sxs-lookup"><span data-stu-id="cd240-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="cd240-615">json_array: klíč</span><span class="sxs-lookup"><span data-stu-id="cd240-615">json_array:key</span></span>          | <span data-ttu-id="cd240-616">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="cd240-616">valueA</span></span> |
| <span data-ttu-id="cd240-617">json_array: dílčí oddíl: 0</span><span class="sxs-lookup"><span data-stu-id="cd240-617">json_array:subsection:0</span></span> | <span data-ttu-id="cd240-618">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="cd240-618">valueB</span></span> |
| <span data-ttu-id="cd240-619">json_array: pododdíl: 1</span><span class="sxs-lookup"><span data-stu-id="cd240-619">json_array:subsection:1</span></span> | <span data-ttu-id="cd240-620">valueC</span><span class="sxs-lookup"><span data-stu-id="cd240-620">valueC</span></span> |
| <span data-ttu-id="cd240-621">json_array: pododdíl: 2</span><span class="sxs-lookup"><span data-stu-id="cd240-621">json_array:subsection:2</span></span> | <span data-ttu-id="cd240-622">s</span><span class="sxs-lookup"><span data-stu-id="cd240-622">valueD</span></span> |

<span data-ttu-id="cd240-623">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="cd240-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="cd240-624">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="cd240-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="cd240-625">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="cd240-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="cd240-626">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="cd240-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="cd240-627">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="cd240-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="cd240-628">0,8</span><span class="sxs-lookup"><span data-stu-id="cd240-628">0</span></span>                                   | <span data-ttu-id="cd240-629">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="cd240-629">valueB</span></span>                              |
| <span data-ttu-id="cd240-630">první</span><span class="sxs-lookup"><span data-stu-id="cd240-630">1</span></span>                                   | <span data-ttu-id="cd240-631">valueC</span><span class="sxs-lookup"><span data-stu-id="cd240-631">valueC</span></span>                              |
| <span data-ttu-id="cd240-632">odst</span><span class="sxs-lookup"><span data-stu-id="cd240-632">2</span></span>                                   | <span data-ttu-id="cd240-633">s</span><span class="sxs-lookup"><span data-stu-id="cd240-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="cd240-634">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="cd240-634">Custom configuration provider</span></span>

<span data-ttu-id="cd240-635">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="cd240-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="cd240-636">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="cd240-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="cd240-637">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="cd240-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="cd240-638">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cd240-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="cd240-639">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cd240-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="cd240-640">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="cd240-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="cd240-641">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="cd240-642">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="cd240-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd240-643">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="cd240-644">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="cd240-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="cd240-645">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="cd240-646">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="cd240-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="cd240-647">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="cd240-648">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="cd240-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="cd240-649">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="cd240-649">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="cd240-650">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-650">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="cd240-651">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-651">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="cd240-652">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-652">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="cd240-653">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-653">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd240-654">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-654">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="cd240-655">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="cd240-655">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="cd240-656">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-656">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="cd240-657">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="cd240-657">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="cd240-658">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-658">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="cd240-659">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="cd240-659">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="cd240-660">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="cd240-660">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="cd240-661">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="cd240-662">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="cd240-662">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="cd240-663">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-663">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="cd240-664">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="cd240-664">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="cd240-665">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="cd240-665">Access configuration during startup</span></span>

<span data-ttu-id="cd240-666">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd240-666">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cd240-667">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="cd240-667">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="cd240-668">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="cd240-668">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="cd240-669">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="cd240-669">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="cd240-670">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="cd240-670">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="cd240-671">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="cd240-671">In a Razor Pages page:</span></span>

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

<span data-ttu-id="cd240-672">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="cd240-672">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="cd240-673">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="cd240-673">Add configuration from an external assembly</span></span>

<span data-ttu-id="cd240-674">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="cd240-674">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="cd240-675">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="cd240-675">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd240-676">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cd240-676">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
