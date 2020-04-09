---
title: Volání webového rozhraní API z ASP.NET CoreBlazor
author: guardrex
description: Zjistěte, jak volat webové Blazor rozhraní API z aplikace pomocí pomocníků JSON, včetně vytváření požadavků na sdílení prostředků napříč zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660144"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="70ef9-103">Volání webového rozhraní API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="70ef9-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="70ef9-104">[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a Juan De [la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="70ef9-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="70ef9-105">Aplikace WebAssembly volají webová api `HttpClient` pomocí předkonfigurované služby. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="70ef9-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="70ef9-106">Komponovat požadavky, které mohou zahrnovat možnosti Blazor [javascriptu fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pomocí pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="70ef9-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="70ef9-107">Služba `HttpClient` v Blazor aplikacích WebAssembly se zaměřuje na vytváření požadavků zpět na server původu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="70ef9-108">Pokyny v tomto tématu se vymýšleny pouze v Blazor aplikacích WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="70ef9-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="70ef9-109">Serverové aplikace volají <xref:System.Net.Http.HttpClient> webová api pomocí <xref:System.Net.Http.IHttpClientFactory>instancí, které se obvykle vytvářejí pomocí . [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="70ef9-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="70ef9-110">Pokyny v tomto tématu se nejedná Blazor o serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="70ef9-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="70ef9-111">Při Blazor vývoji serverových aplikací <xref:fundamentals/http-requests>postupujte podle pokynů v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="70ef9-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="70ef9-112">[Zobrazit nebo stáhnout ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) kód &ndash; ( jak[stáhnout](xref:index#how-to-download-a-sample)) Vyberte aplikaci *BlazorWebAssemblySample.*</span><span class="sxs-lookup"><span data-stu-id="70ef9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="70ef9-113">Podívejte se na následující součásti v ukázkové aplikaci *BlazorWebAssemblySample:*</span><span class="sxs-lookup"><span data-stu-id="70ef9-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="70ef9-114">Call Web API *(Stránky/CallWebAPI.razor)*</span><span class="sxs-lookup"><span data-stu-id="70ef9-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="70ef9-115">Http Request Tester *(Components/HTTPRequestTester.razor)*</span><span class="sxs-lookup"><span data-stu-id="70ef9-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="70ef9-116">Balíčky</span><span class="sxs-lookup"><span data-stu-id="70ef9-116">Packages</span></span>

<span data-ttu-id="70ef9-117">Odkaz na *experimentální* [Microsoft.AspNetCore.Blazor. Balíček HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-117">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="70ef9-118">`Microsoft.AspNetCore.Blazor.HttpClient`je založen `HttpClient` na a [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="70ef9-118">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="70ef9-119">Chcete-li použít stabilní rozhraní API, použijte balíček [Microsoft.AspNet.WebApi.Client,](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) který používá [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="70ef9-119">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="70ef9-120">Použití stabilní rozhraní `Microsoft.AspNet.WebApi.Client` API v neposkytuje pomocné názvy JSON popsané v `Microsoft.AspNetCore.Blazor.HttpClient` tomto tématu, které jsou jedinečné pro experimentální balíček.</span><span class="sxs-lookup"><span data-stu-id="70ef9-120">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="70ef9-121">Pomocníci httpclient a json</span><span class="sxs-lookup"><span data-stu-id="70ef9-121">HttpClient and JSON helpers</span></span>

<span data-ttu-id="70ef9-122">V Blazor aplikaci WebAssembly [httpclient](xref:fundamentals/http-requests) je k dispozici jako předkonfigurovaná služba pro vytváření požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="70ef9-122">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="70ef9-123">Serverová Blazor aplikace ve výchozím `HttpClient` nastavení neobsahuje službu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-123">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="70ef9-124">Poskytněte aplikaci pomocí [tovární infrastruktury HttpClient](xref:fundamentals/http-requests). `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="70ef9-124">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="70ef9-125">`HttpClient`a pomocné sypače JSON se také používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="70ef9-125">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="70ef9-126">`HttpClient`je implementována pomocí rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejné zásady původu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-126">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="70ef9-127">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="70ef9-127">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="70ef9-128">`HttpClient` Vstříkněte `@inject` instanci pomocí směrnice:</span><span class="sxs-lookup"><span data-stu-id="70ef9-128">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="70ef9-129">V následujících příkladech procesy webového rozhraní API todo vytvořit, číst, aktualizovat a odstranit (CRUD) operace.</span><span class="sxs-lookup"><span data-stu-id="70ef9-129">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="70ef9-130">Příklady jsou založeny `TodoItem` na třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="70ef9-130">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="70ef9-131">ID`Id`( `long` &ndash; , ) Jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="70ef9-131">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="70ef9-132">Název`Name`( `string` &ndash; , ) Název položky.</span><span class="sxs-lookup"><span data-stu-id="70ef9-132">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="70ef9-133">Stav`IsComplete`( `bool` &ndash; , ) Označte, zda je položka Todo dokončena.</span><span class="sxs-lookup"><span data-stu-id="70ef9-133">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="70ef9-134">Pomocné metody JSON odesílají požadavky uri (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="70ef9-134">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="70ef9-135">`GetJsonAsync`&ndash; Odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON k vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-135">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="70ef9-136">V následujícím kódu `_todoItems` jsou zobrazeny komponenty.</span><span class="sxs-lookup"><span data-stu-id="70ef9-136">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="70ef9-137">Metoda `GetTodoItems` se aktivuje po dokončení vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="70ef9-137">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="70ef9-138">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70ef9-138">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="70ef9-139">`PostJsonAsync`&ndash; Odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON k vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-139">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="70ef9-140">V následujícím kódu `_newItemName` je poskytována vázaný prvek komponenty.</span><span class="sxs-lookup"><span data-stu-id="70ef9-140">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="70ef9-141">Metoda `AddItem` se aktivuje výběrem `<button>` prvku.</span><span class="sxs-lookup"><span data-stu-id="70ef9-141">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="70ef9-142">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70ef9-142">See the sample app for a complete example.</span></span>

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
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* <span data-ttu-id="70ef9-143">`PutJsonAsync`&ndash; Odešle požadavek HTTP PUT, včetně obsahu kódovaného protokolem JSON.</span><span class="sxs-lookup"><span data-stu-id="70ef9-143">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="70ef9-144">V následujícím kódu `_editItem` jsou `Name` `IsCompleted` hodnoty pro a jsou poskytovány vázané prvky komponenty.</span><span class="sxs-lookup"><span data-stu-id="70ef9-144">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="70ef9-145">Položka `Id` je nastavena, když je položka vybrána v `EditItem` jiné části ui a je volána.</span><span class="sxs-lookup"><span data-stu-id="70ef9-145">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="70ef9-146">Metoda `SaveItem` se spustí výběrem `<button>` Uložit prvek.</span><span class="sxs-lookup"><span data-stu-id="70ef9-146">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="70ef9-147">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70ef9-147">See the sample app for a complete example.</span></span>

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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="70ef9-148"><xref:System.Net.Http>obsahuje další metody rozšíření pro odesílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="70ef9-148"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="70ef9-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) slouží k odeslání požadavku HTTP DELETE do webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="70ef9-149">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="70ef9-150">V následujícím kódu delete `<button>` element `DeleteItem` volá metodu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-150">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="70ef9-151">Vázaný `<input>` prvek dodává `id` položku odstranit.</span><span class="sxs-lookup"><span data-stu-id="70ef9-151">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="70ef9-152">Úplný příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="70ef9-152">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="70ef9-153">Sdílení zdrojů mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="70ef9-153">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="70ef9-154">Zabezpečení prohlížeče brání webové stránce v podávání žádostí do jiné domény, než je doména, která webovou stránku obsluhovala.</span><span class="sxs-lookup"><span data-stu-id="70ef9-154">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="70ef9-155">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="70ef9-155">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="70ef9-156">Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu.</span><span class="sxs-lookup"><span data-stu-id="70ef9-156">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="70ef9-157">Chcete-li vytvořit požadavky z prohlížeče na koncový bod s jiným původem, *koncový bod* musí povolit sdílení prostředků [mezi původem (CORS).](https://www.w3.org/TR/cors/)</span><span class="sxs-lookup"><span data-stu-id="70ef9-157">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="70ef9-158">[ Blazor Ukázková aplikace WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje použití CORS v komponentě rozhraní API volání web *(Pages/CallWebAPI.razor).*</span><span class="sxs-lookup"><span data-stu-id="70ef9-158">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="70ef9-159">Pokud chcete jiným webům povolit, aby do vaší aplikace mohly provádět požadavky na sdílení prostředků napříč zdroji (CORS), přečtěte si další informace o tom, že <xref:security/cors></span><span class="sxs-lookup"><span data-stu-id="70ef9-159">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="70ef9-160">HttpClient a HttpRequestMessage s možnostmi požadavku na načtení rozhraní API</span><span class="sxs-lookup"><span data-stu-id="70ef9-160">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="70ef9-161">Při spuštění na webassembly v aplikaci Blazor WebAssembly použijte [httpclient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> přizpůsobit požadavky.</span><span class="sxs-lookup"><span data-stu-id="70ef9-161">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="70ef9-162">Můžete například zadat identifikátor URI požadavku, metodu HTTP a všechny požadované hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="70ef9-162">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="70ef9-163">Další informace o možnostech načtení rozhraní API naleznete v [tématu MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="70ef9-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="70ef9-164">Při odesílání pověření (autorizační ch cookies/záhlaví) na `Authorization` požadavky CORS musí být hlavička povolena zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="70ef9-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="70ef9-165">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="70ef9-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="70ef9-166">Původ žádosti`http://localhost:5000`( `https://localhost:5001`, ).</span><span class="sxs-lookup"><span data-stu-id="70ef9-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="70ef9-167">Libovolná metoda (sloveso).</span><span class="sxs-lookup"><span data-stu-id="70ef9-167">Any method (verb).</span></span>
* <span data-ttu-id="70ef9-168">`Content-Type`a `Authorization` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="70ef9-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="70ef9-169">Chcete-li povolit vlastní `x-custom-header`záhlaví (například <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>), uveďte záhlaví při volání .</span><span class="sxs-lookup"><span data-stu-id="70ef9-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="70ef9-170">Pověření nastavená pomocí kódu JavaScript`credentials` u `include`klienta ( vlastnost nastavená na ).</span><span class="sxs-lookup"><span data-stu-id="70ef9-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="70ef9-171">Další informace naleznete <xref:security/cors> v tématu a součást http testeru ukázkové aplikace *(Components/HTTPRequestTester.razor).*</span><span class="sxs-lookup"><span data-stu-id="70ef9-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70ef9-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="70ef9-172">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="70ef9-173">Konfigurace koncového bodu Kestrel HTTPS</span><span class="sxs-lookup"><span data-stu-id="70ef9-173">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="70ef9-174">Cross Origin Resource Sharing (CORS) ve společnosti W3C</span><span class="sxs-lookup"><span data-stu-id="70ef9-174">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
