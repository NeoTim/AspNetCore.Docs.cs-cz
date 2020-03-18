---
title: Řešení potíží a ladění ASP.NET Corech projektů
author: Rick-Anderson
description: Pochopení a řešení potíží s chybami a problémy s ASP.NET Core projekty.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511506"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a>Řešení potíží a ladění ASP.NET Corech projektů

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

Následující odkazy obsahují pokyny k odstraňování potíží:

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [NORWEGIAN Developers Conference konference (Londýn, 2018): diagnostikování problémů v aplikacích ASP.NET Core](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [Blog ASP.NET: řešení potíží s výkonem ASP.NET Core problémy s výkonem](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a>Upozornění .NET Core SDK

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a>Nainstalují se 32 i 64 verze .NET Core SDK.

V dialogovém okně **Nový projekt** pro ASP.NET Core se může zobrazit následující upozornění:

> Jsou nainstalovány 32 a 64 bitové verze .NET Core SDK. Jsou zobrazeny pouze šablony z 64 verzí nainstalovaných v umístění C:\\Program Files\\dotnet\\SDK\\.

Toto upozornění se zobrazí, 32 Pokud jsou nainstalovány 32bitové verze [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) (x86) i 64-bit (x64). Mezi běžné příčiny můžou být nainstalované obě verze:

* Původně jste stáhli instalační službu .NET Core SDK pomocí 32 počítače, ale pak jste ji zkopírovali napříč a nainstalovali na 64 počítač.
* 32 bitová .NET Core SDK byla nainstalována jinou aplikací.
* Byla stažena a nainstalována nesprávná verze.

Pokud chcete zabránit tomuto upozornění, odinstalujte 32 .NET Core SDK bitů. Odinstalujte ji > **programy a funkce** v **Ovládacích panelech** > **Odinstalovat nebo změnit program**. Pokud rozumíte tomu, proč k upozornění dojde a jeho důsledky, můžete upozornění ignorovat.

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a>.NET Core SDK je nainstalováno v několika umístěních.

V dialogovém okně **Nový projekt** pro ASP.NET Core se může zobrazit následující upozornění:

> .NET Core SDK je nainstalován v několika umístěních. Zobrazí se pouze šablony sady SDK nainstalované v umístění C:\\Program Files\\dotnet\\SDK\\.

Tato zpráva se zobrazí, pokud máte alespoň jednu instalaci .NET Core SDK v adresáři mimo *C:\\Program Files\\dotnet\\SDK\\* . K tomu obvykle dochází, když .NET Core SDK nasazené na počítači pomocí kopírování a vkládání místo instalačního programu MSI.

Pokud chcete zabránit tomuto upozornění, odinstalujte všechny 32 sady .NET Core SDK a moduly runtime. Odinstalujte ji > **programy a funkce** v **Ovládacích panelech** > **Odinstalovat nebo změnit program**. Pokud rozumíte tomu, proč k upozornění dojde a jeho důsledky, můžete upozornění ignorovat.

### <a name="no-net-core-sdks-were-detected"></a>Nezjistily se žádné sady .NET Core SDK.

* V dialogovém okně **Nový projekt** sady Visual Studio pro ASP.NET Core se může zobrazit následující upozornění:

  > Nezjistily se žádné sady .NET Core SDK, ujistěte se, že jsou zahrnuté v proměnné prostředí `PATH`.

* Při provádění `dotnet` příkazu se zobrazí upozornění jako:

  > Nebylo možné najít žádné nainstalované sady dotnet SDK.

Tato upozornění se zobrazí, pokud proměnná prostředí `PATH` neukazuje na žádné sady .NET Core SDK v počítači. Řešení tohoto problému:

* Nainstalujte .NET Core SDK. Získejte nejnovější instalační program ze [souborů ke stažení pro rozhraní .NET](https://dotnet.microsoft.com/download).
* Ověřte, zda proměnná prostředí `PATH` odkazuje na umístění, kde je nainstalována sada SDK (`C:\Program Files\dotnet\` pro 64 bitů/x64 nebo `C:\Program Files (x86)\dotnet\` pro 32bitové/x86). Instalační program sady SDK obvykle nastaví `PATH`. Vždy nainstalujte stejné sady bitová verze SDK a moduly runtime na stejném počítači.

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a>Chybějící sada SDK po instalaci hostující sady .NET Core

Instalace [hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) upraví `PATH` při instalaci modulu runtime .NET Core, který odkazuje na 32 (x86) verzi .NET core (`C:\Program Files (x86)\dotnet\`). To může mít za následek chybějící sady SDK, pokud je použit příkaz .NET Core `dotnet` s 32 (x86) ([nejsou zjištěny žádné sady .NET Core SDK](#no-net-core-sdks-were-detected)). Chcete-li tento problém vyřešit, přesuňte `C:\Program Files\dotnet\` do pozice před `C:\Program Files (x86)\dotnet\` na `PATH`.

## <a name="obtain-data-from-an-app"></a>Získání dat z aplikace

Pokud aplikace dokáže reagovat na požadavky, můžete z aplikace získat následující data pomocí middlewaru:

* Požadavek &ndash; metoda, schéma, hostitel, pathbase, cesta, řetězec dotazu, záhlaví
* Connection &ndash; Vzdálená IP adresa, vzdálený port, místní IP adresa, místní port, klientský certifikát
* Název &ndash; identity, zobrazovaný název
* Nastavení konfigurace
* Proměnné prostředí

Vložte následující kód [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na začátek kanálu zpracování požadavků metody `Startup.Configure`. Prostředí je zkontrolováno před spuštěním middlewaru, aby bylo zajištěno, že kód je spuštěn pouze ve vývojovém prostředí.

K získání prostředí použijte některý z následujících přístupů:

* Vloží `IHostingEnvironment` do metody `Startup.Configure` a zkontroluje prostředí pomocí místní proměnné. Následující vzorový kód demonstruje tento přístup.

* Přiřaďte prostředí k vlastnosti ve třídě `Startup`. Ověřte prostředí pomocí vlastnosti (například `if (Environment.IsDevelopment())`).

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

Následující odkazy obsahují informace o ladění aplikací ASP.NET Core.

* [Ladění ASP Core v systému Linux](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [Ladění .NET Core v systému UNIX přes SSH](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [Rychlý Start: ladění ASP.NET pomocí ladicího programu sady Visual Studio](/visualstudio/debugger/quickstart-debug-aspnet)
* Další informace o ladění najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/2960) .
