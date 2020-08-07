---
title: Vytváření back-end služeb pro nativní mobilní aplikace s ASP.NET Core
author: ardalis
description: Naučte se vytvářet služby back-endu pomocí ASP.NET Core MVC k podpoře nativních mobilních aplikací.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mobile/native-mobile-backend
ms.openlocfilehash: b719c64e6976e65a725b28ae3f6c245f2f4874a4
ms.sourcegitcommit: b0fa7ff0cb158277df61bcd08058a81222c3fe10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87913597"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="f79b4-103">Vytváření back-end služeb pro nativní mobilní aplikace s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f79b4-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="f79b4-104">[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f79b4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="f79b4-105">Mobilní aplikace můžou komunikovat s ASP.NET Core službami back-endu.</span><span class="sxs-lookup"><span data-stu-id="f79b4-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="f79b4-106">Pokyny k připojení místních webových služeb od simulátorů iOS a emulátorů Androidu najdete v tématu [připojení k místním webovým službám z simulátorů iOS a emulátorů Androidu](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="f79b4-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="f79b4-107">Zobrazit nebo stáhnout ukázkový kód back-end služeb</span><span class="sxs-lookup"><span data-stu-id="f79b4-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="f79b4-108">Ukázková nativní mobilní aplikace</span><span class="sxs-lookup"><span data-stu-id="f79b4-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="f79b4-109">V tomto kurzu se dozvíte, jak vytvořit služby back-endu pomocí ASP.NET Core MVC k podpoře nativních mobilních aplikací.</span><span class="sxs-lookup"><span data-stu-id="f79b4-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="f79b4-110">Používá [aplikaci Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako nativního klienta, který zahrnuje samostatné nativní klienty pro zařízení s Androidem, iOS, Windows Universal a Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="f79b4-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="f79b4-111">Můžete postupovat podle propojeného kurzu a vytvořit nativní aplikaci (a nainstalovat potřebné bezplatné nástroje Xamarin) a stáhnout ukázkové řešení Xamarin.</span><span class="sxs-lookup"><span data-stu-id="f79b4-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="f79b4-112">Ukázka Xamarin zahrnuje projekt služeb ASP.NET Web API 2, který nahrazuje aplikace ASP.NET Core webového rozhraní API 2 (bez nutnosti změny klienta).</span><span class="sxs-lookup"><span data-stu-id="f79b4-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Spuštění aplikace REST na telefonu s Androidem](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="f79b4-114">Funkce</span><span class="sxs-lookup"><span data-stu-id="f79b4-114">Features</span></span>

<span data-ttu-id="f79b4-115">Aplikace ToDoRest podporuje výpis, přidávání, odstraňování a aktualizaci položek úkolů.</span><span class="sxs-lookup"><span data-stu-id="f79b4-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="f79b4-116">Každá položka má ID, název, poznámky a vlastnost, která označuje, zda byla provedena ještě předtím.</span><span class="sxs-lookup"><span data-stu-id="f79b4-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="f79b4-117">Hlavní zobrazení položek, jak je uvedeno výše, uvádí název každé položky a označuje, zda se značka zaškrtne.</span><span class="sxs-lookup"><span data-stu-id="f79b4-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="f79b4-118">Klepnutím na `+` ikonu se otevře dialogové okno Přidat položku:</span><span class="sxs-lookup"><span data-stu-id="f79b4-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Dialogové okno Přidat položku](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="f79b4-120">Klepnutím na položku na obrazovce hlavní seznam se otevře dialogové okno pro úpravy, ve kterém můžete upravit název položky, poznámky a hotové nastavení, nebo můžete položku odstranit:</span><span class="sxs-lookup"><span data-stu-id="f79b4-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Dialogové okno Upravit položku](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="f79b4-122">Tato ukázka je ve výchozím nastavení nakonfigurovaná tak, aby používala back-end služby hostované na developer.xamarin.com, což povoluje operace jen pro čtení.</span><span class="sxs-lookup"><span data-stu-id="f79b4-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="f79b4-123">Pokud si ji chcete vyzkoušet sami s aplikací ASP.NET Core vytvořenou v další části běžící na vašem počítači, budete muset aktualizovat `RestUrl` konstantu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f79b4-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="f79b4-124">Přejděte do `ToDoREST` projektu a otevřete soubor *Constants.cs* .</span><span class="sxs-lookup"><span data-stu-id="f79b4-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="f79b4-125">Nahraďte `RestUrl` adresu URL, která obsahuje IP adresu vašeho počítače (ne localhost nebo 127.0.0.1), protože tato adresa se používá z emulátoru zařízení, nikoli z počítače).</span><span class="sxs-lookup"><span data-stu-id="f79b4-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="f79b4-126">Zahrňte také číslo portu (5000).</span><span class="sxs-lookup"><span data-stu-id="f79b4-126">Include the port number as well (5000).</span></span> <span data-ttu-id="f79b4-127">Aby bylo možné testovat, zda vaše služby pracují se zařízením, ujistěte se, že nemáte aktivní bránu firewall blokující přístup k tomuto portu.</span><span class="sxs-lookup"><span data-stu-id="f79b4-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="f79b4-128">Vytvoření projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f79b4-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="f79b4-129">Vytvořte novou ASP.NET Core webovou aplikaci v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f79b4-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="f79b4-130">Vyberte šablonu webové rozhraní API a bez ověřování.</span><span class="sxs-lookup"><span data-stu-id="f79b4-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="f79b4-131">Pojmenujte projekt *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="f79b4-131">Name the project *ToDoApi*.</span></span>

![Dialogové okno Nová webová aplikace v ASP.NET se zvolenou šablonou projektu webového rozhraní API](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="f79b4-133">Aplikace by měla reagovat na všechny požadavky vytvořené na port 5000.</span><span class="sxs-lookup"><span data-stu-id="f79b4-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="f79b4-134">Aktualizujte *program.cs* , aby se `.UseUrls("http://*:5000")` dosáhlo tohoto:</span><span class="sxs-lookup"><span data-stu-id="f79b4-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="f79b4-135">Ujistěte se, že aplikaci spouštíte přímo, a ne za IIS Express, která ve výchozím nastavení ignoruje jiné požadavky než místní.</span><span class="sxs-lookup"><span data-stu-id="f79b4-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="f79b4-136">Spusťte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) z příkazového řádku nebo vyberte profil název aplikace z rozevíracího seznamu cíl ladění na panelu nástrojů sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="f79b4-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="f79b4-137">Přidejte třídu modelu, která bude představovat položky úkolů.</span><span class="sxs-lookup"><span data-stu-id="f79b4-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="f79b4-138">Označte povinná pole s `[Required]` atributem:</span><span class="sxs-lookup"><span data-stu-id="f79b4-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="f79b4-139">Metody rozhraní API vyžadují nějaký způsob, jak pracovat s daty.</span><span class="sxs-lookup"><span data-stu-id="f79b4-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="f79b4-140">Použijte stejné `IToDoRepository` rozhraní, jaké používá původní ukázka Xamarin:</span><span class="sxs-lookup"><span data-stu-id="f79b4-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="f79b4-141">Pro tuto ukázku implementace používá pouze soukromou kolekci položek:</span><span class="sxs-lookup"><span data-stu-id="f79b4-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="f79b4-142">Konfigurace implementace v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f79b4-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="f79b4-143">V tuto chvíli jste připraveni vytvořit *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="f79b4-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="f79b4-144">Přečtěte si další informace o vytváření webových rozhraní API v [sestavení prvního webového rozhraní API pomocí ASP.NET Core MVC a sady Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="f79b4-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="f79b4-145">Vytváření kontroleru</span><span class="sxs-lookup"><span data-stu-id="f79b4-145">Creating the Controller</span></span>

<span data-ttu-id="f79b4-146">Přidejte do projektu nový kontroler, *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="f79b4-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="f79b4-147">Měl by dědit z Microsoft. AspNetCore. Mvc. Controller.</span><span class="sxs-lookup"><span data-stu-id="f79b4-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="f79b4-148">Přidejte `Route` atribut, který označuje, že kontroler zpracuje požadavky provedené na cesty začínající na `api/todoitems` .</span><span class="sxs-lookup"><span data-stu-id="f79b4-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="f79b4-149">`[controller]`Token v trase je nahrazen názvem kontroleru (vynechání `Controller` přípony) a je zvláště užitečný pro globální trasy.</span><span class="sxs-lookup"><span data-stu-id="f79b4-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="f79b4-150">Přečtěte si další informace o [Směrování](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="f79b4-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="f79b4-151">Kontroler vyžaduje `IToDoRepository` funkci. požádejte o instanci tohoto typu prostřednictvím konstruktoru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="f79b4-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="f79b4-152">Za běhu se tato instance poskytne s použitím podpory rozhraní pro [vkládání závislostí](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="f79b4-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="f79b4-153">Toto rozhraní API podporuje čtyři různé příkazy HTTP pro provádění operací CRUD (vytvoření, čtení, aktualizace, odstranění) na zdroji dat.</span><span class="sxs-lookup"><span data-stu-id="f79b4-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="f79b4-154">Nejjednodušší z nich je operace čtení, která odpovídá požadavku HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="f79b4-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="f79b4-155">Čtení položek</span><span class="sxs-lookup"><span data-stu-id="f79b4-155">Reading Items</span></span>

<span data-ttu-id="f79b4-156">Požadavek na seznam položek se provádí s požadavkem GET na `List` metodu.</span><span class="sxs-lookup"><span data-stu-id="f79b4-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="f79b4-157">`[HttpGet]`Atribut `List` metody označuje, že by tato akce měla zpracovat pouze požadavky GET.</span><span class="sxs-lookup"><span data-stu-id="f79b4-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="f79b4-158">Trasa pro tuto akci je trasa určená na řadiči.</span><span class="sxs-lookup"><span data-stu-id="f79b4-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="f79b4-159">Nemusíte nutně použít název akce jako součást trasy.</span><span class="sxs-lookup"><span data-stu-id="f79b4-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="f79b4-160">Stačí, abyste zajistili, že každá akce má jedinečnou a jednoznačnou trasu.</span><span class="sxs-lookup"><span data-stu-id="f79b4-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="f79b4-161">Atributy směrování lze použít jak na úrovni kontroléru, tak na úrovni metody pro sestavování specifických tras.</span><span class="sxs-lookup"><span data-stu-id="f79b4-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="f79b4-162">`List`Metoda vrátí kód odpovědi 200 OK a všechny položky ToDo serializované jako JSON.</span><span class="sxs-lookup"><span data-stu-id="f79b4-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="f79b4-163">Novou metodu rozhraní API můžete otestovat pomocí nejrůznějších nástrojů, jako je například [post](https://www.getpostman.com/docs/), jak je znázorněno zde:</span><span class="sxs-lookup"><span data-stu-id="f79b4-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Dodatečná konzola ukazující požadavek GET pro TodoItems a tělo odpovědi zobrazující JSON pro tři vrácené položky](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="f79b4-165">Vytváření položek</span><span class="sxs-lookup"><span data-stu-id="f79b4-165">Creating Items</span></span>

<span data-ttu-id="f79b4-166">Podle konvence je vytváření nových datových položek mapováno na operaci HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="f79b4-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="f79b4-167">V `Create` metodě je `[HttpPost]` použit atribut a je přijímána `ToDoItem` instance.</span><span class="sxs-lookup"><span data-stu-id="f79b4-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="f79b4-168">Vzhledem k `item` tomu, že argument je předán v těle příspěvku, tento parametr určuje `[FromBody]` atribut.</span><span class="sxs-lookup"><span data-stu-id="f79b4-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="f79b4-169">V rámci metody je položka kontrolována platnosti a předchozí existence v úložišti dat, a pokud nedojde k žádnému problému, bude přidána pomocí úložiště.</span><span class="sxs-lookup"><span data-stu-id="f79b4-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="f79b4-170">Kontrola `ModelState.IsValid` provede [ověření modelu](../mvc/models/validation.md)a měla by se provést v každé metodě rozhraní API, která přijímá vstup uživatele.</span><span class="sxs-lookup"><span data-stu-id="f79b4-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="f79b4-171">Ukázka používá výčet obsahující chybové kódy, které jsou předány mobilnímu klientovi:</span><span class="sxs-lookup"><span data-stu-id="f79b4-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="f79b4-172">Otestujte přidáním nových položek pomocí příkazu post tak, že vyberete příkaz POST, který poskytuje nový objekt ve formátu JSON v těle požadavku.</span><span class="sxs-lookup"><span data-stu-id="f79b4-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="f79b4-173">Měli byste také přidat hlavičku požadavku určující a `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="f79b4-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Dodatečná konzola ukazující příspěvek a odpověď](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="f79b4-175">Metoda vrátí nově vytvořenou položku v odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f79b4-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="f79b4-176">Aktualizace položek</span><span class="sxs-lookup"><span data-stu-id="f79b4-176">Updating Items</span></span>

<span data-ttu-id="f79b4-177">Změny záznamů se provádí pomocí požadavků HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="f79b4-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="f79b4-178">Kromě této změny `Edit` je metoda skoro shodná s `Create` .</span><span class="sxs-lookup"><span data-stu-id="f79b4-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="f79b4-179">Všimněte si, že pokud se záznam nenajde, `Edit` akce vrátí `NotFound` odpověď (404).</span><span class="sxs-lookup"><span data-stu-id="f79b4-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="f79b4-180">Chcete-li testovat pomocí metody post, změňte operaci na PUT.</span><span class="sxs-lookup"><span data-stu-id="f79b4-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="f79b4-181">Zadejte data aktualizovaných objektů v těle žádosti.</span><span class="sxs-lookup"><span data-stu-id="f79b4-181">Specify the updated object data in the Body of the request.</span></span>

![Dodatečná konzola ukazující vložení a odpověď](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="f79b4-183">Tato metoda vrátí `NoContent` odpověď (204), pokud je úspěšná, kvůli konzistenci s dříve existujícím rozhraním API.</span><span class="sxs-lookup"><span data-stu-id="f79b4-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="f79b4-184">Odstraňování položek</span><span class="sxs-lookup"><span data-stu-id="f79b4-184">Deleting Items</span></span>

<span data-ttu-id="f79b4-185">Odstranění záznamů je provedeno tím, že se do služby vyžádají žádosti o odstranění a předáte ID položky, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="f79b4-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="f79b4-186">Stejně jako u aktualizací jsou požadavky na položky, které neexistují, přijímat `NotFound` odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f79b4-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="f79b4-187">V opačném případě bude úspěšná žádost dostávat `NoContent` odpověď (204).</span><span class="sxs-lookup"><span data-stu-id="f79b4-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="f79b4-188">Všimněte si, že při testování funkce odstranění není v těle žádosti nic nutné.</span><span class="sxs-lookup"><span data-stu-id="f79b4-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Konzola pro publikování ukazující odstranění a odpověď](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="f79b4-190">Společné konvence webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="f79b4-190">Common Web API Conventions</span></span>

<span data-ttu-id="f79b4-191">Při vývoji back-end služeb pro vaši aplikaci budete chtít vytvořit s konzistentním nastavením konvencí nebo zásad pro zpracování otázek mezi jednotlivými průřezy.</span><span class="sxs-lookup"><span data-stu-id="f79b4-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="f79b4-192">Například ve výše uvedené službě požadavky na konkrétní záznamy, které nebyly nalezeny, neobdržely `NotFound` odpověď, nikoli `BadRequest` odpověď.</span><span class="sxs-lookup"><span data-stu-id="f79b4-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="f79b4-193">Podobně příkazy provedené pro tuto službu, které byly předány v typu vázané na model, jsou vždy zkontrolovány `ModelState.IsValid` a vráceny `BadRequest` pro neplatné typy modelů.</span><span class="sxs-lookup"><span data-stu-id="f79b4-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="f79b4-194">Jakmile zjistíte společné zásady pro vaše rozhraní API, můžete je obvykle zapouzdřit ve [filtru](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="f79b4-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="f79b4-195">Přečtěte si další informace o [tom, jak zapouzdřit společné zásady rozhraní API v aplikacích ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="f79b4-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f79b4-196">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="f79b4-196">Additional resources</span></span>

* [<span data-ttu-id="f79b4-197">Ověřování a autorizace</span><span class="sxs-lookup"><span data-stu-id="f79b4-197">Authentication and Authorization</span></span>](/xamarin/xamarin-forms/enterprise-application-patterns/authentication-and-authorization)
