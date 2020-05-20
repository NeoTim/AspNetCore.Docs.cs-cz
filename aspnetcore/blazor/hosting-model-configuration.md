---
<span data-ttu-id="872cc-101">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-101">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-103">'Blazor'</span></span>
- <span data-ttu-id="872cc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-104">'Identity'</span></span>
- <span data-ttu-id="872cc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-106">'Razor'</span></span>
- <span data-ttu-id="872cc-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="872cc-108">ASP.NET Core Blazor Konfigurace modelu hostování</span><span class="sxs-lookup"><span data-stu-id="872cc-108">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="872cc-109">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="872cc-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="872cc-110">Tento článek popisuje konfiguraci modelu hostování.</span><span class="sxs-lookup"><span data-stu-id="872cc-110">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="872cc-111">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="872cc-111"> WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="872cc-112">Prostředí</span><span class="sxs-lookup"><span data-stu-id="872cc-112">Environment</span></span>

<span data-ttu-id="872cc-113">Při místním spuštění aplikace je prostředí standardně vyvíjené.</span><span class="sxs-lookup"><span data-stu-id="872cc-113">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="872cc-114">Při publikování aplikace je prostředí standardně v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="872cc-114">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="872cc-115">Hostovaná Blazor aplikace WebAssembly vybírá prostředí ze serveru prostřednictvím middlewaru, který toto prostředí komunikuje do prohlížeče přidáním `blazor-environment` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="872cc-115">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="872cc-116">Hodnota hlavičky je prostředí.</span><span class="sxs-lookup"><span data-stu-id="872cc-116">The value of the header is the environment.</span></span> <span data-ttu-id="872cc-117">Hostovaná Blazor aplikace a serverová aplikace sdílejí stejné prostředí.</span><span class="sxs-lookup"><span data-stu-id="872cc-117">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="872cc-118">Další informace, včetně postupu konfigurace prostředí, najdete v tématu <xref:fundamentals/environments> .</span><span class="sxs-lookup"><span data-stu-id="872cc-118">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="872cc-119">Pro samostatnou spuštěnou aplikaci, která je spuštěna místně, vývojový server přidá `blazor-environment` hlavičku pro určení vývojového prostředí.</span><span class="sxs-lookup"><span data-stu-id="872cc-119">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="872cc-120">Chcete-li určit prostředí pro jiná hostující prostředí, přidejte `blazor-environment` hlavičku.</span><span class="sxs-lookup"><span data-stu-id="872cc-120">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="872cc-121">V následujícím příkladu pro službu IIS přidejte vlastní hlavičku do publikovaného souboru *Web. config* .</span><span class="sxs-lookup"><span data-stu-id="872cc-121">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="872cc-122">Soubor *Web. config* je umístěný ve složce *bin/Release/{Target Framework}/Publish* :</span><span class="sxs-lookup"><span data-stu-id="872cc-122">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

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
> <span data-ttu-id="872cc-123">Chcete-li použít vlastní soubor *Web. config* pro službu IIS, který není po publikování aplikace ve složce pro *publikování* přepsán, přečtěte si téma <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig> .</span><span class="sxs-lookup"><span data-stu-id="872cc-123">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="872cc-124">Získání prostředí aplikace v součásti vložením `IWebAssemblyHostEnvironment` a čtením `Environment` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="872cc-124">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

<span data-ttu-id="872cc-125">Během spouštění `WebAssemblyHostBuilder` zpřístupňuje `IWebAssemblyHostEnvironment` `HostEnvironment` vlastnost prostřednictvím vlastnosti, která vývojářům umožňuje mít v kódu logiku konkrétního prostředí:</span><span class="sxs-lookup"><span data-stu-id="872cc-125">During startup, the `WebAssemblyHostBuilder` exposes the `IWebAssemblyHostEnvironment` through the `HostEnvironment` property, which enables developers to have environment-specific logic in their code:</span></span>

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

<span data-ttu-id="872cc-126">Následující rozšiřující metody umožňují kontrolu aktuálního prostředí pro názvy pro vývoj, produkci, přípravu a vlastní prostředí:</span><span class="sxs-lookup"><span data-stu-id="872cc-126">The following convenience extension methods permit checking the current environment for Development, Production, Staging, and custom environment names:</span></span>

* `IsDevelopment()`
* `IsProduction()`
* `IsStaging()`
* <span data-ttu-id="872cc-127">"Prostředí" ("{název prostředí}")</span><span class="sxs-lookup"><span data-stu-id="872cc-127">\`IsEnvironment("{ENVIRONMENT NAME}")</span></span>

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

<span data-ttu-id="872cc-128">`IWebAssemblyHostEnvironment.BaseAddress`Vlastnost lze použít při spuštění, když `NavigationManager` není služba k dispozici.</span><span class="sxs-lookup"><span data-stu-id="872cc-128">The `IWebAssemblyHostEnvironment.BaseAddress` property can be used during startup when the `NavigationManager` service isn't available.</span></span>

### <a name="configuration"></a><span data-ttu-id="872cc-129">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="872cc-129">Configuration</span></span>

Blazor<span data-ttu-id="872cc-130">Sestavení WebAssembly načítá konfiguraci z:</span><span class="sxs-lookup"><span data-stu-id="872cc-130"> WebAssembly loads configuration from:</span></span>

* <span data-ttu-id="872cc-131">Soubory nastavení aplikace ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="872cc-131">App settings files by default:</span></span>
  * <span data-ttu-id="872cc-132">*wwwroot/appSettings. JSON*</span><span class="sxs-lookup"><span data-stu-id="872cc-132">*wwwroot/appsettings.json*</span></span>
  * <span data-ttu-id="872cc-133">*wwwroot/appSettings. {ENVIRONMENT}. JSON*</span><span class="sxs-lookup"><span data-stu-id="872cc-133">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>
* <span data-ttu-id="872cc-134">Další [poskytovatelé konfigurace](xref:fundamentals/configuration/index) zaregistrované aplikací</span><span class="sxs-lookup"><span data-stu-id="872cc-134">Other [configuration providers](xref:fundamentals/configuration/index) registered by the app.</span></span> <span data-ttu-id="872cc-135">Ne všichni poskytovatelé jsou vhodný pro Blazor aplikace pro WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="872cc-135">Not all providers are appropriate for Blazor WebAssembly apps.</span></span> <span data-ttu-id="872cc-136">Objasnění, které poskytovatelé podporují pro Blazor WebAssembly, je sledováno pomocí [vysvětlení poskytovatelé konfigurace pro Blazor WASM (dotnet/AspNetCore. Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span><span class="sxs-lookup"><span data-stu-id="872cc-136">Clarification on which providers are supported for Blazor WebAssembly is tracked by [Clarify configuration providers for Blazor WASM (dotnet/AspNetCore.Docs #18134)](https://github.com/dotnet/AspNetCore.Docs/issues/18134).</span></span>

> [!WARNING]
> <span data-ttu-id="872cc-137">Konfigurace v Blazor aplikaci WebAssembly je viditelná pro uživatele.</span><span class="sxs-lookup"><span data-stu-id="872cc-137">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="872cc-138">**Neukládejte tajné klíče aplikace ani přihlašovací údaje v konfiguraci.**</span><span class="sxs-lookup"><span data-stu-id="872cc-138">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="872cc-139">Další informace o poskytovatelích konfigurace najdete v tématu <xref:fundamentals/configuration/index> .</span><span class="sxs-lookup"><span data-stu-id="872cc-139">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

#### <a name="app-settings-configuration"></a><span data-ttu-id="872cc-140">Konfigurace nastavení aplikace</span><span class="sxs-lookup"><span data-stu-id="872cc-140">App settings configuration</span></span>

<span data-ttu-id="872cc-141">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="872cc-141">*wwwroot/appsettings.json*:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="872cc-142">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="872cc-142">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

#### <a name="provider-configuration"></a><span data-ttu-id="872cc-143">Konfigurace zprostředkovatele</span><span class="sxs-lookup"><span data-stu-id="872cc-143">Provider configuration</span></span>

<span data-ttu-id="872cc-144">Následující příklad používá <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> k poskytnutí další konfigurace:</span><span class="sxs-lookup"><span data-stu-id="872cc-144">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="872cc-145">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="872cc-145">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

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

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="872cc-146">Vložení <xref:Microsoft.Extensions.Configuration.IConfiguration> instance do komponenty pro přístup k datům konfigurace:</span><span class="sxs-lookup"><span data-stu-id="872cc-146">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    var wheelsSection = Configuration.GetSection("wheels");
    
    ...
}
```

<span data-ttu-id="872cc-147">Chcete-li číst další konfigurační soubory ze složky *wwwroot* do konfigurace, použijte `HttpClient` k získání obsahu souboru.</span><span class="sxs-lookup"><span data-stu-id="872cc-147">To read other configuration files from the *wwwroot* folder into configuration, use an `HttpClient` to obtain the file's content.</span></span> <span data-ttu-id="872cc-148">Při použití tohoto přístupu existující `HttpClient` Registrace služby může použít místního klienta vytvořeného pro čtení souboru, jak ukazuje následující příklad:</span><span class="sxs-lookup"><span data-stu-id="872cc-148">When using this approach, the existing `HttpClient` service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="872cc-149">*wwwroot/automobily. JSON*:</span><span class="sxs-lookup"><span data-stu-id="872cc-149">*wwwroot/cars.json*:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="872cc-150">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="872cc-150">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var client = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddTransient(sp => client);

using var response = await client.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

#### <a name="authentication-configuration"></a><span data-ttu-id="872cc-151">Konfigurace ověřování</span><span class="sxs-lookup"><span data-stu-id="872cc-151">Authentication configuration</span></span>

<span data-ttu-id="872cc-152">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="872cc-152">*wwwroot/appsettings.json*:</span></span>

```json
{
  "AzureAD": {
    "Authority": "https://login.microsoftonline.com/",
    "ClientId": "aeaebf0f-d416-4d92-a08f-e1d5b51fc494"
  }
}
```

<span data-ttu-id="872cc-153">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="872cc-153">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("AzureAD", options);
```

#### <a name="logging-configuration"></a><span data-ttu-id="872cc-154">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="872cc-154">Logging configuration</span></span>

<span data-ttu-id="872cc-155">*wwwroot/appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="872cc-155">*wwwroot/appsettings.json*:</span></span>

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

<span data-ttu-id="872cc-156">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="872cc-156">`Program.Main`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

#### <a name="host-builder-configuration"></a><span data-ttu-id="872cc-157">Konfigurace tvůrce hostitele</span><span class="sxs-lookup"><span data-stu-id="872cc-157">Host builder configuration</span></span>

<span data-ttu-id="872cc-158">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="872cc-158">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

#### <a name="cached-configuration"></a><span data-ttu-id="872cc-159">Konfigurace uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="872cc-159">Cached configuration</span></span>

<span data-ttu-id="872cc-160">Konfigurační soubory jsou ukládány do mezipaměti pro použití v režimu offline.</span><span class="sxs-lookup"><span data-stu-id="872cc-160">Configuration files are cached for offline use.</span></span> <span data-ttu-id="872cc-161">S [progresivními webovými aplikacemi (PWAs)](xref:blazor/progressive-web-app)můžete aktualizovat pouze konfigurační soubory při vytváření nového nasazení.</span><span class="sxs-lookup"><span data-stu-id="872cc-161">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="872cc-162">Úprava konfiguračních souborů mezi nasazeními nemá žádný vliv z těchto důvodů:</span><span class="sxs-lookup"><span data-stu-id="872cc-162">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="872cc-163">Uživatelé mají verze souborů uložených v mezipaměti, které jsou nadále používány.</span><span class="sxs-lookup"><span data-stu-id="872cc-163">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="872cc-164">Soubory *Service-Worker. js* a *Service-Worker-assets. js* aplikace PWA je nutné znovu sestavit při kompilaci, který signalizuje aplikaci na další stránce uživatele online, na kterou se aplikace znovu nasadila.</span><span class="sxs-lookup"><span data-stu-id="872cc-164">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="872cc-165">Další informace o tom, jak služba PWAs zpracovává aktualizace na pozadí, naleznete v tématu <xref:blazor/progressive-web-app#background-updates> .</span><span class="sxs-lookup"><span data-stu-id="872cc-165">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

### <a name="logging"></a><span data-ttu-id="872cc-166">Protokolování</span><span class="sxs-lookup"><span data-stu-id="872cc-166">Logging</span></span>

<span data-ttu-id="872cc-167">Informace o Blazor podpoře protokolování WebAssembly naleznete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="872cc-167">For information on Blazor WebAssembly logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="872cc-168">WebServer</span><span class="sxs-lookup"><span data-stu-id="872cc-168"> Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="872cc-169">Odrážet stav připojení v uživatelském rozhraní</span><span class="sxs-lookup"><span data-stu-id="872cc-169">Reflect the connection state in the UI</span></span>

<span data-ttu-id="872cc-170">Když klient zjistí, že došlo ke ztrátě připojení, zobrazí se uživateli výchozí uživatelské rozhraní, zatímco se klient pokusí znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="872cc-170">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="872cc-171">Pokud se opětovné připojení nepovede, uživateli se zobrazí možnost opakovat akci.</span><span class="sxs-lookup"><span data-stu-id="872cc-171">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="872cc-172">Chcete-li přizpůsobit uživatelské rozhraní, definujte element na `id` `components-reconnect-modal` `<body>` stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="872cc-172">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="872cc-173">Následující tabulka popisuje třídy CSS použité pro `components-reconnect-modal` element.</span><span class="sxs-lookup"><span data-stu-id="872cc-173">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="872cc-174">CSS – třída</span><span class="sxs-lookup"><span data-stu-id="872cc-174">CSS class</span></span>                       | <span data-ttu-id="872cc-175">Označující&hellip;</span><span class="sxs-lookup"><span data-stu-id="872cc-175">Indicates&hellip;</span></span> |
| ---
<span data-ttu-id="872cc-176">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-176">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-177">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-178">'Blazor'</span></span>
- <span data-ttu-id="872cc-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-179">'Identity'</span></span>
- <span data-ttu-id="872cc-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-181">'Razor'</span></span>
- <span data-ttu-id="872cc-182">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-183">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-183">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-184">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-185">'Blazor'</span></span>
- <span data-ttu-id="872cc-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-186">'Identity'</span></span>
- <span data-ttu-id="872cc-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-188">'Razor'</span></span>
- <span data-ttu-id="872cc-189">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-190">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-190">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-191">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-192">'Blazor'</span></span>
- <span data-ttu-id="872cc-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-193">'Identity'</span></span>
- <span data-ttu-id="872cc-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-195">'Razor'</span></span>
- <span data-ttu-id="872cc-196">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-197">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-197">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-198">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-199">'Blazor'</span></span>
- <span data-ttu-id="872cc-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-200">'Identity'</span></span>
- <span data-ttu-id="872cc-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-202">'Razor'</span></span>
- <span data-ttu-id="872cc-203">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-204">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-204">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-205">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-206">'Blazor'</span></span>
- <span data-ttu-id="872cc-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-207">'Identity'</span></span>
- <span data-ttu-id="872cc-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-209">'Razor'</span></span>
- <span data-ttu-id="872cc-210">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-211">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-211">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-212">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-213">'Blazor'</span></span>
- <span data-ttu-id="872cc-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-214">'Identity'</span></span>
- <span data-ttu-id="872cc-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-216">'Razor'</span></span>
- <span data-ttu-id="872cc-217">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-218">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-218">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-219">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-220">'Blazor'</span></span>
- <span data-ttu-id="872cc-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-221">'Identity'</span></span>
- <span data-ttu-id="872cc-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-223">'Razor'</span></span>
- <span data-ttu-id="872cc-224">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-225">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-225">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-226">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-227">'Blazor'</span></span>
- <span data-ttu-id="872cc-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-228">'Identity'</span></span>
- <span data-ttu-id="872cc-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-230">'Razor'</span></span>
- <span data-ttu-id="872cc-231">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-232">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-232">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-233">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-234">'Blazor'</span></span>
- <span data-ttu-id="872cc-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-235">'Identity'</span></span>
- <span data-ttu-id="872cc-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-237">'Razor'</span></span>
- <span data-ttu-id="872cc-238">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-239">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-239">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-240">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-241">'Blazor'</span></span>
- <span data-ttu-id="872cc-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-242">'Identity'</span></span>
- <span data-ttu-id="872cc-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-244">'Razor'</span></span>
- <span data-ttu-id="872cc-245">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-246">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-246">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-247">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-248">'Blazor'</span></span>
- <span data-ttu-id="872cc-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-249">'Identity'</span></span>
- <span data-ttu-id="872cc-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-251">'Razor'</span></span>
- <span data-ttu-id="872cc-252">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-253">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-253">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-254">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-255">'Blazor'</span></span>
- <span data-ttu-id="872cc-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-256">'Identity'</span></span>
- <span data-ttu-id="872cc-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-258">'Razor'</span></span>
- <span data-ttu-id="872cc-259">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-260">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-260">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-261">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-262">'Blazor'</span></span>
- <span data-ttu-id="872cc-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-263">'Identity'</span></span>
- <span data-ttu-id="872cc-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-265">'Razor'</span></span>
- <span data-ttu-id="872cc-266">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-266">'SignalR' uid:</span></span> 

<span data-ttu-id="872cc-267">---------------- | ---Název: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci modelu hostování, včetně postupu integrace Razor komponent do Razor stránek a aplikací MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-267">---------------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-268">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-269">'Blazor'</span></span>
- <span data-ttu-id="872cc-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-270">'Identity'</span></span>
- <span data-ttu-id="872cc-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-272">'Razor'</span></span>
- <span data-ttu-id="872cc-273">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-274">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-274">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-275">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-276">'Blazor'</span></span>
- <span data-ttu-id="872cc-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-277">'Identity'</span></span>
- <span data-ttu-id="872cc-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-279">'Razor'</span></span>
- <span data-ttu-id="872cc-280">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-281">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-281">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-282">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-283">'Blazor'</span></span>
- <span data-ttu-id="872cc-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-284">'Identity'</span></span>
- <span data-ttu-id="872cc-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-286">'Razor'</span></span>
- <span data-ttu-id="872cc-287">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-288">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-288">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-289">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-290">'Blazor'</span></span>
- <span data-ttu-id="872cc-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-291">'Identity'</span></span>
- <span data-ttu-id="872cc-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-293">'Razor'</span></span>
- <span data-ttu-id="872cc-294">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-295">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-295">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-296">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-297">'Blazor'</span></span>
- <span data-ttu-id="872cc-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-298">'Identity'</span></span>
- <span data-ttu-id="872cc-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-300">'Razor'</span></span>
- <span data-ttu-id="872cc-301">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-302">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-302">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-303">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-304">'Blazor'</span></span>
- <span data-ttu-id="872cc-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-305">'Identity'</span></span>
- <span data-ttu-id="872cc-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-307">'Razor'</span></span>
- <span data-ttu-id="872cc-308">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-308">'SignalR' uid:</span></span> 

<span data-ttu-id="872cc-309">--------- | | `components-reconnect-show`     | Ztracené připojení.</span><span class="sxs-lookup"><span data-stu-id="872cc-309">--------- | | `components-reconnect-show`     | A lost connection.</span></span> <span data-ttu-id="872cc-310">Klient se pokouší znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="872cc-310">The client is attempting to reconnect.</span></span> <span data-ttu-id="872cc-311">Zobrazit modální okno.</span><span class="sxs-lookup"><span data-stu-id="872cc-311">Show the modal.</span></span> <span data-ttu-id="872cc-312">| | `components-reconnect-hide`     | K serveru se znovu naváže aktivní připojení.</span><span class="sxs-lookup"><span data-stu-id="872cc-312">| | `components-reconnect-hide`     | An active connection is re-established to the server.</span></span> <span data-ttu-id="872cc-313">Skryje modální okno.</span><span class="sxs-lookup"><span data-stu-id="872cc-313">Hide the modal.</span></span> <span data-ttu-id="872cc-314">| | `components-reconnect-failed`   | Opětovné připojení se nezdařilo, pravděpodobně kvůli selhání sítě.</span><span class="sxs-lookup"><span data-stu-id="872cc-314">| | `components-reconnect-failed`   | Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="872cc-315">Chcete-li se pokusit znovu připojit, zavolejte `window.Blazor.reconnect()` .</span><span class="sxs-lookup"><span data-stu-id="872cc-315">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> <span data-ttu-id="872cc-316">| | `components-reconnect-rejected` | Opětovné připojení bylo zamítnuto.</span><span class="sxs-lookup"><span data-stu-id="872cc-316">| | `components-reconnect-rejected` | Reconnection rejected.</span></span> <span data-ttu-id="872cc-317">Server byl dosažen, ale odmítl připojení a stav uživatele na serveru je ztracen.</span><span class="sxs-lookup"><span data-stu-id="872cc-317">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="872cc-318">K opětovnému načtení aplikace zavolejte `location.reload()` .</span><span class="sxs-lookup"><span data-stu-id="872cc-318">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="872cc-319">Tento stav připojení může mít za následek:</span><span class="sxs-lookup"><span data-stu-id="872cc-319">This connection state may result when:</span></span><ul><li><span data-ttu-id="872cc-320">Dojde k chybě okruhu na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="872cc-320">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="872cc-321">Klient je dostatečně odpojený, aby server vynechal stav uživatele.</span><span class="sxs-lookup"><span data-stu-id="872cc-321">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="872cc-322">Instance komponent, se kterými uživatel pracuje, jsou vyřazeny.</span><span class="sxs-lookup"><span data-stu-id="872cc-322">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="872cc-323">Server se restartuje nebo se pracovní proces aplikace recykluje.</span><span class="sxs-lookup"><span data-stu-id="872cc-323">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="872cc-324">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="872cc-324">Render mode</span></span>

Blazor<span data-ttu-id="872cc-325">Serverové aplikace se ve výchozím nastavení nastavují tak, aby se před vytvořením připojení klienta k serveru předvedlo uživatelské rozhraní na serveru.</span><span class="sxs-lookup"><span data-stu-id="872cc-325"> Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="872cc-326">To je nastaveno na stránce *_Host. cshtml* Razor :</span><span class="sxs-lookup"><span data-stu-id="872cc-326">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="872cc-327">`RenderMode`nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="872cc-327">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="872cc-328">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="872cc-328">Is prerendered into the page.</span></span>
* <span data-ttu-id="872cc-329">Je vykreslen jako statický kód HTML na stránce nebo obsahuje nezbytné informace pro spuštění Blazor aplikace od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="872cc-329">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="872cc-330">Popis</span><span class="sxs-lookup"><span data-stu-id="872cc-330">Description</span></span> |
| ---
<span data-ttu-id="872cc-331">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-331">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-332">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-332">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-333">'Blazor'</span></span>
- <span data-ttu-id="872cc-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-334">'Identity'</span></span>
- <span data-ttu-id="872cc-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-336">'Razor'</span></span>
- <span data-ttu-id="872cc-337">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-338">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-338">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-339">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-339">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-340">'Blazor'</span></span>
- <span data-ttu-id="872cc-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-341">'Identity'</span></span>
- <span data-ttu-id="872cc-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-343">'Razor'</span></span>
- <span data-ttu-id="872cc-344">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-345">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-345">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-346">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-346">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-347">'Blazor'</span></span>
- <span data-ttu-id="872cc-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-348">'Identity'</span></span>
- <span data-ttu-id="872cc-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-350">'Razor'</span></span>
- <span data-ttu-id="872cc-351">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-352">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-352">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-353">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-353">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-354">'Blazor'</span></span>
- <span data-ttu-id="872cc-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-355">'Identity'</span></span>
- <span data-ttu-id="872cc-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-357">'Razor'</span></span>
- <span data-ttu-id="872cc-358">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-359">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-359">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-360">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-360">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-361">'Blazor'</span></span>
- <span data-ttu-id="872cc-362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-362">'Identity'</span></span>
- <span data-ttu-id="872cc-363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-363">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-364">'Razor'</span></span>
- <span data-ttu-id="872cc-365">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-366">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-366">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-367">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-367">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-368">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-368">'Blazor'</span></span>
- <span data-ttu-id="872cc-369">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-369">'Identity'</span></span>
- <span data-ttu-id="872cc-370">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-370">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-371">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-371">'Razor'</span></span>
- <span data-ttu-id="872cc-372">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-372">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-373">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-373">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-374">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-374">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-375">'Blazor'</span></span>
- <span data-ttu-id="872cc-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-376">'Identity'</span></span>
- <span data-ttu-id="872cc-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-378">'Razor'</span></span>
- <span data-ttu-id="872cc-379">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-379">'SignalR' uid:</span></span> 

<span data-ttu-id="872cc-380">---------- | ---Název: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci modelu hostování, včetně postupu integrace Razor komponent do Razor stránek a aplikací MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-380">---------- | --- title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-381">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-381">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-382">'Blazor'</span></span>
- <span data-ttu-id="872cc-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-383">'Identity'</span></span>
- <span data-ttu-id="872cc-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-385">'Razor'</span></span>
- <span data-ttu-id="872cc-386">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-387">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-387">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-388">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-388">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-389">'Blazor'</span></span>
- <span data-ttu-id="872cc-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-390">'Identity'</span></span>
- <span data-ttu-id="872cc-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-392">'Razor'</span></span>
- <span data-ttu-id="872cc-393">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="872cc-394">title: ' ASP.NET Core Blazor Konfigurace modelu hostování ' Autor: Popis: ' informace o Blazor konfiguraci hostujícího modelu, včetně toho, jak integrovat Razor součásti na Razor stránky a aplikace MVC. '</span><span class="sxs-lookup"><span data-stu-id="872cc-394">title: 'ASP.NET Core Blazor hosting model configuration' author: description: 'Learn about Blazor hosting model configuration, including how to integrate Razor components into Razor Pages and MVC apps.'</span></span>
<span data-ttu-id="872cc-395">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="872cc-395">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="872cc-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="872cc-396">'Blazor'</span></span>
- <span data-ttu-id="872cc-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="872cc-397">'Identity'</span></span>
- <span data-ttu-id="872cc-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="872cc-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="872cc-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="872cc-399">'Razor'</span></span>
- <span data-ttu-id="872cc-400">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="872cc-400">'SignalR' uid:</span></span> 

<span data-ttu-id="872cc-401">------ | | `ServerPrerendered` | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="872cc-401">------ | | `ServerPrerendered` | Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="872cc-402">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="872cc-402">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="872cc-403">| | `Server`            | Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="872cc-403">| | `Server`            | Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="872cc-404">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="872cc-404">Output from the component isn't included.</span></span> <span data-ttu-id="872cc-405">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="872cc-405">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="872cc-406">| | `Static`            | Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="872cc-406">| | `Static`            | Renders the component into static HTML.</span></span> |

<span data-ttu-id="872cc-407">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="872cc-407">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="872cc-408">Konfigurace SignalR klienta pro Blazor serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="872cc-408">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="872cc-409">V některých případech je potřeba nakonfigurovat SignalR klienta používaného Blazor serverovými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="872cc-409">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="872cc-410">Například můžete chtít nakonfigurovat protokolování na SignalR straně klienta, aby bylo možné diagnostikovat problém s připojením.</span><span class="sxs-lookup"><span data-stu-id="872cc-410">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="872cc-411">Chcete-li nakonfigurovat SignalR klienta v souboru *Pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="872cc-411">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="872cc-412">Přidejte `autostart="false"` atribut ke `<script>` značce pro `blazor.server.js` skript.</span><span class="sxs-lookup"><span data-stu-id="872cc-412">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="872cc-413">Zavolejte `Blazor.start` a předejte do konfiguračního objektu, který určuje SignalR Tvůrce.</span><span class="sxs-lookup"><span data-stu-id="872cc-413">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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

### <a name="logging"></a><span data-ttu-id="872cc-414">Protokolování</span><span class="sxs-lookup"><span data-stu-id="872cc-414">Logging</span></span>

<span data-ttu-id="872cc-415">Informace o Blazor podpoře protokolování serveru najdete v tématu <xref:fundamentals/logging/index#create-logs-in-blazor> .</span><span class="sxs-lookup"><span data-stu-id="872cc-415">For information on Blazor Server logging support, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>
