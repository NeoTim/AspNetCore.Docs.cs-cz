---
title: Řešení potíží s projekty ASP.NET Core
author: Rick-Anderson
description: Pochopení a odstraňování potíží upozornění a chyby s projekty ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2018
uid: test/troubleshoot
ms.openlocfilehash: 7a3361970bde2b8761c76884fc1905957d075c5c
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450772"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="c1169-103">Řešení potíží s projekty ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1169-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="c1169-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c1169-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c1169-105">Následující odkazy obsahují pokyny k odstraňování problémů:</span><span class="sxs-lookup"><span data-stu-id="c1169-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="c1169-106">Konference Norwegian (Praha, 2018): Diagnostika problémů v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c1169-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="c1169-107">Blog o ASP.NET: Řešení potíží s ASP.NET Core problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="c1169-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="c1169-108">Upozornění na .NET core SDK</span><span class="sxs-lookup"><span data-stu-id="c1169-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="c1169-109">Jsou nainstalovány 32bitové a 64bitové verze sady .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="c1169-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="c1169-110">V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="c1169-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="c1169-111">32bitová i 64bitová verze sady .NET Core SDK jsou nainstalovány.</span><span class="sxs-lookup"><span data-stu-id="c1169-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="c1169-112">Pouze šablony z 64bitové verze nainstalované na "C:\\Program Files\\dotnet\\sdk\\" se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1169-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/both32and64bit.png)

<span data-ttu-id="c1169-114">Toto upozornění se zobrazí, když (x86) 32bitové i 64bitovou (x 64) verze [.NET Core SDK](https://www.microsoft.com/net/download/all) jsou nainstalovány.</span><span class="sxs-lookup"><span data-stu-id="c1169-114">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="c1169-115">Běžné důvody, které mohou být nainstalovány obě verze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="c1169-115">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="c1169-116">Původně stáhnout instalační program sady SDK .NET Core s použitím 32bitový počítač, ale potom zkopírování napříč a nainstalována na 64bitovém počítači.</span><span class="sxs-lookup"><span data-stu-id="c1169-116">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="c1169-117">32bitová verze .NET Core SDK byla nainstalována jiná aplikace.</span><span class="sxs-lookup"><span data-stu-id="c1169-117">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="c1169-118">Chybná verze se stáhnul a nainstaloval.</span><span class="sxs-lookup"><span data-stu-id="c1169-118">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="c1169-119">Odinstalujte 32-bit .NET Core SDK, aby se zabránilo toto upozornění.</span><span class="sxs-lookup"><span data-stu-id="c1169-119">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="c1169-120">Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="c1169-120">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="c1169-121">Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="c1169-121">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="c1169-122">.NET Core SDK je nainstalována v několika umístěních</span><span class="sxs-lookup"><span data-stu-id="c1169-122">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="c1169-123">V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="c1169-123">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="c1169-124">.NET Core SDK je nainstalována na více místech.</span><span class="sxs-lookup"><span data-stu-id="c1169-124">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="c1169-125">Pouze šablony ze sad SDK nainstalovaných v ' C:\\Program Files\\dotnet\\sdk\\"se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="c1169-125">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/multiplelocations.png)

<span data-ttu-id="c1169-127">Pokud máte alespoň jedna instalace sady .NET Core SDK do adresáře mimo se zobrazí tato zpráva *C:\\Program Files\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="c1169-127">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="c1169-128">K tomu obvykle dochází při .NET Core SDK je nasazený na počítači místo kopírovat/vložit instalační službu MSI.</span><span class="sxs-lookup"><span data-stu-id="c1169-128">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="c1169-129">Odinstalujte 32-bit .NET Core SDK, aby se zabránilo toto upozornění.</span><span class="sxs-lookup"><span data-stu-id="c1169-129">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="c1169-130">Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="c1169-130">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="c1169-131">Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="c1169-131">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="c1169-132">Nezjistily se žádné sady SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="c1169-132">No .NET Core SDKs were detected</span></span>

<span data-ttu-id="c1169-133">V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="c1169-133">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="c1169-134">Nezjistily se žádné sady .NET Core SDK, ujistěte se, že jsou součástí proměnné prostředí 'PATH'.</span><span class="sxs-lookup"><span data-stu-id="c1169-134">No .NET Core SDKs were detected, ensure they are included in the environment variable 'PATH'.</span></span>

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/NoNetCore.png)

<span data-ttu-id="c1169-136">Toto upozornění se zobrazí, když je proměnná prostředí `PATH` neodkazuje na žádné .NET Core SDK na počítači.</span><span class="sxs-lookup"><span data-stu-id="c1169-136">This warning appears when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="c1169-137">Chcete-li tento problém vyřešit:</span><span class="sxs-lookup"><span data-stu-id="c1169-137">To resolve this problem:</span></span>

* <span data-ttu-id="c1169-138">Nainstalujte nebo ověřte, že je nainstalovaná sada .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="c1169-138">Install or verify the .NET Core SDK is installed.</span></span>
* <span data-ttu-id="c1169-139">Ověřte, že `PATH` proměnnou prostředí odkazuje na umístění, ve kterém je nainstalována sada SDK.</span><span class="sxs-lookup"><span data-stu-id="c1169-139">Verify that the `PATH` environment variable points to the location in which the SDK is installed.</span></span> <span data-ttu-id="c1169-140">Instalační program obvykle nastavuje `PATH`.</span><span class="sxs-lookup"><span data-stu-id="c1169-140">The installer normally sets the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="c1169-141">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="c1169-141">Obtain data from an app</span></span>

<span data-ttu-id="c1169-142">Pokud aplikace je schopná reagovat na požadavky, můžete získat následující data z aplikace pomocí middlewaru:</span><span class="sxs-lookup"><span data-stu-id="c1169-142">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="c1169-143">Požádat o &ndash; metoda schéma, hostitele, pathbase, cesta, řetězec, záhlaví dotazu</span><span class="sxs-lookup"><span data-stu-id="c1169-143">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="c1169-144">Připojení &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientského certifikátu</span><span class="sxs-lookup"><span data-stu-id="c1169-144">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="c1169-145">Identita &ndash; název, zobrazovaný název</span><span class="sxs-lookup"><span data-stu-id="c1169-145">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="c1169-146">Nastavení konfigurace</span><span class="sxs-lookup"><span data-stu-id="c1169-146">Configuration settings</span></span>
* <span data-ttu-id="c1169-147">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="c1169-147">Environment variables</span></span>

<span data-ttu-id="c1169-148">Umístěte následující [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kód na začátku `Startup.Configure` metody kanál pro zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="c1169-148">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="c1169-149">Prostředí je zaškrtnuté políčko před spuštěním middleware zajistit, že je kód spuštěn pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="c1169-149">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="c1169-150">Získat prostředí, použijte některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="c1169-150">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="c1169-151">Vložit `IHostingEnvironment` do `Startup.Configure` metoda a kontrola prostředí s místní proměnné.</span><span class="sxs-lookup"><span data-stu-id="c1169-151">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="c1169-152">Následující ukázkový kód demonstruje tento přístup.</span><span class="sxs-lookup"><span data-stu-id="c1169-152">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="c1169-153">Přiřazení k vlastnosti v prostředí `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="c1169-153">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="c1169-154">Zkontrolovat prostředí pomocí vlastnosti (například `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="c1169-154">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
