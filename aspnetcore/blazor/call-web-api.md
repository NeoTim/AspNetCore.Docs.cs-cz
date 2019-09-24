---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Naučte se volat webové rozhraní API z aplikace Blazor pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/call-web-api
ms.openlocfilehash: 2e79c81dc2371ef5b00f9251be47433a141c8ab7
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207139"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="0a203-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0a203-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="0a203-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="0a203-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0a203-105">Blazor aplikace WebAssembly volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby.</span><span class="sxs-lookup"><span data-stu-id="0a203-105">Blazor WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="0a203-106">Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití pomocníků Blazor <xref:System.Net.Http.HttpRequestMessage>JSON nebo s.</span><span class="sxs-lookup"><span data-stu-id="0a203-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="0a203-107">Serverové aplikace Blazor volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí, které <xref:System.Net.Http.IHttpClientFactory>se obvykle vytvářejí pomocí.</span><span class="sxs-lookup"><span data-stu-id="0a203-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="0a203-108">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="0a203-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="0a203-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a203-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0a203-110">Příklady Blazor WebAssembly najdete v následujících součástech v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="0a203-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="0a203-111">Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="0a203-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="0a203-112">Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="0a203-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="0a203-113">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="0a203-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="0a203-114">V aplikacích Blazor WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro zpracování požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="0a203-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="0a203-115">Chcete- `HttpClient` li používat pomocníky JSON, přidejte odkaz na `Microsoft.AspNetCore.Blazor.HttpClient`balíček do.</span><span class="sxs-lookup"><span data-stu-id="0a203-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="0a203-116">`HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="0a203-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="0a203-117">`HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="0a203-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="0a203-118">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="0a203-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="0a203-119">`HttpClient` Vložení instance `@inject` pomocí direktivy:</span><span class="sxs-lookup"><span data-stu-id="0a203-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="0a203-120">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="0a203-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="0a203-121">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="0a203-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="0a203-122">ID (`Id`, `long`) &ndash; jedinečné ID položky</span><span class="sxs-lookup"><span data-stu-id="0a203-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="0a203-123">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="0a203-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="0a203-124">Stav (`IsComplete`, `bool`) &ndash; označuje, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="0a203-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="0a203-125">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="0a203-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="0a203-126">`GetJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="0a203-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="0a203-127">V následujícím kódu `_todoItems` se zobrazí součást.</span><span class="sxs-lookup"><span data-stu-id="0a203-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="0a203-128">Metoda je aktivována při vykreslování komponenty (OnInitializedAsync).[](xref:blazor/components#lifecycle-methods) `GetTodoItems`</span><span class="sxs-lookup"><span data-stu-id="0a203-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="0a203-129">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a203-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* <span data-ttu-id="0a203-130">`PostJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="0a203-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="0a203-131">V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="0a203-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="0a203-132">Metoda je aktivována `<button>` výběrem prvku. `AddItem`</span><span class="sxs-lookup"><span data-stu-id="0a203-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="0a203-133">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a203-133">See the sample app for a complete example.</span></span>

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
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* <span data-ttu-id="0a203-134">`PutJsonAsync`&ndash; Odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="0a203-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="0a203-135">V následujícím kódu `_editItem` jsou hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti.</span><span class="sxs-lookup"><span data-stu-id="0a203-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="0a203-136">Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="0a203-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="0a203-137">Metoda je aktivována výběrem možnosti Uložit `<button>` element. `SaveItem`</span><span class="sxs-lookup"><span data-stu-id="0a203-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="0a203-138">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a203-138">See the sample app for a complete example.</span></span>

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
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<span data-ttu-id="0a203-139"><xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="0a203-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="0a203-140">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0a203-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="0a203-141">V následujícím kódu element Delete `<button>` `DeleteItem` volá metodu.</span><span class="sxs-lookup"><span data-stu-id="0a203-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="0a203-142">Vázaný `<input>` element`id` poskytuje položku, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="0a203-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="0a203-143">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0a203-143">See the sample app for a complete example.</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="0a203-144">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="0a203-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="0a203-145">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="0a203-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="0a203-146">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="0a203-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0a203-147">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="0a203-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0a203-148">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="0a203-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="0a203-149">Ukázková aplikace ukazuje použití CORS v součásti volání webového rozhraní API (*Pages/CallWebAPI. Razor*).</span><span class="sxs-lookup"><span data-stu-id="0a203-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="0a203-150">Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="0a203-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="0a203-151">HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení</span><span class="sxs-lookup"><span data-stu-id="0a203-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="0a203-152">Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> k přizpůsobení požadavků.</span><span class="sxs-lookup"><span data-stu-id="0a203-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="0a203-153">Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="0a203-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="0a203-154">Zadejte možnosti žádosti pro základní [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu pomocí `WebAssemblyHttpMessageHandler.FetchArgs` vlastnosti v žádosti.</span><span class="sxs-lookup"><span data-stu-id="0a203-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="0a203-155">Jak je znázorněno v následujícím příkladu, `credentials` je vlastnost nastavena na některou z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="0a203-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="0a203-156">`FetchCredentialsOption.Include`("include") &ndash; Doporučuje prohlížeči odesílat přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) i pro žádosti mezi zdroji.</span><span class="sxs-lookup"><span data-stu-id="0a203-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="0a203-157">Povoleno jenom v případě, že je zásada CORS nakonfigurovaná tak, aby povolovala přihlašovací údaje.</span><span class="sxs-lookup"><span data-stu-id="0a203-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="0a203-158">`FetchCredentialsOption.Omit`("vynechat") &ndash; Doporučuje prohlížeči, aby nikdy neodesílal přihlašovací údaje (například soubory cookie nebo hlavičky ověřování HTTP).</span><span class="sxs-lookup"><span data-stu-id="0a203-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="0a203-159">`FetchCredentialsOption.SameOrigin`("stejný-původ") &ndash; Doporučuje prohlížeči odesílat přihlašovací údaje (jako jsou soubory cookie nebo hlavičky ověřování HTTP) jenom v případě, že cílová adresa URL je na stejném zdroji jako volající aplikace.</span><span class="sxs-lookup"><span data-stu-id="0a203-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

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
            RequestUri = new Uri("https://localhost:10000/api/todo"),
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

<span data-ttu-id="0a203-160">Další informace o možnostech načtení rozhraní API najdete v [tématu MDN web Docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="0a203-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="0a203-161">Při odesílání přihlašovacích údajů (souborů cookie autorizace/hlaviček) na žádostech `Authorization` CORS musí být záhlaví povoleno zásadami CORS.</span><span class="sxs-lookup"><span data-stu-id="0a203-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="0a203-162">Následující zásady zahrnují konfiguraci pro:</span><span class="sxs-lookup"><span data-stu-id="0a203-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="0a203-163">Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="0a203-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="0a203-164">Libovolná metoda (příkaz).</span><span class="sxs-lookup"><span data-stu-id="0a203-164">Any method (verb).</span></span>
* <span data-ttu-id="0a203-165">`Content-Type`a `Authorization` hlavičky.</span><span class="sxs-lookup"><span data-stu-id="0a203-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="0a203-166">Chcete-li pro vlastní hlavičku (například `x-custom-header`), uveďte záhlaví při volání. <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="0a203-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="0a203-167">Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta`credentials` (vlastnost je `include`nastavena na hodnotu).</span><span class="sxs-lookup"><span data-stu-id="0a203-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="0a203-168">Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).</span><span class="sxs-lookup"><span data-stu-id="0a203-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a203-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0a203-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* [<span data-ttu-id="0a203-170">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="0a203-170">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
