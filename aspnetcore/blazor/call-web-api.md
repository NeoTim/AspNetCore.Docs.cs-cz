---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: f1929b48275a36552f061a64823267df0f3acabc
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943911"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="c01b7-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c01b7-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="c01b7-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="c01b7-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c01b7-105">[Blazor aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) volají webová rozhraní API pomocí předkonfigurované služby `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c01b7-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="c01b7-106">Vytvářené požadavky, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, pomocí Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="c01b7-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="c01b7-107">[Blazor serverové](xref:blazor/hosting-models#blazor-server) aplikace volají webová rozhraní API pomocí instancí <xref:System.Net.Http.HttpClient> obvykle vytvořených pomocí <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="c01b7-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="c01b7-108">Další informace najdete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="c01b7-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="c01b7-109">[Zobrazte nebo Stáhněte ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="c01b7-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="c01b7-110">Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="c01b7-110">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="c01b7-111">Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="c01b7-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="c01b7-112">Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="c01b7-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="c01b7-113">Balíčky</span><span class="sxs-lookup"><span data-stu-id="c01b7-113">Packages</span></span>

<span data-ttu-id="c01b7-114">Odkázat na *experimentální* [Microsoft. AspNetCore.Blazor. HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) balíček NuGet v souboru projektu</span><span class="sxs-lookup"><span data-stu-id="c01b7-114">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="c01b7-115">`Microsoft.AspNetCore.Blazor.HttpClient` je založen na `HttpClient` a [System. text. JSON](https://www.nuget.org/packages/System.Text.Json/).</span><span class="sxs-lookup"><span data-stu-id="c01b7-115">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="c01b7-116">Chcete-li použít stabilní rozhraní API, použijte balíček [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , který používá [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="c01b7-116">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="c01b7-117">Použití stabilního rozhraní API v `Microsoft.AspNet.WebApi.Client` neposkytuje pomocníkům JSON, které jsou popsány v tomto tématu, které jsou jedinečné pro experimentální `Microsoft.AspNetCore.Blazor.HttpClient` balíček.</span><span class="sxs-lookup"><span data-stu-id="c01b7-117">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="c01b7-118">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="c01b7-118">HttpClient and JSON helpers</span></span>

<span data-ttu-id="c01b7-119">V aplikaci Blazor WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="c01b7-119">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="c01b7-120">Aplikace Blazor serveru ve výchozím nastavení neobsahuje službu `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="c01b7-120">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="c01b7-121">Poskytněte `HttpClient` k aplikaci pomocí [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="c01b7-121">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="c01b7-122">`HttpClient` a pomocníky JSON se také používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="c01b7-122">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="c01b7-123">`HttpClient` je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="c01b7-123">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="c01b7-124">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="c01b7-124">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="c01b7-125">Vložení instance `HttpClient` pomocí direktivy `@inject`:</span><span class="sxs-lookup"><span data-stu-id="c01b7-125">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="c01b7-126">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="c01b7-126">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="c01b7-127">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="c01b7-127">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="c01b7-128">ID (`Id`, `long`) &ndash; jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="c01b7-128">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="c01b7-129">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="c01b7-129">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="c01b7-130">Stav (`IsComplete`, `bool`) &ndash; indikaci, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="c01b7-130">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="c01b7-131">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="c01b7-131">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="c01b7-132">`GetJsonAsync` &ndash; odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="c01b7-132">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="c01b7-133">V následujícím kódu jsou `_todoItems` zobrazeny komponentou.</span><span class="sxs-lookup"><span data-stu-id="c01b7-133">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="c01b7-134">Metoda `GetTodoItems` je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="c01b7-134">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="c01b7-135">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c01b7-135">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="c01b7-136">`PostJsonAsync` &ndash; odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="c01b7-136">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="c01b7-137">V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="c01b7-137">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="c01b7-138">Metoda `AddItem` je aktivována výběrem `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="c01b7-138">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="c01b7-139">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c01b7-139">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="c01b7-140">`PutJsonAsync` &ndash; odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="c01b7-140">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="c01b7-141">V následujícím kódu `_editItem` hodnoty pro `Name` a `IsCompleted` poskytují vázané prvky komponenty.</span><span class="sxs-lookup"><span data-stu-id="c01b7-141">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="c01b7-142">`Id` položky je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="c01b7-142">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="c01b7-143">Metoda `SaveItem` je aktivována výběrem prvku uložit `<button>`.</span><span class="sxs-lookup"><span data-stu-id="c01b7-143">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="c01b7-144">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c01b7-144">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="c01b7-145"><xref:System.Net.Http> zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="c01b7-145"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="c01b7-146">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="c01b7-146">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="c01b7-147">V následujícím kódu element Delete `<button>` volá metodu `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="c01b7-147">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="c01b7-148">Vázaný `<input>` element poskytuje `id` položky, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="c01b7-148">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="c01b7-149">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="c01b7-149">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="c01b7-150">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="c01b7-150">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="c01b7-151">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="c01b7-151">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="c01b7-152">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="c01b7-152">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="c01b7-153">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="c01b7-153">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c01b7-154">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="c01b7-154">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="c01b7-155">[Ukázková aplikaceBlazor WebAssembly (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti webového rozhraní API volání (*Pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="c01b7-155">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="c01b7-156">Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS), přečtěte si téma <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="c01b7-156">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="c01b7-157">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="c01b7-157">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="c01b7-158">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte k přizpůsobení žádostí [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="c01b7-158">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="c01b7-159">Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="c01b7-159">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="c01b7-160">Další informace o možnostech načtení rozhraní API najdete v tématu [MDN web Docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="c01b7-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="c01b7-161">Při odesílání přihlašovacích údajů (autorizační soubory cookie/hlavičky) na žádosti CORS musí být záhlaví `Authorization` povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="c01b7-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="c01b7-162">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="c01b7-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="c01b7-163">Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="c01b7-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="c01b7-164">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="c01b7-164">Any method (verb).</span></span>
* <span data-ttu-id="c01b7-165">hlavičky `Content-Type` a `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="c01b7-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="c01b7-166">Pokud chcete pro vlastní hlavičku (například `x-custom-header`), vypište při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>hlavičku.</span><span class="sxs-lookup"><span data-stu-id="c01b7-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="c01b7-167">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta (`credentials` vlastnost nastavenou na `include`).</span><span class="sxs-lookup"><span data-stu-id="c01b7-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="c01b7-168">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="c01b7-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c01b7-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c01b7-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="c01b7-170">Konfigurace koncového bodu HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="c01b7-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="c01b7-171">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="c01b7-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
