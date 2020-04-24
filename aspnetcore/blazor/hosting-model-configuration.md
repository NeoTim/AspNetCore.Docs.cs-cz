---
title: ASP.NET Core Blazor konfigurace modelu hostování
author: guardrex
description: Přečtěte Blazor si o konfiguraci modelu hostování, včetně toho, jak integrovat komponenty Razor do aplikací Razor Pages a MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: cf5776109368dc7353d7e21bcad1e947561e7eb4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111055"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="b75ea-103">Konfigurace modelu hostování ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b75ea-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="b75ea-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b75ea-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="b75ea-105">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="b75ea-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="b75ea-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="b75ea-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="b75ea-107">Prostředí</span><span class="sxs-lookup"><span data-stu-id="b75ea-107">Environment</span></span>

<span data-ttu-id="b75ea-108">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="b75ea-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="b75ea-109">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="b75ea-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="b75ea-110">Hostovaná aplikace WebAssembly Blazor vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="b75ea-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="b75ea-111">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="b75ea-111">The value of the header is the environment.</span></span> <span data-ttu-id="b75ea-112">Hostovaná aplikace Blazor a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b75ea-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="b75ea-113">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="b75ea-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b75ea-114">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, `blazor-environment` vývojový server přidá hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="b75ea-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="b75ea-115">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="b75ea-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="b75ea-116">V následujícím příkladu pro službu IIS přidejte vlastní hlavičku do publikovaného souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="b75ea-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="b75ea-117">Soubor *Web. config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="b75ea-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="b75ea-118">Chcete-li použít vlastní soubor *Web. config* pro službu IIS, který není po publikování aplikace ve složce pro *publikování* přepsán, <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>Přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="b75ea-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="b75ea-119">Získání prostředí aplikace v součásti vložením `IWebAssemblyHostEnvironment` a čtením `Environment` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b75ea-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="b75ea-120">Během spouštění `WebAssemblyHostBuilder` zpřístupňuje `IWebAssemblyHostEnvironment` `HostEnvironment` vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="b75ea-120">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="b75ea-121">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="b75ea-121">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="b75ea-122">"Prostředí" ("{název prostředí}")</span><span class="sxs-lookup"><span data-stu-id="b75ea-122">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

<span data-ttu-id="b75ea-123">`IWebAssemblyHostEnvironment.BaseAddress` Vlastnost lze použít při spuštění, když není `NavigationManager` služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="b75ea-123">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="b75ea-124">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="b75ea-124">Configuration</span></span>

<span data-ttu-id="b75ea-125">Blazor WebAssembly podporuje konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="b75ea-125">Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="b75ea-126">[Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) pro soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="b75ea-126">The [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) for app settings files by default:</span></span>
  * <span data-ttu-id="b75ea-127">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="b75ea-127">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="b75ea-128">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="b75ea-128">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="b75ea-129">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="b75ea-129">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span>

> [!WARNING]
> <span data-ttu-id="b75ea-130">Konfigurace v aplikaci WebAssembly v Blazor je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="b75ea-130">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="b75ea-131">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="b75ea-131">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="b75ea-132">Další informace o poskytovatelích konfigurace najdete v <xref:fundamentals/configuration/index>tématu.</span><span class="sxs-lookup"><span data-stu-id="b75ea-132">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="b75ea-133">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="b75ea-133">App settings configuration</span></span>

<span data-ttu-id="b75ea-134">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b75ea-134">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="b75ea-135">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b75ea-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="b75ea-136">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="b75ea-136">Provider configuration</span></span>

<span data-ttu-id="b75ea-137">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> a [zprostředkovatele konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b75ea-137">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> and the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) to supply additional configuration:</span></span>

<span data-ttu-id="b75ea-138">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b75ea-138">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

...

builder.Configuration
    .Add(memoryConfig)
    .AddJsonFile("cars.json", optional: false, reloadOnChange: true);
```

<span data-ttu-id="b75ea-139">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b75ea-139">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</p>
    <li>Brand: @Configuration["wheels:brand"]</p>
    <li>Type: @Configuration["wheels:brand:type"]</p>
    <li>Year: @Configuration["wheels:year"]</p>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

#### <a name="authentication-configuration"></a><span data-ttu-id="b75ea-140">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="b75ea-140">Authentication configuration</span></span>

<span data-ttu-id="b75ea-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b75ea-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="b75ea-142">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b75ea-142">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="b75ea-143">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="b75ea-143">Logging configuration</span></span>

<span data-ttu-id="b75ea-144">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b75ea-144">*wwwroot/appsettings.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="b75ea-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b75ea-145">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="b75ea-146">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="b75ea-146">Host builder configuration</span></span>

<span data-ttu-id="b75ea-147">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="b75ea-147">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="b75ea-148">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b75ea-148">Cached configuration</span></span>

<span data-ttu-id="b75ea-149">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="b75ea-149">Configuration files are cached for offline use.</span></span> <span data-ttu-id="b75ea-150">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="b75ea-150">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="b75ea-151">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="b75ea-151">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="b75ea-152">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="b75ea-152">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="b75ea-153">Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.</span><span class="sxs-lookup"><span data-stu-id="b75ea-153">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="b75ea-154">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, <xref:blazor/progressive-web-app#background-updates>naleznete v tématu.</span><span class="sxs-lookup"><span data-stu-id="b75ea-154">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="b75ea-155">protokolování</span><span class="sxs-lookup"><span data-stu-id="b75ea-155">Logging</span></span>

<span data-ttu-id="b75ea-156">Informace o podpoře protokolování WebAssembly v Blazor naleznete v <xref:fundamentals/logging/index#create-logs-in-blazor>tématu.</span><span class="sxs-lookup"><span data-stu-id="b75ea-156">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="b75ea-157">Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b75ea-157">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="b75ea-158">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="b75ea-158">Reflect the connection state in the UI</span></span>

<span data-ttu-id="b75ea-159">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="b75ea-159">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="b75ea-160">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="b75ea-160">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="b75ea-161">Chcete-li přizpůsobit uživatelské rozhraní, `id` definujte element `components-reconnect-modal` `<body>` na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="b75ea-161">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="b75ea-162">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="b75ea-162">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="b75ea-163">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="b75ea-163">CSS class</span></span>                       | <span data-ttu-id="b75ea-164">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="b75ea-164">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="b75ea-165">Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="b75ea-165">A lost connection.</span></span> <span data-ttu-id="b75ea-166">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="b75ea-166">The client is attempting to reconnect.</span></span> <span data-ttu-id="b75ea-167">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="b75ea-167">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="b75ea-168">K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="b75ea-168">An active connection is re-established to the server.</span></span> <span data-ttu-id="b75ea-169">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="b75ea-169">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="b75ea-170">Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="b75ea-170">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="b75ea-171">Chcete-li se pokusit `window.Blazor.reconnect()`znovu připojit, zavolejte.</span><span class="sxs-lookup"><span data-stu-id="b75ea-171">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="b75ea-172">Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="b75ea-172">Reconnection rejected.</span></span> <span data-ttu-id="b75ea-173">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="b75ea-173">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="b75ea-174">K opětovnému načtení aplikace zavolejte `location.reload()`.</span><span class="sxs-lookup"><span data-stu-id="b75ea-174">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="b75ea-175">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="b75ea-175">This connection state may result when:</span></span><ul><li><span data-ttu-id="b75ea-176">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="b75ea-176">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="b75ea-177">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="b75ea-177">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="b75ea-178">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="b75ea-178">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="b75ea-179">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="b75ea-179">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="b75ea-180">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="b75ea-180">Render mode</span></span>

<span data-ttu-id="b75ea-181">Aplikace Blazor serveru se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="b75ea-181">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="b75ea-182">To je nastaveno na stránce *_Host. cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="b75ea-182">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="b75ea-183">`RenderMode`nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="b75ea-183">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="b75ea-184">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="b75ea-184">Is prerendered into the page.</span></span>
* <span data-ttu-id="b75ea-185">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="b75ea-185">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="b75ea-186">Popis</span><span class="sxs-lookup"><span data-stu-id="b75ea-186">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="b75ea-187">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b75ea-187">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="b75ea-188">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b75ea-188">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="b75ea-189">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b75ea-189">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="b75ea-190">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="b75ea-190">Output from the component isn't included.</span></span> <span data-ttu-id="b75ea-191">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="b75ea-191">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="b75ea-192">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="b75ea-192">Renders the component into static HTML.</span></span> |

<span data-ttu-id="b75ea-193">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="b75ea-193">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="b75ea-194">Vykreslovat stavově interaktivní komponenty ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="b75ea-194">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="b75ea-195">Stavové interaktivní komponenty lze přidat na stránku nebo zobrazení Razor.</span><span class="sxs-lookup"><span data-stu-id="b75ea-195">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="b75ea-196">Při vykreslení stránky nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="b75ea-196">When the page or view renders:</span></span>

* <span data-ttu-id="b75ea-197">Komponenta je předem vykreslena se stránkou nebo zobrazením.</span><span class="sxs-lookup"><span data-stu-id="b75ea-197">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="b75ea-198">Počáteční stav součásti, který se používá pro předvykreslování, bude ztracen.</span><span class="sxs-lookup"><span data-stu-id="b75ea-198">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="b75ea-199">Po navázání SignalR připojení se vytvoří nový stav součásti.</span><span class="sxs-lookup"><span data-stu-id="b75ea-199">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="b75ea-200">Následující stránka Razor vykresluje `Counter` komponentu:</span><span class="sxs-lookup"><span data-stu-id="b75ea-200">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="b75ea-201">Vykreslování neinteraktivních komponent ze stránek a zobrazení Razor</span><span class="sxs-lookup"><span data-stu-id="b75ea-201">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="b75ea-202">Na následující stránce Razor je `Counter` komponenta staticky vykreslena s počáteční hodnotou zadanou pomocí formuláře:</span><span class="sxs-lookup"><span data-stu-id="b75ea-202">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="b75ea-203">Vzhledem `MyComponent` k tomu, že se staticky vykreslují, komponenta nemůže být interaktivní.</span><span class="sxs-lookup"><span data-stu-id="b75ea-203">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="b75ea-204">Konfigurace SignalR klienta pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="b75ea-204">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="b75ea-205">V SignalR některých případech je potřeba nakonfigurovat klienta používaného Blazor serverovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="b75ea-205">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="b75ea-206">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="b75ea-206">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="b75ea-207">Chcete-li SignalR nakonfigurovat klienta v souboru *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="b75ea-207">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="b75ea-208">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="b75ea-208">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="b75ea-209">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR tvůrce.</span><span class="sxs-lookup"><span data-stu-id="b75ea-209">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

### <a name="logging"></a><span data-ttu-id="b75ea-210">protokolování</span><span class="sxs-lookup"><span data-stu-id="b75ea-210">Logging</span></span>

<span data-ttu-id="b75ea-211">Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="b75ea-211">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
