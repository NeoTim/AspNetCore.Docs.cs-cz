---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: V tomto kurzu se dozvíte, jak vytvořit službu gRPC a klienta gRPC na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor. a přidat volání duplexního streamování.
ms.author: johluo
ms.date: 04/08/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 13eb57bbe671dcc70a1678222a98590f4edc6e6f
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424253"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core

Od [Jan Luo](https://github.com/juntaoluo)

V tomto kurzu se dozvíte, jak vytvořit klienta .NET Core [gRPC](https://grpc.io/docs/guides/) a Server služby ASP.NET Core gRPC.

Na konci budete mít klienta gRPC, který komunikuje se službou Greeter gRPC.

[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte server gRPC.
> * Vytvořte klienta gRPC.
> * Otestujte službu gRPC Client pomocí služby gRPC Greeter.

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte aplikaci Visual Studio a vyberte možnost **vytvořit nový projekt**. Případně můžete v nabídce **soubor** sady Visual Studio vybrat možnost **Nový**  >  **projekt**.
* V dialogovém okně **vytvořit nový projekt** vyberte **Služba GRPC** a vyberte **Další**:

  ![Vytvořit nový projekt – dialogové okno](~/tutorials/grpc/grpc-start/static/cnp.png)

* Pojmenujte projekt **GrpcGreeter**. Je důležité pojmenovat projekt *GrpcGreeter* , aby se obory názvů shodovaly při kopírování a vkládání kódu.
* Vyberte **Vytvořit**.
* V dialogovém okně **vytvořit novou službu gRPC** :
  * Je vybraná Šablona **služby gRPC** .
  * Vyberte **Vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.
* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new`Příkaz vytvoří ve složce *GrpcGreeter* novou službu gRPC.
  * `code`Příkaz otevře složku *GrpcGreeter* v nové instanci Visual Studio Code.

  Zobrazí se dialogové okno s **požadovanými prostředky pro sestavení a ladění chybí v ' GrpcGreeter '. Přidat je?**
* Vyberte **Ano**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Předchozí příkazy používají [.NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.

### <a name="open-the-project"></a>Otevřít projekt

V aplikaci Visual Studio vyberte **soubor**  >  **otevřít**a potom vyberte soubor *GrpcGreeter. csproj* .

---

### <a name="run-the-service"></a>Spuštění služby

  [!INCLUDE[](~/includes/run-the-app.md)]

V protokolech se zobrazuje služba, na které naslouchá `https://localhost:5001` .

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

* *Greeting.* v tomto případě: soubor *...* dedefinuje `Greeter` gRPC a slouží k vygenerování prostředků serveru gRPC. Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).
* Složka *služby* : obsahuje implementaci `Greeter` služby.
* *appSettings.js*: obsahuje konfigurační data, jako je například protokol používaný v Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: obsahuje vstupní bod pro službu gRPC. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.
* *Startup.cs*: obsahuje kód, který konfiguruje chování aplikace. Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v konzolové aplikaci .NET

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřete druhou instanci aplikace Visual Studio a vyberte možnost **vytvořit nový projekt**.
* V dialogovém okně **vytvořit nový projekt** vyberte **Konzolová aplikace (.NET Core)** a pak vyberte **Další**.
* Do textového pole **název projektu** zadejte **GrpcGreeterClient** a vyberte **vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře ( `cd` ) na složku, která bude obsahovat projekt.
* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Postupujte podle pokynů v tématu vytvoření [kompletního řešení .NET Core na MacOS pomocí Visual Studio pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Přidat požadované balíčky

Projekt klienta gRPC vyžaduje následující balíčky:

* [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google. Protobuf](https://www.nuget.org/packages/Google.Protobuf/), která obsahuje rozhraní API pro zprávy Protobuf pro C#.
* [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/), které obsahují podporu nástrojů C# pro soubory protobuf. Balíček nástrojů se za běhu nevyžaduje, takže závislost je označená jako `PrivateAssets="All"` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nainstalujte balíčky buď pomocí konzoly Správce balíčků (PMC), nebo spravujte balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC pro instalaci balíčků

* V aplikaci Visual Studio vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** .
* V okně **konzoly Správce balíčků** spusťte příkaz, `cd GrpcGreeterClient` aby se změnily adresáře do složky obsahující soubory *GrpcGreeterClient. csproj* .
* Spusťte následující příkazy:

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Správa možností balíčků NuGet pro instalaci balíčků

* Klikněte pravým tlačítkem na projekt v **Průzkumník řešení**  >  **Spravovat balíčky NuGet** .
* Vyberte kartu **Procházet**.
* Do vyhledávacího pole zadejte **Grpc .NET. Client** .
* Na kartě **Procházet** vyberte balíček **Grpc .NET. Client** a vyberte **nainstalovat**.
* Opakujte pro `Google.Protobuf` a `Grpc.Tools` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkazy:

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem na složku **balíčky** v **oblast řešení**  >  **Přidat balíčky** .
* Do vyhledávacího pole zadejte **Grpc .NET. Client** .
* V podokně výsledků vyberte balíček **Grpc .NET. Client** a vyberte **Přidat balíček** .
* Opakujte pro `Google.Protobuf` a `Grpc.Tools` .

---

### <a name="add-greetproto"></a>Přidat pozdrav. proto

* V klientském projektu gRPC *vytvořte složku.*
* Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do projektu klienta gRPC.
* Aktualizujte obor názvů uvnitř `greet.proto` souboru na obor názvů projektu:

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* Upravte soubor projektu *GrpcGreeterClient. csproj* :

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Klikněte pravým tlačítkem na projekt a vyberte **Upravit soubor projektu**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Vyberte soubor *GrpcGreeterClient. csproj* .

  # <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  Klikněte pravým tlačítkem na projekt a vyberte **nástroje**  >  **Upravit soubor**.

  ---

* Přidejte skupinu položek s `<Protobuf>` prvkem, který odkazuje na soubor *Greeting.* dekáže:

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Sestavte projekt pro vytvoření typů v `GrpcGreeter` oboru názvů. `GrpcGreeter`Typy jsou generovány automaticky procesem sestavení.

Aktualizujte soubor *program.cs* klienta gRPC pomocí následujícího kódu:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.

Klient s pozdravem vytvořil:

* Vytvoří se instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.
* Použití `GrpcChannel` ke konstrukci klienta Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Klient Greeter volá asynchronní `SayHello` metodu. Zobrazí se výsledek `SayHello` volání:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Test klienta gRPC pomocí služby gRPC Greeter

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ve službě Greeter stiskněte klávesu `Ctrl+F5` a spusťte server bez ladicího programu.
* V `GrpcGreeterClient` projektu stisknutím klávesy `Ctrl+F5` Spusťte klienta bez ladicího programu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte službu Greeter.
* Spusťte klienta.


# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

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
> Kód v tomto článku vyžaduje k zabezpečení služby gRPC ASP.NET Core certifikát pro vývoj HTTPS. Pokud klient .NET gRPC selhal se zprávou `The remote certificate is invalid according to the validation procedure.` nebo `The SSL connection could not be established.` , vývojový certifikát není důvěryhodný. Chcete-li tento problém vyřešit, přečtěte si téma [volání služby gRPC s nedůvěryhodným/neplatným certifikátem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Další kroky

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
