---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: ffc9904c5746fbf0fafa10cf054666608942650c
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74680899"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="d1179-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d1179-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="d1179-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="d1179-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="d1179-105"> aplikace WebAssembly volají webová rozhraní API pomocí předkonfigurované služby `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d1179-105"> WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="d1179-106">Vytvářené požadavky, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, pomocí Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="d1179-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

Blazor<span data-ttu-id="d1179-107"> serverové aplikace volají webová rozhraní API pomocí instancí <xref:System.Net.Http.HttpClient> obvykle vytvořených pomocí <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="d1179-107"> Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="d1179-108">Další informace najdete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="d1179-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="d1179-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d1179-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d1179-110">Příklady Blazor WebAssembly najdete v následujících součástech v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="d1179-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="d1179-111">Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="d1179-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="d1179-112">Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="d1179-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="d1179-113">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="d1179-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="d1179-114">V Blazor aplikace WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="d1179-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="d1179-115">Pokud chcete použít `HttpClient` pomocníků JSON, přidejte odkaz na balíček do `Microsoft.AspNetCore.Blazor.HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d1179-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="d1179-116">`HttpClient` a pomocníky JSON se také používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="d1179-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="d1179-117">`HttpClient` je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="d1179-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="d1179-118">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="d1179-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="d1179-119">Vložení instance `HttpClient` pomocí direktivy `@inject`:</span><span class="sxs-lookup"><span data-stu-id="d1179-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="d1179-120">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="d1179-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="d1179-121">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="d1179-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="d1179-122">ID (`Id`, `long`) &ndash; jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="d1179-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="d1179-123">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="d1179-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="d1179-124">Stav (`IsComplete`, `bool`) &ndash; indikaci, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="d1179-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="d1179-125">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="d1179-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="d1179-126">`GetJsonAsync` &ndash; odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="d1179-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="d1179-127">V následujícím kódu jsou `_todoItems` zobrazeny komponentou.</span><span class="sxs-lookup"><span data-stu-id="d1179-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="d1179-128">Metoda `GetTodoItems` je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="d1179-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="d1179-129">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1179-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="d1179-130">`PostJsonAsync` &ndash; odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="d1179-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="d1179-131">V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="d1179-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="d1179-132">Metoda `AddItem` je aktivována výběrem `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="d1179-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="d1179-133">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1179-133">See the sample app for a complete example.</span></span>

  ```cshtml
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

* <span data-ttu-id="d1179-134">`PutJsonAsync` &ndash; odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="d1179-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="d1179-135">V následujícím kódu `_editItem` hodnoty pro `Name` a `IsCompleted` poskytují vázané prvky komponenty.</span><span class="sxs-lookup"><span data-stu-id="d1179-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="d1179-136">`Id` položky je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="d1179-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="d1179-137">Metoda `SaveItem` je aktivována výběrem prvku uložit `<button>`.</span><span class="sxs-lookup"><span data-stu-id="d1179-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="d1179-138">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1179-138">See the sample app for a complete example.</span></span>

  ```cshtml
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

<span data-ttu-id="d1179-139"><xref:System.Net.Http> zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="d1179-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="d1179-140">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="d1179-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="d1179-141">V následujícím kódu element Delete `<button>` volá metodu `DeleteItem`.</span><span class="sxs-lookup"><span data-stu-id="d1179-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="d1179-142">Vázaný `<input>` element poskytuje `id` položky, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="d1179-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="d1179-143">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d1179-143">See the sample app for a complete example.</span></span>

```cshtml
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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="d1179-144">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="d1179-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="d1179-145">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="d1179-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="d1179-146">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="d1179-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d1179-147">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="d1179-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d1179-148">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="d1179-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="d1179-149">Ukázková aplikace ukazuje použití CORS v součásti volání webového rozhraní API (*Pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d1179-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="d1179-150">Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS), přečtěte si téma <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="d1179-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="d1179-151">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="d1179-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="d1179-152">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte k přizpůsobení žádostí [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="d1179-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="d1179-153">Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="d1179-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="d1179-154">Zadání možností žádosti do základního [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu pomocí vlastnosti `WebAssemblyHttpMessageHandler.FetchArgs` v žádosti.</span><span class="sxs-lookup"><span data-stu-id="d1179-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="d1179-155">Jak je znázorněno v následujícím příkladu, vlastnost `credentials` je nastavena na některou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="d1179-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="d1179-156">`FetchCredentialsOption.Include` ("include") &ndash; upozorní prohlížeč, aby odesílal přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) i pro žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="d1179-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="d1179-157">Povoleno jenom v případě, že je zásada CORS nakonfigurovaná tak, aby povolovala přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="d1179-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="d1179-158">`FetchCredentialsOption.Omit` ("vynechat") &ndash; upozorní prohlížeč, že nikdy neposílá přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP).</span><span class="sxs-lookup"><span data-stu-id="d1179-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="d1179-159">`FetchCredentialsOption.SameOrigin` ("stejný-původ") &ndash; upozorní prohlížeč, aby odesílal přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) pouze v případě, že cílová adresa URL má stejný původ jako volající aplikace.</span><span class="sxs-lookup"><span data-stu-id="d1179-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

```cshtml
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
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

<span data-ttu-id="d1179-160">Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="d1179-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="d1179-161">Při odesílání přihlašovacích údajů (autorizační soubory cookie/hlavičky) na žádosti CORS musí být záhlaví `Authorization` povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="d1179-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="d1179-162">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="d1179-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="d1179-163">Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="d1179-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="d1179-164">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="d1179-164">Any method (verb).</span></span>
* <span data-ttu-id="d1179-165">hlavičky `Content-Type` a `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="d1179-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="d1179-166">Pokud chcete pro vlastní hlavičku (například `x-custom-header`), vypište při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>hlavičku.</span><span class="sxs-lookup"><span data-stu-id="d1179-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="d1179-167">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta (`credentials` vlastnost nastavenou na `include`).</span><span class="sxs-lookup"><span data-stu-id="d1179-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="d1179-168">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="d1179-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d1179-169">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d1179-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="d1179-170">Konfigurace koncového bodu HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="d1179-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="d1179-171">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="d1179-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
