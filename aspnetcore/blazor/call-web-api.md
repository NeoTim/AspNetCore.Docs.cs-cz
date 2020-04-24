---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: abc546cc0079a01e3999b2c7c083235d3fff9b06
ms.sourcegitcommit: e94ecfae6a3ef568fa197da791c8bc595917d17a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82122222"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="84d4e-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="84d4e-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="84d4e-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="84d4e-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="84d4e-105">[Blazor aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby.</span><span class="sxs-lookup"><span data-stu-id="84d4e-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="84d4e-106">Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití pomocníků Blazor <xref:System.Net.Http.HttpRequestMessage>JSON nebo s.</span><span class="sxs-lookup"><span data-stu-id="84d4e-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="84d4e-107">`HttpClient` Služba v Blazorch aplikacích WebAssembly se zaměřuje na poskytování požadavků zpátky na původní server.</span><span class="sxs-lookup"><span data-stu-id="84d4e-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="84d4e-108">Pokyny v tomto tématu se vztahují pouze k Blazor aplikacím WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="84d4e-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="84d4e-109">[Serverové aplikace Blazor](xref:blazor/hosting-models#blazor-server) volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí, které se <xref:System.Net.Http.IHttpClientFactory>obvykle vytvářejí pomocí.</span><span class="sxs-lookup"><span data-stu-id="84d4e-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="84d4e-110">Pokyny v tomto tématu se nevztahují na Blazor serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="84d4e-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="84d4e-111">Při vývoji aplikací serveru Blazor postupujte podle pokynů v <xref:fundamentals/http-requests>části.</span><span class="sxs-lookup"><span data-stu-id="84d4e-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="84d4e-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="84d4e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="84d4e-113">Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="84d4e-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="84d4e-114">Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="84d4e-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="84d4e-115">Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="84d4e-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="84d4e-116">Balíčky</span><span class="sxs-lookup"><span data-stu-id="84d4e-116">Packages</span></span>

<span data-ttu-id="84d4e-117">V souboru projektu se odkázat na balíček NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) .</span><span class="sxs-lookup"><span data-stu-id="84d4e-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="84d4e-118">Přidání služby HttpClient</span><span class="sxs-lookup"><span data-stu-id="84d4e-118">Add the HttpClient service</span></span>

<span data-ttu-id="84d4e-119">V `Program.Main`nástroji přidejte `HttpClient` službu, pokud ještě neexistuje:</span><span class="sxs-lookup"><span data-stu-id="84d4e-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="84d4e-120">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="84d4e-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="84d4e-121">V aplikaci Blazor WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="84d4e-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="84d4e-122">Aplikace serveru Blazor ve výchozím nastavení neobsahuje `HttpClient` službu.</span><span class="sxs-lookup"><span data-stu-id="84d4e-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="84d4e-123">`HttpClient` Poskytněte aplikaci s využitím [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="84d4e-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="84d4e-124">`HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="84d4e-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="84d4e-125">`HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="84d4e-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="84d4e-126">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="84d4e-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="84d4e-127">Vložení `HttpClient` instance pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="84d4e-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="84d4e-128">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="84d4e-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="84d4e-129">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="84d4e-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="84d4e-130">ID (`Id`, `long`) &ndash; jedinečné ID položky</span><span class="sxs-lookup"><span data-stu-id="84d4e-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="84d4e-131">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="84d4e-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="84d4e-132">Stav (`IsComplete`, `bool`) &ndash; označuje, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="84d4e-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="84d4e-133">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="84d4e-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="84d4e-134">`GetFromJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="84d4e-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="84d4e-135">V následujícím kódu `_todoItems` se zobrazí součást.</span><span class="sxs-lookup"><span data-stu-id="84d4e-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="84d4e-136">`GetTodoItems` Metoda je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="84d4e-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="84d4e-137">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84d4e-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="84d4e-138">`PostAsJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="84d4e-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="84d4e-139">V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="84d4e-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="84d4e-140">`AddItem` Metoda je aktivována výběrem `<button>` prvku.</span><span class="sxs-lookup"><span data-stu-id="84d4e-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="84d4e-141">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84d4e-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="84d4e-142">Volání `PostAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="84d4e-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="84d4e-143">K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="84d4e-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="84d4e-144">`PutAsJsonAsync`&ndash; ODEŠLE požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="84d4e-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="84d4e-145">V následujícím kódu jsou `_editItem` hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti.</span><span class="sxs-lookup"><span data-stu-id="84d4e-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="84d4e-146">Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="84d4e-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="84d4e-147">`SaveItem` Metoda je aktivována výběrem možnosti Uložit `<button>` element.</span><span class="sxs-lookup"><span data-stu-id="84d4e-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="84d4e-148">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84d4e-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```
  
  <span data-ttu-id="84d4e-149">Volání `PutAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="84d4e-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="84d4e-150">K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="84d4e-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="84d4e-151"><xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="84d4e-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="84d4e-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="84d4e-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="84d4e-153">V následujícím kódu element Delete `<button>` volá `DeleteItem` metodu.</span><span class="sxs-lookup"><span data-stu-id="84d4e-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="84d4e-154">Vázaný `<input>` element poskytuje `id` položku, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="84d4e-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="84d4e-155">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="84d4e-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="84d4e-156">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="84d4e-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="84d4e-157">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="84d4e-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="84d4e-158">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="84d4e-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="84d4e-159">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="84d4e-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="84d4e-160">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="84d4e-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="84d4e-161">[Ukázková aplikace Blazor WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti webového rozhraní API volání (*Pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="84d4e-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="84d4e-162">Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="84d4e-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="84d4e-163">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="84d4e-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="84d4e-164">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="84d4e-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="84d4e-165">Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="84d4e-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@using System.Net.Http.Json
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                JsonContent.Create(new TodoItem
                { 
                    Name: "A New Todo Item",
                    IsComplete: false
                })
        };
        
        requestMessage.Headers.Authorization = 
           new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="84d4e-166">Implementace rozhraní .NET WebAssembly pro `HttpClient` používá [WindowOrWorkerGlobalScope. Fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span><span class="sxs-lookup"><span data-stu-id="84d4e-166">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="84d4e-167">Načtení umožňuje nakonfigurovat několik [možností specifických pro požadavky](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="84d4e-167">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="84d4e-168">Možnosti požadavku HTTP Fetch lze konfigurovat pomocí `HttpRequestMessage` rozšiřujících metod, které jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="84d4e-168">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="84d4e-169">`HttpRequestMessage`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="84d4e-169">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="84d4e-170">Načíst vlastnost žádosti</span><span class="sxs-lookup"><span data-stu-id="84d4e-170">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="84d4e-171">přihlašovací údaje</span><span class="sxs-lookup"><span data-stu-id="84d4e-171">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="84d4e-172">uchovávat</span><span class="sxs-lookup"><span data-stu-id="84d4e-172">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="84d4e-173">Mode</span><span class="sxs-lookup"><span data-stu-id="84d4e-173">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="84d4e-174">způsobilost</span><span class="sxs-lookup"><span data-stu-id="84d4e-174">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="84d4e-175">Další možnosti můžete nastavit pomocí obecnější metody `SetBrowserRequestOption` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="84d4e-175">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="84d4e-176">Odpověď HTTP je obvykle ukládána do vyrovnávací paměti Blazor v aplikaci WebAssembly, aby umožnila podporu pro čtení v obsahu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="84d4e-176">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="84d4e-177">Pokud chcete povolit podporu pro streamování odpovědí, `SetBrowserResponseStreamingEnabled` použijte metodu rozšíření v žádosti.</span><span class="sxs-lookup"><span data-stu-id="84d4e-177">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="84d4e-178">Pokud chcete do žádosti o více zdrojů zahrnout přihlašovací údaje, použijte `SetBrowserRequestCredentials` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="84d4e-178">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="84d4e-179">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="84d4e-179">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="84d4e-180">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech CORS musí být `Authorization` záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="84d4e-180">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="84d4e-181">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="84d4e-181">The following policy includes configuration for:</span></span>

* <span data-ttu-id="84d4e-182">Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="84d4e-182">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="84d4e-183">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="84d4e-183">Any method (verb).</span></span>
* <span data-ttu-id="84d4e-184">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="84d4e-184">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="84d4e-185">Chcete-li pro vlastní hlavičku (například `x-custom-header`), uveďte záhlaví při volání. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="84d4e-185">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="84d4e-186">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta`credentials` (vlastnost je `include`nastavena na hodnotu).</span><span class="sxs-lookup"><span data-stu-id="84d4e-186">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="84d4e-187">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="84d4e-187">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84d4e-188">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="84d4e-188">Additional resources</span></span>

* [<span data-ttu-id="84d4e-189">Vyžádání dalších přístupových tokenů</span><span class="sxs-lookup"><span data-stu-id="84d4e-189">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="84d4e-190">Připojit tokeny k odchozím žádostem</span><span class="sxs-lookup"><span data-stu-id="84d4e-190">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="84d4e-191">Konfigurace koncového bodu HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="84d4e-191">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="84d4e-192">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="84d4e-192">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
