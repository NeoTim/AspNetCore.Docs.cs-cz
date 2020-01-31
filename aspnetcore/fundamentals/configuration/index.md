---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: df49286c3f050b8e90cb5427cf03e2b896a39294
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885557"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="71a01-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71a01-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="71a01-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="71a01-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="71a01-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="71a01-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="71a01-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="71a01-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="71a01-107">Azure Key Vault</span></span>
* <span data-ttu-id="71a01-108">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="71a01-108">Azure App Configuration</span></span>
* <span data-ttu-id="71a01-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-109">Command-line arguments</span></span>
* <span data-ttu-id="71a01-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="71a01-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="71a01-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="71a01-111">Directory files</span></span>
* <span data-ttu-id="71a01-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-112">Environment variables</span></span>
* <span data-ttu-id="71a01-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="71a01-113">In-memory .NET objects</span></span>
* <span data-ttu-id="71a01-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="71a01-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71a01-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="71a01-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71a01-116">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="71a01-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="71a01-117">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="71a01-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="71a01-118">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="71a01-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="71a01-119">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="71a01-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="71a01-120">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="71a01-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="71a01-121">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71a01-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="71a01-122">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="71a01-122">Host versus app configuration</span></span>

<span data-ttu-id="71a01-123">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="71a01-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="71a01-124">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="71a01-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="71a01-125">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="71a01-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="71a01-126">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="71a01-127">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="71a01-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="71a01-128">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="71a01-128">Other configuration</span></span>

<span data-ttu-id="71a01-129">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="71a01-129">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="71a01-130">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="71a01-130">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="71a01-131">*Launch. json*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:</span><span class="sxs-lookup"><span data-stu-id="71a01-131">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="71a01-132">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="71a01-132">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="71a01-133">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="71a01-133">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="71a01-134">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="71a01-134">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="71a01-135">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="71a01-135">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="71a01-136">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="71a01-136">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71a01-137">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-137">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="71a01-138">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="71a01-138">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="71a01-139">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="71a01-139">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="71a01-140">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="71a01-140">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="71a01-141">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-141">Host configuration is provided from:</span></span>
  * <span data-ttu-id="71a01-142">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-142">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="71a01-143">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-143">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="71a01-144">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-144">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="71a01-145">Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="71a01-145">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="71a01-146">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-146">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="71a01-147">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-147">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="71a01-148">Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="71a01-148">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="71a01-149">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="71a01-149">Enable IIS integration.</span></span>
* <span data-ttu-id="71a01-150">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-150">App configuration is provided from:</span></span>
  * <span data-ttu-id="71a01-151">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-151">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-152">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-152">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-153">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="71a01-153">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="71a01-154">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-154">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-155">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71a01-156">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-156">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="71a01-157">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="71a01-157">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="71a01-158">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="71a01-158">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="71a01-159">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="71a01-159">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="71a01-160">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-160">Host configuration is provided from:</span></span>
  * <span data-ttu-id="71a01-161">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-161">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="71a01-162">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-162">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="71a01-163">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-163">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="71a01-164">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-164">App configuration is provided from:</span></span>
  * <span data-ttu-id="71a01-165">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-165">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-166">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-166">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-167">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="71a01-167">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="71a01-168">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-168">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="71a01-169">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="71a01-169">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="71a01-170">Zabezpečení –</span><span class="sxs-lookup"><span data-stu-id="71a01-170">Security</span></span>

<span data-ttu-id="71a01-171">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="71a01-171">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="71a01-172">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="71a01-172">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="71a01-173">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="71a01-173">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="71a01-174">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="71a01-174">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="71a01-175">Další informace naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="71a01-175">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="71a01-176"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="71a01-176"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="71a01-177">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="71a01-177">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="71a01-178">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="71a01-178">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="71a01-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-179">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="71a01-180">Další informace najdete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="71a01-180">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="71a01-181">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="71a01-181">Hierarchical configuration data</span></span>

<span data-ttu-id="71a01-182">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="71a01-182">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="71a01-183">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="71a01-183">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="71a01-184">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-184">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="71a01-185">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="71a01-185">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="71a01-186">section0:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-186">section0:key0</span></span>
* <span data-ttu-id="71a01-187">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-187">section0:key1</span></span>
* <span data-ttu-id="71a01-188">section1:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-188">section1:key0</span></span>
* <span data-ttu-id="71a01-189">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-189">section1:key1</span></span>

<span data-ttu-id="71a01-190">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="71a01-190"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="71a01-191">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="71a01-191">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="71a01-192">Konvence</span><span class="sxs-lookup"><span data-stu-id="71a01-192">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="71a01-193">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="71a01-193">Sources and providers</span></span>

<span data-ttu-id="71a01-194">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-194">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="71a01-195">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="71a01-195">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="71a01-196">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="71a01-196">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="71a01-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-197"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="71a01-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="71a01-198"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="71a01-199">V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:</span><span class="sxs-lookup"><span data-stu-id="71a01-199">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="71a01-200">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="71a01-200">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="71a01-201">Klíče</span><span class="sxs-lookup"><span data-stu-id="71a01-201">Keys</span></span>

<span data-ttu-id="71a01-202">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="71a01-202">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="71a01-203">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="71a01-203">Keys are case-insensitive.</span></span> <span data-ttu-id="71a01-204">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="71a01-204">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="71a01-205">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="71a01-205">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="71a01-206">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="71a01-206">Hierarchical keys</span></span>
  * <span data-ttu-id="71a01-207">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="71a01-207">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="71a01-208">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="71a01-208">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="71a01-209">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="71a01-209">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="71a01-210">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="71a01-210">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="71a01-211">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-211">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="71a01-212"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="71a01-212">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="71a01-213">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="71a01-213">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="71a01-214">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="71a01-214">Values</span></span>

<span data-ttu-id="71a01-215">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="71a01-215">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="71a01-216">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="71a01-216">Values are strings.</span></span>
* <span data-ttu-id="71a01-217">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="71a01-217">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="71a01-218">Zprostředkovatelé</span><span class="sxs-lookup"><span data-stu-id="71a01-218">Providers</span></span>

<span data-ttu-id="71a01-219">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-219">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="71a01-220">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="71a01-220">Provider</span></span> | <span data-ttu-id="71a01-221">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="71a01-221">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="71a01-222">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="71a01-222">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="71a01-223">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="71a01-223">Azure Key Vault</span></span> |
| <span data-ttu-id="71a01-224">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="71a01-224">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="71a01-225">Konfigurace aplikace Azure</span><span class="sxs-lookup"><span data-stu-id="71a01-225">Azure App Configuration</span></span> |
| [<span data-ttu-id="71a01-226">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-226">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="71a01-227">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-227">Command-line parameters</span></span> |
| [<span data-ttu-id="71a01-228">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="71a01-228">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="71a01-229">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="71a01-229">Custom source</span></span> |
| [<span data-ttu-id="71a01-230">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-230">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="71a01-231">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-231">Environment variables</span></span> |
| [<span data-ttu-id="71a01-232">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="71a01-232">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="71a01-233">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="71a01-233">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="71a01-234">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="71a01-234">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="71a01-235">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="71a01-235">Directory files</span></span> |
| [<span data-ttu-id="71a01-236">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="71a01-236">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="71a01-237">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="71a01-237">In-memory collections</span></span> |
| <span data-ttu-id="71a01-238">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="71a01-238">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="71a01-239">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="71a01-239">File in the user profile directory</span></span> |

<span data-ttu-id="71a01-240">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="71a01-240">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="71a01-241">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="71a01-241">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="71a01-242">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="71a01-242">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="71a01-243">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="71a01-243">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="71a01-244">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="71a01-244">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="71a01-245">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="71a01-245">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="71a01-246">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="71a01-246">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="71a01-247">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-247">Environment variables</span></span>
1. <span data-ttu-id="71a01-248">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-248">Command-line arguments</span></span>

<span data-ttu-id="71a01-249">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="71a01-249">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="71a01-250">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-250">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="71a01-251">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-251">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="71a01-252">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="71a01-252">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="71a01-253">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="71a01-253">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="71a01-254">`ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="71a01-254">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="71a01-255">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="71a01-255">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="71a01-256">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="71a01-256">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="71a01-257">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="71a01-257">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="71a01-258">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="71a01-258">ConfigureAppConfiguration</span></span>

<span data-ttu-id="71a01-259">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="71a01-259">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="71a01-260">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-260">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="71a01-261">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="71a01-261">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="71a01-262">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="71a01-262">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="71a01-263">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="71a01-263">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="71a01-264">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="71a01-264">Consume configuration during app startup</span></span>

<span data-ttu-id="71a01-265">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="71a01-265">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71a01-266">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="71a01-266">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="71a01-267">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="71a01-267">Command-line Configuration Provider</span></span>

<span data-ttu-id="71a01-268"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="71a01-268">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="71a01-269">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="71a01-269">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="71a01-270">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="71a01-270">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="71a01-271">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-271">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="71a01-272">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="71a01-272">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="71a01-273">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="71a01-273">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="71a01-274">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-274">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="71a01-275">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-275">Environment variables.</span></span>

<span data-ttu-id="71a01-276">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="71a01-276">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="71a01-277">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="71a01-277">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="71a01-278">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-278">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="71a01-279">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="71a01-279">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="71a01-280">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-280">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="71a01-281">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="71a01-281">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="71a01-282">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="71a01-282">**Example**</span></span>

<span data-ttu-id="71a01-283">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="71a01-283">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="71a01-284">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="71a01-284">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="71a01-285">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="71a01-285">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="71a01-286">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="71a01-286">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="71a01-287">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="71a01-287">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="71a01-288">Arguments</span><span class="sxs-lookup"><span data-stu-id="71a01-288">Arguments</span></span>

<span data-ttu-id="71a01-289">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="71a01-289">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="71a01-290">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="71a01-290">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="71a01-291">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="71a01-291">Key prefix</span></span>               | <span data-ttu-id="71a01-292">Příklad</span><span class="sxs-lookup"><span data-stu-id="71a01-292">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="71a01-293">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="71a01-293">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="71a01-294">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="71a01-294">Two dashes (`--`)</span></span>        | <span data-ttu-id="71a01-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="71a01-295">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="71a01-296">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="71a01-296">Forward slash (`/`)</span></span>      | <span data-ttu-id="71a01-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="71a01-297">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="71a01-298">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="71a01-298">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="71a01-299">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="71a01-299">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="71a01-300">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="71a01-300">Switch mappings</span></span>

<span data-ttu-id="71a01-301">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="71a01-301">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="71a01-302">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="71a01-302">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="71a01-303">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="71a01-303">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="71a01-304">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="71a01-304">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="71a01-305">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="71a01-305">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="71a01-306">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="71a01-306">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="71a01-307">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="71a01-307">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="71a01-308">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="71a01-308">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="71a01-309">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="71a01-309">Create a switch mappings dictionary.</span></span> <span data-ttu-id="71a01-310">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="71a01-310">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="71a01-311">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="71a01-311">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="71a01-312">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="71a01-312">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="71a01-313">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-313">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="71a01-314">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="71a01-314">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="71a01-315">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="71a01-315">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="71a01-316">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-316">Key</span></span>       | <span data-ttu-id="71a01-317">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-317">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="71a01-318">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="71a01-318">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="71a01-319">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="71a01-319">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="71a01-320">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-320">Key</span></span>               | <span data-ttu-id="71a01-321">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-321">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="71a01-322">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-322">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="71a01-323"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="71a01-323">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="71a01-324">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-324">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="71a01-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-325">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="71a01-326">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="71a01-326">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71a01-327">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-327">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="71a01-328">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-328">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71a01-329">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-329">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="71a01-330">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-330">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="71a01-331">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="71a01-331">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="71a01-332">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="71a01-332">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="71a01-333">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="71a01-333">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="71a01-334">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-334">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="71a01-335">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="71a01-335">Command-line arguments.</span></span>

<span data-ttu-id="71a01-336">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="71a01-336">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="71a01-337">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="71a01-337">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="71a01-338">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:</span><span class="sxs-lookup"><span data-stu-id="71a01-338">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="71a01-339">Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.</span><span class="sxs-lookup"><span data-stu-id="71a01-339">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="71a01-340">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="71a01-340">**Example**</span></span>

<span data-ttu-id="71a01-341">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="71a01-341">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="71a01-342">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-342">Run the sample app.</span></span> <span data-ttu-id="71a01-343">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="71a01-343">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="71a01-344">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="71a01-344">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="71a01-345">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="71a01-345">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="71a01-346">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-346">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="71a01-347">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-347">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="71a01-348">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="71a01-348">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="71a01-349">Předpony</span><span class="sxs-lookup"><span data-stu-id="71a01-349">Prefixes</span></span>

<span data-ttu-id="71a01-350">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="71a01-350">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="71a01-351">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-351">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="71a01-352">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="71a01-352">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="71a01-353">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-353">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="71a01-354">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-354">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="71a01-355">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="71a01-355">**Connection string prefixes**</span></span>

<span data-ttu-id="71a01-356">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-356">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="71a01-357">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="71a01-357">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="71a01-358">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="71a01-358">Connection string prefix</span></span> | <span data-ttu-id="71a01-359">Zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="71a01-359">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="71a01-360">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="71a01-360">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="71a01-361">MySQL</span><span class="sxs-lookup"><span data-stu-id="71a01-361">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="71a01-362">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="71a01-362">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="71a01-363">SQL Server</span><span class="sxs-lookup"><span data-stu-id="71a01-363">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="71a01-364">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="71a01-364">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="71a01-365">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="71a01-365">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="71a01-366">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="71a01-366">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="71a01-367">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="71a01-367">Environment variable key</span></span> | <span data-ttu-id="71a01-368">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="71a01-368">Converted configuration key</span></span> | <span data-ttu-id="71a01-369">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="71a01-369">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="71a01-370">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="71a01-370">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="71a01-371">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="71a01-371">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="71a01-372">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="71a01-372">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="71a01-373">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="71a01-373">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="71a01-374">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="71a01-374">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="71a01-375">Klíč: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="71a01-375">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="71a01-376">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="71a01-376">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="71a01-377">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="71a01-377">File Configuration Provider</span></span>

<span data-ttu-id="71a01-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="71a01-378"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="71a01-379">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="71a01-379">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="71a01-380">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="71a01-380">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="71a01-381">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="71a01-381">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="71a01-382">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="71a01-382">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="71a01-383">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="71a01-383">INI Configuration Provider</span></span>

<span data-ttu-id="71a01-384"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="71a01-384">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="71a01-385">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-385">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="71a01-386">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="71a01-386">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="71a01-387">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="71a01-387">Overloads permit specifying:</span></span>

* <span data-ttu-id="71a01-388">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="71a01-388">Whether the file is optional.</span></span>
* <span data-ttu-id="71a01-389">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-389">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="71a01-390"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-390">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="71a01-391">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="71a01-391">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="71a01-392">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="71a01-392">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="71a01-393">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="71a01-393">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="71a01-394">section0:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-394">section0:key0</span></span>
* <span data-ttu-id="71a01-395">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-395">section0:key1</span></span>
* <span data-ttu-id="71a01-396">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="71a01-396">section1:subsection:key</span></span>
* <span data-ttu-id="71a01-397">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="71a01-397">section2:subsection0:key</span></span>
* <span data-ttu-id="71a01-398">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="71a01-398">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="71a01-399">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="71a01-399">JSON Configuration Provider</span></span>

<span data-ttu-id="71a01-400"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="71a01-400">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="71a01-401">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-401">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="71a01-402">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="71a01-402">Overloads permit specifying:</span></span>

* <span data-ttu-id="71a01-403">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="71a01-403">Whether the file is optional.</span></span>
* <span data-ttu-id="71a01-404">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-404">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="71a01-405"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-405">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="71a01-406">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-406">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="71a01-407">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="71a01-407">The method is called to load configuration from:</span></span>

* <span data-ttu-id="71a01-408">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="71a01-408">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="71a01-409">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="71a01-409">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="71a01-410">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="71a01-410">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="71a01-411">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="71a01-411">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="71a01-412">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="71a01-412">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="71a01-413">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-413">Environment variables.</span></span>
* <span data-ttu-id="71a01-414">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-414">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="71a01-415">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="71a01-415">Command-line arguments.</span></span>

<span data-ttu-id="71a01-416">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="71a01-416">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="71a01-417">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="71a01-417">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="71a01-418">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="71a01-418">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="71a01-419">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="71a01-419">**Example**</span></span>

<span data-ttu-id="71a01-420">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="71a01-420">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="71a01-421">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="71a01-421">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="71a01-422">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="71a01-422">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="71a01-423">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="71a01-423">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="71a01-424">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-424">Run the sample app.</span></span> <span data-ttu-id="71a01-425">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="71a01-425">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="71a01-426">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-426">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="71a01-427">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-427">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="71a01-428">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="71a01-428">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="71a01-429">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="71a01-429">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="71a01-430">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="71a01-430">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="71a01-431">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-431">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="71a01-432">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="71a01-432">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="71a01-433">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Information`.</span><span class="sxs-lookup"><span data-stu-id="71a01-433">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="71a01-434">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="71a01-434">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="71a01-435">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="71a01-435">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="71a01-436">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="71a01-436">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="71a01-437">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-437">Run the sample app.</span></span> <span data-ttu-id="71a01-438">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="71a01-438">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="71a01-439">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-439">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="71a01-440">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-440">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="71a01-441">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="71a01-441">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="71a01-442">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="71a01-442">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="71a01-443">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="71a01-443">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="71a01-444">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a01-444">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="71a01-445">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="71a01-445">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="71a01-446">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Warning`.</span><span class="sxs-lookup"><span data-stu-id="71a01-446">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="71a01-447">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="71a01-447">XML Configuration Provider</span></span>

<span data-ttu-id="71a01-448"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="71a01-448">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="71a01-449">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-449">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="71a01-450">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="71a01-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="71a01-451">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="71a01-451">Whether the file is optional.</span></span>
* <span data-ttu-id="71a01-452">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-452">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="71a01-453"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-453">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="71a01-454">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-454">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="71a01-455">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-455">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="71a01-456">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="71a01-456">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="71a01-457">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="71a01-457">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="71a01-458">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="71a01-458">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="71a01-459">section0:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-459">section0:key0</span></span>
* <span data-ttu-id="71a01-460">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-460">section0:key1</span></span>
* <span data-ttu-id="71a01-461">section1:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-461">section1:key0</span></span>
* <span data-ttu-id="71a01-462">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-462">section1:key1</span></span>

<span data-ttu-id="71a01-463">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="71a01-463">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="71a01-464">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="71a01-464">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="71a01-465">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-465">section:section0:key:key0</span></span>
* <span data-ttu-id="71a01-466">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="71a01-466">section:section0:key:key1</span></span>
* <span data-ttu-id="71a01-467">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-467">section:section1:key:key0</span></span>
* <span data-ttu-id="71a01-468">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-468">section:section1:key:key1</span></span>

<span data-ttu-id="71a01-469">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="71a01-469">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="71a01-470">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="71a01-470">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="71a01-471">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="71a01-471">key:attribute</span></span>
* <span data-ttu-id="71a01-472">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="71a01-472">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="71a01-473">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="71a01-473">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="71a01-474"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="71a01-474">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="71a01-475">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-475">The key is the file name.</span></span> <span data-ttu-id="71a01-476">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="71a01-476">The value contains the file's contents.</span></span> <span data-ttu-id="71a01-477">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="71a01-477">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="71a01-478">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-478">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="71a01-479">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="71a01-479">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="71a01-480">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="71a01-480">Overloads permit specifying:</span></span>

* <span data-ttu-id="71a01-481">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="71a01-481">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="71a01-482">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="71a01-482">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="71a01-483">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="71a01-483">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="71a01-484">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="71a01-484">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="71a01-485">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="71a01-485">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="71a01-486">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="71a01-486">Memory Configuration Provider</span></span>

<span data-ttu-id="71a01-487"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="71a01-487">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="71a01-488">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="71a01-488">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="71a01-489">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="71a01-489">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="71a01-490">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="71a01-490">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="71a01-491">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="71a01-491">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="71a01-492">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-492">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="71a01-493">GetValue</span><span class="sxs-lookup"><span data-stu-id="71a01-493">GetValue</span></span>

<span data-ttu-id="71a01-494">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="71a01-494">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="71a01-495">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="71a01-495">An overload accepts a default value.</span></span>

<span data-ttu-id="71a01-496">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="71a01-496">The following example:</span></span>

* <span data-ttu-id="71a01-497">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="71a01-497">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="71a01-498">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="71a01-498">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="71a01-499">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="71a01-499">Types the value as an `int`.</span></span>
* <span data-ttu-id="71a01-500">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="71a01-500">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="71a01-501">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="71a01-501">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="71a01-502">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="71a01-502">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="71a01-503">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="71a01-503">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="71a01-504">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-504">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="71a01-505">section0:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-505">section0:key0</span></span>
* <span data-ttu-id="71a01-506">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-506">section0:key1</span></span>
* <span data-ttu-id="71a01-507">section1:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-507">section1:key0</span></span>
* <span data-ttu-id="71a01-508">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-508">section1:key1</span></span>
* <span data-ttu-id="71a01-509">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-509">section2:subsection0:key0</span></span>
* <span data-ttu-id="71a01-510">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-510">section2:subsection0:key1</span></span>
* <span data-ttu-id="71a01-511">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="71a01-511">section2:subsection1:key0</span></span>
* <span data-ttu-id="71a01-512">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="71a01-512">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="71a01-513">GetSection</span><span class="sxs-lookup"><span data-stu-id="71a01-513">GetSection</span></span>

<span data-ttu-id="71a01-514">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="71a01-514">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="71a01-515">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="71a01-515">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="71a01-516">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="71a01-516">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="71a01-517">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="71a01-517">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="71a01-518">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="71a01-518">`GetSection` never returns `null`.</span></span> <span data-ttu-id="71a01-519">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="71a01-519">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="71a01-520">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="71a01-520">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="71a01-521">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="71a01-521">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="71a01-522">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="71a01-522">GetChildren</span></span>

<span data-ttu-id="71a01-523">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-523">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="71a01-524">Existuje</span><span class="sxs-lookup"><span data-stu-id="71a01-524">Exists</span></span>

<span data-ttu-id="71a01-525">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="71a01-525">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="71a01-526">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="71a01-526">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="71a01-527">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="71a01-527">Bind to a class</span></span>

<span data-ttu-id="71a01-528">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="71a01-528">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="71a01-529">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="71a01-529">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="71a01-530">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="71a01-530">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="71a01-531">Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="71a01-531">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="71a01-532">Pole nejsou **svázána** .</span><span class="sxs-lookup"><span data-stu-id="71a01-532">Fields are **not** bound.</span></span>

<span data-ttu-id="71a01-533">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="71a01-533">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="71a01-534">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="71a01-534">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="71a01-535">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-535">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="71a01-536">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-536">Key</span></span>                   | <span data-ttu-id="71a01-537">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-537">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="71a01-538">Starship: název</span><span class="sxs-lookup"><span data-stu-id="71a01-538">starship:name</span></span>         | <span data-ttu-id="71a01-539">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="71a01-539">USS Enterprise</span></span>                                    |
| <span data-ttu-id="71a01-540">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="71a01-540">starship:registry</span></span>     | <span data-ttu-id="71a01-541">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="71a01-541">NCC-1701</span></span>                                          |
| <span data-ttu-id="71a01-542">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="71a01-542">starship:class</span></span>        | <span data-ttu-id="71a01-543">Založen</span><span class="sxs-lookup"><span data-stu-id="71a01-543">Constitution</span></span>                                      |
| <span data-ttu-id="71a01-544">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="71a01-544">starship:length</span></span>       | <span data-ttu-id="71a01-545">304,8</span><span class="sxs-lookup"><span data-stu-id="71a01-545">304.8</span></span>                                             |
| <span data-ttu-id="71a01-546">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="71a01-546">starship:commissioned</span></span> | <span data-ttu-id="71a01-547">Nepravda</span><span class="sxs-lookup"><span data-stu-id="71a01-547">False</span></span>                                             |
| <span data-ttu-id="71a01-548">Patka</span><span class="sxs-lookup"><span data-stu-id="71a01-548">trademark</span></span>             | <span data-ttu-id="71a01-549">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="71a01-549">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="71a01-550">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="71a01-550">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="71a01-551">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="71a01-551">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="71a01-552">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="71a01-552">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="71a01-553">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="71a01-553">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="71a01-554">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="71a01-554">Bind to an object graph</span></span>

<span data-ttu-id="71a01-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="71a01-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="71a01-556">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="71a01-556">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="71a01-557">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="71a01-557">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="71a01-558">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="71a01-558">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="71a01-559">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="71a01-559">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="71a01-560">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="71a01-560">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="71a01-561">[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="71a01-561">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="71a01-562">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="71a01-562">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="71a01-563">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="71a01-563">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="71a01-564">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="71a01-564">Bind an array to a class</span></span>

<span data-ttu-id="71a01-565">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="71a01-565">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="71a01-566"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="71a01-566">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="71a01-567">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="71a01-567">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="71a01-568">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="71a01-568">Binding is provided by convention.</span></span> <span data-ttu-id="71a01-569">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="71a01-569">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="71a01-570">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="71a01-570">**In-memory array processing**</span></span>

<span data-ttu-id="71a01-571">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="71a01-571">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="71a01-572">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-572">Key</span></span>             | <span data-ttu-id="71a01-573">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-573">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="71a01-574">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="71a01-574">array:entries:0</span></span> | <span data-ttu-id="71a01-575">value0</span><span class="sxs-lookup"><span data-stu-id="71a01-575">value0</span></span> |
| <span data-ttu-id="71a01-576">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="71a01-576">array:entries:1</span></span> | <span data-ttu-id="71a01-577">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="71a01-577">value1</span></span> |
| <span data-ttu-id="71a01-578">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="71a01-578">array:entries:2</span></span> | <span data-ttu-id="71a01-579">Argument</span><span class="sxs-lookup"><span data-stu-id="71a01-579">value2</span></span> |
| <span data-ttu-id="71a01-580">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="71a01-580">array:entries:4</span></span> | <span data-ttu-id="71a01-581">value4</span><span class="sxs-lookup"><span data-stu-id="71a01-581">value4</span></span> |
| <span data-ttu-id="71a01-582">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="71a01-582">array:entries:5</span></span> | <span data-ttu-id="71a01-583">value5</span><span class="sxs-lookup"><span data-stu-id="71a01-583">value5</span></span> |

<span data-ttu-id="71a01-584">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="71a01-584">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="71a01-585">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="71a01-585">The array skips a value for index &num;3.</span></span> <span data-ttu-id="71a01-586">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="71a01-586">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="71a01-587">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="71a01-587">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="71a01-588">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="71a01-588">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="71a01-589">[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="71a01-589">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="71a01-590">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-590">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="71a01-591">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="71a01-591">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="71a01-592">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="71a01-592">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="71a01-593">0</span><span class="sxs-lookup"><span data-stu-id="71a01-593">0</span></span>                            | <span data-ttu-id="71a01-594">value0</span><span class="sxs-lookup"><span data-stu-id="71a01-594">value0</span></span>                       |
| <span data-ttu-id="71a01-595">1</span><span class="sxs-lookup"><span data-stu-id="71a01-595">1</span></span>                            | <span data-ttu-id="71a01-596">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="71a01-596">value1</span></span>                       |
| <span data-ttu-id="71a01-597">2</span><span class="sxs-lookup"><span data-stu-id="71a01-597">2</span></span>                            | <span data-ttu-id="71a01-598">Argument</span><span class="sxs-lookup"><span data-stu-id="71a01-598">value2</span></span>                       |
| <span data-ttu-id="71a01-599">3</span><span class="sxs-lookup"><span data-stu-id="71a01-599">3</span></span>                            | <span data-ttu-id="71a01-600">value4</span><span class="sxs-lookup"><span data-stu-id="71a01-600">value4</span></span>                       |
| <span data-ttu-id="71a01-601">4</span><span class="sxs-lookup"><span data-stu-id="71a01-601">4</span></span>                            | <span data-ttu-id="71a01-602">value5</span><span class="sxs-lookup"><span data-stu-id="71a01-602">value5</span></span>                       |

<span data-ttu-id="71a01-603">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="71a01-603">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="71a01-604">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="71a01-604">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="71a01-605">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="71a01-605">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="71a01-606">Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="71a01-606">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="71a01-607">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="71a01-607">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="71a01-608">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="71a01-608">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="71a01-609">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="71a01-609">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="71a01-610">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-610">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="71a01-611">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-611">Key</span></span>             | <span data-ttu-id="71a01-612">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-612">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="71a01-613">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="71a01-613">array:entries:3</span></span> | <span data-ttu-id="71a01-614">hodnota3</span><span class="sxs-lookup"><span data-stu-id="71a01-614">value3</span></span> |

<span data-ttu-id="71a01-615">Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="71a01-615">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="71a01-616">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="71a01-616">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="71a01-617">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="71a01-617">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="71a01-618">0</span><span class="sxs-lookup"><span data-stu-id="71a01-618">0</span></span>                            | <span data-ttu-id="71a01-619">value0</span><span class="sxs-lookup"><span data-stu-id="71a01-619">value0</span></span>                       |
| <span data-ttu-id="71a01-620">1</span><span class="sxs-lookup"><span data-stu-id="71a01-620">1</span></span>                            | <span data-ttu-id="71a01-621">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="71a01-621">value1</span></span>                       |
| <span data-ttu-id="71a01-622">2</span><span class="sxs-lookup"><span data-stu-id="71a01-622">2</span></span>                            | <span data-ttu-id="71a01-623">Argument</span><span class="sxs-lookup"><span data-stu-id="71a01-623">value2</span></span>                       |
| <span data-ttu-id="71a01-624">3</span><span class="sxs-lookup"><span data-stu-id="71a01-624">3</span></span>                            | <span data-ttu-id="71a01-625">hodnota3</span><span class="sxs-lookup"><span data-stu-id="71a01-625">value3</span></span>                       |
| <span data-ttu-id="71a01-626">4</span><span class="sxs-lookup"><span data-stu-id="71a01-626">4</span></span>                            | <span data-ttu-id="71a01-627">value4</span><span class="sxs-lookup"><span data-stu-id="71a01-627">value4</span></span>                       |
| <span data-ttu-id="71a01-628">5</span><span class="sxs-lookup"><span data-stu-id="71a01-628">5</span></span>                            | <span data-ttu-id="71a01-629">value5</span><span class="sxs-lookup"><span data-stu-id="71a01-629">value5</span></span>                       |

<span data-ttu-id="71a01-630">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="71a01-630">**JSON array processing**</span></span>

<span data-ttu-id="71a01-631">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="71a01-631">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="71a01-632">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="71a01-632">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="71a01-633">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="71a01-633">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="71a01-634">Key</span><span class="sxs-lookup"><span data-stu-id="71a01-634">Key</span></span>                     | <span data-ttu-id="71a01-635">Hodnota</span><span class="sxs-lookup"><span data-stu-id="71a01-635">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="71a01-636">json_array:key</span><span class="sxs-lookup"><span data-stu-id="71a01-636">json_array:key</span></span>          | <span data-ttu-id="71a01-637">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="71a01-637">valueA</span></span> |
| <span data-ttu-id="71a01-638">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="71a01-638">json_array:subsection:0</span></span> | <span data-ttu-id="71a01-639">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="71a01-639">valueB</span></span> |
| <span data-ttu-id="71a01-640">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="71a01-640">json_array:subsection:1</span></span> | <span data-ttu-id="71a01-641">valueC</span><span class="sxs-lookup"><span data-stu-id="71a01-641">valueC</span></span> |
| <span data-ttu-id="71a01-642">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="71a01-642">json_array:subsection:2</span></span> | <span data-ttu-id="71a01-643">s</span><span class="sxs-lookup"><span data-stu-id="71a01-643">valueD</span></span> |

<span data-ttu-id="71a01-644">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="71a01-644">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="71a01-645">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="71a01-645">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="71a01-646">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="71a01-646">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="71a01-647">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="71a01-647">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="71a01-648">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="71a01-648">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="71a01-649">0</span><span class="sxs-lookup"><span data-stu-id="71a01-649">0</span></span>                                   | <span data-ttu-id="71a01-650">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="71a01-650">valueB</span></span>                              |
| <span data-ttu-id="71a01-651">1</span><span class="sxs-lookup"><span data-stu-id="71a01-651">1</span></span>                                   | <span data-ttu-id="71a01-652">valueC</span><span class="sxs-lookup"><span data-stu-id="71a01-652">valueC</span></span>                              |
| <span data-ttu-id="71a01-653">2</span><span class="sxs-lookup"><span data-stu-id="71a01-653">2</span></span>                                   | <span data-ttu-id="71a01-654">s</span><span class="sxs-lookup"><span data-stu-id="71a01-654">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="71a01-655">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="71a01-655">Custom configuration provider</span></span>

<span data-ttu-id="71a01-656">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="71a01-656">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="71a01-657">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="71a01-657">The provider has the following characteristics:</span></span>

* <span data-ttu-id="71a01-658">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="71a01-658">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="71a01-659">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="71a01-659">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="71a01-660">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="71a01-660">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="71a01-661">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="71a01-661">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="71a01-662">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-662">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="71a01-663">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="71a01-663">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71a01-664">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-664">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="71a01-665">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="71a01-665">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="71a01-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-666">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="71a01-667">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="71a01-667">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="71a01-668">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-668">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="71a01-669">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="71a01-669">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="71a01-670">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="71a01-670">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="71a01-671">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="71a01-671">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="71a01-672">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="71a01-673">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="71a01-674">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="71a01-675">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71a01-676">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-676">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="71a01-677">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="71a01-677">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="71a01-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-678">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="71a01-679">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="71a01-679">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="71a01-680">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-680">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="71a01-681">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="71a01-681">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="71a01-682">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="71a01-682">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="71a01-683">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-683">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="71a01-684">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="71a01-684">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="71a01-685">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-685">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="71a01-686">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="71a01-686">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="71a01-687">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="71a01-687">Access configuration during startup</span></span>

<span data-ttu-id="71a01-688">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="71a01-688">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="71a01-689">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="71a01-689">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="71a01-690">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="71a01-690">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="71a01-691">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="71a01-691">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="71a01-692">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="71a01-692">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="71a01-693">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="71a01-693">In a Razor Pages page:</span></span>

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

<span data-ttu-id="71a01-694">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="71a01-694">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="71a01-695">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="71a01-695">Add configuration from an external assembly</span></span>

<span data-ttu-id="71a01-696">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a01-696">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="71a01-697">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="71a01-697">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71a01-698">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="71a01-698">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
