---
<span data-ttu-id="5b222-101">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="5b222-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="5b222-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5b222-102">'Blazor'</span></span>
- <span data-ttu-id="5b222-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5b222-103">'Identity'</span></span>
- <span data-ttu-id="5b222-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5b222-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5b222-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5b222-105">'Razor'</span></span>
- <span data-ttu-id="5b222-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="5b222-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="5b222-107">Řešení potíží a ladění ASP.NET Corech projektů</span><span class="sxs-lookup"><span data-stu-id="5b222-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="5b222-108">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5b222-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5b222-109">Následující odkazy obsahují pokyny k odstraňování potíží:</span><span class="sxs-lookup"><span data-stu-id="5b222-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="5b222-110">NORWEGIAN Developers Conference konference (Londýn, 2018): diagnostikování problémů v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b222-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="5b222-111">Blog ASP.NET: řešení potíží s výkonem ASP.NET Core problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="5b222-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="5b222-112">Upozornění .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="5b222-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="5b222-113">Nainstalují se 32 i 64 verze .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="5b222-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="5b222-114">V dialogovém okně **Nový projekt** pro ASP.NET Core se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="5b222-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="5b222-115">Jsou nainstalovány 32 a 64 bitové verze .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="5b222-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="5b222-116">Jsou zobrazeny pouze šablony z 64 verzí nainstalovaných v umístění C: \\ Program Files \\ dotnet \\ SDK \\ .</span><span class="sxs-lookup"><span data-stu-id="5b222-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="5b222-117">Toto upozornění se zobrazí, 32 Pokud jsou nainstalovány 32bitové verze [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) (x86) i 64-bit (x64).</span><span class="sxs-lookup"><span data-stu-id="5b222-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="5b222-118">Mezi běžné příčiny můžou být nainstalované obě verze:</span><span class="sxs-lookup"><span data-stu-id="5b222-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="5b222-119">Původně jste stáhli instalační službu .NET Core SDK pomocí 32 počítače, ale pak jste ji zkopírovali napříč a nainstalovali na 64 počítač.</span><span class="sxs-lookup"><span data-stu-id="5b222-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="5b222-120">32 bitová .NET Core SDK byla nainstalována jinou aplikací.</span><span class="sxs-lookup"><span data-stu-id="5b222-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="5b222-121">Byla stažena a nainstalována nesprávná verze.</span><span class="sxs-lookup"><span data-stu-id="5b222-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="5b222-122">Pokud chcete zabránit tomuto upozornění, odinstalujte 32 .NET Core SDK bitů.</span><span class="sxs-lookup"><span data-stu-id="5b222-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="5b222-123">Odinstalujte ji pomocí **ovládacího panelu**  >  **programy a funkce**  >  **Odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="5b222-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="5b222-124">Pokud rozumíte tomu, proč k upozornění dojde a jeho důsledky, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="5b222-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="5b222-125">.NET Core SDK je nainstalováno v několika umístěních.</span><span class="sxs-lookup"><span data-stu-id="5b222-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="5b222-126">V dialogovém okně **Nový projekt** pro ASP.NET Core se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="5b222-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="5b222-127">.NET Core SDK je nainstalován v několika umístěních.</span><span class="sxs-lookup"><span data-stu-id="5b222-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="5b222-128">Zobrazí se pouze šablony sady SDK nainstalované v umístění C: \\ Program Files \\ dotnet \\ SDK \\ .</span><span class="sxs-lookup"><span data-stu-id="5b222-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="5b222-129">Tato zpráva se zobrazí, pokud máte alespoň jednu instalaci .NET Core SDK v adresáři mimo jazyk \*C: \\ Program Files \\ dotnet \\ SDK \\ \*.</span><span class="sxs-lookup"><span data-stu-id="5b222-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="5b222-130">K tomu obvykle dochází, když .NET Core SDK nasazené na počítači pomocí kopírování a vkládání místo instalačního programu MSI.</span><span class="sxs-lookup"><span data-stu-id="5b222-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="5b222-131">Pokud chcete zabránit tomuto upozornění, odinstalujte všechny 32 sady .NET Core SDK a moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="5b222-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="5b222-132">Odinstalujte ji pomocí **ovládacího panelu**  >  **programy a funkce**  >  **Odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="5b222-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="5b222-133">Pokud rozumíte tomu, proč k upozornění dojde a jeho důsledky, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="5b222-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="5b222-134">Nezjistily se žádné sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="5b222-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="5b222-135">V dialogovém okně **Nový projekt** sady Visual Studio pro ASP.NET Core se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="5b222-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="5b222-136">Nezjistily se žádné sady .NET Core SDK, ujistěte se, že jsou zahrnuté v proměnné prostředí `PATH` .</span><span class="sxs-lookup"><span data-stu-id="5b222-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="5b222-137">Při provádění `dotnet` příkazu se zobrazí upozornění jako:</span><span class="sxs-lookup"><span data-stu-id="5b222-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="5b222-138">Nebylo možné najít žádné nainstalované sady dotnet SDK.</span><span class="sxs-lookup"><span data-stu-id="5b222-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="5b222-139">Tato upozornění se zobrazí, pokud proměnná prostředí `PATH` neodkazuje na žádné sady SDK .NET Core v počítači.</span><span class="sxs-lookup"><span data-stu-id="5b222-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="5b222-140">Řešení tohoto problému:</span><span class="sxs-lookup"><span data-stu-id="5b222-140">To resolve this problem:</span></span>

* <span data-ttu-id="5b222-141">Nainstalujte .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="5b222-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="5b222-142">Získejte nejnovější instalační program ze [souborů ke stažení pro rozhraní .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5b222-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="5b222-143">Ověřte, zda `PATH` Proměnná prostředí odkazuje na umístění, kde je nainstalována sada SDK ( `C:\Program Files\dotnet\` pro 64 bitů/x64 nebo `C:\Program Files (x86)\dotnet\` pro 32bitovou verzi/x86).</span><span class="sxs-lookup"><span data-stu-id="5b222-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="5b222-144">Instalační program sady SDK obvykle nastaví `PATH` .</span><span class="sxs-lookup"><span data-stu-id="5b222-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="5b222-145">Vždy nainstalujte stejné sady bitová verze SDK a moduly runtime na stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="5b222-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="5b222-146">Chybějící sada SDK po instalaci hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="5b222-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="5b222-147">Instalace [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) upravuje `PATH` při instalaci modulu runtime .NET Core, který odkazuje na 32 (x86) verzi .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="5b222-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="5b222-148">To může mít za následek chybějící sady SDK, když se použije příkaz .NET Core (x86) 32 `dotnet` ([nejsou zjištěny žádné sady .NET Core SDK](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="5b222-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="5b222-149">Chcete-li tento problém vyřešit, přejděte na `C:\Program Files\dotnet\` pozici před `C:\Program Files (x86)\dotnet\` dnem `PATH` .</span><span class="sxs-lookup"><span data-stu-id="5b222-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="5b222-150">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="5b222-150">Obtain data from an app</span></span>

<span data-ttu-id="5b222-151">Pokud aplikace dokáže reagovat na požadavky, můžete z aplikace získat následující data pomocí middlewaru:</span><span class="sxs-lookup"><span data-stu-id="5b222-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="5b222-152">Request: metoda, schéma, hostitel, pathbase, cesta, řetězec dotazu, záhlaví</span><span class="sxs-lookup"><span data-stu-id="5b222-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="5b222-153">Připojení: Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát</span><span class="sxs-lookup"><span data-stu-id="5b222-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="5b222-154">Nastavení konfigurace</span><span class="sxs-lookup"><span data-stu-id="5b222-154">Configuration settings</span></span>
* <span data-ttu-id="5b222-155">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="5b222-155">Environment variables</span></span>

<span data-ttu-id="5b222-156">Vložte následující kód [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na začátek `Startup.Configure` kanálu zpracování požadavků metody.</span><span class="sxs-lookup"><span data-stu-id="5b222-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="5b222-157">Prostředí je zkontrolováno před spuštěním middlewaru, aby bylo zajištěno, že kód je spuštěn pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="5b222-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="5b222-158">K získání prostředí použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="5b222-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="5b222-159">Vloží `IHostingEnvironment` do `Startup.Configure` metody a zkontroluje prostředí pomocí místní proměnné.</span><span class="sxs-lookup"><span data-stu-id="5b222-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="5b222-160">Následující vzorový kód demonstruje tento přístup.</span><span class="sxs-lookup"><span data-stu-id="5b222-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="5b222-161">Přiřaďte prostředí k vlastnosti ve `Startup` třídě.</span><span class="sxs-lookup"><span data-stu-id="5b222-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="5b222-162">Ověřte prostředí pomocí vlastnosti (například `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="5b222-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="5b222-163">Ladění aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5b222-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="5b222-164">Následující odkazy obsahují informace o ladění aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5b222-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="5b222-165">Ladění ASP Core v systému Linux</span><span class="sxs-lookup"><span data-stu-id="5b222-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="5b222-166">Ladění .NET Core v systému UNIX přes SSH</span><span class="sxs-lookup"><span data-stu-id="5b222-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="5b222-167">Rychlý Start: ladění ASP.NET pomocí ladicího programu sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b222-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="5b222-168">Další informace o ladění najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .</span><span class="sxs-lookup"><span data-stu-id="5b222-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
