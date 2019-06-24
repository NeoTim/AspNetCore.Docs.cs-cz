---
title: Řešení potíží s projekty ASP.NET Core
author: Rick-Anderson
description: Pochopení a odstraňování potíží upozornění a chyby s projekty ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: test/troubleshoot
ms.openlocfilehash: bcec8a55a5111e1f3acf53ae2f57b45e6e609d25
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313677"
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

> 32bitové a 64bitové verze rozhraní .NET Core SDK jsou nainstalovány. Pouze šablony z 64bitové verze nainstalované na "C:\\Program Files\\dotnet\\sdk\\" se zobrazí.

Toto upozornění se zobrazí, když (x86) 32bitové i 64bitovou (x 64) verze [.NET Core SDK](https://www.microsoft.com/net/download/all) jsou nainstalovány. Běžné důvody, které mohou být nainstalovány obě verze zahrnují:

* Původně stáhnout instalační program sady SDK .NET Core s použitím 32bitový počítač, ale potom zkopírování napříč a nainstalována na 64bitovém počítači.
* 32bitová verze .NET Core SDK byla nainstalována jiná aplikace.
* Chybná verze se stáhnul a nainstaloval.

Odinstalujte 32-bit .NET Core SDK, aby se zabránilo toto upozornění. Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**. Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK je nainstalována v několika umístěních

V **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:

> .NET Core SDK je nainstalována na více místech. Pouze šablony ze sad SDK nainstalovaných v ' C:\\Program Files\\dotnet\\sdk\\"se zobrazí.

Pokud máte alespoň jedna instalace sady .NET Core SDK do adresáře mimo se zobrazí tato zpráva *C:\\Program Files\\dotnet\\sdk\\* . K tomu obvykle dochází při .NET Core SDK je nasazený na počítači místo kopírovat/vložit instalační službu MSI.

Odinstalujte všechny 32bitové .NET Core SDK a moduly runtime zabránit toto upozornění. Odinstalovat z **ovládací panely** > **programy a funkce** > **odinstalovat nebo změnit program**. Pokud budete rozumět tomu, proč dojde k upozornění a jeho dopady, můžete upozornění ignorovat.

### <a name="no-net-core-sdks-were-detected"></a>Nezjistily se žádné sady SDK .NET Core

* V sadě Visual Studio **nový projekt** dialogové okno pro ASP.NET Core, může zobrazit následující upozornění:

  > Nezjistily se žádné sady .NET Core SDK, ujistěte se, jsou zahrnuté v proměnné prostředí `PATH`.

* Při provádění `dotnet` příkazu, ale upozornění se zobrazí jako:

  > Nebylo možné najít žádné nainstalované dotnet sady SDK.

Tato upozornění se zobrazí, když je proměnná prostředí `PATH` neodkazuje na žádné .NET Core SDK na počítači. Chcete-li tento problém vyřešit:

* Nainstalujte sadu .NET Core SDK. Získat nejnovější verzi Instalační služby z [.NET stáhne](https://dotnet.microsoft.com/download).
* Ověřte, že `PATH` proměnnou prostředí odkazuje na umístění, ve kterém je nainstalována sada SDK (`C:\Program Files\dotnet\` pro 64-bit/x64 nebo `C:\Program Files (x86)\dotnet\` pro 32-bit/x86). Instalační program sady SDK se obvykle nastavuje `PATH`. Vždy instalovat stejné bitové verze sady SDK a moduly runtime ve stejném počítači.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Chybí SDK po instalaci sady hostování rozhraní .NET Core

Instalace [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) změní `PATH` po instalaci modulu runtime .NET Core tak, aby odkazoval na 32bitový (x 86) verzi .NET Core (`C:\Program Files (x86)\dotnet\`). To může způsobit chybějící sady SDK při .NET Core 32bitový (x 86) `dotnet` pomocí příkazu ([nebyly zjištěny žádné .NET Core SDK](#no-net-core-sdks-were-detected)). Chcete-li vyřešit tento problém, přesuňte `C:\Program Files\dotnet\` do polohy před `C:\Program Files (x86)\dotnet\` na `PATH`.

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
