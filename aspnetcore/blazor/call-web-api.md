---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Zjistěte, jak volat webové rozhraní API z aplikace Blazor pomocí pomocné rutiny JSON, včetně vytváření prostředků mezi zdroji (CORS) žádosti o sdílení.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2019
uid: blazor/call-web-api
ms.openlocfilehash: 1a13f9f1f9e660b39a1df584e49198c4bbb61533
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67399179"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="a0904-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a0904-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="a0904-104">Podle [Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a0904-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="a0904-105">Aplikace na straně klienta Blazor volání webového rozhraní API pomocí předkonfigurovaného `HttpClient` služby.</span><span class="sxs-lookup"><span data-stu-id="a0904-105">Blazor client-side apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="a0904-106">Vytváření požadavků, které mohou zahrnovat JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) možnosti použití pomocné rutiny Blazor JSON nebo s <xref:System.Net.Http.HttpRequestMessage>.</span><span class="sxs-lookup"><span data-stu-id="a0904-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="a0904-107">Blazor serverové aplikace volání webového rozhraní API pomocí <xref:System.Net.Http.HttpClient> instance většinou vytvořené využitím <xref:System.Net.Http.IHttpClientFactory>.</span><span class="sxs-lookup"><span data-stu-id="a0904-107">Blazor server-side apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="a0904-108">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="a0904-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="a0904-109">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a0904-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a0904-110">Příklady Blazor na straně klienta najdete v následující součásti v ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a0904-110">For Blazor client-side examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="a0904-111">Volání webového rozhraní API (*Pages/CallWebAPI.razor*)</span><span class="sxs-lookup"><span data-stu-id="a0904-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="a0904-112">Testování požadavku HTTP (*Components/HTTPRequestTester.razor*)</span><span class="sxs-lookup"><span data-stu-id="a0904-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="a0904-113">Pomocné rutiny HttpClient a JSON</span><span class="sxs-lookup"><span data-stu-id="a0904-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="a0904-114">V aplikacích na straně klienta Blazor [HttpClient](xref:fundamentals/http-requests) je k dispozici jako předem nakonfigurované služby pro zasílání požadavků zpět na původním serveru.</span><span class="sxs-lookup"><span data-stu-id="a0904-114">In Blazor client-side apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="a0904-115">`HttpClient` a pomocné rutiny JSON se také používají k volání koncových bodů třetích stran webové rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a0904-115">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="a0904-116">`HttpClient` je implementováno pomocí prohlížeče [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) a podléhá jeho omezení, včetně vynucení zásada stejného zdroje.</span><span class="sxs-lookup"><span data-stu-id="a0904-116">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="a0904-117">Základní adresa klienta je nastavena na původním serveru adresu.</span><span class="sxs-lookup"><span data-stu-id="a0904-117">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="a0904-118">Vložení `HttpClient` instance pomocí `@inject` – direktiva:</span><span class="sxs-lookup"><span data-stu-id="a0904-118">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="a0904-119">V následujících příkladech Todo webové rozhraní API procesu vytvoření, čtení, aktualizace a odstranění (CRUD) operací.</span><span class="sxs-lookup"><span data-stu-id="a0904-119">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="a0904-120">Příklady jsou založeny na `TodoItem` třídu, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="a0904-120">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="a0904-121">ID (`Id`, `long`) &ndash; jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="a0904-121">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="a0904-122">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="a0904-122">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="a0904-123">Stav (`IsComplete`, `bool`) &ndash; údaj, zda je dokončené položky seznamu úkolů.</span><span class="sxs-lookup"><span data-stu-id="a0904-123">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="a0904-124">Metody helper JSON odesílání požadavků na identifikátor URI (webového rozhraní API v následujících příkladech) a zpracování odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a0904-124">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="a0904-125">`GetJsonAsync` &ndash; Pošle požadavek HTTP GET a analyzuje těla odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="a0904-125">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a0904-126">V následujícím kódu `_todoItems` zobrazují komponentou.</span><span class="sxs-lookup"><span data-stu-id="a0904-126">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="a0904-127">`GetTodoItems` Metoda se aktivuje, když je součást dokončena vykreslování ([OnInitAsync](xref:blazor/components#lifecycle-methods)).</span><span class="sxs-lookup"><span data-stu-id="a0904-127">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="a0904-128">Zobrazte ukázkovou aplikaci pro kompletní příklad.</span><span class="sxs-lookup"><span data-stu-id="a0904-128">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* <span data-ttu-id="a0904-129">`PostJsonAsync` &ndash; Odešle požadavek HTTP POST, včetně obsah kódovaný ve formátu JSON a Parsuje těla odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="a0904-129">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="a0904-130">V následujícím kódu `_newItemName` poskytuje vázaný prvek součásti.</span><span class="sxs-lookup"><span data-stu-id="a0904-130">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="a0904-131">`AddItem` Metoda se aktivuje při výběru `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="a0904-131">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="a0904-132">Zobrazte ukázkovou aplikaci pro kompletní příklad.</span><span class="sxs-lookup"><span data-stu-id="a0904-132">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="a0904-133">`PutJsonAsync` &ndash; Odešle požadavek HTTP PUT, včetně obsah kódovaný ve formátu JSON.</span><span class="sxs-lookup"><span data-stu-id="a0904-133">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="a0904-134">V následujícím kódu `_editItem` hodnoty `Name` a `IsCompleted` jsou k dispozici v vázaných prvků komponenty.</span><span class="sxs-lookup"><span data-stu-id="a0904-134">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="a0904-135">Položky `Id` nastavena při výběru položky v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="a0904-135">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="a0904-136">`SaveItem` Metoda se aktivuje tak, že vyberete Uložit `<button>` elementu.</span><span class="sxs-lookup"><span data-stu-id="a0904-136">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="a0904-137">Zobrazte ukázkovou aplikaci pro kompletní příklad.</span><span class="sxs-lookup"><span data-stu-id="a0904-137">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="a0904-138"><xref:System.Net.Http> zahrnuje další rozšiřující metody pro odesílání požadavků HTTP a příjmu odpovědi protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="a0904-138"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="a0904-139">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání do webového rozhraní API požadavek HTTP DELETE.</span><span class="sxs-lookup"><span data-stu-id="a0904-139">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="a0904-140">V následujícím kódu, odstranění `<button>` element volání `DeleteItem` metody.</span><span class="sxs-lookup"><span data-stu-id="a0904-140">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="a0904-141">Je mez `<input>` element poskytuje `id` položky k odstranění.</span><span class="sxs-lookup"><span data-stu-id="a0904-141">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="a0904-142">Zobrazte ukázkovou aplikaci pro kompletní příklad.</span><span class="sxs-lookup"><span data-stu-id="a0904-142">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="a0904-143">Prostředků mezi zdroji (CORS) pro sdílení obsahu</span><span class="sxs-lookup"><span data-stu-id="a0904-143">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="a0904-144">Zabezpečení prohlížečů brání webovou stránku zasílání požadavků na jiné doméně než ten, který obsluhuje webovou stránku.</span><span class="sxs-lookup"><span data-stu-id="a0904-144">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="a0904-145">Toto omezení je volána *zásada stejného zdroje*.</span><span class="sxs-lookup"><span data-stu-id="a0904-145">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="a0904-146">Zásada stejného zdroje brání škodlivým webům ve čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="a0904-146">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="a0904-147">Aby požadavky z prohlížeče pro koncový bod s jinou původu *koncový bod* musíte povolit [prostředků mezi zdroji (CORS) pro sdílení obsahu](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="a0904-147">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="a0904-148">Ukázková aplikace předvádí použití CORS v komponentě volání webového rozhraní API (*Pages/CallWebAPI.razor*).</span><span class="sxs-lookup"><span data-stu-id="a0904-148">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="a0904-149">Povolení prostředků různého původu (CORS) žádosti do své aplikace pro sdílení obsahu na jiných serverech, najdete v článku <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="a0904-149">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="a0904-150">Možnosti žádosti HttpClient a objekt HttpRequestMessage načíst rozhraní API</span><span class="sxs-lookup"><span data-stu-id="a0904-150">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="a0904-151">Při spuštění na WebAssembly v aplikaci na straně klienta Blazor, použijte [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> přizpůsobení požadavky.</span><span class="sxs-lookup"><span data-stu-id="a0904-151">When running on WebAssembly in a Blazor client-side app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="a0904-152">Můžete například zadat identifikátoru URI požadavku, metodu HTTP a hlavičky všechny požadované žádosti.</span><span class="sxs-lookup"><span data-stu-id="a0904-152">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="a0904-153">Zadat možnosti žádosti pro základní jazyk JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pomocí `WebAssemblyHttpMessageHandler.FetchArgs` vlastnosti požadavku.</span><span class="sxs-lookup"><span data-stu-id="a0904-153">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="a0904-154">Jak je znázorněno v následujícím příkladu `credentials` vlastnost je nastavena na jednu z následujících hodnot:</span><span class="sxs-lookup"><span data-stu-id="a0904-154">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="a0904-155">`FetchCredentialsOption.Include` ("zahrnutí") &ndash; Výzva prohlížeče k odeslání přihlašovací údaje (například soubory cookie nebo ověřování hlaviček protokolu HTTP) i pro požadavky cross-origin.</span><span class="sxs-lookup"><span data-stu-id="a0904-155">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="a0904-156">Povoleny, pouze je-li povolit přihlašovací údaje jsou nakonfigurované zásady CORS.</span><span class="sxs-lookup"><span data-stu-id="a0904-156">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="a0904-157">`FetchCredentialsOption.Omit` ("vynechat") &ndash; Vás informuje o tom prohlížeč nikdy odeslat přihlašovací údaje (například soubory cookie a hlavičky ověření HTTP).</span><span class="sxs-lookup"><span data-stu-id="a0904-157">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="a0904-158">`FetchCredentialsOption.SameOrigin` ("stejného zdroje") &ndash; Výzva prohlížeče k odeslání přihlašovací údaje (například soubory cookie a hlavičky ověření HTTP), pouze pokud je cílová adresa URL na stejného původu jako volající aplikace.</span><span class="sxs-lookup"><span data-stu-id="a0904-158">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

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

<span data-ttu-id="a0904-159">Další informace o možnostech rozhraní Fetch API najdete v tématu [MDN web dokumentace: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="a0904-159">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="a0904-160">Při odesílání přihlašovací údaje (soubory cookie a hlavičky ověření) u požadavků CORS `Authorization` záhlaví musí být povoleno zásadou CORS.</span><span class="sxs-lookup"><span data-stu-id="a0904-160">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="a0904-161">Zahrnuje tyto zásady konfigurace pro:</span><span class="sxs-lookup"><span data-stu-id="a0904-161">The following policy includes configuration for:</span></span>

* <span data-ttu-id="a0904-162">Požádat o původu (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="a0904-162">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="a0904-163">Libovolné metody (akce).</span><span class="sxs-lookup"><span data-stu-id="a0904-163">Any method (verb).</span></span>
* <span data-ttu-id="a0904-164">`Content-Type` a `Authorization` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="a0904-164">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="a0904-165">Povolit vlastní hlavičky (například `x-custom-header`), vypsat hlavičku při volání metody <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="a0904-165">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="a0904-166">Přihlašovací údaje sadu podle kódu jazyka JavaScript na straně klienta (`credentials` nastavenou na `include`).</span><span class="sxs-lookup"><span data-stu-id="a0904-166">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="a0904-167">Další informace najdete v tématu <xref:security/cors> a součást testování požadavku HTTP ukázkovou aplikaci (*Components/HTTPRequestTester.razor*).</span><span class="sxs-lookup"><span data-stu-id="a0904-167">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0904-168">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a0904-168">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* [<span data-ttu-id="a0904-169">Různé sdílení prostředků zdroji (CORS na W3C)</span><span class="sxs-lookup"><span data-stu-id="a0904-169">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
