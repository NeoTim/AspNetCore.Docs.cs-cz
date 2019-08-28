---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor. a přidat volání duplexního streamování.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 8/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: ffe0034f1d33fb9282b39c030421b9b307413cdb
ms.sourcegitcommit: de17150e5ec7507d7114dde0e5dbc2e45a66ef53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113270"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core

Od [Jan Luo](https://github.com/juntaoluo)

V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.

Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte server gRPC.
> * Vytvořte klienta gRPC.
> * Otestujte službu gRPC Client pomocí služby gRPC Greeter.

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**. Případně můžete v nabídce **soubor** sady Visual Studio vybrat možnost **Nový** > **projekt**.
* V dialogovém okně **vytvořit nový projekt** vyberte **Služba GPRC** a vyberte **Další**:

  ![\* * Vytvořit nový projekt * * dialog](~/tutorials/grpc/grpc-start/static/cnp.png)

* Pojmenujte projekt **GrpcGreeter**. Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.
* Vyberte **Vytvořit**.
* V dialogovém okně **vytvořit novou službu gRPC** :
  * Je vybraná Šablona **služby gRPC** .
  * Vyberte **Vytvořit**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.
* Spusťte následující příkazy:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Příkaz vytvoří ve složce GrpcGreeter novou službu gRPC. `dotnet new`
  * Příkaz otevře složku GrpcGreeter v nové instanci Visual Studio Code. `code`

  Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' GrpcGreeter '. Přidat je?**
* Vyberte **Ano**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.

### <a name="open-the-project"></a>Otevřít projekt

V aplikaci Visual Studio vyberte **soubor** > **otevřít**a potom vyberte soubor *GrpcGreeter. sln* .

---

### <a name="run-the-service"></a>Spuštění služby

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím `Ctrl+F5` klávesy spustíte službu gRPC bez ladicího programu.

  Visual Studio spouští službu z příkazového řádku.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte projekt gRPC Greeter *GrpcGreeter* z příkazového řádku pomocí `dotnet run`příkazu.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Spusťte projekt gRPC Greeter *GrpcGreeter* z příkazového řádku pomocí `dotnet run`příkazu.

---

V protokolech se zobrazuje služba, `https://localhost:5001`na které naslouchá.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Šablona gRPC je nakonfigurovaná tak, aby používala [protokol TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246). gRPC klienti potřebují pro volání serveru použít protokol HTTPS.
>
> macOS nepodporuje ASP.NET Core gRPC s protokolem TLS. K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace. Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Prověřte soubory projektu

Soubory projektu *GrpcGreeter* :

* *pozdrav.* &ndash; v tomto případě soubor `Greeter` *...* dedefinuje gRPC a slouží k vygenerování prostředků serveru gRPC. Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).
* Složka *služeb* : Obsahuje implementaci `Greeter` služby.
* *appSettings. JSON* &ndash; obsahuje konfigurační data, jako je protokol, který používá Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* Program.cs&ndash; obsahuje vstupní bod pro službu gRPC. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.
* Startup.cs&ndash; obsahuje kód, který konfiguruje chování aplikace. Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v konzolové aplikaci .NET

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřete druhou instanci aplikace Visual Studio a vyberte možnost **vytvořit nový projekt**.
* V dialogovém okně **vytvořit nový projekt** vyberte konzolová **aplikace (.NET Core)** a pak vyberte **Další**.
* Do textového pole **název** zadejte **GrpcGreeterClient** a vyberte **vytvořit**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude obsahovat projekt.
* Spusťte následující příkazy:

  ```console
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Postupujte podle pokynů v tématu vytvoření [kompletního řešení .NET Core na MacOS pomocí Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Přidat požadované balíčky

Projekt klienta gRPC vyžaduje následující balíčky:

* [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje rozhraní API pro zprávy C#Protobuf pro.
* [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují C# podporu nástrojů pro soubory protobuf. Balíček nástrojů se za běhu nevyžaduje, takže závislost je označená jako `PrivateAssets="All"`.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC pro instalaci balíčků

* V aplikaci Visual Studio vyberte **nástroje** >  > **Správce balíčků NuGet** **Konzola správce balíčků** .
* V okně **konzoly Správce balíčků** spusťte příkaz `cd GrpcGreeterClient` , aby se změnily adresáře do složky obsahující soubory *GrpcGreeterClient. csproj* .
* Spusťte následující příkazy:

  ```powershell
  Install-Package Grpc.Net.Client -prerelease
  Install-Package Google.Protobuf -prerelease
  Install-Package Grpc.Tools -prerelease
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Správa možností balíčků NuGet pro instalaci balíčků

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení** > **Spravovat balíčky NuGet** .
* Vyberte kartu **Procházet** .
* Do vyhledávacího pole zadejte **Grpc .NET. Client** .
* Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.
* Opakujte pro `Google.Protobuf` a `Grpc.Tools`.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkazy:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku **balíčky** v **oblast řešení** > **Přidat balíčky** .
* Do vyhledávacího pole zadejte **Grpc .NET. Client** .
* V podokně výsledků vyberte balíček **Grpc .NET. Client** a vyberte **Přidat balíček** .
* Opakujte pro `Google.Protobuf` a `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Přidat pozdrav. proto

* V klientském projektu gRPC vytvořte složku.
* Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do projektu klienta gRPC.
* Upravte soubor projektu *GrpcGreeterClient. csproj* :

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

  Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Vyberte soubor *GrpcGreeterClient. csproj* .

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  Klikněte pravým tlačítkem na projekt a vyberte **nástroje** > **Upravit soubor**.

  ---

* Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor *Greeting.* dekáže:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Sestavte projekt pro vytvoření typů v `GrpcGreeter` oboru názvů. `GrpcGreeter` Typy jsou generovány automaticky procesem sestavení.

Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.

Klient s pozdravem vytvořil:

* Vytvoří se instance obsahující informace pro vytvoření připojení ke službě gRPC. `HttpClient`
* `HttpClient` Použití ke konstrukci klienta Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-6)]

Klient Greeter volá asynchronní `SayHello` metodu. Zobrazí se výsledek `SayHello` volání:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-9)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Test klienta gRPC pomocí služby gRPC Greeter

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ve službě Greeter stiskněte klávesu `Ctrl+F5` a spusťte server bez ladicího programu.
* V projektu stisknutím klávesy `Ctrl+F5` spusťte klienta bez ladicího programu. `GrpcGreeterClient`

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte službu Greeter.
* Spusťte klienta.


# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Spusťte službu Greeter.
* Spusťte klienta.

---

Klient pošle službě pozdrav zprávy s názvem, který obsahuje, *GreeterClient*. Služba odešle jako odpověď zprávu "Hello GreeterClient". Odpověď "Hello GreeterClient" se zobrazí na příkazovém řádku:

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba gRPC zaznamenává podrobnosti o úspěšném volání do protokolů zapsaných do příkazového řádku:

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

> [!NOTE]
> Kód v tomto článku vyžaduje k zabezpečení služby gRPC ASP.NET Core certifikát pro vývoj HTTPS. Pokud se klient u zprávy `The remote certificate is invalid according to the validation procedure.`nezdařil, vývojový certifikát není důvěryhodný. Pokyny k vyřešení tohoto problému naleznete v tématu [Trust ASP.NET Core certifikát pro vývoj https ve Windows a MacOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

### <a name="next-steps"></a>Další postup

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
