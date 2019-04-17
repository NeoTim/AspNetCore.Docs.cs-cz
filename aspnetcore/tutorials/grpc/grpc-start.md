---
title: 'Kurz: Začínáme s gRPC v ASP.NET Core'
author: juntaoluo
description: Tato série kurzů ukazuje, jak vytvořit gRPC služba v ASP.NET Core. Zjistěte, jak vytvořit projekt služby gRPC, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 2/26/2019
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: a67050331cc8563b1baf5312b92910c1bbdfbd69
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672564"
---
# <a name="tutorial-get-started-with-grpc-service-in-aspnet-core"></a>Kurz: Začínáme se službou gRPC v ASP.NET Core

Podle [Luo Jan](https://github.com/juntaoluo)

V tomto kurzu se naučíte se základy vytváření gRPC služba v ASP.NET Core.

Na konci budete mít gRPC služba, která vypisuje greetings.

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření služby gRPC.
> * GRPC službu spusťte.
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

### <a name="run-the-service"></a>Spuštění služby

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kláves Ctrl + F5 ke spuštění služby gRPC bez ladicího programu.

  Visual Studio spustí službu v příkazovém řádku. V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_start.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Pomocí příkazového řádku, spusťte projekt Greeter gRPC GrpcGreeter `dotnet run`. V protokolech zobrazuje, že služba spustilo se naslouchání v `http://localhost:50051`.

```console
dbug: Grpc.AspNetCore.Server.Internal.GrpcServiceBinder[1]
      Added gRPC method 'SayHello' to service 'Greet.Greeter'. Method type: 'Unary', route pattern: '/Greet.Greeter/SayHello'.
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://localhost:50051
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\gh\Docs\aspnetcore\tutorials\grpc\grpc-start\samples\GrpcGreeter
```

<!-- End of combined VS/Mac tabs -->

---

Další kurz vám ukáže jak sestavit gRPC klienta, který je nutný k otestování služby Greeter.

### <a name="examine-the-project-files-of-the-grpc-project"></a>Zkontrolujte soubory projektu gRPC projektu

GrpcGreeter soubory:

* greet.proto: *Protos/greet.proto* soubor definuje `Greeter` gRPC a slouží ke generování gRPC serverovými prostředky. Další informace naleznete v tématu <xref:grpc/index>.
* *Služby* složky: Obsahuje implementaci `Greeter` služby.
* *appSettings.json*: obsahuje konfigurační data, třeba protokol používá Kestrel. Další informace naleznete v tématu <xref:fundamentals/configuration/index>.
* *Program.cs*: Vstupní bod pro službu gRPC obsahuje. Další informace naleznete v tématu <xref:fundamentals/host/web-host>.
* Startup.cs: Obsahuje kód, který nakonfiguruje chování aplikace. Další informace naleznete v tématu <xref:fundamentals/startup>.

### <a name="test-the-service"></a>Testování služby

## <a name="additional-resources"></a>Další zdroje

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * GRPC službu vytvořili.
> * Spuštění služby gRPC.
> * Prozkoumat soubory projektu.

> [!div class="step-by-step"]
> [Další: Vytvoření klienta gRPC .NET Core](xref:tutorials/grpc/grpc-client)