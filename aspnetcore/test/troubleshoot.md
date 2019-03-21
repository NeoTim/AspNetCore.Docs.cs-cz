---
title: Řešení potíží s projekty ASP.NET Core
author: Rick-Anderson
description: Pochopení a odstraňování potíží upozornění a chyby s projekty ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/26/2019
uid: test/troubleshoot
ms.openlocfilehash: 1f53375a6aa504c989920a0bff83b400b7bd83ec
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208840"
---
# <a name="troubleshoot-aspnet-core-projects"></a>Řešení potíží s projekty ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

Následující odkazy obsahují pokyny k odstraňování problémů:

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Konference Norwegian (Praha, 2018): Diagnostika problémů v aplikacích ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog o ASP.NET: Řešení potíží s ASP.NET Core problémy s výkonem](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Upozornění na .NET core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Jsou nainstalovány 32bitové a 64bitové verze sady .NET Core SDK

V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:

> 32bitová i 64bitová verze sady .NET Core SDK jsou nainstalovány. Pouze šablony z 64bitové verze nainstalované na "C:\\Program Files\\dotnet\\sdk\\" se zobrazí.

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/both32and64bit.png)

Toto upozornění se zobrazí, když (x86) 32bitové i 64bitovou (x 64) verze [.NET Core SDK](https://www.microsoft.com/net/download/all) jsou nainstalovány. Běžné důvody, které mohou být nainstalovány obě verze zahrnují:

* Původně stáhnout instalační program sady SDK .NET Core s použitím 32bitový počítač, ale potom zkopírování napříč a nainstalována na 64bitovém počítači.
* 32bitová verze .NET Core SDK byla nainstalována jiná aplikace.
* Chybná verze se stáhnul a nainstaloval.

Odinstalujte 32-bit .NET Core SDK, aby se zabránilo toto upozornění. Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**. Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK je nainstalována v několika umístěních

V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:

> .NET Core SDK je nainstalována na více místech. Pouze šablony ze sad SDK nainstalovaných v ' C:\\Program Files\\dotnet\\sdk\\"se zobrazí.

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/multiplelocations.png)

Pokud máte alespoň jedna instalace sady .NET Core SDK do adresáře mimo se zobrazí tato zpráva *C:\\Program Files\\dotnet\\sdk\\*. K tomu obvykle dochází při .NET Core SDK je nasazený na počítači místo kopírovat/vložit instalační službu MSI.

Odinstalujte všechny 32bitové .NET Core SDK a moduly runtime zabránit toto upozornění. Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**. Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.

### <a name="no-net-core-sdks-were-detected"></a>Nezjistily se žádné sady SDK .NET Core

V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:

> Nezjistily se žádné sady .NET Core SDK, ujistěte se, že jsou součástí proměnné prostředí 'PATH'.

![Snímek obrazovky dialogového okna OneASP.NET zprávou upozornění](troubleshoot/_static/NoNetCore.png)

Toto upozornění se zobrazí, když je proměnná prostředí `PATH` neodkazuje na žádné .NET Core SDK na počítači (například `C:\Program Files\dotnet\` a `C:\Program Files (x86)\dotnet\`). Chcete-li tento problém vyřešit:

* Nainstalujte nebo ověřte, že je nainstalovaná sada .NET Core SDK. Získat nejnovější verzi Instalační služby z [.NET stáhne](https://dotnet.microsoft.com/download). 
* Ověřte, že `PATH` proměnnou prostředí odkazuje na umístění, kde je nainstalován sady SDK. Instalační program obvykle nastavuje `PATH`.

## <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace je schopná reagovat na požadavky, můžete získat následující data z aplikace pomocí middlewaru:

* Požádat o &ndash; metoda schéma, hostitele, pathbase, cesta, řetězec, záhlaví dotazu
* Připojení &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientského certifikátu
* Identita &ndash; název, zobrazovaný název
* Nastavení konfigurace
* Proměnné prostředí

Umístěte následující [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) kód na začátku `Startup.Configure` metody kanál pro zpracování požadavku. Prostředí je zaškrtnuté políčko před spuštěním middleware zajistit, že je kód spuštěn pouze ve vývojovém prostředí.

Získat prostředí, použijte některou z následujících postupů:

* Vložit `IHostingEnvironment` do `Startup.Configure` metoda a kontrola prostředí s místní proměnné. Následující ukázkový kód demonstruje tento přístup.

* Přiřazení k vlastnosti v prostředí `Startup` třídy. Zkontrolovat prostředí pomocí vlastnosti (například `if (Environment.IsDevelopment())`).

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
