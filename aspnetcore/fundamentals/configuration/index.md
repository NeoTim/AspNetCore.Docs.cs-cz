---
title: Konfigurace v ASP.NET Core
author: guardrex
description: Naučte se, jak pomocí konfiguračního rozhraní API nakonfigurovat aplikaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171902"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="a4869-103">Konfigurace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4869-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="a4869-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a4869-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a4869-105">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="a4869-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="a4869-106">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="a4869-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-107">Azure Key Vault</span></span>
* <span data-ttu-id="a4869-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="a4869-108">Azure App Configuration</span></span>
* <span data-ttu-id="a4869-109">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-109">Command-line arguments</span></span>
* <span data-ttu-id="a4869-110">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="a4869-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="a4869-111">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="a4869-111">Directory files</span></span>
* <span data-ttu-id="a4869-112">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-112">Environment variables</span></span>
* <span data-ttu-id="a4869-113">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-113">In-memory .NET objects</span></span>
* <span data-ttu-id="a4869-114">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="a4869-114">Settings files</span></span>

<span data-ttu-id="a4869-115">Konfigurační balíčky pro scénáře Common Configuration Provider ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou implicitně zahrnuty v rámci rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a4869-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="a4869-116">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="a4869-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="a4869-117">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="a4869-118">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="a4869-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="a4869-119">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a4869-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a4869-120">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4869-120">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a4869-121">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="a4869-121">Host versus app configuration</span></span>

<span data-ttu-id="a4869-122">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="a4869-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a4869-123">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="a4869-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a4869-124">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a4869-125">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a4869-126">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a4869-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a4869-127">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-127">Other configuration</span></span>

<span data-ttu-id="a4869-128">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="a4869-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a4869-129">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="a4869-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a4869-130">*Launch. json*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:</span><span class="sxs-lookup"><span data-stu-id="a4869-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a4869-131">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="a4869-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a4869-132">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="a4869-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a4869-133">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="a4869-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a4869-134">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="a4869-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="a4869-135">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-135">Default configuration</span></span>

<span data-ttu-id="a4869-136">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="a4869-137">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="a4869-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="a4869-138">Následující požadavky se vztahují na aplikace, které používají [obecného hostitele](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a4869-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="a4869-139">Podrobnosti o výchozí konfiguraci při použití [webového hostitele](xref:fundamentals/host/web-host)naleznete v [ASP.NET Core 2,2 verzi tohoto tématu](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="a4869-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="a4869-140">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a4869-141">Proměnné prostředí s předponou `DOTNET_` (například `DOTNET_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a4869-142">Předpona (`DOTNET_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a4869-143">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a4869-144">Je navázána výchozí konfigurace webového hostitele (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="a4869-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="a4869-145">Kestrel se používá jako webový server a je nakonfigurovaný pomocí poskytovatelů konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="a4869-146">Přidejte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="a4869-147">Pokud je proměnná prostředí `ASPNETCORE_FORWARDEDHEADERS_ENABLED` nastavená na `true`, přidejte middleware s předanými hlavičkami.</span><span class="sxs-lookup"><span data-stu-id="a4869-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="a4869-148">Povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="a4869-148">Enable IIS integration.</span></span>
* <span data-ttu-id="a4869-149">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="a4869-150">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-151">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-152">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="a4869-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a4869-153">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-154">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="a4869-155">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="a4869-155">Security</span></span>

<span data-ttu-id="a4869-156">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="a4869-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="a4869-157">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="a4869-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="a4869-158">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="a4869-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a4869-159">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="a4869-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a4869-160">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="a4869-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a4869-161"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="a4869-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a4869-162">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="a4869-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="a4869-163">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a4869-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a4869-165">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a4869-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a4869-166">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="a4869-166">Hierarchical configuration data</span></span>

<span data-ttu-id="a4869-167">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a4869-168">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="a4869-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="a4869-169">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="a4869-170">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="a4869-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="a4869-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-171">section0:key0</span></span>
* <span data-ttu-id="a4869-172">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-172">section0:key1</span></span>
* <span data-ttu-id="a4869-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-173">section1:key0</span></span>
* <span data-ttu-id="a4869-174">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-174">section1:key1</span></span>

<span data-ttu-id="a4869-175">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="a4869-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a4869-176">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="a4869-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="a4869-177">Zásady</span><span class="sxs-lookup"><span data-stu-id="a4869-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="a4869-178">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="a4869-178">Sources and providers</span></span>

<span data-ttu-id="a4869-179">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="a4869-180">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="a4869-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="a4869-181">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="a4869-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="a4869-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a4869-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="a4869-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="a4869-184">V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:</span><span class="sxs-lookup"><span data-stu-id="a4869-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="a4869-185">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="a4869-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="a4869-186">Klíče</span><span class="sxs-lookup"><span data-stu-id="a4869-186">Keys</span></span>

<span data-ttu-id="a4869-187">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a4869-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="a4869-188">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="a4869-188">Keys are case-insensitive.</span></span> <span data-ttu-id="a4869-189">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a4869-190">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="a4869-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="a4869-191">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="a4869-191">Hierarchical keys</span></span>
  * <span data-ttu-id="a4869-192">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a4869-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a4869-193">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a4869-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a4869-194">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="a4869-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="a4869-195">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="a4869-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a4869-196">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a4869-197"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a4869-198">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="a4869-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="a4869-199">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="a4869-199">Values</span></span>

<span data-ttu-id="a4869-200">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a4869-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="a4869-201">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="a4869-201">Values are strings.</span></span>
* <span data-ttu-id="a4869-202">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="a4869-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="a4869-203">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="a4869-203">Providers</span></span>

<span data-ttu-id="a4869-204">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a4869-205">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-205">Provider</span></span> | <span data-ttu-id="a4869-206">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="a4869-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="a4869-207">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="a4869-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="a4869-208">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-208">Azure Key Vault</span></span> |
| <span data-ttu-id="a4869-209">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="a4869-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="a4869-210">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="a4869-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="a4869-211">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="a4869-212">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-212">Command-line parameters</span></span> |
| [<span data-ttu-id="a4869-213">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a4869-214">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="a4869-214">Custom source</span></span> |
| [<span data-ttu-id="a4869-215">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="a4869-216">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-216">Environment variables</span></span> |
| [<span data-ttu-id="a4869-217">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a4869-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a4869-218">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="a4869-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="a4869-219">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="a4869-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a4869-220">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="a4869-220">Directory files</span></span> |
| [<span data-ttu-id="a4869-221">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a4869-222">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-222">In-memory collections</span></span> |
| <span data-ttu-id="a4869-223">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="a4869-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="a4869-224">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="a4869-224">File in the user profile directory</span></span> |

<span data-ttu-id="a4869-225">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="a4869-226">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="a4869-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="a4869-227">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="a4869-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a4869-228">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="a4869-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a4869-229">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="a4869-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="a4869-230">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="a4869-231">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="a4869-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="a4869-232">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-232">Environment variables</span></span>
1. <span data-ttu-id="a4869-233">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-233">Command-line arguments</span></span>

<span data-ttu-id="a4869-234">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="a4869-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a4869-235">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-236">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="a4869-237">Konfigurace tvůrce hostitele pomocí ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4869-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="a4869-238">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> a zadejte konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="a4869-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="a4869-239">`ConfigureHostConfiguration` slouží k inicializaci <xref:Microsoft.Extensions.Hosting.IHostEnvironment> pro pozdější použití v procesu sestavení.</span><span class="sxs-lookup"><span data-stu-id="a4869-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="a4869-240">`ConfigureHostConfiguration` lze volat vícekrát pomocí aditivních výsledků.</span><span class="sxs-lookup"><span data-stu-id="a4869-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="a4869-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4869-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a4869-242">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a4869-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="a4869-243">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="a4869-244">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="a4869-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="a4869-245">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="a4869-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="a4869-246">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="a4869-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="a4869-247">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a4869-247">Consume configuration during app startup</span></span>

<span data-ttu-id="a4869-248">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4869-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4869-249">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="a4869-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="a4869-250">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="a4869-251"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-252">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a4869-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-253">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="a4869-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="a4869-254">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-255">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-256">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a4869-257">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-258">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-258">Environment variables.</span></span>

<span data-ttu-id="a4869-259">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="a4869-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="a4869-260">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="a4869-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="a4869-261">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="a4869-262">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="a4869-263">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-264">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="a4869-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="a4869-265">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-265">**Example**</span></span>

<span data-ttu-id="a4869-266">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a4869-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="a4869-267">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="a4869-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="a4869-268">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="a4869-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="a4869-269">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-270">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a4869-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="a4869-271">Argumenty</span><span class="sxs-lookup"><span data-stu-id="a4869-271">Arguments</span></span>

<span data-ttu-id="a4869-272">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="a4869-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="a4869-273">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="a4869-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="a4869-274">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="a4869-274">Key prefix</span></span>               | <span data-ttu-id="a4869-275">Příklad</span><span class="sxs-lookup"><span data-stu-id="a4869-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="a4869-276">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="a4869-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="a4869-277">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="a4869-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="a4869-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="a4869-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="a4869-279">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="a4869-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="a4869-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="a4869-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="a4869-281">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="a4869-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="a4869-282">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="a4869-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="a4869-283">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="a4869-283">Switch mappings</span></span>

<span data-ttu-id="a4869-284">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="a4869-285">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="a4869-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a4869-286">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a4869-287">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a4869-288">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="a4869-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a4869-289">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a4869-290">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="a4869-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a4869-291">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a4869-292">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="a4869-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="a4869-293">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="a4869-294">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="a4869-295">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="a4869-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a4869-296">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-297">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="a4869-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="a4869-298">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a4869-299">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-299">Key</span></span>       | <span data-ttu-id="a4869-300">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="a4869-301">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="a4869-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="a4869-302">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="a4869-303">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-303">Key</span></span>               | <span data-ttu-id="a4869-304">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="a4869-305">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="a4869-306"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-307">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a4869-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="a4869-309">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a4869-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a4869-310">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `DOTNET_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [obecným hostitelem](xref:fundamentals/host/generic-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="a4869-311">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-312">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-313">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="a4869-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="a4869-314">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a4869-315">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-316">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-316">Command-line arguments.</span></span>

<span data-ttu-id="a4869-317">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="a4869-318">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="a4869-319">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:</span><span class="sxs-lookup"><span data-stu-id="a4869-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="a4869-320">Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.</span><span class="sxs-lookup"><span data-stu-id="a4869-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="a4869-321">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-321">**Example**</span></span>

<span data-ttu-id="a4869-322">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a4869-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="a4869-323">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-323">Run the sample app.</span></span> <span data-ttu-id="a4869-324">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-325">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a4869-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="a4869-326">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="a4869-327">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="a4869-328">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="a4869-329">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="a4869-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="a4869-330">Předpony</span><span class="sxs-lookup"><span data-stu-id="a4869-330">Prefixes</span></span>

<span data-ttu-id="a4869-331">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a4869-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="a4869-332">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="a4869-333">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="a4869-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="a4869-334">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="a4869-335">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-336">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="a4869-336">**Connection string prefixes**</span></span>

<span data-ttu-id="a4869-337">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a4869-338">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="a4869-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a4869-339">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="a4869-339">Connection string prefix</span></span> | <span data-ttu-id="a4869-340">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a4869-341">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a4869-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="a4869-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a4869-343">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="a4869-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a4869-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a4869-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a4869-345">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="a4869-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a4869-346">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a4869-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a4869-347">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="a4869-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a4869-348">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-348">Environment variable key</span></span> | <span data-ttu-id="a4869-349">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="a4869-349">Converted configuration key</span></span> | <span data-ttu-id="a4869-350">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="a4869-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-351">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="a4869-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-352">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-353">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-354">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-355">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-356">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-357">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="a4869-358">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-358">**Example**</span></span>

<span data-ttu-id="a4869-359">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="a4869-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="a4869-360">Název &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="a4869-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="a4869-361">Hodnota &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="a4869-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="a4869-362">Pokud je `IConfiguration` vložená a přiřazená k poli s názvem `_config`, přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a4869-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="a4869-363">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a4869-363">File Configuration Provider</span></span>

<span data-ttu-id="a4869-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="a4869-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a4869-365">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="a4869-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="a4869-366">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a4869-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a4869-367">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a4869-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="a4869-368">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a4869-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a4869-369">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a4869-369">INI Configuration Provider</span></span>

<span data-ttu-id="a4869-370"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-371">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-372">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="a4869-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="a4869-373">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-374">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-374">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-375">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-376"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-377">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-378">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="a4869-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="a4869-379">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-380">section0:key0</span></span>
* <span data-ttu-id="a4869-381">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-381">section0:key1</span></span>
* <span data-ttu-id="a4869-382">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-382">section1:subsection:key</span></span>
* <span data-ttu-id="a4869-383">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-383">section2:subsection0:key</span></span>
* <span data-ttu-id="a4869-384">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="a4869-385">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a4869-385">JSON Configuration Provider</span></span>

<span data-ttu-id="a4869-386"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="a4869-387">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-388">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-389">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-389">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-390">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-391"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-392">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-393">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="a4869-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="a4869-394">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="a4869-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="a4869-395">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="a4869-396">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a4869-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="a4869-397">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-398">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-399">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-399">Environment variables.</span></span>
* <span data-ttu-id="a4869-400">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-401">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-401">Command-line arguments.</span></span>

<span data-ttu-id="a4869-402">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="a4869-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="a4869-403">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="a4869-404">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-405">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-405">**Example**</span></span>

<span data-ttu-id="a4869-406">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="a4869-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="a4869-407">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="a4869-408">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a4869-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="a4869-409">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="a4869-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="a4869-410">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-410">Run the sample app.</span></span> <span data-ttu-id="a4869-411">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-412">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="a4869-413">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="a4869-414">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="a4869-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="a4869-415">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="a4869-416">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="a4869-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="a4869-417">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="a4869-418">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a4869-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="a4869-419">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Information`.</span><span class="sxs-lookup"><span data-stu-id="a4869-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="a4869-420">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a4869-420">XML Configuration Provider</span></span>

<span data-ttu-id="a4869-421"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-422">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-423">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-424">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-424">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-425">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-426"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-427">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="a4869-428">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="a4869-429">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-430">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="a4869-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="a4869-431">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-432">section0:key0</span></span>
* <span data-ttu-id="a4869-433">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-433">section0:key1</span></span>
* <span data-ttu-id="a4869-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-434">section1:key0</span></span>
* <span data-ttu-id="a4869-435">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-435">section1:key1</span></span>

<span data-ttu-id="a4869-436">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="a4869-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="a4869-437">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-438">section:section0:key:key0</span></span>
* <span data-ttu-id="a4869-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="a4869-439">section:section0:key:key1</span></span>
* <span data-ttu-id="a4869-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-440">section:section1:key:key0</span></span>
* <span data-ttu-id="a4869-441">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-441">section:section1:key:key1</span></span>

<span data-ttu-id="a4869-442">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="a4869-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a4869-443">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-444">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="a4869-444">key:attribute</span></span>
* <span data-ttu-id="a4869-445">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="a4869-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a4869-446">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="a4869-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="a4869-447"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a4869-448">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-448">The key is the file name.</span></span> <span data-ttu-id="a4869-449">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-449">The value contains the file's contents.</span></span> <span data-ttu-id="a4869-450">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="a4869-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a4869-451">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a4869-452">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="a4869-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a4869-453">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-454">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="a4869-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a4869-455">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="a4869-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a4869-456">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="a4869-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a4869-457">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a4869-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a4869-458">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="a4869-459">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-459">Memory Configuration Provider</span></span>

<span data-ttu-id="a4869-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a4869-461">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-462">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="a4869-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="a4869-463">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a4869-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a4869-464">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="a4869-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="a4869-465">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="a4869-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="a4869-466">GetValue</span></span>

<span data-ttu-id="a4869-467">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="a4869-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="a4869-468">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4869-468">An overload accepts a default value.</span></span>

<span data-ttu-id="a4869-469">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="a4869-469">The following example:</span></span>

* <span data-ttu-id="a4869-470">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="a4869-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="a4869-471">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="a4869-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="a4869-472">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="a4869-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="a4869-473">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="a4869-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a4869-474">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="a4869-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a4869-475">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="a4869-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="a4869-476">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="a4869-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="a4869-477">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="a4869-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-478">section0:key0</span></span>
* <span data-ttu-id="a4869-479">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-479">section0:key1</span></span>
* <span data-ttu-id="a4869-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-480">section1:key0</span></span>
* <span data-ttu-id="a4869-481">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-481">section1:key1</span></span>
* <span data-ttu-id="a4869-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="a4869-483">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="a4869-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="a4869-485">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="a4869-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="a4869-486">GetSection</span></span>

<span data-ttu-id="a4869-487">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="a4869-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a4869-488">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="a4869-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="a4869-489">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="a4869-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="a4869-490">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="a4869-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="a4869-491">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="a4869-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a4869-492">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="a4869-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a4869-493">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="a4869-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a4869-494">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="a4869-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="a4869-495">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="a4869-495">GetChildren</span></span>

<span data-ttu-id="a4869-496">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="a4869-497">Existuje</span><span class="sxs-lookup"><span data-stu-id="a4869-497">Exists</span></span>

<span data-ttu-id="a4869-498">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="a4869-499">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="a4869-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="a4869-500">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="a4869-500">Bind to a class</span></span>

<span data-ttu-id="a4869-501">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="a4869-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="a4869-502">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a4869-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a4869-503">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="a4869-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="a4869-504">Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="a4869-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="a4869-505">Pole nejsou **svázána** .</span><span class="sxs-lookup"><span data-stu-id="a4869-505">Fields are **not** bound.</span></span>

<span data-ttu-id="a4869-506">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="a4869-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="a4869-507">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="a4869-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="a4869-508">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="a4869-509">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-509">Key</span></span>                   | <span data-ttu-id="a4869-510">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="a4869-511">Starship: název</span><span class="sxs-lookup"><span data-stu-id="a4869-511">starship:name</span></span>         | <span data-ttu-id="a4869-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="a4869-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="a4869-513">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="a4869-513">starship:registry</span></span>     | <span data-ttu-id="a4869-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="a4869-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="a4869-515">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="a4869-515">starship:class</span></span>        | <span data-ttu-id="a4869-516">Založen</span><span class="sxs-lookup"><span data-stu-id="a4869-516">Constitution</span></span>                                      |
| <span data-ttu-id="a4869-517">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="a4869-517">starship:length</span></span>       | <span data-ttu-id="a4869-518">304,8</span><span class="sxs-lookup"><span data-stu-id="a4869-518">304.8</span></span>                                             |
| <span data-ttu-id="a4869-519">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="a4869-519">starship:commissioned</span></span> | <span data-ttu-id="a4869-520">False</span><span class="sxs-lookup"><span data-stu-id="a4869-520">False</span></span>                                             |
| <span data-ttu-id="a4869-521">Patka</span><span class="sxs-lookup"><span data-stu-id="a4869-521">trademark</span></span>             | <span data-ttu-id="a4869-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="a4869-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="a4869-523">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="a4869-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="a4869-524">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="a4869-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="a4869-525">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="a4869-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="a4869-526">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="a4869-527">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="a4869-527">Bind to an object graph</span></span>

<span data-ttu-id="a4869-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="a4869-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="a4869-529">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="a4869-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="a4869-530">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="a4869-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="a4869-531">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="a4869-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="a4869-532">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a4869-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="a4869-533">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="a4869-534">[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="a4869-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a4869-535">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a4869-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="a4869-536">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a4869-537">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="a4869-537">Bind an array to a class</span></span>

<span data-ttu-id="a4869-538">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="a4869-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="a4869-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a4869-540">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="a4869-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="a4869-541">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="a4869-541">Binding is provided by convention.</span></span> <span data-ttu-id="a4869-542">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="a4869-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="a4869-543">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="a4869-543">**In-memory array processing**</span></span>

<span data-ttu-id="a4869-544">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="a4869-545">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-545">Key</span></span>             | <span data-ttu-id="a4869-546">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a4869-547">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="a4869-547">array:entries:0</span></span> | <span data-ttu-id="a4869-548">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-548">value0</span></span> |
| <span data-ttu-id="a4869-549">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="a4869-549">array:entries:1</span></span> | <span data-ttu-id="a4869-550">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-550">value1</span></span> |
| <span data-ttu-id="a4869-551">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="a4869-551">array:entries:2</span></span> | <span data-ttu-id="a4869-552">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-552">value2</span></span> |
| <span data-ttu-id="a4869-553">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="a4869-553">array:entries:4</span></span> | <span data-ttu-id="a4869-554">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-554">value4</span></span> |
| <span data-ttu-id="a4869-555">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="a4869-555">array:entries:5</span></span> | <span data-ttu-id="a4869-556">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-556">value5</span></span> |

<span data-ttu-id="a4869-557">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="a4869-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="a4869-558">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="a4869-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="a4869-559">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="a4869-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="a4869-560">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="a4869-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="a4869-561">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="a4869-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="a4869-562">[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="a4869-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="a4869-563">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="a4869-564">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a4869-565">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a4869-566">0</span><span class="sxs-lookup"><span data-stu-id="a4869-566">0</span></span>                            | <span data-ttu-id="a4869-567">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-567">value0</span></span>                       |
| <span data-ttu-id="a4869-568">1</span><span class="sxs-lookup"><span data-stu-id="a4869-568">1</span></span>                            | <span data-ttu-id="a4869-569">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-569">value1</span></span>                       |
| <span data-ttu-id="a4869-570">2</span><span class="sxs-lookup"><span data-stu-id="a4869-570">2</span></span>                            | <span data-ttu-id="a4869-571">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-571">value2</span></span>                       |
| <span data-ttu-id="a4869-572">3</span><span class="sxs-lookup"><span data-stu-id="a4869-572">3</span></span>                            | <span data-ttu-id="a4869-573">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-573">value4</span></span>                       |
| <span data-ttu-id="a4869-574">4</span><span class="sxs-lookup"><span data-stu-id="a4869-574">4</span></span>                            | <span data-ttu-id="a4869-575">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-575">value5</span></span>                       |

<span data-ttu-id="a4869-576">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="a4869-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a4869-577">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="a4869-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a4869-578">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="a4869-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a4869-579">Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="a4869-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="a4869-580">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="a4869-581">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="a4869-582">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="a4869-583">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="a4869-584">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-584">Key</span></span>             | <span data-ttu-id="a4869-585">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a4869-586">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="a4869-586">array:entries:3</span></span> | <span data-ttu-id="a4869-587">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a4869-587">value3</span></span> |

<span data-ttu-id="a4869-588">Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4869-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="a4869-589">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a4869-590">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a4869-591">0</span><span class="sxs-lookup"><span data-stu-id="a4869-591">0</span></span>                            | <span data-ttu-id="a4869-592">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-592">value0</span></span>                       |
| <span data-ttu-id="a4869-593">1</span><span class="sxs-lookup"><span data-stu-id="a4869-593">1</span></span>                            | <span data-ttu-id="a4869-594">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-594">value1</span></span>                       |
| <span data-ttu-id="a4869-595">2</span><span class="sxs-lookup"><span data-stu-id="a4869-595">2</span></span>                            | <span data-ttu-id="a4869-596">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-596">value2</span></span>                       |
| <span data-ttu-id="a4869-597">3</span><span class="sxs-lookup"><span data-stu-id="a4869-597">3</span></span>                            | <span data-ttu-id="a4869-598">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a4869-598">value3</span></span>                       |
| <span data-ttu-id="a4869-599">4</span><span class="sxs-lookup"><span data-stu-id="a4869-599">4</span></span>                            | <span data-ttu-id="a4869-600">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-600">value4</span></span>                       |
| <span data-ttu-id="a4869-601">5</span><span class="sxs-lookup"><span data-stu-id="a4869-601">5</span></span>                            | <span data-ttu-id="a4869-602">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-602">value5</span></span>                       |

<span data-ttu-id="a4869-603">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="a4869-603">**JSON array processing**</span></span>

<span data-ttu-id="a4869-604">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="a4869-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="a4869-605">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="a4869-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="a4869-606">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="a4869-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="a4869-607">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-607">Key</span></span>                     | <span data-ttu-id="a4869-608">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="a4869-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="a4869-609">json_array:key</span></span>          | <span data-ttu-id="a4869-610">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="a4869-610">valueA</span></span> |
| <span data-ttu-id="a4869-611">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="a4869-611">json_array:subsection:0</span></span> | <span data-ttu-id="a4869-612">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a4869-612">valueB</span></span> |
| <span data-ttu-id="a4869-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="a4869-613">json_array:subsection:1</span></span> | <span data-ttu-id="a4869-614">valueC</span><span class="sxs-lookup"><span data-stu-id="a4869-614">valueC</span></span> |
| <span data-ttu-id="a4869-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="a4869-615">json_array:subsection:2</span></span> | <span data-ttu-id="a4869-616">s</span><span class="sxs-lookup"><span data-stu-id="a4869-616">valueD</span></span> |

<span data-ttu-id="a4869-617">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="a4869-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="a4869-618">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="a4869-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="a4869-619">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="a4869-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="a4869-620">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a4869-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="a4869-621">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a4869-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="a4869-622">0</span><span class="sxs-lookup"><span data-stu-id="a4869-622">0</span></span>                                   | <span data-ttu-id="a4869-623">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a4869-623">valueB</span></span>                              |
| <span data-ttu-id="a4869-624">1</span><span class="sxs-lookup"><span data-stu-id="a4869-624">1</span></span>                                   | <span data-ttu-id="a4869-625">valueC</span><span class="sxs-lookup"><span data-stu-id="a4869-625">valueC</span></span>                              |
| <span data-ttu-id="a4869-626">2</span><span class="sxs-lookup"><span data-stu-id="a4869-626">2</span></span>                                   | <span data-ttu-id="a4869-627">s</span><span class="sxs-lookup"><span data-stu-id="a4869-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="a4869-628">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-628">Custom configuration provider</span></span>

<span data-ttu-id="a4869-629">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a4869-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a4869-630">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a4869-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a4869-631">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="a4869-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a4869-632">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a4869-633">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a4869-634">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="a4869-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a4869-635">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a4869-636">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4869-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a4869-637">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a4869-638">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="a4869-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a4869-639">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a4869-640">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a4869-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a4869-641">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a4869-642">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a4869-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a4869-643">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="a4869-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="a4869-644">Vzhledem k tomu, že v [konfiguračních klíčích nejsou](#keys)rozlišována velká a malá písmena, je vytvořen slovník použitý k inicializaci databáze s porovnáváním bez rozlišení velkých a malých písmen ([StringComparer. OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="a4869-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="a4869-645">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a4869-646">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a4869-647">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a4869-648">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="a4869-649">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="a4869-649">Access configuration during startup</span></span>

<span data-ttu-id="a4869-650">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4869-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4869-651">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a4869-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="a4869-652">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a4869-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="a4869-653">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="a4869-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="a4869-654">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a4869-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="a4869-655">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="a4869-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="a4869-656">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="a4869-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a4869-657">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="a4869-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="a4869-658">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a4869-659">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a4869-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4869-660">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a4869-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a4869-661">Konfigurace aplikací v ASP.NET Core je založená na páru klíč-hodnota vytvořených *poskytovateli konfigurací*.</span><span class="sxs-lookup"><span data-stu-id="a4869-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="a4869-662">Poskytovatelé konfigurace čtou konfigurační data do párů klíč-hodnota z nejrůznějších zdrojů konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="a4869-663">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-663">Azure Key Vault</span></span>
* <span data-ttu-id="a4869-664">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="a4869-664">Azure App Configuration</span></span>
* <span data-ttu-id="a4869-665">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-665">Command-line arguments</span></span>
* <span data-ttu-id="a4869-666">Vlastní poskytovatelé (nainstalováno nebo vytvořeno)</span><span class="sxs-lookup"><span data-stu-id="a4869-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="a4869-667">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="a4869-667">Directory files</span></span>
* <span data-ttu-id="a4869-668">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-668">Environment variables</span></span>
* <span data-ttu-id="a4869-669">Objekty .NET v paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-669">In-memory .NET objects</span></span>
* <span data-ttu-id="a4869-670">Soubory nastavení</span><span class="sxs-lookup"><span data-stu-id="a4869-670">Settings files</span></span>

<span data-ttu-id="a4869-671">Konfigurační balíčky pro scénáře zprostředkovatele běžných konfigurací ([Microsoft. Extensions. Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) jsou zahrnuté ve [službě Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a4869-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a4869-672">Příklady kódu, které následují a v ukázkové aplikaci používají obor názvů <xref:Microsoft.Extensions.Configuration>:</span><span class="sxs-lookup"><span data-stu-id="a4869-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="a4869-673">*Vzor možností* je rozšíření konceptů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="a4869-674">Možnosti používají třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="a4869-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="a4869-675">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a4869-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a4869-676">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4869-676">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="a4869-677">Konfigurace versus konfigurace aplikace</span><span class="sxs-lookup"><span data-stu-id="a4869-677">Host versus app configuration</span></span>

<span data-ttu-id="a4869-678">Před konfigurací a spuštěním aplikace je *hostitel* nakonfigurovaný a spuštěný.</span><span class="sxs-lookup"><span data-stu-id="a4869-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="a4869-679">Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací.</span><span class="sxs-lookup"><span data-stu-id="a4869-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="a4869-680">Aplikace i hostitel se konfigurují pomocí zprostředkovatelů konfigurace popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="a4869-681">V konfiguraci aplikace jsou také zahrnuty páry klíč-hodnota konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="a4869-682">Další informace o tom, jak se používají poskytovatelé konfigurace, když je hostitel sestavený a jak zdroje konfigurace ovlivňují konfiguraci hostitele, najdete v tématu <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="a4869-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="a4869-683">Další konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-683">Other configuration</span></span>

<span data-ttu-id="a4869-684">Toto téma se týká pouze *Konfigurace aplikace*.</span><span class="sxs-lookup"><span data-stu-id="a4869-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="a4869-685">Další aspekty používání a hostování ASP.NET Corech aplikací jsou nakonfigurované pomocí konfiguračních souborů, které nejsou popsané v tomto tématu:</span><span class="sxs-lookup"><span data-stu-id="a4869-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="a4869-686">*Launch. json*/*launchSettings. JSON* jsou konfigurační soubory nástroje pro vývojové prostředí, které jsou popsané tady:</span><span class="sxs-lookup"><span data-stu-id="a4869-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="a4869-687">V <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="a4869-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="a4869-688">V dokumentaci sady, kde se soubory používají ke konfiguraci ASP.NET Core aplikací pro vývojové scénáře.</span><span class="sxs-lookup"><span data-stu-id="a4869-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="a4869-689">*Web. config* je konfigurační soubor serveru, který je popsaný v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="a4869-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="a4869-690">Další informace o migraci konfigurace aplikace z dřívějších verzí ASP.NET najdete v tématu <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="a4869-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="a4869-691">Výchozí konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-691">Default configuration</span></span>

<span data-ttu-id="a4869-692">Webové aplikace založené na ASP.NET Core [dotnet nové](/dotnet/core/tools/dotnet-new) šablony volají <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> při sestavování hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="a4869-693">`CreateDefaultBuilder` poskytuje výchozí konfiguraci pro aplikaci v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="a4869-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="a4869-694">Následující postup platí pro aplikace používající [webového hostitele](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="a4869-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="a4869-695">Podrobnosti o výchozí konfiguraci při použití [obecného hostitele](xref:fundamentals/host/generic-host)najdete v [nejnovější verzi tohoto tématu](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="a4869-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="a4869-696">Konfigurace hostitele se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="a4869-697">Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`) pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a4869-698">Předpona (`ASPNETCORE_`) je odstraněna, když jsou načteny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="a4869-699">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="a4869-700">Konfigurace aplikace se poskytuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="a4869-701">*appSettings. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-702">*appSettings. {Environment}. JSON* s použitím [poskytovatele konfigurace souboru](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-703">[Správce tajných klíčů](xref:security/app-secrets) při spuštění aplikace v prostředí `Development` pomocí sestavení záznamu</span><span class="sxs-lookup"><span data-stu-id="a4869-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="a4869-704">Proměnné prostředí pomocí [poskytovatele konfigurace proměnných prostředí](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="a4869-705">Argumenty příkazového řádku, které používají [poskytovatele konfigurace příkazového řádku](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a4869-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="a4869-706">Zabezpečení</span><span class="sxs-lookup"><span data-stu-id="a4869-706">Security</span></span>

<span data-ttu-id="a4869-707">Při zabezpečování citlivých konfiguračních dat proveďte následující postupy:</span><span class="sxs-lookup"><span data-stu-id="a4869-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="a4869-708">Nikdy neukládejte hesla nebo další citlivá data v kódu poskytovatele konfigurace nebo v konfiguračních souborech s prostým textem.</span><span class="sxs-lookup"><span data-stu-id="a4869-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="a4869-709">Nepoužívejte provozní tajemství ve vývojových nebo testovacích prostředích.</span><span class="sxs-lookup"><span data-stu-id="a4869-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="a4869-710">Zadejte tajné klíče mimo projekt, aby se nemohly omylem potvrdit do úložiště zdrojového kódu.</span><span class="sxs-lookup"><span data-stu-id="a4869-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="a4869-711">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="a4869-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="a4869-712"><xref:security/app-secrets> &ndash; obsahuje rady týkající se používání proměnných prostředí k ukládání citlivých dat.</span><span class="sxs-lookup"><span data-stu-id="a4869-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="a4869-713">Správce tajného kódu používá zprostředkovatele konfigurace souborů k ukládání tajných klíčů uživatele do souboru JSON v místním systému.</span><span class="sxs-lookup"><span data-stu-id="a4869-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="a4869-714">Poskytovatel konfigurace souboru je popsán dále v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a4869-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="a4869-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) bezpečně ukládá tajné klíče aplikace pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="a4869-716">Další informace naleznete v tématu <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a4869-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="a4869-717">Hierarchická konfigurační data</span><span class="sxs-lookup"><span data-stu-id="a4869-717">Hierarchical configuration data</span></span>

<span data-ttu-id="a4869-718">Rozhraní API pro konfiguraci umožňuje udržovat hierarchická konfigurační data sloučením hierarchických dat s použitím oddělovače v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="a4869-719">V následujícím souboru JSON existují čtyři klíče ve strukturované hierarchii dvou oddílů:</span><span class="sxs-lookup"><span data-stu-id="a4869-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="a4869-720">Když je soubor čten do konfigurace, jsou vytvořeny jedinečné klíče pro zachování původní hierarchické struktury dat ve zdroji konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="a4869-721">Oddíly a klíče jsou shrnuty s použitím dvojtečky (`:`) k zachování původní struktury:</span><span class="sxs-lookup"><span data-stu-id="a4869-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="a4869-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-722">section0:key0</span></span>
* <span data-ttu-id="a4869-723">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-723">section0:key1</span></span>
* <span data-ttu-id="a4869-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-724">section1:key0</span></span>
* <span data-ttu-id="a4869-725">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-725">section1:key1</span></span>

<span data-ttu-id="a4869-726">metody <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> a <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> jsou k dispozici k izolaci oddílů a podřízených objektů oddílu v konfiguračních datech.</span><span class="sxs-lookup"><span data-stu-id="a4869-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="a4869-727">Tyto metody jsou popsány dále v [tématu GetSection, GetChildren a Exists](#getsection-getchildren-and-exists).</span><span class="sxs-lookup"><span data-stu-id="a4869-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="a4869-728">Zásady</span><span class="sxs-lookup"><span data-stu-id="a4869-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="a4869-729">Zdroje a poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="a4869-729">Sources and providers</span></span>

<span data-ttu-id="a4869-730">Při spuštění aplikace se zdroje konfigurace čtou v pořadí, v jakém jsou určení jejich poskytovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="a4869-731">Poskytovatelé konfigurace implementující zjišťování změn mají možnost znovu načíst konfiguraci při změně podkladového nastavení.</span><span class="sxs-lookup"><span data-stu-id="a4869-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="a4869-732">Například poskytovatel konfigurace souboru (popsaný dále v tomto tématu) a [zprostředkovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) implementují detekci změn.</span><span class="sxs-lookup"><span data-stu-id="a4869-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="a4869-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> je k dispozici v kontejneru pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a4869-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> lze vložit do <xref:Microsoft.AspNetCore.Mvc.Controller> Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> nebo MVC a získat tak konfiguraci pro třídu.</span><span class="sxs-lookup"><span data-stu-id="a4869-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="a4869-735">V následujících příkladech se pro přístup k hodnotám konfigurace používá pole `_config`:</span><span class="sxs-lookup"><span data-stu-id="a4869-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="a4869-736">Poskytovatelé konfigurace nemůžou využívat DI, protože není k dispozici, když je nastavil hostitel.</span><span class="sxs-lookup"><span data-stu-id="a4869-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="a4869-737">Klíče</span><span class="sxs-lookup"><span data-stu-id="a4869-737">Keys</span></span>

<span data-ttu-id="a4869-738">Konfigurační klíče přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a4869-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="a4869-739">U klíčů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="a4869-739">Keys are case-insensitive.</span></span> <span data-ttu-id="a4869-740">Například `ConnectionString` a `connectionstring` se považují za ekvivalentní klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="a4869-741">Pokud je hodnota pro stejný klíč nastavená stejným nebo jiným poskytovatelem konfigurace, použije se poslední hodnota nastavená v klíči.</span><span class="sxs-lookup"><span data-stu-id="a4869-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="a4869-742">Hierarchické klíče</span><span class="sxs-lookup"><span data-stu-id="a4869-742">Hierarchical keys</span></span>
  * <span data-ttu-id="a4869-743">V rozhraní API pro konfiguraci funguje oddělovač dvojtečky (`:`) na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a4869-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="a4869-744">V proměnných prostředí nemusí oddělovač dvojtečky fungovat na všech platformách.</span><span class="sxs-lookup"><span data-stu-id="a4869-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="a4869-745">Dvojité podtržítko (`__`) je podporováno všemi platformami a je automaticky převedeno na dvojtečku.</span><span class="sxs-lookup"><span data-stu-id="a4869-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="a4869-746">V Azure Key Vault hierarchické klíče používají `--` (dvě pomlčky) jako oddělovač.</span><span class="sxs-lookup"><span data-stu-id="a4869-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a4869-747">Napište kód, který nahradí pomlčky dvojtečkou, pokud jsou tajné klíče načteny do konfigurace aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="a4869-748"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a4869-749">Vazba pole je popsána v tématu [vazba pole na oddíl třídy](#bind-an-array-to-a-class) .</span><span class="sxs-lookup"><span data-stu-id="a4869-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="a4869-750">Hodnoty</span><span class="sxs-lookup"><span data-stu-id="a4869-750">Values</span></span>

<span data-ttu-id="a4869-751">Konfigurační hodnoty přijímají následující konvence:</span><span class="sxs-lookup"><span data-stu-id="a4869-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="a4869-752">Hodnoty jsou řetězce.</span><span class="sxs-lookup"><span data-stu-id="a4869-752">Values are strings.</span></span>
* <span data-ttu-id="a4869-753">Hodnoty null nelze uložit v konfiguraci ani svázat s objekty.</span><span class="sxs-lookup"><span data-stu-id="a4869-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="a4869-754">Poskytovatelé</span><span class="sxs-lookup"><span data-stu-id="a4869-754">Providers</span></span>

<span data-ttu-id="a4869-755">V následující tabulce jsou uvedeny poskytovatelé konfigurace dostupné pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="a4869-756">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-756">Provider</span></span> | <span data-ttu-id="a4869-757">Poskytuje konfiguraci z&hellip;</span><span class="sxs-lookup"><span data-stu-id="a4869-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="a4869-758">[Poskytovatel konfigurace Azure Key Vault](xref:security/key-vault-configuration) (témata*zabezpečení* )</span><span class="sxs-lookup"><span data-stu-id="a4869-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="a4869-759">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-759">Azure Key Vault</span></span> |
| <span data-ttu-id="a4869-760">[Poskytovatel konfigurace Azure App](/azure/azure-app-configuration/quickstart-aspnet-core-app) (dokumentace Azure)</span><span class="sxs-lookup"><span data-stu-id="a4869-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="a4869-761">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="a4869-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="a4869-762">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="a4869-763">Parametry příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-763">Command-line parameters</span></span> |
| [<span data-ttu-id="a4869-764">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="a4869-765">Vlastní zdroj</span><span class="sxs-lookup"><span data-stu-id="a4869-765">Custom source</span></span> |
| [<span data-ttu-id="a4869-766">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="a4869-767">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-767">Environment variables</span></span> |
| [<span data-ttu-id="a4869-768">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a4869-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="a4869-769">Soubory (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="a4869-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="a4869-770">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="a4869-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="a4869-771">Soubory adresáře</span><span class="sxs-lookup"><span data-stu-id="a4869-771">Directory files</span></span> |
| [<span data-ttu-id="a4869-772">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="a4869-773">Kolekce v paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-773">In-memory collections</span></span> |
| <span data-ttu-id="a4869-774">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (*bezpečnostní* témata)</span><span class="sxs-lookup"><span data-stu-id="a4869-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="a4869-775">Soubor v adresáři profilu uživatele</span><span class="sxs-lookup"><span data-stu-id="a4869-775">File in the user profile directory</span></span> |

<span data-ttu-id="a4869-776">Zdroje konfigurace jsou čteny v pořadí, ve kterém jsou jejich poskytovatelé konfigurace určení při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="a4869-777">Poskytovatelé konfigurace popsané v tomto tématu jsou popsány v abecedním pořadí, nikoli v pořadí, v jakém je kód uspořádá.</span><span class="sxs-lookup"><span data-stu-id="a4869-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="a4869-778">Seřazení zprostředkovatelů konfigurace v kódu, aby odpovídal prioritám pro základní zdroje konfigurace vyžadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="a4869-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="a4869-779">Typická posloupnost zprostředkovatelů konfigurace je:</span><span class="sxs-lookup"><span data-stu-id="a4869-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="a4869-780">Soubory (*appSettings. JSON*, *appSettings. { Environment}. JSON*, kde `{Environment}` je aktuální hostující prostředí aplikace)</span><span class="sxs-lookup"><span data-stu-id="a4869-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="a4869-781">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a4869-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="a4869-782">[Uživatelské klíče (správce tajných klíčů)](xref:security/app-secrets) (jenom vývojové prostředí)</span><span class="sxs-lookup"><span data-stu-id="a4869-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="a4869-783">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-783">Environment variables</span></span>
1. <span data-ttu-id="a4869-784">Argumenty příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-784">Command-line arguments</span></span>

<span data-ttu-id="a4869-785">Běžným postupem je umístit poskytovatele konfigurace příkazového řádku na poslední v řadě zprostředkovatelů, aby argumenty příkazového řádku mohly přepsat konfiguraci nastavenou ostatními zprostředkovateli.</span><span class="sxs-lookup"><span data-stu-id="a4869-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="a4869-786">Předchozí sekvence zprostředkovatelů se používá při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-787">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="a4869-788">Konfigurace tvůrce hostitele pomocí UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4869-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="a4869-789">Chcete-li nakonfigurovat tvůrce hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> v Tvůrci hostitele s konfigurací.</span><span class="sxs-lookup"><span data-stu-id="a4869-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="a4869-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="a4869-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="a4869-791">Při vytváření hostitele volejte `ConfigureAppConfiguration`, aby bylo možné určit poskytovatele konfigurace aplikace společně s automaticky přidanými `CreateDefaultBuilder`:</span><span class="sxs-lookup"><span data-stu-id="a4869-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="a4869-792">Přepsat předchozí konfiguraci pomocí argumentů příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="a4869-793">Chcete-li poskytnout konfiguraci aplikace, kterou lze přepsat pomocí argumentů příkazového řádku, zavolejte `AddCommandLine` Poslední:</span><span class="sxs-lookup"><span data-stu-id="a4869-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="a4869-794">Odebrat poskytovatele přidaných pomocí CreateDefaultBuilder</span><span class="sxs-lookup"><span data-stu-id="a4869-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="a4869-795">Chcete-li odebrat poskytovatele přidaných pomocí `CreateDefaultBuilder`[, zavolejte nejprve](/dotnet/api/system.collections.generic.icollection-1.clear) na [IConfigurationBuilder. sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) :</span><span class="sxs-lookup"><span data-stu-id="a4869-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="a4869-796">Využití konfigurace při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a4869-796">Consume configuration during app startup</span></span>

<span data-ttu-id="a4869-797">Konfigurace zadaná do aplikace v `ConfigureAppConfiguration` je dostupná během spouštění aplikace, včetně `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4869-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4869-798">Další informace najdete v části [Konfigurace přístupu během spuštění](#access-configuration-during-startup) .</span><span class="sxs-lookup"><span data-stu-id="a4869-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="a4869-799">Zprostředkovatel konfigurace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a4869-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="a4869-800"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> načítá konfiguraci z párů klíč-hodnota argumentu klíč-hodnota za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-801">Chcete-li aktivovat konfiguraci příkazového řádku, je jako instance <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>volána metoda rozšíření <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a4869-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-802">`AddCommandLine` je automaticky volána, když je volána `CreateDefaultBuilder(string [])`.</span><span class="sxs-lookup"><span data-stu-id="a4869-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="a4869-803">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-804">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-805">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a4869-806">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-807">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-807">Environment variables.</span></span>

<span data-ttu-id="a4869-808">`CreateDefaultBuilder` přidá poskytovatele konfigurace příkazového řádku naposledy.</span><span class="sxs-lookup"><span data-stu-id="a4869-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="a4869-809">Argumenty příkazového řádku předané v konfiguraci přepsání za běhu nastavené jinými poskytovateli.</span><span class="sxs-lookup"><span data-stu-id="a4869-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="a4869-810">`CreateDefaultBuilder` funguje při sestavení hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="a4869-811">Proto může konfigurace z příkazového řádku aktivované pomocí `CreateDefaultBuilder` ovlivnit způsob konfigurace hostitele.</span><span class="sxs-lookup"><span data-stu-id="a4869-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="a4869-812">Pro aplikace založené na šablonách ASP.NET Core `AddCommandLine` již byly volány `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-813">Chcete-li přidat další poskytovatele konfigurace a zachovat možnost přepsat konfiguraci z těchto zprostředkovatelů pomocí argumentů příkazového řádku, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddCommandLine` Last.</span><span class="sxs-lookup"><span data-stu-id="a4869-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="a4869-814">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-814">**Example**</span></span>

<span data-ttu-id="a4869-815">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span><span class="sxs-lookup"><span data-stu-id="a4869-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="a4869-816">Otevřete příkazový řádek v adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="a4869-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="a4869-817">Zadejte argument příkazového řádku pro příkaz `dotnet run` `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="a4869-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="a4869-818">Po spuštění aplikace otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-819">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro argument příkazového řádku konfigurace, který je k dispozici pro `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="a4869-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="a4869-820">Argumenty</span><span class="sxs-lookup"><span data-stu-id="a4869-820">Arguments</span></span>

<span data-ttu-id="a4869-821">Hodnota musí následovat po znaménku rovná se (`=`), nebo klíč musí obsahovat předponu (`--` nebo `/`), pokud se hodnota řídí mezerou.</span><span class="sxs-lookup"><span data-stu-id="a4869-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="a4869-822">Hodnota není povinná, pokud se používá znaménko rovná se (například `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="a4869-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="a4869-823">Klíčová předpona</span><span class="sxs-lookup"><span data-stu-id="a4869-823">Key prefix</span></span>               | <span data-ttu-id="a4869-824">Příklad</span><span class="sxs-lookup"><span data-stu-id="a4869-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="a4869-825">Bez předpony</span><span class="sxs-lookup"><span data-stu-id="a4869-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="a4869-826">Dvě pomlčky (`--`)</span><span class="sxs-lookup"><span data-stu-id="a4869-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="a4869-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="a4869-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="a4869-828">Lomítko (`/`)</span><span class="sxs-lookup"><span data-stu-id="a4869-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="a4869-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="a4869-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="a4869-830">V rámci stejného příkazu Nekombinujte páry klíč-hodnota argumentu příkazového řádku, které používají symbol rovná se s páry klíč-hodnota, které používají mezeru.</span><span class="sxs-lookup"><span data-stu-id="a4869-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="a4869-831">Příklady příkazů:</span><span class="sxs-lookup"><span data-stu-id="a4869-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="a4869-832">Mapování přepínačů</span><span class="sxs-lookup"><span data-stu-id="a4869-832">Switch mappings</span></span>

<span data-ttu-id="a4869-833">Mapování přepínačů povolují logiku nahrazení názvu klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="a4869-834">Při ručním sestavování konfigurace pomocí <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>zadejte do metody <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> slovník přepínačů pro nahrazení.</span><span class="sxs-lookup"><span data-stu-id="a4869-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="a4869-835">Při použití slovníku mapování přepínačů je slovník zaškrtnut pro klíč, který odpovídá klíči poskytnutému argumentem příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="a4869-836">Pokud se klíč příkazového řádku nachází ve slovníku, vrátí se hodnota slovníku (nahrazení klíče) zpět, aby se v konfiguraci aplikace nastavil pár klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="a4869-837">Mapování přepínačů je vyžadováno pro jakýkoliv klíč příkazového řádku s jedním spojovníkem (`-`).</span><span class="sxs-lookup"><span data-stu-id="a4869-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="a4869-838">Pravidla klíče slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="a4869-839">Přepínače musí začínat spojovníkem (`-`) nebo dvojitou pomlčkou (`--`).</span><span class="sxs-lookup"><span data-stu-id="a4869-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="a4869-840">Slovník mapování přepínačů nesmí obsahovat duplicitní klíče.</span><span class="sxs-lookup"><span data-stu-id="a4869-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="a4869-841">Vytvořte slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="a4869-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="a4869-842">V následujícím příkladu se vytvoří mapování dvou přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="a4869-843">Po sestavení hostitele volejte `AddCommandLine` pomocí slovníku mapování přepínačů:</span><span class="sxs-lookup"><span data-stu-id="a4869-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="a4869-844">Pro aplikace, které používají mapování přepínačů, by volání `CreateDefaultBuilder` nemělo předávat argumenty.</span><span class="sxs-lookup"><span data-stu-id="a4869-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="a4869-845">Volání `AddCommandLine` metody `CreateDefaultBuilder` nezahrnuje mapované přepínače a neexistuje žádný způsob, jak předávat slovník mapování přepínačů na `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-846">Řešení nepředá argumenty `CreateDefaultBuilder`, ale namísto toho umožní metodě `AddCommandLine` `ConfigurationBuilder` metody zpracovat argumenty a slovník mapování přepínačů.</span><span class="sxs-lookup"><span data-stu-id="a4869-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="a4869-847">Po vytvoření slovníku mapování přepínačů obsahuje data uvedená v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="a4869-848">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-848">Key</span></span>       | <span data-ttu-id="a4869-849">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="a4869-850">Pokud se klíče mapovaného přepínače používají při spuštění aplikace, konfigurace obdrží hodnotu konfigurace klíče poskytnutého slovníkem:</span><span class="sxs-lookup"><span data-stu-id="a4869-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="a4869-851">Po spuštění předchozího příkazu obsahuje konfigurace hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="a4869-852">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-852">Key</span></span>               | <span data-ttu-id="a4869-853">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="a4869-854">Poskytovatel konfigurace proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="a4869-855"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> načte konfiguraci z páru klíč-hodnota proměnné prostředí za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-856">Chcete-li aktivovat konfiguraci proměnných prostředí, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="a4869-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) povoluje nastavení proměnných prostředí na webu Azure Portal, které mohou přepsat konfiguraci aplikace pomocí poskytovatele konfigurace proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="a4869-858">Další informace najdete v tématu [aplikace Azure: přepište konfiguraci aplikace pomocí webu Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="a4869-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="a4869-859">`AddEnvironmentVariables` se používá k načtení proměnných prostředí s předponou `ASPNETCORE_` pro [konfiguraci hostitele](#host-versus-app-configuration) při inicializaci nového hostitele s [webovým hostitelem](xref:fundamentals/host/web-host) a volání `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="a4869-860">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-861">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-862">Konfigurace aplikace z neprefixových proměnných prostředí voláním `AddEnvironmentVariables` bez předpony.</span><span class="sxs-lookup"><span data-stu-id="a4869-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="a4869-863">Volitelná konfigurace z *appSettings. JSON* a *appSettings. { Soubory Environment}. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="a4869-864">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-865">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-865">Command-line arguments.</span></span>

<span data-ttu-id="a4869-866">Poskytovatel konfigurace proměnných prostředí se volá po vytvoření konfigurace z uživatelských tajných kódů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="a4869-867">Volání zprostředkovatele v této pozici umožňuje, aby proměnné prostředí byly čteny za běhu, aby bylo možné přepsat konfiguraci nastavenou pomocí tajných kódů uživatelů a souborů *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="a4869-868">Chcete-li poskytnout konfiguraci aplikace z dalších proměnných prostředí, zavolejte další poskytovatele aplikace v `ConfigureAppConfiguration` a zavolejte `AddEnvironmentVariables` s předponou:</span><span class="sxs-lookup"><span data-stu-id="a4869-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="a4869-869">Pokud chcete, aby proměnné prostředí s danou předponou přepsaly hodnoty od jiných zprostředkovatelů, zavolejte `AddEnvironmentVariables` jako poslední.</span><span class="sxs-lookup"><span data-stu-id="a4869-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="a4869-870">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-870">**Example**</span></span>

<span data-ttu-id="a4869-871">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, který obsahuje volání `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a4869-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="a4869-872">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-872">Run the sample app.</span></span> <span data-ttu-id="a4869-873">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-874">Všimněte si, že výstup obsahuje dvojici klíč-hodnota pro proměnnou prostředí `ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="a4869-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="a4869-875">Hodnota odráží prostředí, ve kterém je aplikace spuštěná, obvykle `Development` při místním spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="a4869-876">Chcete-li zachovat seznam proměnných prostředí vygenerovaných aplikací v krátkém prostředí, aplikace filtruje proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="a4869-877">Podívejte se na soubor *pages/index. cshtml. cs* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="a4869-878">Pokud chcete zpřístupnit všechny proměnné prostředí dostupné pro aplikaci, změňte `FilteredConfiguration` na *stránce pages/index. cshtml. cs* na následující:</span><span class="sxs-lookup"><span data-stu-id="a4869-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="a4869-879">Předpony</span><span class="sxs-lookup"><span data-stu-id="a4869-879">Prefixes</span></span>

<span data-ttu-id="a4869-880">Proměnné prostředí načtené do konfigurace aplikace jsou filtrovány při zadání předpony metody `AddEnvironmentVariables`.</span><span class="sxs-lookup"><span data-stu-id="a4869-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="a4869-881">Chcete-li například filtrovat proměnné prostředí v `CUSTOM_`předpony, zadejte předponu do poskytovatele konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="a4869-882">Pokud jsou vytvořeny páry klíč-hodnota konfigurace, je předpona odstraněna.</span><span class="sxs-lookup"><span data-stu-id="a4869-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="a4869-883">Když je vytvořen tvůrce hostitele, konfigurace hostitele je poskytována proměnnými prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="a4869-884">Další informace o předponě používané pro tyto proměnné prostředí naleznete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-885">**Předpony připojovacího řetězce**</span><span class="sxs-lookup"><span data-stu-id="a4869-885">**Connection string prefixes**</span></span>

<span data-ttu-id="a4869-886">Rozhraní API pro konfiguraci má speciální pravidla zpracování pro čtyři proměnné prostředí připojovacích řetězců, které se podílejí na konfiguraci připojovacích řetězců Azure pro prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="a4869-887">Proměnné prostředí s předponami, které jsou uvedeny v tabulce, se načtou do aplikace, pokud není k `AddEnvironmentVariables`zadána žádná předpona.</span><span class="sxs-lookup"><span data-stu-id="a4869-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="a4869-888">Předpona připojovacího řetězce</span><span class="sxs-lookup"><span data-stu-id="a4869-888">Connection string prefix</span></span> | <span data-ttu-id="a4869-889">Poskytovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="a4869-890">Vlastní zprostředkovatel</span><span class="sxs-lookup"><span data-stu-id="a4869-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="a4869-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="a4869-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="a4869-892">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="a4869-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="a4869-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="a4869-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="a4869-894">Když je proměnná prostředí zjištěna a načtena do konfigurace se všemi čtyřmi předponami, které jsou uvedeny v tabulce:</span><span class="sxs-lookup"><span data-stu-id="a4869-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="a4869-895">Konfigurační klíč se vytvoří odebráním předpony proměnné prostředí a přidáním oddílu konfiguračního klíče (`ConnectionStrings`).</span><span class="sxs-lookup"><span data-stu-id="a4869-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="a4869-896">Vytvoří se nová dvojice klíč-hodnota konfigurace, která představuje poskytovatele připojení databáze (s výjimkou `CUSTOMCONNSTR_`, který nemá zadaného poskytovatele).</span><span class="sxs-lookup"><span data-stu-id="a4869-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="a4869-897">Klíč proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="a4869-897">Environment variable key</span></span> | <span data-ttu-id="a4869-898">Konfigurační klíč převedený na převod</span><span class="sxs-lookup"><span data-stu-id="a4869-898">Converted configuration key</span></span> | <span data-ttu-id="a4869-899">Položka konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="a4869-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-900">Položka konfigurace není vytvořená.</span><span class="sxs-lookup"><span data-stu-id="a4869-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-901">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-902">Hodnota: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-903">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-904">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="a4869-905">Klíč: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="a4869-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="a4869-906">Hodnota: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="a4869-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="a4869-907">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-907">**Example**</span></span>

<span data-ttu-id="a4869-908">Na serveru se vytvoří proměnná prostředí vlastního připojovacího řetězce:</span><span class="sxs-lookup"><span data-stu-id="a4869-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="a4869-909">Název &ndash; `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="a4869-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="a4869-910">Hodnota &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="a4869-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="a4869-911">Pokud je `IConfiguration` vložená a přiřazená k poli s názvem `_config`, přečtěte si hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a4869-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="a4869-912">Poskytovatel konfigurace souboru</span><span class="sxs-lookup"><span data-stu-id="a4869-912">File Configuration Provider</span></span>

<span data-ttu-id="a4869-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> je základní třídou pro načítání konfigurace ze systému souborů.</span><span class="sxs-lookup"><span data-stu-id="a4869-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="a4869-914">Následující poskytovatelé konfigurace jsou vyhrazeni pro určité typy souborů:</span><span class="sxs-lookup"><span data-stu-id="a4869-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="a4869-915">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a4869-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="a4869-916">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a4869-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="a4869-917">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a4869-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="a4869-918">Poskytovatel konfigurace INI</span><span class="sxs-lookup"><span data-stu-id="a4869-918">INI Configuration Provider</span></span>

<span data-ttu-id="a4869-919"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> načte konfiguraci z párů klíč-hodnota souboru INI za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-920">Chcete-li aktivovat konfiguraci souboru INI, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-921">Dvojtečku lze použít jako oddělovač oddílů v konfiguraci souboru INI.</span><span class="sxs-lookup"><span data-stu-id="a4869-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="a4869-922">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-923">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-923">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-924">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-925"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-926">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-927">Obecný příklad konfiguračního souboru INI:</span><span class="sxs-lookup"><span data-stu-id="a4869-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="a4869-928">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-929">section0:key0</span></span>
* <span data-ttu-id="a4869-930">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-930">section0:key1</span></span>
* <span data-ttu-id="a4869-931">Section1: dílčí oddíl: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-931">section1:subsection:key</span></span>
* <span data-ttu-id="a4869-932">section2: subsection0: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-932">section2:subsection0:key</span></span>
* <span data-ttu-id="a4869-933">section2: subsection1: klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="a4869-934">Zprostředkovatel konfigurace JSON</span><span class="sxs-lookup"><span data-stu-id="a4869-934">JSON Configuration Provider</span></span>

<span data-ttu-id="a4869-935"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru JSON během běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="a4869-936">Chcete-li aktivovat konfiguraci souboru JSON, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-937">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-938">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-938">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-939">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-940"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-941">`AddJsonFile` se automaticky volá dvakrát při inicializaci nového hostitele pomocí `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="a4869-942">Metoda je volána pro načtení konfigurace z:</span><span class="sxs-lookup"><span data-stu-id="a4869-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="a4869-943">*appSettings. json* &ndash; tento soubor je nejdřív načtený.</span><span class="sxs-lookup"><span data-stu-id="a4869-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="a4869-944">Verze prostředí souboru může přepsat hodnoty poskytnuté souborem *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="a4869-945">*appSettings. {Environment}. JSON* &ndash; verze prostředí souboru je načtená na základě rozhraní [IHostingEnvironment. Environment](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span><span class="sxs-lookup"><span data-stu-id="a4869-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="a4869-946">Další informace najdete v části [výchozí konfigurační](#default-configuration) oddíl.</span><span class="sxs-lookup"><span data-stu-id="a4869-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="a4869-947">`CreateDefaultBuilder` také načte:</span><span class="sxs-lookup"><span data-stu-id="a4869-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="a4869-948">Proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-948">Environment variables.</span></span>
* <span data-ttu-id="a4869-949">[Uživatelských tajných kódů (správce tajných klíčů)](xref:security/app-secrets) ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="a4869-950">Argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a4869-950">Command-line arguments.</span></span>

<span data-ttu-id="a4869-951">Jako první se navázal Poskytovatel konfigurace JSON.</span><span class="sxs-lookup"><span data-stu-id="a4869-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="a4869-952">Proto klíče uživatele, proměnné prostředí a argumenty příkazového řádku přepíší konfiguraci nastavenou soubory *appSettings* .</span><span class="sxs-lookup"><span data-stu-id="a4869-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="a4869-953">Při sestavování hostitele zavolá `ConfigureAppConfiguration`, aby se určila konfigurace aplikace pro jiné soubory než *appSettings. JSON* a *appSettings. { Prostředí}. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-954">**Příklad**</span><span class="sxs-lookup"><span data-stu-id="a4869-954">**Example**</span></span>

<span data-ttu-id="a4869-955">Ukázková aplikace využívá metodu statického usnadnění `CreateDefaultBuilder` k sestavení hostitele, což zahrnuje dvě volání `AddJsonFile`:</span><span class="sxs-lookup"><span data-stu-id="a4869-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="a4869-956">První volání `AddJsonFile` načte konfiguraci z *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="a4869-957">Druhé volání `AddJsonFile` načte konfiguraci z *appSettings. { Environment}. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a4869-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="a4869-958">Pro *appSettings. Vývoj. JSON* v ukázkové aplikaci je načtený následující soubor:</span><span class="sxs-lookup"><span data-stu-id="a4869-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="a4869-959">Spuštění ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-959">Run the sample app.</span></span> <span data-ttu-id="a4869-960">Otevřete v aplikaci prohlížeč na `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a4869-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="a4869-961">Výstup obsahuje páry klíč-hodnota pro konfiguraci na základě prostředí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="a4869-962">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Debug` při spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="a4869-963">Znovu spusťte ukázkovou aplikaci v produkčním prostředí:</span><span class="sxs-lookup"><span data-stu-id="a4869-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="a4869-964">Otevřete soubor *Properties/launchSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a4869-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="a4869-965">V profilu `ConfigurationSample` změňte hodnotu proměnné prostředí `ASPNETCORE_ENVIRONMENT` na `Production`.</span><span class="sxs-lookup"><span data-stu-id="a4869-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="a4869-966">Uložte soubor a spusťte aplikaci pomocí `dotnet run` v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a4869-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="a4869-967">Nastavení v souboru *appSettings. Vývoj. JSON* již nepřepisuje nastavení v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="a4869-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="a4869-968">Úroveň protokolu pro Key `Logging:LogLevel:Default` je `Warning`.</span><span class="sxs-lookup"><span data-stu-id="a4869-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="a4869-969">Poskytovatel konfigurace XML</span><span class="sxs-lookup"><span data-stu-id="a4869-969">XML Configuration Provider</span></span>

<span data-ttu-id="a4869-970"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> načítá konfiguraci z párů klíč-hodnota souboru XML za běhu.</span><span class="sxs-lookup"><span data-stu-id="a4869-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="a4869-971">Chcete-li aktivovat konfiguraci souboru XML, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-972">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-973">Zda je soubor nepovinný.</span><span class="sxs-lookup"><span data-stu-id="a4869-973">Whether the file is optional.</span></span>
* <span data-ttu-id="a4869-974">Určuje, zda je konfigurace znovu načtena v případě, že dojde ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="a4869-975"><xref:Microsoft.Extensions.FileProviders.IFileProvider>, který se používá pro přístup k souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="a4869-976">Kořenový uzel konfiguračního souboru je ignorován, pokud jsou vytvořeny páry klíč-hodnota konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="a4869-977">Nezadávejte definici typu dokumentu (DTD) nebo obor názvů v souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="a4869-978">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="a4869-979">Konfigurační soubory XML mohou používat odlišné názvy elementů pro opakující se oddíly:</span><span class="sxs-lookup"><span data-stu-id="a4869-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="a4869-980">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-981">section0:key0</span></span>
* <span data-ttu-id="a4869-982">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-982">section0:key1</span></span>
* <span data-ttu-id="a4869-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-983">section1:key0</span></span>
* <span data-ttu-id="a4869-984">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-984">section1:key1</span></span>

<span data-ttu-id="a4869-985">Opakující se prvky, které používají stejný název elementu fungují, pokud atribut `name` slouží k rozlišení prvků:</span><span class="sxs-lookup"><span data-stu-id="a4869-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="a4869-986">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-987">section:section0:key:key0</span></span>
* <span data-ttu-id="a4869-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="a4869-988">section:section0:key:key1</span></span>
* <span data-ttu-id="a4869-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-989">section:section1:key:key0</span></span>
* <span data-ttu-id="a4869-990">oddíl: Section1: Key: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-990">section:section1:key:key1</span></span>

<span data-ttu-id="a4869-991">Atributy lze použít k zadávání hodnot:</span><span class="sxs-lookup"><span data-stu-id="a4869-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="a4869-992">Předchozí konfigurační soubor načte následující klíče pomocí `value`:</span><span class="sxs-lookup"><span data-stu-id="a4869-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="a4869-993">Key: – atribut</span><span class="sxs-lookup"><span data-stu-id="a4869-993">key:attribute</span></span>
* <span data-ttu-id="a4869-994">oddíl: klíč: atribut</span><span class="sxs-lookup"><span data-stu-id="a4869-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="a4869-995">Poskytovatel konfigurace klíče na soubor</span><span class="sxs-lookup"><span data-stu-id="a4869-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="a4869-996"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> používá soubory adresáře jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="a4869-997">Klíč je název souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-997">The key is the file name.</span></span> <span data-ttu-id="a4869-998">Hodnota obsahuje obsah souboru.</span><span class="sxs-lookup"><span data-stu-id="a4869-998">The value contains the file's contents.</span></span> <span data-ttu-id="a4869-999">Poskytovatel konfigurace klíče na soubor se používá ve scénářích hostování Docker.</span><span class="sxs-lookup"><span data-stu-id="a4869-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="a4869-1000">Chcete-li aktivovat konfiguraci klíče na soubor, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="a4869-1001">`directoryPath` souborů musí být absolutní cesta.</span><span class="sxs-lookup"><span data-stu-id="a4869-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="a4869-1002">Přetížení umožňují zadat:</span><span class="sxs-lookup"><span data-stu-id="a4869-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="a4869-1003">Delegát `Action<KeyPerFileConfigurationSource>`, který konfiguruje zdroj.</span><span class="sxs-lookup"><span data-stu-id="a4869-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="a4869-1004">Zda je adresář nepovinný a cesta k adresáři.</span><span class="sxs-lookup"><span data-stu-id="a4869-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="a4869-1005">Dvojité podtržítko (`__`) se používá jako oddělovač konfiguračního klíče v názvech souborů.</span><span class="sxs-lookup"><span data-stu-id="a4869-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="a4869-1006">Například název souboru `Logging__LogLevel__System` vytvoří konfigurační klíč `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="a4869-1007">Při sestavování hostitele, který určuje konfiguraci aplikace, volejte `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="a4869-1008">Poskytovatel konfigurace paměti</span><span class="sxs-lookup"><span data-stu-id="a4869-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="a4869-1009"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> používá kolekci v paměti jako konfigurační páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a4869-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="a4869-1010">Chcete-li aktivovat konfiguraci kolekce v paměti, zavolejte metodu rozšíření <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> v instanci <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="a4869-1011">Zprostředkovatele konfigurace lze inicializovat pomocí `IEnumerable<KeyValuePair<String,String>>`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="a4869-1012">Při sestavování hostitele k určení konfigurace aplikace volejte `ConfigureAppConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="a4869-1013">V následujícím příkladu je vytvořen konfigurační slovník:</span><span class="sxs-lookup"><span data-stu-id="a4869-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="a4869-1014">Slovník se používá s voláním `AddInMemoryCollection` k poskytnutí konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="a4869-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="a4869-1015">GetValue</span></span>

<span data-ttu-id="a4869-1016">[ConfigurationBinder. GetValue\<t >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahuje jednu hodnotu z konfigurace se zadaným klíčem a převede ji na zadaný typ nekolekce.</span><span class="sxs-lookup"><span data-stu-id="a4869-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="a4869-1017">Přetížení přijímá výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="a4869-1018">Následující příklad:</span><span class="sxs-lookup"><span data-stu-id="a4869-1018">The following example:</span></span>

* <span data-ttu-id="a4869-1019">Extrahuje hodnotu řetězce z konfigurace pomocí klíče `NumberKey`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="a4869-1020">Pokud se v konfiguračních klíčích `NumberKey` nenajde, použije se výchozí hodnota `99`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="a4869-1021">Zadá hodnotu jako `int`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="a4869-1022">Ukládá hodnotu vlastnosti `NumberConfig` pro použití stránkou.</span><span class="sxs-lookup"><span data-stu-id="a4869-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="a4869-1023">GetSection, GetChildren a Exists</span><span class="sxs-lookup"><span data-stu-id="a4869-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="a4869-1024">V následujících příkladech zvažte následující soubor JSON.</span><span class="sxs-lookup"><span data-stu-id="a4869-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="a4869-1025">Čtyři klíče se nacházejí ve dvou oddílech, z nichž jeden obsahuje dvojici dílčích částí:</span><span class="sxs-lookup"><span data-stu-id="a4869-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="a4869-1026">Když je soubor čten do konfigurace, vytvoří se následující jedinečné hierarchické klíče, které budou obsahovat hodnoty konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="a4869-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-1027">section0:key0</span></span>
* <span data-ttu-id="a4869-1028">section0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-1028">section0:key1</span></span>
* <span data-ttu-id="a4869-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-1029">section1:key0</span></span>
* <span data-ttu-id="a4869-1030">Section1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-1030">section1:key1</span></span>
* <span data-ttu-id="a4869-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="a4869-1032">section2: subsection0: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="a4869-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="a4869-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="a4869-1034">section2: subsection1: klíč1</span><span class="sxs-lookup"><span data-stu-id="a4869-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="a4869-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="a4869-1035">GetSection</span></span>

<span data-ttu-id="a4869-1036">[IConfiguration. GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahuje pododdíl konfigurace se zadaným klíčem dílčího oddílu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="a4869-1037">Chcete-li vrátit <xref:Microsoft.Extensions.Configuration.IConfigurationSection> obsahující pouze páry klíč-hodnota v `section1`, zavolejte `GetSection` a zadejte název oddílu:</span><span class="sxs-lookup"><span data-stu-id="a4869-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="a4869-1038">`configSection` nemá hodnotu, jenom klíč a cestu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="a4869-1039">Podobně pokud chcete získat hodnoty pro klíče v `section2:subsection0`, zavolejte `GetSection` a zadejte cestu k oddílu:</span><span class="sxs-lookup"><span data-stu-id="a4869-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="a4869-1040">`GetSection` nikdy nevrátí `null`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="a4869-1041">Pokud se nenalezne shodný oddíl, vrátí se prázdná `IConfigurationSection`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="a4869-1042">Když `GetSection` vrátí vyhovující oddíl, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> se nezadá.</span><span class="sxs-lookup"><span data-stu-id="a4869-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="a4869-1043">Když oddíl existuje, vrátí se <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> a <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="a4869-1044">GetChildren –</span><span class="sxs-lookup"><span data-stu-id="a4869-1044">GetChildren</span></span>

<span data-ttu-id="a4869-1045">Volání [IConfiguration. GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) v `section2` získá `IEnumerable<IConfigurationSection>`, které obsahuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="a4869-1046">Existuje</span><span class="sxs-lookup"><span data-stu-id="a4869-1046">Exists</span></span>

<span data-ttu-id="a4869-1047">Použijte [ConfigurationExtensions. existuje](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) k určení, zda konfigurační oddíl existuje:</span><span class="sxs-lookup"><span data-stu-id="a4869-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="a4869-1048">Vzhledem k ukázkovým datům je `sectionExists` `false`, protože konfigurační data nejsou v části `section2:subsection2`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="a4869-1049">Vazba na třídu</span><span class="sxs-lookup"><span data-stu-id="a4869-1049">Bind to a class</span></span>

<span data-ttu-id="a4869-1050">Konfigurace může být vázána na třídy, které reprezentují skupiny souvisejících nastavení pomocí *vzoru možností*.</span><span class="sxs-lookup"><span data-stu-id="a4869-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="a4869-1051">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="a4869-1052">Konfigurační hodnoty jsou vraceny jako řetězce, ale volání <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> umožňuje konstrukci objektů [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) .</span><span class="sxs-lookup"><span data-stu-id="a4869-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="a4869-1053">Pořadač váže hodnoty ke všem veřejným vlastnostem pro čtení a zápis zadaného typu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="a4869-1054">Pole nejsou **svázána** .</span><span class="sxs-lookup"><span data-stu-id="a4869-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="a4869-1055">Ukázková aplikace obsahuje model `Starship` (*modely/Starship. cs*):</span><span class="sxs-lookup"><span data-stu-id="a4869-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="a4869-1056">V části `starship` souboru *Starship. JSON* se vytvoří konfigurace, když ukázková aplikace pomocí zprostředkovatele konfigurace JSON načte konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="a4869-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="a4869-1057">Vytvoří se následující páry klíč-hodnota konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="a4869-1058">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-1058">Key</span></span>                   | <span data-ttu-id="a4869-1059">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="a4869-1060">Starship: název</span><span class="sxs-lookup"><span data-stu-id="a4869-1060">starship:name</span></span>         | <span data-ttu-id="a4869-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="a4869-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="a4869-1062">Starship: Registry</span><span class="sxs-lookup"><span data-stu-id="a4869-1062">starship:registry</span></span>     | <span data-ttu-id="a4869-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="a4869-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="a4869-1064">Starship: třída</span><span class="sxs-lookup"><span data-stu-id="a4869-1064">starship:class</span></span>        | <span data-ttu-id="a4869-1065">Založen</span><span class="sxs-lookup"><span data-stu-id="a4869-1065">Constitution</span></span>                                      |
| <span data-ttu-id="a4869-1066">Starship: délka</span><span class="sxs-lookup"><span data-stu-id="a4869-1066">starship:length</span></span>       | <span data-ttu-id="a4869-1067">304,8</span><span class="sxs-lookup"><span data-stu-id="a4869-1067">304.8</span></span>                                             |
| <span data-ttu-id="a4869-1068">Starship: vyřazení z provozu</span><span class="sxs-lookup"><span data-stu-id="a4869-1068">starship:commissioned</span></span> | <span data-ttu-id="a4869-1069">False</span><span class="sxs-lookup"><span data-stu-id="a4869-1069">False</span></span>                                             |
| <span data-ttu-id="a4869-1070">Patka</span><span class="sxs-lookup"><span data-stu-id="a4869-1070">trademark</span></span>             | <span data-ttu-id="a4869-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="a4869-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="a4869-1072">Ukázková aplikace volá `GetSection` s klíčem `starship`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="a4869-1073">Páry klíč-hodnota `starship` jsou izolované.</span><span class="sxs-lookup"><span data-stu-id="a4869-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="a4869-1074">Metoda `Bind` je volána v podsekci předávání instance `Starship` třídy.</span><span class="sxs-lookup"><span data-stu-id="a4869-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="a4869-1075">Po vytvoření vazby hodnot instance je instance přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="a4869-1076">Vytvoření vazby na graf objektů</span><span class="sxs-lookup"><span data-stu-id="a4869-1076">Bind to an object graph</span></span>

<span data-ttu-id="a4869-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> je schopný svázat celý graf objektů POCO.</span><span class="sxs-lookup"><span data-stu-id="a4869-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="a4869-1078">Stejně jako u vazeb jednoduchých objektů jsou vázány pouze veřejné vlastnosti čtení a zápisu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="a4869-1079">Ukázka obsahuje model `TvShow`, jehož graf objektů zahrnuje `Metadata` a třídy `Actors` (*modely/TvShow. cs*):</span><span class="sxs-lookup"><span data-stu-id="a4869-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="a4869-1080">Ukázková aplikace obsahuje soubor *tvshow. XML* obsahující konfigurační data:</span><span class="sxs-lookup"><span data-stu-id="a4869-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="a4869-1081">Konfigurace je svázána s celým grafem `TvShow` objektů pomocí metody `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="a4869-1082">Vázaná instance je přiřazena k vlastnosti pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="a4869-1083">[ConfigurationBinder. Get >\<t](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) vytvoří vazby a vrátí zadaný typ.</span><span class="sxs-lookup"><span data-stu-id="a4869-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="a4869-1084">`Get<T>` je pohodlnější než použití `Bind`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="a4869-1085">Následující kód ukazuje, jak použít `Get<T>` s předchozím příkladem, který umožňuje, aby se vázaná instance přímo přiřadila k vlastnosti používané pro vykreslování:</span><span class="sxs-lookup"><span data-stu-id="a4869-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a4869-1086">Vazba pole na třídu</span><span class="sxs-lookup"><span data-stu-id="a4869-1086">Bind an array to a class</span></span>

<span data-ttu-id="a4869-1087">*Ukázková aplikace ukazuje koncepty popsané v této části.*</span><span class="sxs-lookup"><span data-stu-id="a4869-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="a4869-1088"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> podporuje vazby polí na objekty pomocí indexů pole v konfiguračních klíčích.</span><span class="sxs-lookup"><span data-stu-id="a4869-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="a4869-1089">Libovolný formát pole, který zveřejňuje numerický klíčový segment (`:0:`, `:1:`, &hellip; `:{n}:`), je schopný vytvořit vazbu pole k poli třídy POCO.</span><span class="sxs-lookup"><span data-stu-id="a4869-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="a4869-1090">Vazba je poskytována podle konvence.</span><span class="sxs-lookup"><span data-stu-id="a4869-1090">Binding is provided by convention.</span></span> <span data-ttu-id="a4869-1091">Vlastní poskytovatelé konfigurace nejsou k implementaci vazby pole potřeba.</span><span class="sxs-lookup"><span data-stu-id="a4869-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="a4869-1092">**Zpracování pole v paměti**</span><span class="sxs-lookup"><span data-stu-id="a4869-1092">**In-memory array processing**</span></span>

<span data-ttu-id="a4869-1093">Vezměte v úvahu konfigurační klíče a hodnoty uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="a4869-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="a4869-1094">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-1094">Key</span></span>             | <span data-ttu-id="a4869-1095">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a4869-1096">pole: položky: 0</span><span class="sxs-lookup"><span data-stu-id="a4869-1096">array:entries:0</span></span> | <span data-ttu-id="a4869-1097">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-1097">value0</span></span> |
| <span data-ttu-id="a4869-1098">pole: položky: 1</span><span class="sxs-lookup"><span data-stu-id="a4869-1098">array:entries:1</span></span> | <span data-ttu-id="a4869-1099">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-1099">value1</span></span> |
| <span data-ttu-id="a4869-1100">pole: položky: 2</span><span class="sxs-lookup"><span data-stu-id="a4869-1100">array:entries:2</span></span> | <span data-ttu-id="a4869-1101">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-1101">value2</span></span> |
| <span data-ttu-id="a4869-1102">pole: položky: 4</span><span class="sxs-lookup"><span data-stu-id="a4869-1102">array:entries:4</span></span> | <span data-ttu-id="a4869-1103">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-1103">value4</span></span> |
| <span data-ttu-id="a4869-1104">pole: položky: 5</span><span class="sxs-lookup"><span data-stu-id="a4869-1104">array:entries:5</span></span> | <span data-ttu-id="a4869-1105">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-1105">value5</span></span> |

<span data-ttu-id="a4869-1106">Tyto klíče a hodnoty se načtou do ukázkové aplikace pomocí poskytovatele konfigurace paměti:</span><span class="sxs-lookup"><span data-stu-id="a4869-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="a4869-1107">Pole přeskočí hodnotu pro index &num;3.</span><span class="sxs-lookup"><span data-stu-id="a4869-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="a4869-1108">Pořadač konfigurace není schopen svázat hodnoty null nebo vytvořit položky null ve vázaných objektech, které se v okamžiku, kdy je znázorněn výsledek vazby tohoto pole k objektu, nejasní.</span><span class="sxs-lookup"><span data-stu-id="a4869-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="a4869-1109">V ukázkové aplikaci je k dispozici třída POCO pro uchovávání vázaných konfiguračních dat:</span><span class="sxs-lookup"><span data-stu-id="a4869-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="a4869-1110">Konfigurační data jsou svázána s objektem:</span><span class="sxs-lookup"><span data-stu-id="a4869-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="a4869-1111">[ConfigurationBinder.\<Get >](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntaxe, která je také možné použít, což má za následek kompaktnější kód:</span><span class="sxs-lookup"><span data-stu-id="a4869-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="a4869-1112">Vázaný objekt, instance `ArrayExample`, obdrží data pole z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="a4869-1113">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a4869-1114">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a4869-1115">0</span><span class="sxs-lookup"><span data-stu-id="a4869-1115">0</span></span>                            | <span data-ttu-id="a4869-1116">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-1116">value0</span></span>                       |
| <span data-ttu-id="a4869-1117">1</span><span class="sxs-lookup"><span data-stu-id="a4869-1117">1</span></span>                            | <span data-ttu-id="a4869-1118">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-1118">value1</span></span>                       |
| <span data-ttu-id="a4869-1119">2</span><span class="sxs-lookup"><span data-stu-id="a4869-1119">2</span></span>                            | <span data-ttu-id="a4869-1120">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-1120">value2</span></span>                       |
| <span data-ttu-id="a4869-1121">3</span><span class="sxs-lookup"><span data-stu-id="a4869-1121">3</span></span>                            | <span data-ttu-id="a4869-1122">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-1122">value4</span></span>                       |
| <span data-ttu-id="a4869-1123">4</span><span class="sxs-lookup"><span data-stu-id="a4869-1123">4</span></span>                            | <span data-ttu-id="a4869-1124">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-1124">value5</span></span>                       |

<span data-ttu-id="a4869-1125">Index &num;3 ve vázaném objektu obsahuje konfigurační data pro konfigurační klíč `array:4` a jeho hodnotu `value4`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="a4869-1126">Když jsou data konfigurace obsahující pole svázána, indexy pole v konfiguračních klíčích slouží pouze k iterování konfiguračních dat při vytváření objektu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="a4869-1127">Hodnotu null nelze uchovat v konfiguračních datech a v vázaném objektu není vytvořena položka s hodnotou null, pokud pole v konfiguračních klíčích přeskočí jeden nebo více indexů.</span><span class="sxs-lookup"><span data-stu-id="a4869-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="a4869-1128">Chybějící položka konfigurace pro index &num;3 může být zadána před vytvořením vazby na instanci `ArrayExample` pomocí jakéhokoli poskytovatele konfigurace, který vytváří správnou dvojici klíč-hodnota v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="a4869-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="a4869-1129">Pokud ukázka zahrnovala dalšího poskytovatele konfigurace JSON s chybějící dvojicí klíč-hodnota, `ArrayExample.Entries` odpovídá kompletnímu poli konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a4869-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="a4869-1130">*missing_value. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="a4869-1131">V `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="a4869-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="a4869-1132">Pár klíč-hodnota zobrazený v tabulce je načten do konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="a4869-1133">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-1133">Key</span></span>             | <span data-ttu-id="a4869-1134">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="a4869-1135">pole: položky: 3</span><span class="sxs-lookup"><span data-stu-id="a4869-1135">array:entries:3</span></span> | <span data-ttu-id="a4869-1136">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a4869-1136">value3</span></span> |

<span data-ttu-id="a4869-1137">Pokud je instance třídy `ArrayExample` vázána poté, co Poskytovatel konfigurace JSON obsahuje položku pro index &num;3, pole `ArrayExample.Entries` obsahuje hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a4869-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="a4869-1138">Index `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="a4869-1139">Hodnota `ArrayExample.Entries`</span><span class="sxs-lookup"><span data-stu-id="a4869-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="a4869-1140">0</span><span class="sxs-lookup"><span data-stu-id="a4869-1140">0</span></span>                            | <span data-ttu-id="a4869-1141">value0</span><span class="sxs-lookup"><span data-stu-id="a4869-1141">value0</span></span>                       |
| <span data-ttu-id="a4869-1142">1</span><span class="sxs-lookup"><span data-stu-id="a4869-1142">1</span></span>                            | <span data-ttu-id="a4869-1143">Hodnota1</span><span class="sxs-lookup"><span data-stu-id="a4869-1143">value1</span></span>                       |
| <span data-ttu-id="a4869-1144">2</span><span class="sxs-lookup"><span data-stu-id="a4869-1144">2</span></span>                            | <span data-ttu-id="a4869-1145">Argument</span><span class="sxs-lookup"><span data-stu-id="a4869-1145">value2</span></span>                       |
| <span data-ttu-id="a4869-1146">3</span><span class="sxs-lookup"><span data-stu-id="a4869-1146">3</span></span>                            | <span data-ttu-id="a4869-1147">hodnota3</span><span class="sxs-lookup"><span data-stu-id="a4869-1147">value3</span></span>                       |
| <span data-ttu-id="a4869-1148">4</span><span class="sxs-lookup"><span data-stu-id="a4869-1148">4</span></span>                            | <span data-ttu-id="a4869-1149">value4</span><span class="sxs-lookup"><span data-stu-id="a4869-1149">value4</span></span>                       |
| <span data-ttu-id="a4869-1150">5</span><span class="sxs-lookup"><span data-stu-id="a4869-1150">5</span></span>                            | <span data-ttu-id="a4869-1151">value5</span><span class="sxs-lookup"><span data-stu-id="a4869-1151">value5</span></span>                       |

<span data-ttu-id="a4869-1152">**Zpracování pole JSON**</span><span class="sxs-lookup"><span data-stu-id="a4869-1152">**JSON array processing**</span></span>

<span data-ttu-id="a4869-1153">Pokud soubor JSON obsahuje pole, jsou vytvořeny konfigurační klíče pro prvky pole s indexem oddílu založeným na nule.</span><span class="sxs-lookup"><span data-stu-id="a4869-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="a4869-1154">V následujícím konfiguračním souboru je `subsection` pole:</span><span class="sxs-lookup"><span data-stu-id="a4869-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="a4869-1155">Zprostředkovatel konfigurace JSON načte konfigurační data do následujících párů klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="a4869-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="a4869-1156">Klíč</span><span class="sxs-lookup"><span data-stu-id="a4869-1156">Key</span></span>                     | <span data-ttu-id="a4869-1157">Hodnota</span><span class="sxs-lookup"><span data-stu-id="a4869-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="a4869-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="a4869-1158">json_array:key</span></span>          | <span data-ttu-id="a4869-1159">Hodnotaa</span><span class="sxs-lookup"><span data-stu-id="a4869-1159">valueA</span></span> |
| <span data-ttu-id="a4869-1160">json_array: pododdíl: 0</span><span class="sxs-lookup"><span data-stu-id="a4869-1160">json_array:subsection:0</span></span> | <span data-ttu-id="a4869-1161">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a4869-1161">valueB</span></span> |
| <span data-ttu-id="a4869-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="a4869-1162">json_array:subsection:1</span></span> | <span data-ttu-id="a4869-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="a4869-1163">valueC</span></span> |
| <span data-ttu-id="a4869-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="a4869-1164">json_array:subsection:2</span></span> | <span data-ttu-id="a4869-1165">s</span><span class="sxs-lookup"><span data-stu-id="a4869-1165">valueD</span></span> |

<span data-ttu-id="a4869-1166">V ukázkové aplikaci je k dispozici následující třída POCO, která umožňuje vytvořit vazby mezi konfiguračními páry klíč-hodnota:</span><span class="sxs-lookup"><span data-stu-id="a4869-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="a4869-1167">Po vytvoření vazby obsahuje `JsonArrayExample.Key` hodnotu `valueA`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="a4869-1168">Hodnoty pododdílu jsou uloženy ve vlastnosti pole POCO `Subsection`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="a4869-1169">Index `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a4869-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="a4869-1170">Hodnota `JsonArrayExample.Subsection`</span><span class="sxs-lookup"><span data-stu-id="a4869-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="a4869-1171">0</span><span class="sxs-lookup"><span data-stu-id="a4869-1171">0</span></span>                                   | <span data-ttu-id="a4869-1172">Hodnotab</span><span class="sxs-lookup"><span data-stu-id="a4869-1172">valueB</span></span>                              |
| <span data-ttu-id="a4869-1173">1</span><span class="sxs-lookup"><span data-stu-id="a4869-1173">1</span></span>                                   | <span data-ttu-id="a4869-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="a4869-1174">valueC</span></span>                              |
| <span data-ttu-id="a4869-1175">2</span><span class="sxs-lookup"><span data-stu-id="a4869-1175">2</span></span>                                   | <span data-ttu-id="a4869-1176">s</span><span class="sxs-lookup"><span data-stu-id="a4869-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="a4869-1177">Vlastní poskytovatel konfigurace</span><span class="sxs-lookup"><span data-stu-id="a4869-1177">Custom configuration provider</span></span>

<span data-ttu-id="a4869-1178">Ukázková aplikace ukazuje, jak vytvořit základního poskytovatele konfigurace, který čte páry klíč-hodnota konfigurace z databáze pomocí [Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a4869-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="a4869-1179">Zprostředkovatel má následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a4869-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="a4869-1180">Pro demonstrační účely se používá databáze EF v paměti.</span><span class="sxs-lookup"><span data-stu-id="a4869-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="a4869-1181">Chcete-li použít databázi, která vyžaduje připojovací řetězec, implementujte sekundární `ConfigurationBuilder` pro zadání připojovacího řetězce od jiného poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a4869-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="a4869-1182">Poskytovatel čte databázovou tabulku do konfigurace při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a4869-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="a4869-1183">Zprostředkovatel nedotazuje databázi na bázi jednotlivých klíčů.</span><span class="sxs-lookup"><span data-stu-id="a4869-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="a4869-1184">Opětovné načtení není implementováno, takže aktualizace databáze po spuštění aplikace nemá žádný vliv na konfiguraci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="a4869-1185">Definujte entitu `EFConfigurationValue` pro ukládání konfiguračních hodnot do databáze.</span><span class="sxs-lookup"><span data-stu-id="a4869-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="a4869-1186">*Modely/EFConfigurationValue. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="a4869-1187">Přidejte `EFConfigurationContext` pro uložení a přístup k nakonfigurovaným hodnotám.</span><span class="sxs-lookup"><span data-stu-id="a4869-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="a4869-1188">*EFConfigurationProvider/EFConfigurationContext. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="a4869-1189">Vytvořte třídu, která implementuje <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="a4869-1190">*EFConfigurationProvider/EFConfigurationSource. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="a4869-1191">Vytvořte vlastního poskytovatele konfigurace děděním z <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="a4869-1192">Poskytovatel konfigurace inicializuje databázi, pokud je prázdná.</span><span class="sxs-lookup"><span data-stu-id="a4869-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="a4869-1193">*EFConfigurationProvider/EFConfigurationProvider. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="a4869-1194">Metoda rozšíření `AddEFConfiguration` umožňuje přidání zdroje konfigurace do `ConfigurationBuilder`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="a4869-1195">*Rozšíření/EntityFrameworkExtensions. cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="a4869-1196">Následující kód ukazuje, jak použít vlastní `EFConfigurationProvider` v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a4869-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="a4869-1197">Konfigurace přístupu během spuštění</span><span class="sxs-lookup"><span data-stu-id="a4869-1197">Access configuration during startup</span></span>

<span data-ttu-id="a4869-1198">Vložení `IConfiguration` do konstruktoru `Startup` pro přístup k hodnotám konfigurace v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a4869-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a4869-1199">Chcete-li získat přístup ke konfiguraci v `Startup.Configure`, buď je třeba vložit `IConfiguration` přímo do metody nebo použít instanci z konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a4869-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="a4869-1200">Příklad přístupu ke konfiguraci pomocí snadno ovladatelného způsobu spuštění aplikace najdete v tématu [metody spuštění aplikace: pohodlí](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="a4869-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="a4869-1201">Přístup ke konfiguraci na Razor Pages stránce nebo zobrazení MVC</span><span class="sxs-lookup"><span data-stu-id="a4869-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="a4869-1202">Chcete-li získat přístup k nastavení konfigurace na stránce Razor Pages nebo zobrazení MVC, přidejte [direktivu using](xref:mvc/views/razor#using) ([ C# odkaz: using](/dotnet/csharp/language-reference/keywords/using-directive)) pro [obor názvů Microsoft. Extensions. Configuration](xref:Microsoft.Extensions.Configuration) a vložte <xref:Microsoft.Extensions.Configuration.IConfiguration> do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a4869-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="a4869-1203">Na stránce Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="a4869-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="a4869-1204">V zobrazení MVC:</span><span class="sxs-lookup"><span data-stu-id="a4869-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="a4869-1205">Přidat konfiguraci z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="a4869-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="a4869-1206">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="a4869-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="a4869-1207">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="a4869-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a4869-1208">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a4869-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
