---
title: Vytvoření klienta a serveru .NET Core gRPC v ASP.NET Core
author: juntaoluo
description: Tento kurz ukazuje, jak vytvořit gRPC service a gRPC klienta na ASP.NET Core. Naučte se, jak vytvořit projekt služby gRPC, upravit soubor proto a přidat volání duplexního streamování.
ms.author: johluo
ms.date: 04/08/2020
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 2bbd40b4b89af170dae40b8a5277749d6bcd5faf
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994632"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Kurz: Vytvoření klienta a serveru gRPC v ASP.NET Core

Podle [John Luo](https://github.com/juntaoluo)

Tento kurz ukazuje, jak vytvořit klienta [gRPC](https://grpc.io/docs/guides/) .NET Core a ASP.NET Core gRPC Server.

Na konci budete mít gRPC klienta, který komunikuje se službou gRPC Greeter.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak stáhnout](xref:index#how-to-download-a-sample)).

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte gRPC Server.
> * Vytvořte gRPC klienta.
> * Otestujte klientskou službu gRPC se službou gRPC Greeter.

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Spusťte Visual Studio a vyberte **Vytvořit nový projekt**. Případně v nabídce **Soubor** sady Visual Studio vyberte **Nový** > **projekt**.
* V **dialogovém** okně Vytvořit nový projekt vyberte **gRPC Service** a vyberte **Další**:

  ![Vytvořit dialogové okno vytvořit nový projekt](~/tutorials/grpc/grpc-start/static/cnp.png)

* Název projektu **GrpcGreeter**. Je důležité pojmenovat projekt *GrpcGreeter,* aby se jmenné prostory shodovaly při kopírování a vkládání kódu.
* Vyberte **Vytvořit**.
* V **dialogovém okně Vytvořit novou službu gRPC:**
  * Je vybrána šablona **služby gRPC.**
  * Vyberte **Vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.
* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * Příkaz `dotnet new` vytvoří novou službu gRPC ve složce *GrpcGreeter.*
  * Příkaz `code` otevře složku *GrpcGreeter* v nové instanci kódu sady Visual Studio.

  V poli GrpcGreeter se zobrazí dialogové okno s **požadovanými prostředky k sestavení a laděním. Přidat?**
* Vyberte **ano**.

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

```dotnetcli
dotnet new grpc -o GrpcGreeter
cd GrpcGreeter
```

Předchozí příkazy používají [rozhraní PŘÍKAZU .NET Core CLI](/dotnet/core/tools/dotnet) k vytvoření služby gRPC.

### <a name="open-the-project"></a>Otevření projektu

V sadě Visual Studio vyberte **Soubor** > **otevřít**a pak vyberte soubor *GrpcGreeter.csproj.*

---

### <a name="run-the-service"></a>Spuštění služby

  [!INCLUDE[](~/includes/run-the-app.md)]

Protokoly zobrazit službu `https://localhost:5001`naslouchání na .

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> Šablona gRPC je nakonfigurována pro použití [zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246) gRPC klienti musí používat PROTOKOL HTTPS pro volání serveru.
>
> macOS nepodporuje ASP.NET Core gRPC s TLS. K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace. Další informace najdete [v tématu Nelze spustit ASP.NET aplikaci Core gRPC v systému macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

### <a name="examine-the-project-files"></a>Prohlédněte si soubory projektu

*GrpcGreeter* soubory projektu:

* *greet.proto* &ndash; Soubor *Protos/greet.proto* definuje `Greeter` gRPC a používá se ke generování prostředků serveru gRPC. Další informace naleznete [v tématu Úvod do gRPC](xref:grpc/index).
* *Složka služby:* Obsahuje `Greeter` implementaci služby.
* *appSettings.json* &ndash; Obsahuje konfigurační data, například protokol používaný společností Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs* &ndash; Obsahuje vstupní bod pro službu gRPC. Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.
* *Startup.cs* &ndash; Obsahuje kód, který konfiguruje chování aplikace. Další informace naleznete v [tématu App startup](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v aplikaci konzoly .NET

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Otevřete druhou instanci sady Visual Studio a vyberte **Vytvořit nový projekt**.
* V **dialogovém** okně Vytvořit nový projekt vyberte **Console App (.NET Core)** a vyberte **Další**.
* Do textového pole **Název** zadejte **GrpcGreeterClient** a vyberte **Vytvořit**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřete [integrovanou svorku](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresáře (`cd`) na složku, která bude projekt obsahovat.
* Spusťte následující příkazy:

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Postupujte podle pokynů v [části Vytvoření kompletního řešení .NET Core v macOS pomocí Visual Studia pro Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) a vytvořte konzolovou aplikaci s názvem *GrpcGreeterClient*.

---

### <a name="add-required-packages"></a>Přidání požadovaných balíčků

Projekt klienta gRPC vyžaduje následující balíčky:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobufové zprávy API pro C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje podporu nástrojů C# pro soubory protobuf. Balíček nástrojů není vyžadován za běhu, takže závislost je `PrivateAssets="All"`označena .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Nainstalujte balíčky pomocí konzoly Správce balíčků (PMC) nebo Spravovat balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC pro instalaci balíčků

* V sadě Visual Studio vyberte **Nástroje, které** > **nástrojiNuGet Správce** > **balíčků konzoly Správce balíčků**
* V okně **Konzola** správce `cd GrpcGreeterClient` balíčků spusťte a změňte adresáře do složky obsahující soubory *GrpcGreeterClient.csproj.*
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

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Z **integrovaného terminálu**spusťte následující příkazy :

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši na složku **Balíčky** v **panelu řešení** > **Přidat balíčky**
* Do vyhledávacího pole zadejte **Grpc.Net.Client.**
* V podokně výsledků vyberte balíček **Grpc.Net.Client** a vyberte **Přidat balíček.**
* Opakujte `Google.Protobuf` `Grpc.Tools`pro a .

---

### <a name="add-greetproto"></a>Přidat greet.proto

* Vytvořte složku *Protos* v projektu klienta gRPC.
* Zkopírujte soubor *Protos\greet.proto* ze služby gRPC Greeter do klientského projektu gRPC.
* Upravte soubor projektu *GrpcGreeterClient.csproj:*

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  Klepněte pravým tlačítkem myši na projekt a vyberte **upravit soubor projektu**.

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  Vyberte soubor *GrpcGreeterClient.csproj.*

  # <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  Klepněte pravým tlačítkem myši na projekt a vyberte **příkaz Nástroje** > **upravit soubor**.

  ---

* Přidejte skupinu `<Protobuf>` položek s prvkem, který odkazuje na soubor *greet.proto:*

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Vytvořte projekt a vytvořte `GrpcGreeter` typy v oboru názvů. Typy `GrpcGreeter` jsou generovány automaticky procesem sestavení.

Aktualizujte soubor *klienta gRPC Program.cs* následujícím kódem:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* obsahuje vstupní bod a logiku pro klienta gRPC.

Klient Greeter je vytvořen:

* Vytvoření instance `GrpcChannel` obsahující informace pro vytvoření připojení ke službě gRPC.
* Použití `GrpcChannel` k vytvoření klienta Greeter:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Klient Greeter volá asynchronní `SayHello` metodu. Zobrazí se `SayHello` výsledek volání:

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Otestujte klienta gRPC pomocí služby gRPC Greeter

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ve službě Greeter `Ctrl+F5` spusťte server bez ladicího programu stisknutím klávesy .
* V `GrpcGreeterClient` projektu stisknutím `Ctrl+F5` klávesy spusťte klienta bez ladicího programu.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Spusťte službu Greeter.
* Spusťte klienta.


# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Spusťte službu Greeter.
* Spusťte klienta.

---

Klient odešle službu pozdrav se zprávou obsahující její název *GreeterClient*. Služba odešle zprávu "Hello GreeterClient" jako odpověď. V příkazovém řádku se zobrazí odpověď "Hello GreeterClient":

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba gRPC zaznamenává podrobnosti o úspěšném volání v protokolech zapsaných do příkazového řádku:

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
> Kód v tomto článku vyžaduje ASP.NET vývojový certifikát Core HTTPS k zabezpečení služby gRPC. Pokud klient gRPC rozhraní s `The remote certificate is invalid according to the validation procedure.` chybou nezdaří se zprávou nebo `The SSL connection could not be established.`, vývojový certifikát není důvěryhodný. Chcete-li tento problém vyřešit, přečtěte si téma [Volání služby gRPC s nedůvěryhodným/neplatným certifikátem](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>Další kroky

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
