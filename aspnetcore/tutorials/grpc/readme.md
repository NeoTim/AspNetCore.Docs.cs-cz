---
page_type: sample
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: create-grpc-client
ms.openlocfilehash: b9feb9eed62177358fffc0d7da582f625a431e32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660928"
---
# <a name="create-a-grpc-client-and-server-in-aspnet-core-30-using-visual-studio"></a>Vytvoření klienta a serveru gRPC v ASP.NET Core 3,0 pomocí sady Visual Studio

V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.

Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.

V tomto kurzu jste.

* Vytvořte server gRPC.
* Vytvořte klienta gRPC.
* Otestujte službu gRPC Client pomocí služby gRPC Greeter.

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

* V nabídce **soubor** v aplikaci Visual Studio vyberte **Nový** > **projekt**.
* V dialogovém okně **vytvořit nový projekt** vyberte **ASP.NET Core webová aplikace**.
* Vyberte **Další**.
* Pojmenujte projekt **GrpcGreeter**. Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.
* Vyberte **Vytvořit**.
* V dialogovém okně **vytvořit novou ASP.NET Core webovou aplikaci** :
  * V rozevíracích nabídkách vyberte **.NET Core** a **ASP.NET Core 3,0** . 
  * Vyberte šablonu **služby gRPC** .
  * Vyberte **Vytvořit**.

### <a name="run-the-service"></a>Spuštění služby

* Stisknutím `Ctrl+F5` spustíte službu gRPC bez ladicího programu.

  Visual Studio spouští službu z příkazového řádku.

Protokoly zobrazují službu, která naslouchá na `https://localhost:5001`.

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
> Šablona gRPC je nakonfigurovaná tak, aby používala [protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246). gRPC klienti potřebují pro volání serveru použít protokol HTTPS.
>
> macOS nepodporuje ASP.NET Core gRPC s protokolem TLS. K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace. Další informace najdete v tématu [gRPC a ASP.NET Core v MacOS](xref:grpc/aspnetcore#grpc-and-aspnet-core-on-macos).

### <a name="examine-the-project-files"></a>Prověřte soubory projektu

Soubory projektu *GrpcGreeter* :

* *Greeting.* v tomto případě: soubor *....* dedefinuje `Greeter` gRPC a slouží k vygenerování prostředků serveru gRPC. Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).
* Složka *Services* : obsahuje implementaci služby `Greeter`.
* *appSettings. JSON*: obsahuje konfigurační data, jako je například protokol, který používá Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: obsahuje vstupní bod pro službu gRPC. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.
* *Startup.cs*: obsahuje kód, který konfiguruje chování aplikace. Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v konzolové aplikaci .NET

* Otevřete druhou instanci aplikace Visual Studio.
* Z řádku nabídek vyberte **soubor** > **Nový** > **projekt** .
* V dialogovém okně **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Core)** .
* Vyberte **Další**.
* Do textového pole **název** zadejte "GrpcGreeterClient".
* Vyberte **Vytvořit**.

### <a name="add-required-packages"></a>Přidat požadované balíčky

Projekt klienta gRPC vyžaduje následující balíčky:

* [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.
* [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf. Balíček nástrojů se za běhu nevyžaduje, takže je tato závislost označená jako `PrivateAssets="All"`.

Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC pro instalaci balíčků

* V aplikaci Visual Studio vyberte **nástroje** > **správce balíčků NuGet** > **Konzola správce balíčků** .
* V okně **konzoly Správce balíčků** přejděte do adresáře, ve kterém existuje soubor *GrpcGreeterClient. csproj* .
* Spusťte následující příkazy:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Správa možností balíčků NuGet pro instalaci balíčků

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .
* Vyberte kartu **Procházet**.
* Do vyhledávacího pole zadejte **Grpc .NET. Client** .
* Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.
* Opakujte pro `Google.Protobuf` a `Grpc.Tools`.

### <a name="add-greetproto"></a>Přidat pozdrav. proto

* V klientském projektu gRPC **vytvořte složku.**
* Zkopírujte soubor **Protos\greet.proto** ze služby gRPC Greeter do projektu klienta gRPC.
* Upravte soubor projektu *GrpcGreeterClient. csproj* :

  Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.

* Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor **Greeting.** dekáže:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Sestavte projekt pro vytvoření typů v oboru názvů `GrpcGreeter`. Typy `GrpcGreeter` jsou automaticky generovány procesem sestavení.

Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:

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

Klient s pozdravem vytvořil:

* Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.
* Vytvoření klienta Greeter pomocí `GrpcChannel`.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Test klienta gRPC pomocí služby gRPC Greeter

* V případě služby Greeter stiskněte `Ctrl+F5` a spusťte server bez ladicího programu.
* V projektu `GrpcGreeterClient` stisknutím `Ctrl+F5` spusťte klienta bez ladicího programu.

Klient pošle službě pozdravu zprávu, která obsahuje název "GreeterClient". Služba odešle jako odpověď zprávu "Hello GreeterClient". Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku.

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

### <a name="docs-help--next-steps-for-grpc"></a>Dokumentace k dokumentaci & další kroky pro gRPC

* [Úvod do gRPC na ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/index?view=aspnetcore-3.0)
* [gRPC služby sC#](https://docs.microsoft.com/aspnet/core/grpc/basics?view=aspnetcore-3.0)
* [Migrace služeb gRPC z C-Core na ASP.NET Core](https://docs.microsoft.com/aspnet/core/grpc/migration?view=aspnetcore-3.0)
