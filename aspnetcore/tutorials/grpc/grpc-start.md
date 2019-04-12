---
title: 'Kurz: Začínáme s gRPC v ASP.NET Core'
author: juntaoluo
description: Tato série kurzů ukazuje, jak vytvořit gRPC služba v ASP.NET Core. Zjistěte, jak vytvořit projekt služby gRPC, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 84c98ed341adc48d4ecbeda4305100c492ffb5e1
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516232"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>Kurz: Začínáme se službou gRPC v ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

V tomto kurzu se naučíte se základy vytváření gRPC služba v ASP.NET Core.

Na konci budete mít gRPC služba, která vypisuje greetings.

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby gRPC.
> * Spuštění služby.
> * Zkontrolujte soubory projektu.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-grpc-service"></a>Vytvoření služby gRPC

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Ze sady Visual Studio **souboru** nabídce vyberte možnost **nový** > **projektu**.
* Vytvořte novou webovou aplikaci ASP.NET Core.
  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.1.png)
* Pojmenujte projekt **GrpcGreeter**. Je důležité projekt pojmenujte *GrpcGreeter* tak obory názvů budou porovnávány názvy při zkopírujte a vložte kód.
  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/np_3_0.2.png)
* Vyberte **.NET Core** a **ASP.NET Core 3.0** v rozevírací nabídce. Zvolte **gRPC služby** šablony.

  Následující počáteční projekt je vytvořen:

  ![Průzkumník řešení](grpc-start/_static/se3.0.png)

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

### <a name="test-the-service"></a>Testování služby

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Zkontrolujte, **GrpcGreeter.Server** je nastavit jako spouštěný projekt a stisknutím kláves Ctrl + F5 ke spuštění služby gRPC bez ladicího programu.

  Visual Studio spustí službu v příkazovém řádku. V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_start.png)

* Jakmile je služba spuštěná, nastavte **GrpcGreeter.Client** jako projekt po spuštění a stisknete klávesu Ctrl + F5 a spusťte tak klienta bez ladicího programu.

  Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient". Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/client.png)

  Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Pomocí příkazového řádku, spusťte projekt serveru GrpcGreeter.Server `dotnet run`. V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\example\GrpcGreeter\GrpcGreeter.Server
```

* Pomocí samostatných příkazového řádku, spusťte klientský projekt GrpcGreeter.Client `dotnet run`.

Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient". Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\tp\GrpcGreeter\GrpcGreeter.Server
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 107.46730000000001ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Zkontrolujte soubory projektu gRPC projektu

GrpcGreeter.Server soubory:

* greet.proto: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky. Další informace naleznete v tématu <xref:grpc/index>.
* *Služby* složky: Obsahuje implementaci `Greeter` služby.
* *appSettings.json*: obsahuje konfigurační data, třeba protokol používá Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: Vstupní bod pro službu gRPC obsahuje. Další informace naleznete v tématu <xref:fundamentals/host/web-host>.
* Startup.cs

Obsahuje kód, který nakonfiguruje chování aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

soubor GrpcGreeter.Client gRPC klienta:

*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * GRPC službu vytvořili.
> * Spustil službu a klienta k otestování služby.
> * Prozkoumat soubory projektu.
