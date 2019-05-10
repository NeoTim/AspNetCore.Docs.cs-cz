---
title: 'Kurz: Vytvoření klienta .NET Core gRPC'
author: juntaoluo
description: Tato série kurzů ukazuje, jak vytvořit gRPC služba v ASP.NET Core. Zjistěte, jak vytvořit projekt gRPC služby, upravte soubor proto a přidat duplexní streamování volání.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 4/10/2019
uid: tutorials/grpc/grpc-client
ms.openlocfilehash: ec6bf5072c76de640a78b2c3f13dd1fc552b9d04
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212647"
---
# <a name="tutorial-create-a-net-core-grpc-client"></a>Kurz: Vytvoření klienta .NET Core gRPC

Podle [Luo Jan](https://github.com/juntaoluo)

Tento kurz ukazuje, jak vytvořit .NET Core [gRPC](https://grpc.io/docs/guides/) klienta, který může komunikovat se službou gRPC.

Na konci budete mít gRPC klienta, který komunikuje s gRPC Greeter služby.

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření klienta gRPC.
> * Spusťte službu pro gRPC Greeter služby vytvořené v předchozím kurzu.
> * Zkontrolujte soubory projektu.

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a>Vytvoření konzolové aplikace .NET

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Postupujte podle pokynů [tady](/dotnet/core/tutorials/with-visual-studio) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Postupujte podle pokynů [tady](/dotnet/core/tutorials/with-visual-studio-code) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

Postupujte podle pokynů [tady](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a>Přidání požadovaných balíčků

Přidejte do projektu klienta gRPC následující balíčky:

* [Grpc.Core](https://www.nuget.org/packages/Grpc.Core), které se nachází C# rozhraní API pro C core klienta.
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobuf zpráva rozhraní API pro C#.
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje C# Podpora nástrojů pro soubory protobuf. Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.

Balíčky lze přidat pomocí následujících postupů:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a>Možnost PMC se mají balíčky nainstalovat

* Ze sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**
* Z **Konzola správce balíčků** okno, přejděte do adresáře, ve kterém *GrpcGreeterClient.csproj* soubor existuje.
* Spusťte následující příkaz:

    ```powershell
    Install-Package Grpc.Core
    ```

* Opakovat `Install-Package` Google.Protobuf a Grpc.Tools

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a>Spravovat balíčky NuGet možnost instalace balíčků

* Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**
* Nastavte **zdroj balíčku** do "nuget.org"
* Do vyhledávacího pole zadejte "Grpc.Core"
* Vyberte balíček "Grpc.Core" z **Procházet** kartě a klikněte na tlačítko **instalace**
* Tento postup opakujte pro Google.Protobuf a Grpc.Tools

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Spuštěním následujícího příkazu z **integrovaný terminál**:

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

Tento postup opakujte pro Google.Protobuf a Grpc.Tools

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**
* Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"
* Do vyhledávacího pole zadejte "Grpc.Core"
* Vyberte v podokně výsledků "Grpc.Core" balíček a klikněte na tlačítko **přidat balíček**
* Tento postup opakujte pro Google.Protobuf a Grpc.Tools

---

## <a name="add-the-greetproto-file"></a>Přidání souboru greet.proto

Kopírovat **Protos\greet.proto** souboru z gRPC Greeter služby gRPC klientský projekt. Přidat **greet.proto** do souboru `<Protobuf>` skupiny položek GrpcGreeterClient souboru projektu:

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> Pravým tlačítkem myši projekt a výběrem můžete otevřít soubor projektu GrpcGreeterClient **upravit GrpcGreeterClient.csproj** možnost z rozevírací nabídky.
>
> ![Nová webová aplikace ASP.NET Core](grpc-start/_static/edit_csproj.png)
>
> Případně přejděte do adresáře GrpcGreeterClient a upravit `GrpcGreeterClient.csproj` pomocí oblíbeného editoru.

`GrpcServices="Client"` Takže jen přidat atribut C# prostředky klienta jsou generovány pro soubor zahrnutý protobuf. Sestavení projektu klienta k aktivaci generování C# prostředky klienta.

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a>Vytvoření GreeterClient a vyvolání volání unární SayHello

Přidejte následující kód, který `Main` metodu `Program.cs` soubor projektu gRPC klienta:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

Pro přístup k požadované typy pomocí následující příkazy jsou povinné:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

Po vytvoření instance je vytvořena GreeterClient `Channel` obsahující informace o vytvoření připojení ke službě gRPC a jeho použití k vytvoření `GreeterClient`:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

GreeterClient obsahuje volání unární `SayHello` které mohou být vyvolány asynchronně:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

Výsledky `SayHello` volání je uložen v `reply` které lze následně zobrazit:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

`Channel` Používá klient by měl být vypnut při dokončení operace uvolnit všechny prostředky:

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> Budete muset sestavit projekt před typy v **Greeter** oboru názvů lze vyřešit. Tyto typy jsou automaticky generovány během sestavení a jsou nebudou k dispozici před spuštěním sestavení.

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>Testovací klient gRPC s gRPC Greeter služby

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Zajištění Greeter je spuštěna služba vytvořili v předchozím kurzu.

* Jakmile je služba spuštěna, vraťte se do **GrpcGreeterClient** projektu nastavte jako spouštěný projekt. Stisknutím kláves Ctrl + F5 a spusťte tak klienta bez ladicího programu.

  Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient". Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/client.png)

  Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* Zajištění Greeter je spuštěna služba vytvořili v předchozím kurzu.

* Pomocí samostatných příkazového řádku, spusťte klientský projekt GrpcGreeter.Client `dotnet run`.

Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient". Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.

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
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[1]
      Request starting HTTP/2 POST http://localhost:50051/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Internal.GenericWebHostService[2]
      Request finished in 194.5798ms 200 application/grpc
```

<!-- End of combined VS/Mac tabs -->

---

### <a name="examine-the-project-files-of-the-grpc-project"></a>Zkontrolujte soubory projektu gRPC projektu

soubor GrpcGreeterClient gRPC klienta:

*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.

## <a name="additional-resources"></a>Další zdroje

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření klienta gRPC.
> * Spusťte službu pro gRPC Greeter služby vytvořené v předchozím kurzu.
> * Zkontrolujte soubory projektu.

> [!div class="step-by-step"]
> [Předchozí: Vytvoření gRPC Greeter služby](xref:tutorials/grpc/grpc-start)
