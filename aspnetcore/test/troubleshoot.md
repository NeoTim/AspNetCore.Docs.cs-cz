---
title: Poradce při potížích a ladění ASP.NET základní projekty
author: Rick-Anderson
description: Seznamte se s ASP.NET základními projekty a vyřešujte je.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511506"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="65b3f-103">Poradce při potížích a ladění ASP.NET základní projekty</span><span class="sxs-lookup"><span data-stu-id="65b3f-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="65b3f-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65b3f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="65b3f-105">Následující odkazy poskytují pokyny pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="65b3f-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="65b3f-106">Konference NDC (Londýn, 2018): Diagnostika problémů v ASP.NET základních aplikacích</span><span class="sxs-lookup"><span data-stu-id="65b3f-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="65b3f-107">ASP.NET Blog: Řešení problémů s výkonem ASP.NET jádra</span><span class="sxs-lookup"><span data-stu-id="65b3f-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="65b3f-108">Upozornění sady .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="65b3f-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="65b3f-109">Jsou nainstalovány 32bitové i 64bitové verze sady .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="65b3f-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="65b3f-110">V dialogovém okně **Nový projekt** pro ASP.NET jádra se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="65b3f-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="65b3f-111">Jsou nainstalovány 32bitové i 64bitové verze sady .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="65b3f-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="65b3f-112">Zobrazí se pouze šablony z 64bitových verzí\\nainstalovaných na\\adrese\\C: Program Files\\dotnet sdk .</span><span class="sxs-lookup"><span data-stu-id="65b3f-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="65b3f-113">Toto upozornění se zobrazí, pokud [jsou](https://dotnet.microsoft.com/download/dotnet-core) nainstalovány verze 32bitové (x86) a 64bitové (x64).</span><span class="sxs-lookup"><span data-stu-id="65b3f-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="65b3f-114">Mezi běžné důvody, proč mohou být nainstalovány obě verze, patří:</span><span class="sxs-lookup"><span data-stu-id="65b3f-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="65b3f-115">Instalační program sady .NET Core SDK jste původně stáhli pomocí 32bitového počítače, ale pak jste jej zkopírovali a nainstalovali do 64bitového počítače.</span><span class="sxs-lookup"><span data-stu-id="65b3f-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="65b3f-116">32bitová sada .NET Core SDK byla nainstalována jinou aplikací.</span><span class="sxs-lookup"><span data-stu-id="65b3f-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="65b3f-117">Byla stažena a nainstalována nesprávná verze.</span><span class="sxs-lookup"><span data-stu-id="65b3f-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="65b3f-118">Chcete-li tomuto upozornění zabránit, odinstalujte 32bitovou sadu SDK jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="65b3f-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="65b3f-119">Odinstalujte z **Ovládacích panelů** > **Programy a funkce** > **Odinstalujte nebo změňte program**.</span><span class="sxs-lookup"><span data-stu-id="65b3f-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="65b3f-120">Pokud pochopíte, proč dojde k upozornění a jeho důsledky, můžete ignorovat upozornění.</span><span class="sxs-lookup"><span data-stu-id="65b3f-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="65b3f-121">Sada .NET Core SDK je nainstalována na více místech</span><span class="sxs-lookup"><span data-stu-id="65b3f-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="65b3f-122">V dialogovém okně **Nový projekt** pro ASP.NET jádra se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="65b3f-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="65b3f-123">Sada .NET Core SDK je nainstalována ve více umístěních.</span><span class="sxs-lookup"><span data-stu-id="65b3f-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="65b3f-124">Zobrazí se pouze šablony z sad SDK\\nainstalovaných\\na adrese\\C:\\Program Files dotnet sdk .</span><span class="sxs-lookup"><span data-stu-id="65b3f-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="65b3f-125">Tato zpráva se zobrazí, pokud máte alespoň jednu instalaci sady .NET Core SDK v adresáři mimo *C:\\Program Files\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="65b3f-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="65b3f-126">Obvykle k tomu dochází, když byla sada .NET Core SDK nasazena v počítači pomocí copy/paste namísto instalačního programu MSI.</span><span class="sxs-lookup"><span data-stu-id="65b3f-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="65b3f-127">Chcete-li tomuto upozornění zabránit, odinstalujte všechny 32bitové sady SDK jádra .NET a dobu běhu.</span><span class="sxs-lookup"><span data-stu-id="65b3f-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="65b3f-128">Odinstalujte z **Ovládacích panelů** > **Programy a funkce** > **Odinstalujte nebo změňte program**.</span><span class="sxs-lookup"><span data-stu-id="65b3f-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="65b3f-129">Pokud pochopíte, proč dojde k upozornění a jeho důsledky, můžete ignorovat upozornění.</span><span class="sxs-lookup"><span data-stu-id="65b3f-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="65b3f-130">Nebyly zjištěny žádné sady SDK jádra .NET.</span><span class="sxs-lookup"><span data-stu-id="65b3f-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="65b3f-131">V dialogovém okně **Nový projekt** sady Visual Studio pro ASP.NET jádra se může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="65b3f-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="65b3f-132">Nebyly zjištěny žádné sady SDK jádra .NET, `PATH`ujistěte se, že jsou zahrnuty v proměnné prostředí .</span><span class="sxs-lookup"><span data-stu-id="65b3f-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="65b3f-133">Při provádění `dotnet` příkazu se upozornění zobrazí jako:</span><span class="sxs-lookup"><span data-stu-id="65b3f-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="65b3f-134">Nebylo možné najít žádné nainstalované sady SDK dotnet.</span><span class="sxs-lookup"><span data-stu-id="65b3f-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="65b3f-135">Tato upozornění se zobrazí, když proměnná `PATH` prostředí neukazuje na žádné sady SDK jádra .NET v počítači.</span><span class="sxs-lookup"><span data-stu-id="65b3f-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="65b3f-136">Chcete-li tento problém vyřešit:</span><span class="sxs-lookup"><span data-stu-id="65b3f-136">To resolve this problem:</span></span>

* <span data-ttu-id="65b3f-137">Nainstalujte sadu .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="65b3f-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="65b3f-138">Získejte nejnovější instalační program ze [služby Downloads .NET](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="65b3f-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="65b3f-139">Ověřte, zda proměnná `PATH` prostředí odkazuje na umístění, kde je nainstalována sada SDK (`C:\Program Files\dotnet\` pro 64bitovou nebo x64 nebo `C:\Program Files (x86)\dotnet\` 32bitovou/x86).</span><span class="sxs-lookup"><span data-stu-id="65b3f-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="65b3f-140">Instalační program sady SDK `PATH`obvykle nastavuje .</span><span class="sxs-lookup"><span data-stu-id="65b3f-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="65b3f-141">Vždy nainstalujte stejné bitové sady SDK a runtimes do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="65b3f-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="65b3f-142">Chybějící sada SDK po instalaci sady .NET Core Hosting Bundle</span><span class="sxs-lookup"><span data-stu-id="65b3f-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="65b3f-143">Instalace [sady .NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) Bundle `PATH` upravuje při instalaci runtime .NET Core tak, aby přecózoval na 32bitovou (x86) verzi .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="65b3f-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="65b3f-144">To může mít za následek chybějící sady SDK při použití 32bitového (x86) příkazu .NET Core `dotnet` ( byly[zjištěny sady SDK .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="65b3f-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="65b3f-145">Chcete-li tento `C:\Program Files\dotnet\` problém vyřešit, přesuňte se na pozici před `C:\Program Files (x86)\dotnet\` na `PATH`.</span><span class="sxs-lookup"><span data-stu-id="65b3f-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="65b3f-146">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="65b3f-146">Obtain data from an app</span></span>

<span data-ttu-id="65b3f-147">Pokud je aplikace schopná reagovat na požadavky, můžete z aplikace získat následující data pomocí middlewaru:</span><span class="sxs-lookup"><span data-stu-id="65b3f-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="65b3f-148">Metoda &ndash; požadavku, schéma, hostitel, cesta, cesta, řetězec dotazu, záhlaví</span><span class="sxs-lookup"><span data-stu-id="65b3f-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="65b3f-149">Připojení &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát</span><span class="sxs-lookup"><span data-stu-id="65b3f-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="65b3f-150">Název &ndash; identity, zobrazovaný název</span><span class="sxs-lookup"><span data-stu-id="65b3f-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="65b3f-151">Nastavení konfigurace</span><span class="sxs-lookup"><span data-stu-id="65b3f-151">Configuration settings</span></span>
* <span data-ttu-id="65b3f-152">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="65b3f-152">Environment variables</span></span>

<span data-ttu-id="65b3f-153">Umístěte následující [kód middlewaru](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na `Startup.Configure` začátek kanálu zpracování požadavků metody.</span><span class="sxs-lookup"><span data-stu-id="65b3f-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="65b3f-154">Prostředí je kontrolována před middleware je spuštěn a ujistěte se, že kód je spuštěn pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="65b3f-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="65b3f-155">Chcete-li získat prostředí, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="65b3f-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="65b3f-156">Vstříkněte `IHostingEnvironment` do `Startup.Configure` metody a zkontrolujte prostředí s místní proměnnou.</span><span class="sxs-lookup"><span data-stu-id="65b3f-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="65b3f-157">Následující ukázkový kód ukazuje tento přístup.</span><span class="sxs-lookup"><span data-stu-id="65b3f-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="65b3f-158">Přiřaďte prostředí k `Startup` vlastnosti ve třídě.</span><span class="sxs-lookup"><span data-stu-id="65b3f-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="65b3f-159">Zkontrolujte prostředí pomocí vlastnosti `if (Environment.IsDevelopment())`(například).</span><span class="sxs-lookup"><span data-stu-id="65b3f-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="65b3f-160">Ladění aplikací ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="65b3f-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="65b3f-161">Následující odkazy poskytují informace o ladění aplikací ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="65b3f-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="65b3f-162">Ladění jádra ASP na Linuxu</span><span class="sxs-lookup"><span data-stu-id="65b3f-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="65b3f-163">Ladění .NET Core na Unixu přes SSH</span><span class="sxs-lookup"><span data-stu-id="65b3f-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="65b3f-164">Úvodní příručka: Ladění ASP.NET ladicím programem sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65b3f-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="65b3f-165">Další informace o ladění najdete v [tomto problému s GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/2960)</span><span class="sxs-lookup"><span data-stu-id="65b3f-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
