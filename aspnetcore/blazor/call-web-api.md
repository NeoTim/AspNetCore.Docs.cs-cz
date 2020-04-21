---
title: Volání webového rozhraní Blazor API z ASP.NET Core WebAssembly
author: guardrex
description: Zjistěte, jak volat webové Blazor rozhraní API z aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření požadavků na sdílení prostředků napříč zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 943f9d440adbe11ac1977f28aebee53a5510a86b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661581"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="df61a-103">Volání webového rozhraní API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="df61a-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="df61a-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a Juan De [la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="df61a-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="df61a-105">Aplikace WebAssembly volají webová api `HttpClient` pomocí předkonfigurované služby. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="df61a-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="df61a-106">Komponovat požadavky, které mohou zahrnovat možnosti Blazor [javascriptu fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pomocí pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="df61a-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="df61a-107">Služba `HttpClient` v Blazor aplikacích WebAssembly se zaměřuje na vytváření požadavků zpět na server původu.</span><span class="sxs-lookup"><span data-stu-id="df61a-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="df61a-108">Pokyny v tomto tématu se vymýšleny pouze v Blazor aplikacích WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="df61a-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="df61a-109">Serverové aplikace volají <xref:System.Net.Http.HttpClient> webová api pomocí <xref:System.Net.Http.IHttpClientFactory>instancí, které se obvykle vytvářejí pomocí . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="df61a-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="df61a-110">Pokyny v tomto tématu se nejedná Blazor o serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="df61a-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="df61a-111">Při Blazor vývoji serverových aplikací <xref:fundamentals/http-requests>postupujte podle pokynů v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="df61a-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="df61a-112">[Zobrazit nebo stáhnout ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) kód &ndash; ( jak[stáhnout](xref:index#how-to-download-a-sample)) Vyberte aplikaci *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="df61a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="df61a-113">Podívejte se na následující součásti v ukázkové aplikaci *BlazorWebAssemblySample:*</span><span class="sxs-lookup"><span data-stu-id="df61a-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="df61a-114">Call Web API *(Stránky/CallWebAPI.razor)*</span><span class="sxs-lookup"><span data-stu-id="df61a-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="df61a-115">Http Request Tester *(Components/HTTPRequestTester.razor)*</span><span class="sxs-lookup"><span data-stu-id="df61a-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="df61a-116">Balíčky</span><span class="sxs-lookup"><span data-stu-id="df61a-116">Packages</span></span>

<span data-ttu-id="df61a-117">Odkaz na balíček [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="df61a-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="df61a-118">Přidání služby HttpClient</span><span class="sxs-lookup"><span data-stu-id="df61a-118">Add the HttpClient service</span></span>

<span data-ttu-id="df61a-119">V `Program.Main`aplikaci `HttpClient` přidejte službu, pokud ještě neexistuje:</span><span class="sxs-lookup"><span data-stu-id="df61a-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="df61a-120">Pomocníci httpclient a json</span><span class="sxs-lookup"><span data-stu-id="df61a-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="df61a-121">V Blazor aplikaci WebAssembly [httpclient](xref:fundamentals/http-requests) je k dispozici jako předkonfigurovaná služba pro vytváření požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="df61a-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="df61a-122">Serverová Blazor aplikace ve výchozím `HttpClient` nastavení neobsahuje službu.</span><span class="sxs-lookup"><span data-stu-id="df61a-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="df61a-123">Poskytněte aplikaci pomocí [tovární infrastruktury HttpClient](xref:fundamentals/http-requests). `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="df61a-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="df61a-124">`HttpClient`a pomocné sypače JSON se také používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="df61a-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="df61a-125">`HttpClient`je implementována pomocí rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejné zásady původu.</span><span class="sxs-lookup"><span data-stu-id="df61a-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="df61a-126">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="df61a-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="df61a-127">`HttpClient` Vstříkněte `@inject` instanci pomocí směrnice:</span><span class="sxs-lookup"><span data-stu-id="df61a-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="df61a-128">V následujících příkladech procesy webového rozhraní API todo vytvořit, číst, aktualizovat a odstranit (CRUD) operace.</span><span class="sxs-lookup"><span data-stu-id="df61a-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="df61a-129">Příklady jsou založeny `TodoItem` na třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="df61a-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="df61a-130">ID`Id`( `long` &ndash; , ) Jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="df61a-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="df61a-131">Název`Name`( `string` &ndash; , ) Název položky.</span><span class="sxs-lookup"><span data-stu-id="df61a-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="df61a-132">Stav`IsComplete`( `bool` &ndash; , ) Označte, zda je položka Todo dokončena.</span><span class="sxs-lookup"><span data-stu-id="df61a-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="df61a-133">Pomocné metody JSON odesílají požadavky uri (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="df61a-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="df61a-134">`GetFromJsonAsync`&ndash; Odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON k vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="df61a-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="df61a-135">V následujícím kódu `_todoItems` jsou zobrazeny komponenty.</span><span class="sxs-lookup"><span data-stu-id="df61a-135">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="df61a-136">Metoda `GetTodoItems` se aktivuje po dokončení vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="df61a-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="df61a-137">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="df61a-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="df61a-138">`PostAsJsonAsync`&ndash; Odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON k vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="df61a-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="df61a-139">V následujícím kódu `_newItemName` je poskytována vázaný prvek komponenty.</span><span class="sxs-lookup"><span data-stu-id="df61a-139">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="df61a-140">Metoda `AddItem` se aktivuje výběrem `<button>` prvku.</span><span class="sxs-lookup"><span data-stu-id="df61a-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="df61a-141">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="df61a-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="df61a-142">Volání `PostAsJsonAsync` pro <xref:System.Net.Http.HttpResponseMessage>návrat .</span><span class="sxs-lookup"><span data-stu-id="df61a-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="df61a-143">Chcete-li dekonstruovat obsah JSON ze `ReadFromJsonAsync<T>` zprávy odpovědi, použijte metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="df61a-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="df61a-144">`PutAsJsonAsync`&ndash; Odešle požadavek HTTP PUT, včetně obsahu kódovaného protokolem JSON.</span><span class="sxs-lookup"><span data-stu-id="df61a-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="df61a-145">V následujícím kódu `_editItem` jsou `Name` `IsCompleted` hodnoty pro a jsou poskytovány vázané prvky komponenty.</span><span class="sxs-lookup"><span data-stu-id="df61a-145">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="df61a-146">Položka `Id` je nastavena, když je položka vybrána v `EditItem` jiné části ui a je volána.</span><span class="sxs-lookup"><span data-stu-id="df61a-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="df61a-147">Metoda `SaveItem` se spustí výběrem `<button>` Uložit prvek.</span><span class="sxs-lookup"><span data-stu-id="df61a-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="df61a-148">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="df61a-148">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="df61a-149">Volání `PutAsJsonAsync` pro <xref:System.Net.Http.HttpResponseMessage>návrat .</span><span class="sxs-lookup"><span data-stu-id="df61a-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="df61a-150">Chcete-li dekonstruovat obsah JSON ze `ReadFromJsonAsync<T>` zprávy odpovědi, použijte metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="df61a-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="df61a-151"><xref:System.Net.Http>obsahuje další metody rozšíření pro odesílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="df61a-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="df61a-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) slouží k odeslání požadavku HTTP DELETE do webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="df61a-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="df61a-153">V následujícím kódu delete `<button>` element `DeleteItem` volá metodu.</span><span class="sxs-lookup"><span data-stu-id="df61a-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="df61a-154">Vázaný `<input>` prvek dodává `id` položku odstranit.</span><span class="sxs-lookup"><span data-stu-id="df61a-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="df61a-155">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="df61a-155">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="df61a-156">Sdílení zdrojů mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="df61a-156">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="df61a-157">Zabezpečení prohlížeče brání webové stránce v podávání žádostí do jiné domény, než je doména, která webovou stránku obsluhovala.</span><span class="sxs-lookup"><span data-stu-id="df61a-157">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="df61a-158">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="df61a-158">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="df61a-159">Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu.</span><span class="sxs-lookup"><span data-stu-id="df61a-159">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="df61a-160">Chcete-li vytvořit požadavky z prohlížeče na koncový bod s jiným původem, *koncový bod* musí povolit sdílení prostředků [mezi původem (CORS).](https://www.w3.org/TR/cors/)</span><span class="sxs-lookup"><span data-stu-id="df61a-160">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="df61a-161">[ Blazor Ukázková aplikace WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje použití CORS v komponentě rozhraní API volání web *(Pages/CallWebAPI.razor).*</span><span class="sxs-lookup"><span data-stu-id="df61a-161">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="df61a-162">Pokud chcete jiným webům povolit, aby do vaší aplikace mohly provádět požadavky na sdílení prostředků napříč zdroji (CORS), přečtěte si další informace o tom, že <xref:security/cors></span><span class="sxs-lookup"><span data-stu-id="df61a-162">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="df61a-163">HttpClient a HttpRequestMessage s možnostmi požadavku na načtení rozhraní API</span><span class="sxs-lookup"><span data-stu-id="df61a-163">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="df61a-164">Při spuštění na webassembly v aplikaci Blazor WebAssembly použijte [httpclient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> přizpůsobit požadavky.</span><span class="sxs-lookup"><span data-stu-id="df61a-164">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="df61a-165">Můžete například zadat identifikátor URI požadavku, metodu HTTP a všechny požadované hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="df61a-165">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

```razor
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="df61a-166">Další informace o možnostech načtení rozhraní API naleznete v [tématu MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="df61a-166">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="df61a-167">Při odesílání pověření (autorizační ch cookies/záhlaví) na `Authorization` požadavky CORS musí být hlavička povolena zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="df61a-167">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="df61a-168">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="df61a-168">The following policy includes configuration for:</span></span>

* <span data-ttu-id="df61a-169">Původ žádosti`http://localhost:5000`( `https://localhost:5001`, ).</span><span class="sxs-lookup"><span data-stu-id="df61a-169">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="df61a-170">Libovolná metoda (sloveso).</span><span class="sxs-lookup"><span data-stu-id="df61a-170">Any method (verb).</span></span>
* <span data-ttu-id="df61a-171">`Content-Type`a `Authorization` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="df61a-171">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="df61a-172">Chcete-li povolit vlastní `x-custom-header`záhlaví (například <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>), uveďte záhlaví při volání .</span><span class="sxs-lookup"><span data-stu-id="df61a-172">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="df61a-173">Pověření nastavená pomocí kódu JavaScript`credentials` u `include`klienta ( vlastnost nastavená na ).</span><span class="sxs-lookup"><span data-stu-id="df61a-173">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="df61a-174">Další informace naleznete <xref:security/cors> v tématu a součást http testeru ukázkové aplikace *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="df61a-174">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="df61a-175">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="df61a-175">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="df61a-176">Konfigurace koncového bodu Kestrel HTTPS</span><span class="sxs-lookup"><span data-stu-id="df61a-176">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="df61a-177">Cross Origin Resource Sharing (CORS) ve společnosti W3C</span><span class="sxs-lookup"><span data-stu-id="df61a-177">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
