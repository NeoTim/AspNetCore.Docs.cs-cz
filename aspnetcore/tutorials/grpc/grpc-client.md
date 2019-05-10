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
# <a name="tutorial-create-a-net-core-grpc-client"></a><span data-ttu-id="17c02-104">Kurz: Vytvoření klienta .NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="17c02-104">Tutorial: Create a .NET Core gRPC client</span></span>

<span data-ttu-id="17c02-105">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="17c02-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="17c02-106">Tento kurz ukazuje, jak vytvořit .NET Core [gRPC](https://grpc.io/docs/guides/) klienta, který může komunikovat se službou gRPC.</span><span class="sxs-lookup"><span data-stu-id="17c02-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client that can communicate with gRPC services.</span></span>

<span data-ttu-id="17c02-107">Na konci budete mít gRPC klienta, který komunikuje s gRPC Greeter služby.</span><span class="sxs-lookup"><span data-stu-id="17c02-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/downloadClient.md)]

<span data-ttu-id="17c02-108">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="17c02-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17c02-109">Vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="17c02-109">Create a gRPC client.</span></span>
> * <span data-ttu-id="17c02-110">Spusťte službu pro gRPC Greeter služby vytvořené v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="17c02-110">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="17c02-111">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="17c02-111">Examine the project files.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="create-a-net-console-application"></a><span data-ttu-id="17c02-112">Vytvoření konzolové aplikace .NET</span><span class="sxs-lookup"><span data-stu-id="17c02-112">Create a .NET console application</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17c02-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17c02-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="17c02-114">Postupujte podle pokynů [tady](/dotnet/core/tutorials/with-visual-studio) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="17c02-114">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="17c02-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17c02-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="17c02-116">Postupujte podle pokynů [tady](/dotnet/core/tutorials/with-visual-studio-code) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="17c02-116">Follow the instructions [here](/dotnet/core/tutorials/with-visual-studio-code) to create a console app with the name *GrpcGreeterClient*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="17c02-117">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="17c02-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="17c02-118">Postupujte podle pokynů [tady](/dotnet/core/tutorials/using-on-mac-vs-full-solution) k vytvoření konzolové aplikace s názvem *GrpcGreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="17c02-118">Follow the instructions [here](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

<!-- End of VS tabs -->

---

## <a name="add-required-packages"></a><span data-ttu-id="17c02-119">Přidání požadovaných balíčků</span><span class="sxs-lookup"><span data-stu-id="17c02-119">Add required packages</span></span>

<span data-ttu-id="17c02-120">Přidejte do projektu klienta gRPC následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="17c02-120">Add the following packages to the gRPC client project:</span></span>

* <span data-ttu-id="17c02-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), které se nachází C# rozhraní API pro C core klienta.</span><span class="sxs-lookup"><span data-stu-id="17c02-121">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core), which contains the C# API for the C-core client.</span></span>
* <span data-ttu-id="17c02-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), který obsahuje protobuf zpráva rozhraní API pro C#.</span><span class="sxs-lookup"><span data-stu-id="17c02-122">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="17c02-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), který obsahuje C# Podpora nástrojů pro soubory protobuf.</span><span class="sxs-lookup"><span data-stu-id="17c02-123">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="17c02-124">Balíček nástroje není nutné za běhu, takže závislost je označená pomocí `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="17c02-124">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

<span data-ttu-id="17c02-125">Balíčky lze přidat pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="17c02-125">Packages can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17c02-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17c02-126">Visual Studio</span></span>](#tab/visual-studio)

### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="17c02-127">Možnost PMC se mají balíčky nainstalovat</span><span class="sxs-lookup"><span data-stu-id="17c02-127">PMC option to install packages</span></span>

* <span data-ttu-id="17c02-128">Ze sady Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **Konzola správce balíčků**</span><span class="sxs-lookup"><span data-stu-id="17c02-128">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="17c02-129">Z **Konzola správce balíčků** okno, přejděte do adresáře, ve kterém *GrpcGreeterClient.csproj* soubor existuje.</span><span class="sxs-lookup"><span data-stu-id="17c02-129">From the **Package Manager Console** window, navigate to the directory in which the *GrpcGreeterClient.csproj* file exists.</span></span>
* <span data-ttu-id="17c02-130">Spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="17c02-130">Run the following command:</span></span>

    ```powershell
    Install-Package Grpc.Core
    ```

* <span data-ttu-id="17c02-131">Opakovat `Install-Package` Google.Protobuf a Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="17c02-131">Repeat the `Install-Package` for Google.Protobuf and Grpc.Tools</span></span>

<!-- Tutorials shouldn't have multiple options. Select what you think is the best approach. Recommend you removed this approach. -->

### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="17c02-132">Spravovat balíčky NuGet možnost instalace balíčků</span><span class="sxs-lookup"><span data-stu-id="17c02-132">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="17c02-133">Klikněte pravým tlačítkem na projekt v **Průzkumníka řešení** > **spravovat balíčky NuGet**</span><span class="sxs-lookup"><span data-stu-id="17c02-133">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
* <span data-ttu-id="17c02-134">Nastavte **zdroj balíčku** do "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="17c02-134">Set the **Package source** to "nuget.org"</span></span>
* <span data-ttu-id="17c02-135">Do vyhledávacího pole zadejte "Grpc.Core"</span><span class="sxs-lookup"><span data-stu-id="17c02-135">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="17c02-136">Vyberte balíček "Grpc.Core" z **Procházet** kartě a klikněte na tlačítko **instalace**</span><span class="sxs-lookup"><span data-stu-id="17c02-136">Select the "Grpc.Core" package from the **Browse** tab and click **Install**</span></span>
* <span data-ttu-id="17c02-137">Tento postup opakujte pro Google.Protobuf a Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="17c02-137">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="17c02-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17c02-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="17c02-139">Spuštěním následujícího příkazu z **integrovaný terminál**:</span><span class="sxs-lookup"><span data-stu-id="17c02-139">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add GrpcGreeterClient.csproj package Grpc.Core
```

<span data-ttu-id="17c02-140">Tento postup opakujte pro Google.Protobuf a Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="17c02-140">Repeat for Google.Protobuf and Grpc.Tools</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="17c02-141">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="17c02-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="17c02-142">Klikněte pravým tlačítkem myši *balíčky* složky v **oblasti řešení** > **přidat balíčky...**</span><span class="sxs-lookup"><span data-stu-id="17c02-142">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="17c02-143">Nastavte **přidat balíčky** okna **zdroj** rozevíracího seznamu "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="17c02-143">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="17c02-144">Do vyhledávacího pole zadejte "Grpc.Core"</span><span class="sxs-lookup"><span data-stu-id="17c02-144">Enter "Grpc.Core" in the search box</span></span>
* <span data-ttu-id="17c02-145">Vyberte v podokně výsledků "Grpc.Core" balíček a klikněte na tlačítko **přidat balíček**</span><span class="sxs-lookup"><span data-stu-id="17c02-145">Select the "Grpc.Core" package from the results pane and click **Add Package**</span></span>
* <span data-ttu-id="17c02-146">Tento postup opakujte pro Google.Protobuf a Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="17c02-146">Repeat for Google.Protobuf and Grpc.Tools</span></span>

---

## <a name="add-the-greetproto-file"></a><span data-ttu-id="17c02-147">Přidání souboru greet.proto</span><span class="sxs-lookup"><span data-stu-id="17c02-147">Add the greet.proto file</span></span>

<span data-ttu-id="17c02-148">Kopírovat **Protos\greet.proto** souboru z gRPC Greeter služby gRPC klientský projekt.</span><span class="sxs-lookup"><span data-stu-id="17c02-148">Copy the **Protos\greet.proto** file from the gRPC Greeter service to the gRPC client project.</span></span> <span data-ttu-id="17c02-149">Přidat **greet.proto** do souboru `<Protobuf>` skupiny položek GrpcGreeterClient souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="17c02-149">Add the **greet.proto** file to the `<Protobuf>` item group of the GrpcGreeterClient project file:</span></span>

```XML
<Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
```

> [!NOTE]
> <span data-ttu-id="17c02-150">Pravým tlačítkem myši projekt a výběrem můžete otevřít soubor projektu GrpcGreeterClient **upravit GrpcGreeterClient.csproj** možnost z rozevírací nabídky.</span><span class="sxs-lookup"><span data-stu-id="17c02-150">You can open the project file of GrpcGreeterClient by right-clicking the project and selecting the **Edit GrpcGreeterClient.csproj** option from the dropdown menu.</span></span>
>
> ![Nová webová aplikace ASP.NET Core](grpc-start/_static/edit_csproj.png)
>
> <span data-ttu-id="17c02-152">Případně přejděte do adresáře GrpcGreeterClient a upravit `GrpcGreeterClient.csproj` pomocí oblíbeného editoru.</span><span class="sxs-lookup"><span data-stu-id="17c02-152">Alternatively, you can navigate to the GrpcGreeterClient directory and edit the `GrpcGreeterClient.csproj` with your favorite editor.</span></span>

<span data-ttu-id="17c02-153">`GrpcServices="Client"` Takže jen přidat atribut C# prostředky klienta jsou generovány pro soubor zahrnutý protobuf.</span><span class="sxs-lookup"><span data-stu-id="17c02-153">The `GrpcServices="Client"` attribute is added so that only the C# client assets are generated for the included protobuf file.</span></span> <span data-ttu-id="17c02-154">Sestavení projektu klienta k aktivaci generování C# prostředky klienta.</span><span class="sxs-lookup"><span data-stu-id="17c02-154">Build the client project to trigger the generation of the C# client assets.</span></span>

## <a name="create-a-greeterclient-and-invoke-the-sayhello-unary-call"></a><span data-ttu-id="17c02-155">Vytvoření GreeterClient a vyvolání volání unární SayHello</span><span class="sxs-lookup"><span data-stu-id="17c02-155">Create a GreeterClient and invoke the SayHello unary call</span></span>

<span data-ttu-id="17c02-156">Přidejte následující kód, který `Main` metodu `Program.cs` soubor projektu gRPC klienta:</span><span class="sxs-lookup"><span data-stu-id="17c02-156">Add the following code to `Main` method of the `Program.cs` file of the gRPC client project:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="17c02-157">Pro přístup k požadované typy pomocí následující příkazy jsou povinné:</span><span class="sxs-lookup"><span data-stu-id="17c02-157">To access the required types the following using statements are required:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=using)]

<span data-ttu-id="17c02-158">Po vytvoření instance je vytvořena GreeterClient `Channel` obsahující informace o vytvoření připojení ke službě gRPC a jeho použití k vytvoření `GreeterClient`:</span><span class="sxs-lookup"><span data-stu-id="17c02-158">The GreeterClient is created by instantiating a `Channel` containing the information for creating the connection to the gRPC service and using it to construct the `GreeterClient`:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="17c02-159">GreeterClient obsahuje volání unární `SayHello` které mohou být vyvolány asynchronně:</span><span class="sxs-lookup"><span data-stu-id="17c02-159">The GreeterClient contains the unary call `SayHello` which can be invoked asynchronously:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=7-8)]

<span data-ttu-id="17c02-160">Výsledky `SayHello` volání je uložen v `reply` které lze následně zobrazit:</span><span class="sxs-lookup"><span data-stu-id="17c02-160">The results of the `SayHello` call is stored in `reply` which can then be displayed:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=9)]

<span data-ttu-id="17c02-161">`Channel` Používá klient by měl být vypnut při dokončení operace uvolnit všechny prostředky:</span><span class="sxs-lookup"><span data-stu-id="17c02-161">The `Channel` used by the client should be shut down when operations have finished to release all resources:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?name=snippet&highlight=11)]

> [!NOTE]
> <span data-ttu-id="17c02-162">Budete muset sestavit projekt před typy v **Greeter** oboru názvů lze vyřešit.</span><span class="sxs-lookup"><span data-stu-id="17c02-162">You will need to build the project before the types in the **Greeter** namespace can be resolved.</span></span> <span data-ttu-id="17c02-163">Tyto typy jsou automaticky generovány během sestavení a jsou nebudou k dispozici před spuštěním sestavení.</span><span class="sxs-lookup"><span data-stu-id="17c02-163">These types are generated automatically during build and are not be available before a build is run.</span></span>

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="17c02-164">Testovací klient gRPC s gRPC Greeter služby</span><span class="sxs-lookup"><span data-stu-id="17c02-164">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17c02-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17c02-165">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="17c02-166">Zajištění Greeter je spuštěna služba vytvořili v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="17c02-166">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="17c02-167">Jakmile je služba spuštěna, vraťte se do **GrpcGreeterClient** projektu nastavte jako spouštěný projekt.</span><span class="sxs-lookup"><span data-stu-id="17c02-167">Once the service is running, return to the **GrpcGreeterClient** project set it as the Startup Project.</span></span> <span data-ttu-id="17c02-168">Stisknutím kláves Ctrl + F5 a spusťte tak klienta bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="17c02-168">Press Ctrl+F5 to run the client without the debugger.</span></span>

  <span data-ttu-id="17c02-169">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="17c02-169">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="17c02-170">Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17c02-170">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/client.png)

  <span data-ttu-id="17c02-172">Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17c02-172">The service records the details of the successful call in the logs written to the command prompt.</span></span>

  ![Nová webová aplikace ASP.NET Core](grpc-start/_static/server_complete.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="17c02-174">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="17c02-174">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="17c02-175">Zajištění Greeter je spuštěna služba vytvořili v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="17c02-175">Ensure the Greeter service created in the previous tutorial is running.</span></span>

* <span data-ttu-id="17c02-176">Pomocí samostatných příkazového řádku, spusťte klientský projekt GrpcGreeter.Client `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="17c02-176">Run the Client project GrpcGreeter.Client from the separate command line using `dotnet run`.</span></span>

<span data-ttu-id="17c02-177">Klient odešle pozdrav ke službě a zobrazí se zpráva obsahující jeho jméno "GreeterClient".</span><span class="sxs-lookup"><span data-stu-id="17c02-177">The client sends a greeting to the service with a message containing its name "GreeterClient".</span></span> <span data-ttu-id="17c02-178">Služba se poslat zprávu "Hello GreeterClient" jako odpověď, která se zobrazí v okně příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17c02-178">The service will send a message "Hello GreeterClient" as a response that is displayed in the command prompt.</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="17c02-179">Služba zaznamenával podrobnosti o úspěšném volání v protokolech zapsána do příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="17c02-179">The service records the details of the successful call in the logs written to the command prompt.</span></span>

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

### <a name="examine-the-project-files-of-the-grpc-project"></a><span data-ttu-id="17c02-180">Zkontrolujte soubory projektu gRPC projektu</span><span class="sxs-lookup"><span data-stu-id="17c02-180">Examine the project files of the gRPC project</span></span>

<span data-ttu-id="17c02-181">soubor GrpcGreeterClient gRPC klienta:</span><span class="sxs-lookup"><span data-stu-id="17c02-181">gRPC client GrpcGreeterClient file:</span></span>

<span data-ttu-id="17c02-182">*Soubor program.cs* obsahuje vstupní bod a logiku pro gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="17c02-182">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17c02-183">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="17c02-183">Additional resources</span></span>

<span data-ttu-id="17c02-184">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="17c02-184">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="17c02-185">Vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="17c02-185">Create a gRPC client.</span></span>
> * <span data-ttu-id="17c02-186">Spusťte službu pro gRPC Greeter služby vytvořené v předchozím kurzu.</span><span class="sxs-lookup"><span data-stu-id="17c02-186">Run the service against the gRPC Greeter service created in the previous tutorial.</span></span>
> * <span data-ttu-id="17c02-187">Zkontrolujte soubory projektu.</span><span class="sxs-lookup"><span data-stu-id="17c02-187">Examine the project files.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="17c02-188">Předchozí: Vytvoření gRPC Greeter služby</span><span class="sxs-lookup"><span data-stu-id="17c02-188">Previous: Create a gRPC Greeter Service</span></span>](xref:tutorials/grpc/grpc-start)
