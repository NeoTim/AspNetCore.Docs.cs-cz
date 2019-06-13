---
title: Vytvoření .NET Core gRPC klientem a serverem v ASP.NET Core
author: juntaoluo
description: Tento kurz ukazuje, jak vytvořit klienta služby a gRPC gRPC v ASP.NET Core. Zjistěte, jak vytvořit projekt gRPC služby, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 06/12/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 919db3f31310342657c89100a6e25e8293648a9f
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034803"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>Kurz: Vytvoření gRPC klientem a serverem v ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

Tento kurz ukazuje, jak vytvořit .NET Core [gRPC](https://grpc.io/docs/guides/) klienta a ASP.NET Core gRPC serveru.

Na konci budete mít gRPC klienta, který komunikuje s gRPC Greeter služby.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření gRPC serveru.
> * Vytvoření klienta gRPC.
> * Testovat službu klienta gRPC s gRPC Greeter služby.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* V **vytvořte nový projekt** dialogového okna, vyberte **webové aplikace ASP.NET Core**.
* Vyberte **další**
* Pojmenujte projekt **GrpcGreeter**. Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.
* Vyberte **Vytvořit**.
* V **vytvořit novou webovou aplikaci ASP.NET Core** dialogové okno:
  * Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevíracích nabídek. 
  * Vyberte **gRPC služby** šablony.
  * Vyberte **Vytvořit**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresář (`cd`) do složky, která bude obsahovat projektu.
* Spusťte následující příkazy:

  ```console
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` Příkaz vytvoří novou službu gRPC u *GrpcGreeter* složky.
  * `code` Příkaz otevře *GrpcGreeter* složky v nové instanci sady Visual Studio Code.

  Zobrazí se dialogové okno s **'GrpcGreeter' chybí požadované prostředky pro sestavení a ladění. Přidat?**
* Vyberte **Ano**

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Z terminálu spusťte následující příkazy:

```console
  dotnet new grpc -o GrpcGreeter
  cd GrpcGreeter
```

Předchozí příkazy použití [rozhraní příkazového řádku .NET Core](/dotnet/core/tools/dotnet) vytvoření gRPC služby.

### <a name="open-the-project"></a>Otevřete projekt

Ze sady Visual Studio, vyberte **soubor > Otevřít**a pak vyberte *GrpcGreeter.sln* souboru.

<!-- End of VS tabs -->

---

### <a name="run-the-service"></a>Spuštění služby

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím klávesy `Ctrl+F5` ke spuštění služby gRPC bez ladicího programu.

  Visual Studio spustí službu v příkazovém řádku.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Spusťte projekt Greeter gRPC *GrpcGreeter* z příkazového řádku pomocí `dotnet run`.

<!-- End of combined VS/Mac tabs -->

---

V protokolech zobrazuje služba naslouchá na `http://localhost:50051`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
```

### <a name="examine-the-project-files"></a>Zkontrolujte soubory projektu

*GrpcGreeter* soubory projektu:

* *greet.proto*: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky. Další informace najdete v tématu [Úvod do gRPC](xref:grpc/index).
* *Služby* složky: Obsahuje implementaci `Greeter` služby.
* *appSettings.json*: Obsahuje konfigurační data, třeba protokol používá Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: Vstupní bod pro službu gRPC obsahuje. Další informace naleznete v tématu <xref:fundamentals/host/web-host>.
* *Startup.cs*: Obsahuje kód, který nakonfiguruje chování aplikace. Další informace najdete v tématu [spuštění aplikace](xref:fundamentals/startup).

## <a name="create-the-grpc-client-in-a-net-console-app"></a>Vytvoření klienta gRPC v konzolové aplikace .NET

## <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Vyberte **souboru** > **nový** > **projektu** z řádku nabídek.
* V **vytvořte nový projekt** dialogového okna, vyberte **Konzolová aplikace (.NET Core)** .
* Vyberte **další**
* V **název** textové pole, zadejte "GrpcGreeterClient".
* Vyberte **Vytvořit**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Otevřít [integrovaný terminál](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Změňte adresář (`cd`) do složky, která bude obsahovat projektu.
* Spusťte následující příkazy:

```console
dotnet new console -o GrpcGreeterClient
code -r GrpcGreeterClient
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Postupujte podle pokynů [tady](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

<!-- End of VS tabs -->

---

### <a name="add-required-packages"></a>Přidání požadovaných balíčků

Přidejte do projektu klienta gRPC následující balíčky:

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), který obsahuje klienta .NET Core.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobuf zpráva rozhraní API pro C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje C# Podpora nástrojů pro soubory protobuf. Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Instalace balíčků pomocí konzoly Správce balíčků (PMC) nebo spravovat balíčky NuGet.

#### <a name="pmc-option-to-install-packages"></a>Možnost PMC se mají balíčky nainstalovat

* Ze sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**
* Z **Konzola správce balíčků** okno, přejděte do adresáře, ve kterém *GrpcGreeterClient.csproj* soubor existuje.
* Spusťte následující příkazy:

 ```powershell
Install-Package Grpc.Net.Client
Install-Package Google.Protobuf
Install-Package Grpc.Tools
```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>Spravovat balíčky NuGet možnost instalace balíčků

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**
* Vyberte **Procházet** kartu.
* Zadejte **Grpc.Core** do vyhledávacího pole.
* Vyberte **Grpc.Core** balíček ze **Procházet** kartě a vyberte **nainstalovat**.
* Opakujte pro `Google.Protobuf` a `Grpc.Tools`.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spusťte následující příkazy z **integrovaný terminál**:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši **balíčky** složky v **oblasti řešení** > **přidat balíčky**
* Zadejte **Grpc.Net.Client** do vyhledávacího pole.
* Vyberte **Grpc.Net.Client** balíček v podokně výsledků a vyberte **přidat balíček**
* Opakujte pro `Google.Protobuf` a `Grpc.Tools`.

---

### <a name="add-greetproto"></a>Přidat greet.proto

* Vytvoření **Protos** složky v klientském projektu gRPC.
* Kopírovat **Protos\greet.proto** souboru z gRPC Greeter služby gRPC klientský projekt.
* Upravit *GrpcGreeterClient.csproj* souboru projektu:

  # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

  Klikněte pravým tlačítkem na projekt a vyberte **upravit GrpcGreeterClient.csproj**.

  # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

  Vyberte *GrpcGreeterClient.csproj* souboru.

  # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  Klikněte pravým tlačítkem na projekt a vyberte **nástroje > Upravit soubor**.

  ---

* Přidat **greet.proto** do souboru `<Protobuf>` skupiny položek GrpcGreeterClient souboru projektu:

  ```XML
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

Sestavení projektu klienta k aktivaci generování C# prostředky klienta.

### <a name="create-the-greeter-client"></a>Vytvoření klienta Greeter

Projekt pro vytvoření typů v sestavení **Greeter** oboru názvů. `Greeter` Typy jsou automaticky generovány procesu sestavení.

Také aktualizovat klienta sady gRPC *Program.cs* souboru následujícím kódem:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.

Klient Greeter vytvořil:

* Vytvoření instance `HttpClient` obsahující informace o vytvoření připojení ke službě gRPC.
* Použití `HttpClient` k sestavení kompletních Greeter klienta:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-9)]

Klient Greeter volá asynchronní `SayHello` metody. Výsledkem `SayHello` volání se zobrazí:

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=10-12)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testovací klient gRPC s gRPC Greeter služby

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ve službě Greeter stiskněte `Ctrl+F5` spuštění serveru bez ladicího programu.
* V `GrpcGreeterClient` projekt, stiskněte klávesu `Ctrl+F5` spuštění serveru bez ladicího programu.

### <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Spusťte službu Greeter.
* Spusťte klienta.

<!-- End of combined VS/Mac tabs -->

---

Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient". Služba odesílá zprávu "Hello GreeterClient" jako odpověď. Na příkazovém řádku se zobrazí odpověď "Hello GreeterClient":

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba gRPC zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

### <a name="next-steps"></a>Další kroky

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
