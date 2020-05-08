---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 7476e9dce3fa26948d2091235747f893d805d7be
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967269"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="450f2-103">Volání webového rozhraní API z ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="450f2-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="450f2-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="450f2-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="450f2-105">Aplikace WebAssembly volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly)</span><span class="sxs-lookup"><span data-stu-id="450f2-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="450f2-106">Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro načtení Blazor JavaScriptu, použití pomocníků <xref:System.Net.Http.HttpRequestMessage>JSON nebo s.</span><span class="sxs-lookup"><span data-stu-id="450f2-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="450f2-107">`HttpClient` Služba v aplikacích Blazor pro WebAssembly se zaměřuje na poskytování požadavků zpět na server původu.</span><span class="sxs-lookup"><span data-stu-id="450f2-107">The `HttpClient` service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="450f2-108">Pokyny v tomto tématu se vztahují pouze na Blazor aplikace WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="450f2-108">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="450f2-109">Serverové aplikace volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí, které se <xref:System.Net.Http.IHttpClientFactory>obvykle vytvářejí pomocí. [ Blazor ](xref:blazor/hosting-models#blazor-server)</span><span class="sxs-lookup"><span data-stu-id="450f2-109">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="450f2-110">Pokyny v tomto tématu se nevztahují Blazor na serverové aplikace.</span><span class="sxs-lookup"><span data-stu-id="450f2-110">The guidance in this topic doesn't pertain to Blazor Server apps.</span></span> <span data-ttu-id="450f2-111">Při vývoji Blazor serverových aplikací postupujte podle pokynů v <xref:fundamentals/http-requests>části.</span><span class="sxs-lookup"><span data-stu-id="450f2-111">When developing Blazor Server apps, follow the guidance in <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="450f2-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .</span><span class="sxs-lookup"><span data-stu-id="450f2-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="450f2-113">Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :</span><span class="sxs-lookup"><span data-stu-id="450f2-113">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="450f2-114">Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)</span><span class="sxs-lookup"><span data-stu-id="450f2-114">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="450f2-115">Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)</span><span class="sxs-lookup"><span data-stu-id="450f2-115">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="450f2-116">Balíčky</span><span class="sxs-lookup"><span data-stu-id="450f2-116">Packages</span></span>

<span data-ttu-id="450f2-117">V souboru projektu se odkázat na balíček NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) .</span><span class="sxs-lookup"><span data-stu-id="450f2-117">Reference the [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="450f2-118">Přidání služby HttpClient</span><span class="sxs-lookup"><span data-stu-id="450f2-118">Add the HttpClient service</span></span>

<span data-ttu-id="450f2-119">V `Program.Main`nástroji přidejte `HttpClient` službu, pokud ještě neexistuje:</span><span class="sxs-lookup"><span data-stu-id="450f2-119">In `Program.Main`, add an `HttpClient` service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="450f2-120">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="450f2-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="450f2-121">Blazor V aplikaci WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="450f2-121">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="450f2-122">Blazor Serverová aplikace ve výchozím nastavení `HttpClient` nezahrnuje službu.</span><span class="sxs-lookup"><span data-stu-id="450f2-122">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="450f2-123">`HttpClient` Poskytněte aplikaci s využitím [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="450f2-123">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="450f2-124">`HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="450f2-124">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="450f2-125">`HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="450f2-125">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="450f2-126">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="450f2-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="450f2-127">Vložení `HttpClient` instance pomocí `@inject` direktivy:</span><span class="sxs-lookup"><span data-stu-id="450f2-127">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="450f2-128">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="450f2-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="450f2-129">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="450f2-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="450f2-130">ID (`Id`, `long`) &ndash; jedinečné ID položky</span><span class="sxs-lookup"><span data-stu-id="450f2-130">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="450f2-131">Název (`Name`, `string`) &ndash; název položky.</span><span class="sxs-lookup"><span data-stu-id="450f2-131">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="450f2-132">Stav (`IsComplete`, `bool`) &ndash; označuje, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="450f2-132">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="450f2-133">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="450f2-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="450f2-134">`GetFromJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="450f2-134">`GetFromJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="450f2-135">V následujícím kódu `todoItems` se zobrazí součást.</span><span class="sxs-lookup"><span data-stu-id="450f2-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="450f2-136">`GetTodoItems` Metoda je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span><span class="sxs-lookup"><span data-stu-id="450f2-136">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="450f2-137">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="450f2-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="450f2-138">`PostAsJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="450f2-138">`PostAsJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="450f2-139">V následujícím kódu `newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="450f2-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="450f2-140">`AddItem` Metoda je aktivována výběrem `<button>` prvku.</span><span class="sxs-lookup"><span data-stu-id="450f2-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="450f2-141">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="450f2-141">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = newItemName, IsComplete = false };
          await Http.PostAsJsonAsync("api/TodoItems", addItem);
      }
  }
  ```
  
  <span data-ttu-id="450f2-142">Volání `PostAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="450f2-142">Calls to `PostAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="450f2-143">K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="450f2-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="450f2-144">`PutAsJsonAsync`&ndash; ODEŠLE požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="450f2-144">`PutAsJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="450f2-145">V následujícím kódu jsou `editItem` hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti.</span><span class="sxs-lookup"><span data-stu-id="450f2-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="450f2-146">Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="450f2-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="450f2-147">`SaveItem` Metoda je aktivována výběrem možnosti Uložit `<button>` element.</span><span class="sxs-lookup"><span data-stu-id="450f2-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="450f2-148">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="450f2-148">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="editItem.IsComplete" />
  <input @bind="editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = todoItems.Single(i => i.Id == id);
          editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="450f2-149">Volání `PutAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>.</span><span class="sxs-lookup"><span data-stu-id="450f2-149">Calls to `PutAsJsonAsync` return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="450f2-150">K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="450f2-150">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="450f2-151"><xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="450f2-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="450f2-152">[HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="450f2-152">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="450f2-153">V následujícím kódu element Delete `<button>` volá `DeleteItem` metodu.</span><span class="sxs-lookup"><span data-stu-id="450f2-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="450f2-154">Vázaný `<input>` element poskytuje `id` položku, která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="450f2-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="450f2-155">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="450f2-155">See the sample app for a complete example.</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{id}");
}
```

## <a name="handle-errors"></a><span data-ttu-id="450f2-156">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="450f2-156">Handle errors</span></span>

<span data-ttu-id="450f2-157">Když při interakci s webovým rozhraním API dojde k chybám, mohou být zpracovány vývojářským kódem.</span><span class="sxs-lookup"><span data-stu-id="450f2-157">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="450f2-158">Například `GetFromJsonAsync` očekává odpověď JSON z rozhraní API serveru s `Content-Type` `application/json`.</span><span class="sxs-lookup"><span data-stu-id="450f2-158">For example, `GetFromJsonAsync` expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="450f2-159">Pokud odpověď není ve formátu JSON, ověření obsahu vyvolá <xref:System.NotSupportedException>.</span><span class="sxs-lookup"><span data-stu-id="450f2-159">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="450f2-160">V následujícím příkladu je koncový bod identifikátoru URI pro požadavek na data předpovědi počasí špatně napsaný.</span><span class="sxs-lookup"><span data-stu-id="450f2-160">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="450f2-161">Identifikátor URI by měl být `WeatherForecast` , ale zobrazí se ve volání `WeatherForcast` jako (chybí "e").</span><span class="sxs-lookup"><span data-stu-id="450f2-161">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="450f2-162">`GetFromJsonAsync` Volání očekává vrácení formátu JSON, ale server vrátí kód HTML pro neošetřenou výjimku na serveru s `Content-Type` `text/html`.</span><span class="sxs-lookup"><span data-stu-id="450f2-162">The `GetFromJsonAsync` call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="450f2-163">Neošetřená výjimka probíhá na serveru, protože nebyla nalezena cesta a middleware nemůže pro požadavek obsluhovat stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="450f2-163">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="450f2-164">V `OnInitializedAsync` klientovi je vyvolána, <xref:System.NotSupportedException> když je obsah odpovědi ověřen jako jiný než JSON.</span><span class="sxs-lookup"><span data-stu-id="450f2-164">In `OnInitializedAsync` on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="450f2-165">Výjimka je zachycena v `catch` bloku, kde vlastní logika by mohla protokolovat chybu nebo prezentovat uživateli popisnou chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="450f2-165">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForcast");
    }
    catch (NotSupportedException exception)
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="450f2-166">Předchozí příklad je pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="450f2-166">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="450f2-167">Aplikaci webového rozhraní API je možné nakonfigurovat tak, aby vracela JSON i v případě, že koncový bod neexistuje nebo když dojde k neošetřené výjimky na serveru.</span><span class="sxs-lookup"><span data-stu-id="450f2-167">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled excpetion on the server occurs.</span></span>

<span data-ttu-id="450f2-168">Další informace naleznete v tématu <xref:blazor/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="450f2-168">For more information, see <xref:blazor/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="450f2-169">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="450f2-169">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="450f2-170">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="450f2-170">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="450f2-171">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="450f2-171">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="450f2-172">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="450f2-172">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="450f2-173">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="450f2-173">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="450f2-174">[Ukázková aplikace WebAssembly (BlazorWebAssemblySample) ukazuje použití CORS v součásti webového rozhraní API volání (Pages/CallWebAPI. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*</span><span class="sxs-lookup"><span data-stu-id="450f2-174">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="450f2-175">Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.</span><span class="sxs-lookup"><span data-stu-id="450f2-175">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="450f2-176">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="450f2-176">Additional resources</span></span>

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="450f2-177">Konfigurace koncového bodu HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="450f2-177">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="450f2-178">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="450f2-178">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
