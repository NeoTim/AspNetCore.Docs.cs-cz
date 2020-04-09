---
page_type: sample
description: Tento kurz ukazuje, jak vytvořit gRPC service a gRPC klienta na ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660928"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Vytvoření klienta a serveru gRPC v ASP.NET Core 3.0 pomocí sady Visual Studio

Tento kurz ukazuje, jak vytvořit klienta [gRPC](https://grpc.io/docs/guides/) .NET Core a ASP.NET Core gRPC Server.

Na konci budete mít gRPC klienta, který komunikuje se službou gRPC Greeter.

V tomto tutoriálu, vy;

* Vytvořte gRPC Server.
* Vytvořte gRPC klienta.
* Otestujte klientskou službu gRPC se službou gRPC Greeter.

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

* V nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* V **dialogovém** okně Vytvořit nový projekt vyberte **ASP.NET základní webovou aplikaci**.
* Vybrat **další**
* Název projektu **GrpcGreeter**. Je důležité pojmenovat projekt *GrpcGreeter,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.
* Vybrat **vytvořit**
* V **dialogovém okně Vytvořit novou ASP.NET základní webovou aplikaci:**
  * V rozevíracích nabídkách vyberte **.NET Core** a **ASP.NET Core 3.0.** 
  * Vyberte šablonu **služby gRPC.**
  * Vybrat **vytvořit**

### <a name="run-the-service"></a>Spuštění služby

* Stisknutím `Ctrl+F5` klávesy spusťte službu gRPC bez ladicího programu.

  Visual Studio spustí službu v příkazovém řádku.

Protokoly zobrazit službu `https://localhost:5001`naslouchání na .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

> [!NOTE]
> Šablona gRPC je nakonfigurována pro použití [zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246) gRPC klienti musí používat PROTOKOL HTTPS pro volání serveru.
>
> macOS nepodporuje ASP.NET Core gRPC s TLS. K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace. Další informace najdete [v tématu gRPC a ASP.NET Core v macOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Prohlédněte si soubory projektu

*GrpcGreeter* soubory projektu:

* *greet.proto*: Soubor *Protos/greet.proto* `Greeter` definuje gRPC a používá se ke generování prostředků serveru gRPC. Další informace naleznete [v tématu Úvod do gRPC](xref:grpc/index).
* *Složka služby:* Obsahuje `Greeter` implementaci služby.
* *appSettings.json*: Obsahuje konfigurační data, například protokol používaný kestrelem. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: Obsahuje vstupní bod pro službu gRPC. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.
* *Startup.cs*: Obsahuje kód, který konfiguruje chování aplikace. Další informace naleznete v [tématu App startup](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v aplikaci konzoly .NET

* Otevřete druhou instanci sady Visual Studio.
* Na řádku nabídek vyberte **Soubor** > **nového** > **projektu.**
* V **dialogovém** okně Vytvořit nový projekt vyberte **Console App (.NET Core)**.
* Vybrat **další**
* Do textového pole **Název** zadejte "GrpcGreeterClient".
* Vyberte **Vytvořit**.

### <a name="add-required-packages"></a>Přidání požadovaných balíčků

Projekt klienta gRPC vyžaduje následující balíčky:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobufové zprávy API pro C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje podporu nástrojů C# pro soubory protobuf. Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena .

Nainstalujte balíčky pomocí konzoly Správce balíčků (PMC) nebo Spravovat balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC pro instalaci balíčků

* V sadě Visual Studio vyberte **Nástroje, které** > **nástrojiNuGet Správce** > **balíčků konzoly Správce balíčků**
* Z okna **Konzoly správce balíčků** přejděte do adresáře, ve kterém existuje soubor *GrpcGreeterClient.csproj.*
* Spusťte následující příkazy:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Možnost Spravovat balíčky NuGet pro instalaci balíčků

* Klikněte pravým tlačítkem myši na projekt v **Průzkumníku** > řešení**Spravovat balíčky NuGet**
* Vyberte kartu **Procházet**.
* Do vyhledávacího pole zadejte **Grpc.Net.Client.**
* Na kartě **Procházet** vyberte balíček **Grpc.Net.Client** a vyberte **Instalovat**.
* Opakujte `Google.Protobuf` `Grpc.Tools`pro a .

### <a name="add-greetproto"></a>Přidat greet.proto

* Vytvořte složku **Protos** v projektu klienta gRPC.
* Zkopírujte soubor **Protos\greet.proto** ze služby gRPC Greeter do klientského projektu gRPC.
* Upravte soubor projektu *GrpcGreeterClient.csproj:*

  Klepněte pravým tlačítkem myši na projekt a vyberte **upravit soubor projektu**.

* Přidejte skupinu `<Protobuf>` položek s prvkem, který odkazuje na soubor **greet.proto:**

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Vytvořte projekt a vytvořte `GrpcGreeter` typy v oboru názvů. Typy `GrpcGreeter` jsou generovány automaticky procesem sestavení.

Aktualizujte soubor *klienta gRPC Program.cs* následujícím kódem:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using GrpcGreeter;
using Grpc.Net.Client;

namespace GrpcGreeterClient
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // The port number(5001) must match the port of the gRPC server.
            var channel = GrpcChannel.ForAddress("https://localhost:5001");
            var client = new Greeter.GreeterClient(channel);
            var reply = await client.SayHelloAsync(
                              new HelloRequest { Name = "GreeterClient" });
            Console.WriteLine("Greeting: " + reply.Message);
            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.

Klient Greeter je vytvořen:

* Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.
* Pomocí `GrpcChannel` k vytvoření klienta Greeter.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Otestujte klienta gRPC pomocí služby gRPC Greeter

* Ve službě Greeter `Ctrl+F5` spusťte server bez ladicího programu stisknutím klávesy .
* V `GrpcGreeterClient` projektu stisknutím `Ctrl+F5` klávesy spusťte klienta bez ladicího programu.

Klient odešle službu pozdrav se zprávou obsahující jeho název "GreeterClient". Služba odešle zprávu "Hello GreeterClient" jako odpověď. V příkazovém řádku se zobrazí odpověď "Hello GreeterClient":

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba gRPC zaznamenává podrobnosti úspěšného volání v protokolech zapsaných do příkazového řádku.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="docs-help--next-steps-for-grpc"></a>Dokumenty pomáhají & další kroky pro gRPC

* [Úvod do gRPC na ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [Služby gRPC s jazykem C#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrace služeb gRPC z C-core do ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
