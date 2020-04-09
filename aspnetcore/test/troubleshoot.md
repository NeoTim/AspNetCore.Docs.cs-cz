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
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Poradce při potížích a ladění ASP.NET základní projekty

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Následující odkazy poskytují pokyny pro řešení potíží:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Konference NDC (Londýn, 2018): Diagnostika problémů v ASP.NET základních aplikacích](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [ASP.NET Blog: Řešení problémů s výkonem ASP.NET jádra](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Upozornění sady .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Jsou nainstalovány 32bitové i 64bitové verze sady .NET Core SDK

V dialogovém okně **Nový projekt** pro ASP.NET jádra se může zobrazit následující upozornění:

> Jsou nainstalovány 32bitové i 64bitové verze sady .NET Core SDK. Zobrazí se pouze šablony z 64bitových verzí\\nainstalovaných na\\adrese\\C: Program Files\\dotnet sdk .

Toto upozornění se zobrazí, pokud [jsou](https://dotnet.microsoft.com/download/dotnet-core) nainstalovány verze 32bitové (x86) a 64bitové (x64). Mezi běžné důvody, proč mohou být nainstalovány obě verze, patří:

* Instalační program sady .NET Core SDK jste původně stáhli pomocí 32bitového počítače, ale pak jste jej zkopírovali a nainstalovali do 64bitového počítače.
* 32bitová sada .NET Core SDK byla nainstalována jinou aplikací.
* Byla stažena a nainstalována nesprávná verze.

Chcete-li tomuto upozornění zabránit, odinstalujte 32bitovou sadu SDK jádra .NET. Odinstalujte z **Ovládacích panelů** > **Programy a funkce** > **Odinstalujte nebo změňte program**. Pokud pochopíte, proč dojde k upozornění a jeho důsledky, můžete ignorovat upozornění.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>Sada .NET Core SDK je nainstalována na více místech

V dialogovém okně **Nový projekt** pro ASP.NET jádra se může zobrazit následující upozornění:

> Sada .NET Core SDK je nainstalována ve více umístěních. Zobrazí se pouze šablony z sad SDK\\nainstalovaných\\na adrese\\C:\\Program Files dotnet sdk .

Tato zpráva se zobrazí, pokud máte alespoň jednu instalaci sady .NET Core SDK v adresáři mimo *C:\\Program Files\\dotnet\\sdk\\*. Obvykle k tomu dochází, když byla sada .NET Core SDK nasazena v počítači pomocí copy/paste namísto instalačního programu MSI.

Chcete-li tomuto upozornění zabránit, odinstalujte všechny 32bitové sady SDK jádra .NET a dobu běhu. Odinstalujte z **Ovládacích panelů** > **Programy a funkce** > **Odinstalujte nebo změňte program**. Pokud pochopíte, proč dojde k upozornění a jeho důsledky, můžete ignorovat upozornění.

### <a name="no-net-core-sdks-were-detected"></a>Nebyly zjištěny žádné sady SDK jádra .NET.

* V dialogovém okně **Nový projekt** sady Visual Studio pro ASP.NET jádra se může zobrazit následující upozornění:

  > Nebyly zjištěny žádné sady SDK jádra .NET, `PATH`ujistěte se, že jsou zahrnuty v proměnné prostředí .

* Při provádění `dotnet` příkazu se upozornění zobrazí jako:

  > Nebylo možné najít žádné nainstalované sady SDK dotnet.

Tato upozornění se zobrazí, když proměnná `PATH` prostředí neukazuje na žádné sady SDK jádra .NET v počítači. Chcete-li tento problém vyřešit:

* Nainstalujte sadu .NET Core SDK. Získejte nejnovější instalační program ze [služby Downloads .NET](https://dotnet.microsoft.com/download).
* Ověřte, zda proměnná `PATH` prostředí odkazuje na umístění, kde je nainstalována sada SDK (`C:\Program Files\dotnet\` pro 64bitovou nebo x64 nebo `C:\Program Files (x86)\dotnet\` 32bitovou/x86). Instalační program sady SDK `PATH`obvykle nastavuje . Vždy nainstalujte stejné bitové sady SDK a runtimes do stejného počítače.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Chybějící sada SDK po instalaci sady .NET Core Hosting Bundle

Instalace [sady .NET Core Hosting](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) Bundle `PATH` upravuje při instalaci runtime .NET Core tak, aby přecózoval na 32bitovou (x86) verzi .NET Core (`C:\Program Files (x86)\dotnet\`). To může mít za následek chybějící sady SDK při použití 32bitového (x86) příkazu .NET Core `dotnet` ( byly[zjištěny sady SDK .NET Core](#no-net-core-sdks-were-detected)). Chcete-li tento `C:\Program Files\dotnet\` problém vyřešit, přesuňte se na pozici před `C:\Program Files (x86)\dotnet\` na `PATH`.

## <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud je aplikace schopná reagovat na požadavky, můžete z aplikace získat následující data pomocí middlewaru:

* Metoda &ndash; požadavku, schéma, hostitel, cesta, cesta, řetězec dotazu, záhlaví
* Připojení &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát
* Název &ndash; identity, zobrazovaný název
* Nastavení konfigurace
* Proměnné prostředí

Umístěte následující [kód middlewaru](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na `Startup.Configure` začátek kanálu zpracování požadavků metody. Prostředí je kontrolována před middleware je spuštěn a ujistěte se, že kód je spuštěn pouze ve vývojovém prostředí.

Chcete-li získat prostředí, použijte některý z následujících přístupů:

* Vstříkněte `IHostingEnvironment` do `Startup.Configure` metody a zkontrolujte prostředí s místní proměnnou. Následující ukázkový kód ukazuje tento přístup.

* Přiřaďte prostředí k `Startup` vlastnosti ve třídě. Zkontrolujte prostředí pomocí vlastnosti `if (Environment.IsDevelopment())`(například).

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

## <a name="debug-aspnet-core-apps"></a>Ladění aplikací ASP.NET Core

Následující odkazy poskytují informace o ladění aplikací ASP.NET Core.

* [Ladění jádra ASP na Linuxu](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Ladění .NET Core na Unixu přes SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Úvodní příručka: Ladění ASP.NET ladicím programem sady Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Další informace o ladění najdete v [tomto problému s GitHubem.](https://github.com/dotnet/AspNetCore.Docs/issues/2960)
