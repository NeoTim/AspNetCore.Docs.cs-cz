---
title: Řešení potíží s projekty ASP.NET Core
author: Rick-Anderson
description: Pochopení a odstraňování potíží upozornění a chyby s projekty ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 03/13/2019
uid: test/troubleshoot
ms.openlocfilehash: 3d755b2f0c509d65dea86bbe719e42935d87d546
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901947"
---
# <a name="troubleshoot-aspnet-core-projects"></a><span data-ttu-id="71a17-103">Řešení potíží s projekty ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71a17-103">Troubleshoot ASP.NET Core projects</span></span>

<span data-ttu-id="71a17-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71a17-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71a17-105">Následující odkazy obsahují pokyny k odstraňování problémů:</span><span class="sxs-lookup"><span data-stu-id="71a17-105">The following links provide troubleshooting guidance:</span></span>

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="71a17-106">Konference Norwegian (Praha, 2018): Diagnostika problémů v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71a17-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="71a17-107">Blog o ASP.NET: Řešení potíží s ASP.NET Core problémy s výkonem</span><span class="sxs-lookup"><span data-stu-id="71a17-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="71a17-108">Upozornění na .NET core SDK</span><span class="sxs-lookup"><span data-stu-id="71a17-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="71a17-109">Jsou nainstalovány 32bitové a 64bitové verze sady .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="71a17-109">Both the 32 bit and 64 bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="71a17-110">V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="71a17-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="71a17-111">32bitová i 64bitová verze sady .NET Core SDK jsou nainstalovány.</span><span class="sxs-lookup"><span data-stu-id="71a17-111">Both 32 and 64 bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="71a17-112">Pouze šablony z 64bitové verze nainstalované na "C:\\Program Files\\dotnet\\sdk\\" se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="71a17-112">Only templates from the 64 bit version(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

<span data-ttu-id="71a17-113">Toto upozornění se zobrazí, když (x86) 32bitové i 64bitovou (x 64) verze [.NET Core SDK](https://www.microsoft.com/net/download/all) jsou nainstalovány.</span><span class="sxs-lookup"><span data-stu-id="71a17-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://www.microsoft.com/net/download/all) are installed.</span></span> <span data-ttu-id="71a17-114">Běžné důvody, které mohou být nainstalovány obě verze zahrnují:</span><span class="sxs-lookup"><span data-stu-id="71a17-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="71a17-115">Původně stáhnout instalační program sady SDK .NET Core s použitím 32bitový počítač, ale potom zkopírování napříč a nainstalována na 64bitovém počítači.</span><span class="sxs-lookup"><span data-stu-id="71a17-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="71a17-116">32bitová verze .NET Core SDK byla nainstalována jiná aplikace.</span><span class="sxs-lookup"><span data-stu-id="71a17-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="71a17-117">Chybná verze se stáhnul a nainstaloval.</span><span class="sxs-lookup"><span data-stu-id="71a17-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="71a17-118">Odinstalujte 32-bit .NET Core SDK, aby se zabránilo toto upozornění.</span><span class="sxs-lookup"><span data-stu-id="71a17-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="71a17-119">Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="71a17-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="71a17-120">Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="71a17-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="71a17-121">.NET Core SDK je nainstalována v několika umístěních</span><span class="sxs-lookup"><span data-stu-id="71a17-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="71a17-122">V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="71a17-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="71a17-123">.NET Core SDK je nainstalována na více místech.</span><span class="sxs-lookup"><span data-stu-id="71a17-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="71a17-124">Pouze šablony ze sad SDK nainstalovaných v ' C:\\Program Files\\dotnet\\sdk\\"se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="71a17-124">Only templates from the SDK(s) installed at 'C:\\Program Files\\dotnet\\sdk\\' will be displayed.</span></span>

<span data-ttu-id="71a17-125">Pokud máte alespoň jedna instalace sady .NET Core SDK do adresáře mimo se zobrazí tato zpráva *C:\\Program Files\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="71a17-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="71a17-126">K tomu obvykle dochází při .NET Core SDK je nasazený na počítači místo kopírovat/vložit instalační službu MSI.</span><span class="sxs-lookup"><span data-stu-id="71a17-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="71a17-127">Odinstalujte všechny 32bitové .NET Core SDK a moduly runtime zabránit toto upozornění.</span><span class="sxs-lookup"><span data-stu-id="71a17-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="71a17-128">Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**.</span><span class="sxs-lookup"><span data-stu-id="71a17-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="71a17-129">Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.</span><span class="sxs-lookup"><span data-stu-id="71a17-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="71a17-130">Nezjistily se žádné sady SDK .NET Core</span><span class="sxs-lookup"><span data-stu-id="71a17-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="71a17-131">V sadě Visual Studio **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:</span><span class="sxs-lookup"><span data-stu-id="71a17-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="71a17-132">Nezjistily se žádné sady .NET Core SDK, ujistěte se, jsou zahrnuté v proměnné prostředí `PATH`.</span><span class="sxs-lookup"><span data-stu-id="71a17-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="71a17-133">Při provádění `dotnet` příkazu, ale upozornění se zobrazí jako:</span><span class="sxs-lookup"><span data-stu-id="71a17-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="71a17-134">Nebylo možné najít žádné nainstalované dotnet sady SDK.</span><span class="sxs-lookup"><span data-stu-id="71a17-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="71a17-135">Tato upozornění se zobrazí, když je proměnná prostředí `PATH` neodkazuje na žádné .NET Core SDK na počítači.</span><span class="sxs-lookup"><span data-stu-id="71a17-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="71a17-136">Chcete-li tento problém vyřešit:</span><span class="sxs-lookup"><span data-stu-id="71a17-136">To resolve this problem:</span></span>

* <span data-ttu-id="71a17-137">Nainstalujte sadu .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="71a17-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="71a17-138">Získat nejnovější verzi Instalační služby z [.NET stáhne](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="71a17-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="71a17-139">Ověřte, že `PATH` proměnnou prostředí odkazuje na umístění, ve kterém je nainstalována sada SDK (`C:\Program Files\dotnet\` pro 64-bit/x64 nebo `C:\Program Files (x86)\dotnet\` pro 32-bit/x86).</span><span class="sxs-lookup"><span data-stu-id="71a17-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="71a17-140">Instalační program sady SDK se obvykle nastavuje `PATH`.</span><span class="sxs-lookup"><span data-stu-id="71a17-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="71a17-141">Vždy instalovat stejné bitové verze sady SDK a moduly runtime ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="71a17-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="71a17-142">Chybí SDK po instalaci sady hostování rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="71a17-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="71a17-143">Instalace [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) změní `PATH` po instalaci modulu runtime .NET Core tak, aby odkazoval na 32bitový (x 86) verzi .NET Core (`C:\Program Files (x86)\dotnet\`).</span><span class="sxs-lookup"><span data-stu-id="71a17-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="71a17-144">To může způsobit chybějící sady SDK při .NET Core 32bitový (x 86) `dotnet` pomocí příkazu ([nebyly zjištěny žádné .NET Core SDK](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="71a17-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="71a17-145">Chcete-li vyřešit tento problém, přesuňte `C:\Program Files\dotnet\` do polohy před `C:\Program Files (x86)\dotnet\` na `PATH`.</span><span class="sxs-lookup"><span data-stu-id="71a17-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="71a17-146">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="71a17-146">Obtain data from an app</span></span>

<span data-ttu-id="71a17-147">Pokud aplikace je schopná reagovat na požadavky, můžete získat následující data z aplikace pomocí middlewaru:</span><span class="sxs-lookup"><span data-stu-id="71a17-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="71a17-148">Požádat o &ndash; metoda schéma, hostitele, pathbase, cesta, řetězec, záhlaví dotazu</span><span class="sxs-lookup"><span data-stu-id="71a17-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="71a17-149">Připojení &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientského certifikátu</span><span class="sxs-lookup"><span data-stu-id="71a17-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="71a17-150">Identita &ndash; název, zobrazovaný název</span><span class="sxs-lookup"><span data-stu-id="71a17-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="71a17-151">Nastavení konfigurace</span><span class="sxs-lookup"><span data-stu-id="71a17-151">Configuration settings</span></span>
* <span data-ttu-id="71a17-152">Proměnné prostředí</span><span class="sxs-lookup"><span data-stu-id="71a17-152">Environment variables</span></span>

<span data-ttu-id="71a17-153">Umístěte následující [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kód na začátku `Startup.Configure` metody kanál pro zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="71a17-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="71a17-154">Prostředí je zaškrtnuté políčko před spuštěním middleware zajistit, že je kód spuštěn pouze ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="71a17-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="71a17-155">Získat prostředí, použijte některou z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="71a17-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="71a17-156">Vložit `IHostingEnvironment` do `Startup.Configure` metoda a kontrola prostředí s místní proměnné.</span><span class="sxs-lookup"><span data-stu-id="71a17-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="71a17-157">Následující ukázkový kód demonstruje tento přístup.</span><span class="sxs-lookup"><span data-stu-id="71a17-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="71a17-158">Přiřazení k vlastnosti v prostředí `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="71a17-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="71a17-159">Zkontrolovat prostředí pomocí vlastnosti (například `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="71a17-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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
