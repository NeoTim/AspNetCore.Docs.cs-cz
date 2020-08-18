---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor WebAssembly aplikace pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: f8c105624506d13f3ea8e963ceb49aeaf6d22a66
ms.sourcegitcommit: dfea24471f4f3d7904faa92fe60c000853bddc3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88504564"
---
# <a name="call-a-web-api-from-aspnet-core-no-locblazor"></a><span data-ttu-id="73e83-103">Volání webového rozhraní API z ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="73e83-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="73e83-104">Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="73e83-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

> [!NOTE]
> <span data-ttu-id="73e83-105">Toto téma se týká Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="73e83-105">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="73e83-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) aplikace volají webová rozhraní API pomocí <xref:System.Net.Http.HttpClient> instancí, které se obvykle vytvářejí pomocí <xref:System.Net.Http.IHttpClientFactory> .</span><span class="sxs-lookup"><span data-stu-id="73e83-106">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances, typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="73e83-107">Pokyny, které se týkají Blazor Server , najdete v tématu <xref:fundamentals/http-requests> .</span><span class="sxs-lookup"><span data-stu-id="73e83-107">For guidance that applies to Blazor Server, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="73e83-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) aplikace volají webová rozhraní API pomocí předem nakonfigurované <xref:System.Net.Http.HttpClient> služby.</span><span class="sxs-lookup"><span data-stu-id="73e83-108">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured <xref:System.Net.Http.HttpClient> service.</span></span> <span data-ttu-id="73e83-109">Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage> .</span><span class="sxs-lookup"><span data-stu-id="73e83-109">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span> <span data-ttu-id="73e83-110"><xref:System.Net.Http.HttpClient>Služba v Blazor WebAssembly aplikacích se zaměřuje na poskytování požadavků zpět na server původu.</span><span class="sxs-lookup"><span data-stu-id="73e83-110">The <xref:System.Net.Http.HttpClient> service in Blazor WebAssembly apps is focused on making requests back to the server of origin.</span></span> <span data-ttu-id="73e83-111">Pokyny v tomto tématu se vztahují jenom na Blazor WebAssembly aplikace.</span><span class="sxs-lookup"><span data-stu-id="73e83-111">The guidance in this topic only pertains to Blazor WebAssembly apps.</span></span>

<span data-ttu-id="73e83-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)): vyberte `BlazorWebAssemblySample` aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73e83-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)): Select the `BlazorWebAssemblySample` app.</span></span>

<span data-ttu-id="73e83-113">Podívejte se na následující komponenty v `BlazorWebAssemblySample` ukázkové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="73e83-113">See the following components in the `BlazorWebAssemblySample` sample app:</span></span>

* <span data-ttu-id="73e83-114">Volání webového rozhraní API ( `Pages/CallWebAPI.razor` )</span><span class="sxs-lookup"><span data-stu-id="73e83-114">Call Web API (`Pages/CallWebAPI.razor`)</span></span>
* <span data-ttu-id="73e83-115">Tester požadavků HTTP ( `Components/HTTPRequestTester.razor` )</span><span class="sxs-lookup"><span data-stu-id="73e83-115">HTTP Request Tester (`Components/HTTPRequestTester.razor`)</span></span>

## <a name="packages"></a><span data-ttu-id="73e83-116">Balíčky</span><span class="sxs-lookup"><span data-stu-id="73e83-116">Packages</span></span>

<span data-ttu-id="73e83-117">Odkázat na [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) balíček NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73e83-117">Reference the [`System.Net.Http.Json`](https://www.nuget.org/packages/System.Net.Http.Json) NuGet package in the project file.</span></span>

## <a name="add-the-httpclient-service"></a><span data-ttu-id="73e83-118">Přidání služby HttpClient</span><span class="sxs-lookup"><span data-stu-id="73e83-118">Add the HttpClient service</span></span>

<span data-ttu-id="73e83-119">V nástroji `Program.Main` přidejte <xref:System.Net.Http.HttpClient> službu, pokud ještě neexistuje:</span><span class="sxs-lookup"><span data-stu-id="73e83-119">In `Program.Main`, add an <xref:System.Net.Http.HttpClient> service if it doesn't already exist:</span></span>

```csharp
builder.Services.AddScoped(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="73e83-120">HttpClient a pomocníky JSON</span><span class="sxs-lookup"><span data-stu-id="73e83-120">HttpClient and JSON helpers</span></span>

<span data-ttu-id="73e83-121">V Blazor WebAssembly aplikaci [`HttpClient`](xref:fundamentals/http-requests) je k dispozici jako předkonfigurovaná služba pro zpracování požadavků zpátky na zdrojový server.</span><span class="sxs-lookup"><span data-stu-id="73e83-121">In a Blazor WebAssembly app, [`HttpClient`](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="73e83-122">Blazor ServerAplikace <xref:System.Net.Http.HttpClient> ve výchozím nastavení neobsahuje službu.</span><span class="sxs-lookup"><span data-stu-id="73e83-122">A Blazor Server app doesn't include an <xref:System.Net.Http.HttpClient> service by default.</span></span> <span data-ttu-id="73e83-123">Poskytněte <xref:System.Net.Http.HttpClient> aplikaci do aplikace pomocí [ `HttpClient` produkční infrastruktury](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="73e83-123">Provide an <xref:System.Net.Http.HttpClient> to the app using the [`HttpClient` factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="73e83-124"><xref:System.Net.Http.HttpClient> a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran.</span><span class="sxs-lookup"><span data-stu-id="73e83-124"><xref:System.Net.Http.HttpClient> and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="73e83-125"><xref:System.Net.Http.HttpClient> je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.</span><span class="sxs-lookup"><span data-stu-id="73e83-125"><xref:System.Net.Http.HttpClient> is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="73e83-126">Základní adresa klienta je nastavena na adresu původního serveru.</span><span class="sxs-lookup"><span data-stu-id="73e83-126">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="73e83-127">Vložení <xref:System.Net.Http.HttpClient> instance pomocí [`@inject`](xref:mvc/views/razor#inject) direktivy:</span><span class="sxs-lookup"><span data-stu-id="73e83-127">Inject an <xref:System.Net.Http.HttpClient> instance using the [`@inject`](xref:mvc/views/razor#inject) directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="73e83-128">V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD).</span><span class="sxs-lookup"><span data-stu-id="73e83-128">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="73e83-129">Příklady jsou založeny na `TodoItem` třídě, která ukládá:</span><span class="sxs-lookup"><span data-stu-id="73e83-129">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="73e83-130">ID ( `Id` , `long` ): jedinečné ID položky.</span><span class="sxs-lookup"><span data-stu-id="73e83-130">ID (`Id`, `long`): Unique ID of the item.</span></span>
* <span data-ttu-id="73e83-131">Název ( `Name` , `string` ): název položky.</span><span class="sxs-lookup"><span data-stu-id="73e83-131">Name (`Name`, `string`): Name of the item.</span></span>
* <span data-ttu-id="73e83-132">Status ( `IsComplete` , `bool` ): indikace, zda je položka TODO dokončena.</span><span class="sxs-lookup"><span data-stu-id="73e83-132">Status (`IsComplete`, `bool`): Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="73e83-133">Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:</span><span class="sxs-lookup"><span data-stu-id="73e83-133">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="73e83-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="73e83-134"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>: Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="73e83-135">V následujícím kódu se `todoItems` zobrazí součást.</span><span class="sxs-lookup"><span data-stu-id="73e83-135">In the following code, the `todoItems` are displayed by the component.</span></span> <span data-ttu-id="73e83-136">`GetTodoItems`Metoda je aktivována, když je dokončena vykreslování součásti ( [`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) ).</span><span class="sxs-lookup"><span data-stu-id="73e83-136">The `GetTodoItems` method is triggered when the component is finished rendering ([`OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="73e83-137">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73e83-137">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="73e83-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Odesílá požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.</span><span class="sxs-lookup"><span data-stu-id="73e83-138"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>: Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="73e83-139">V následujícím kódu `newItemName` je poskytován vázaným prvkem komponenty.</span><span class="sxs-lookup"><span data-stu-id="73e83-139">In the following code, `newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="73e83-140">`AddItem`Metoda je aktivována výběrem `<button>` prvku.</span><span class="sxs-lookup"><span data-stu-id="73e83-140">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="73e83-141">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73e83-141">See the sample app for a complete example.</span></span>

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
  
  <span data-ttu-id="73e83-142">Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="73e83-142">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="73e83-143">K deserializaci obsahu JSON ze zprávy odpovědi použijte `ReadFromJsonAsync<T>` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="73e83-143">To deserialize the JSON content from the response message, use the `ReadFromJsonAsync<T>` extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <span data-ttu-id="73e83-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.</span><span class="sxs-lookup"><span data-stu-id="73e83-144"><xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>: Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="73e83-145">V následujícím kódu `editItem` jsou hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti.</span><span class="sxs-lookup"><span data-stu-id="73e83-145">In the following code, `editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="73e83-146">Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána.</span><span class="sxs-lookup"><span data-stu-id="73e83-146">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="73e83-147">`SaveItem`Metoda je aktivována výběrem možnosti Uložit `<button>` element.</span><span class="sxs-lookup"><span data-stu-id="73e83-147">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="73e83-148">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73e83-148">See the sample app for a complete example.</span></span>

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
          editItem = todoItems.Single(i => i.Id == id);
      }

      private async Task SaveItem() =>
          await Http.PutAsJsonAsync($"api/TodoItems/{editItem.Id}, editItem);
  }
  ```
  
  <span data-ttu-id="73e83-149">Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> .</span><span class="sxs-lookup"><span data-stu-id="73e83-149">Calls to <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> return an <xref:System.Net.Http.HttpResponseMessage>.</span></span> <span data-ttu-id="73e83-150">K deserializaci obsahu JSON ze zprávy odpovědi použijte <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="73e83-150">To deserialize the JSON content from the response message, use the <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> extension method:</span></span>
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

<span data-ttu-id="73e83-151"><xref:System.Net.Http> zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP.</span><span class="sxs-lookup"><span data-stu-id="73e83-151"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="73e83-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> slouží k odeslání požadavku HTTP DELETE webovému rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="73e83-152"><xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType> is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="73e83-153">V následujícím kódu `<button>` element Delete volá `DeleteItem` metodu.</span><span class="sxs-lookup"><span data-stu-id="73e83-153">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="73e83-154">Vázaný `<input>` element poskytuje položku, `id` která se má odstranit.</span><span class="sxs-lookup"><span data-stu-id="73e83-154">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="73e83-155">Kompletní příklad najdete v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="73e83-155">See the sample app for a complete example.</span></span>

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

## <a name="named-httpclient-with-ihttpclientfactory"></a><span data-ttu-id="73e83-156">S názvem HttpClient s IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="73e83-156">Named HttpClient with IHttpClientFactory</span></span>

<span data-ttu-id="73e83-157"><xref:System.Net.Http.IHttpClientFactory> podporují se služby a konfigurace s názvem <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="73e83-157"><xref:System.Net.Http.IHttpClientFactory> services and the configuration of a named <xref:System.Net.Http.HttpClient> are supported.</span></span>

<span data-ttu-id="73e83-158">Odkázat na [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) balíček NuGet v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="73e83-158">Reference the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package in the project file.</span></span>

<span data-ttu-id="73e83-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="73e83-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="73e83-160">`FetchData` součást ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="73e83-160">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecast");
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="73e83-161">Typové HttpClient</span><span class="sxs-lookup"><span data-stu-id="73e83-161">Typed HttpClient</span></span>

<span data-ttu-id="73e83-162"><xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> Pro návratová data z jednoho nebo více koncových bodů webového rozhraní API používá typ jednu nebo více instancí aplikace, výchozí nebo pojmenované.</span><span class="sxs-lookup"><span data-stu-id="73e83-162">Typed <xref:System.Net.Http.HttpClient> uses one or more of the app's <xref:System.Net.Http.HttpClient> instances, default or named, to return data from one or more web API endpoints.</span></span>

<span data-ttu-id="73e83-163">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="73e83-163">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;

public class WeatherForecastClient
{
    private readonly HttpClient client;

    public WeatherForecastClient(HttpClient client)
    {
        this.client = client;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];
    
        try
        {
            forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch
        {
            ...
        }
    
        return forecasts;
    }
}
```

<span data-ttu-id="73e83-164">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="73e83-164">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="73e83-165">Komponenty vkládají typ <xref:System.Net.Http.HttpClient> pro volání webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="73e83-165">Components inject the typed <xref:System.Net.Http.HttpClient> to call the web API.</span></span>

<span data-ttu-id="73e83-166">`FetchData` součást ( `Pages/FetchData.razor` ):</span><span class="sxs-lookup"><span data-stu-id="73e83-166">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Client.GetForecastAsync();
    }
}
```

## <a name="handle-errors"></a><span data-ttu-id="73e83-167">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="73e83-167">Handle errors</span></span>

<span data-ttu-id="73e83-168">Když při interakci s webovým rozhraním API dojde k chybám, mohou být zpracovány vývojářským kódem.</span><span class="sxs-lookup"><span data-stu-id="73e83-168">When errors occur while interacting with a web API, they can be handled by developer code.</span></span> <span data-ttu-id="73e83-169">Například <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> očekává odpověď JSON z rozhraní API serveru s `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="73e83-169">For example, <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> expects a JSON response from the server API with a `Content-Type` of `application/json`.</span></span> <span data-ttu-id="73e83-170">Pokud odpověď není ve formátu JSON, ověření obsahu vyvolá <xref:System.NotSupportedException> .</span><span class="sxs-lookup"><span data-stu-id="73e83-170">If the response isn't in JSON format, content validation throws a <xref:System.NotSupportedException>.</span></span>

<span data-ttu-id="73e83-171">V následujícím příkladu je koncový bod identifikátoru URI pro požadavek na data předpovědi počasí špatně napsaný.</span><span class="sxs-lookup"><span data-stu-id="73e83-171">In the following example, the URI endpoint for the weather forecast data request is misspelled.</span></span> <span data-ttu-id="73e83-172">Identifikátor URI by měl být, `WeatherForecast` ale zobrazí se ve volání jako `WeatherForcast` (chybí "e").</span><span class="sxs-lookup"><span data-stu-id="73e83-172">The URI should be to `WeatherForecast` but appears in the call as `WeatherForcast` (missing "e").</span></span>

<span data-ttu-id="73e83-173"><xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Volání očekává vrácení formátu JSON, ale server vrátí kód HTML pro neošetřenou výjimku na serveru s `Content-Type` `text/html` .</span><span class="sxs-lookup"><span data-stu-id="73e83-173">The <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> call expects JSON to be returned, but the server returns HTML for an unhandled exception on the server with a `Content-Type` of `text/html`.</span></span> <span data-ttu-id="73e83-174">Neošetřená výjimka probíhá na serveru, protože nebyla nalezena cesta a middleware nemůže pro požadavek obsluhovat stránku nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="73e83-174">The unhandled exception occurs on the server because the path isn't found and middleware can't serve a page or view for the request.</span></span>

<span data-ttu-id="73e83-175">V <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> klientovi <xref:System.NotSupportedException> je vyvolána, když je obsah odpovědi ověřen jako jiný než JSON.</span><span class="sxs-lookup"><span data-stu-id="73e83-175">In <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> on the client, <xref:System.NotSupportedException> is thrown when the response content is validated as non-JSON.</span></span> <span data-ttu-id="73e83-176">Výjimka je zachycena v `catch` bloku, kde vlastní logika by mohla protokolovat chybu nebo prezentovat uživateli popisnou chybovou zprávu:</span><span class="sxs-lookup"><span data-stu-id="73e83-176">The exception is caught in the `catch` block, where custom logic could log the error or present a friendly error message to the user:</span></span>

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
> <span data-ttu-id="73e83-177">Předchozí příklad je pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="73e83-177">The preceding example is for demonstration purposes.</span></span> <span data-ttu-id="73e83-178">Serverová aplikace webového rozhraní API se dá nakonfigurovat tak, aby vracela JSON, i když koncový bod neexistuje nebo dojde k neošetřené výjimce na serveru.</span><span class="sxs-lookup"><span data-stu-id="73e83-178">A web API server app can be configured to return JSON even when an endpoint doesn't exist or an unhandled exception on the server occurs.</span></span>

<span data-ttu-id="73e83-179">Další informace naleznete v tématu <xref:blazor/fundamentals/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="73e83-179">For more information, see <xref:blazor/fundamentals/handle-errors>.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="73e83-180">Sdílení prostředků mezi zdroji (CORS)</span><span class="sxs-lookup"><span data-stu-id="73e83-180">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="73e83-181">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje.</span><span class="sxs-lookup"><span data-stu-id="73e83-181">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="73e83-182">Toto omezení se nazývá *zásady stejného původu*.</span><span class="sxs-lookup"><span data-stu-id="73e83-182">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="73e83-183">Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality.</span><span class="sxs-lookup"><span data-stu-id="73e83-183">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="73e83-184">Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).</span><span class="sxs-lookup"><span data-stu-id="73e83-184">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="73e83-185">[ Blazor WebAssembly Ukázková aplikace ( Blazor WebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti volání webového rozhraní API ( `Pages/CallWebAPI.razor` ).</span><span class="sxs-lookup"><span data-stu-id="73e83-185">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (`Pages/CallWebAPI.razor`).</span></span>

<span data-ttu-id="73e83-186">Další informace o CORS se zabezpečenými požadavky v Blazor aplikacích najdete v tématu <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors> .</span><span class="sxs-lookup"><span data-stu-id="73e83-186">For more information on CORS with secure requests in Blazor apps, see <xref:blazor/security/webassembly/additional-scenarios#cross-origin-resource-sharing-cors>.</span></span>

<span data-ttu-id="73e83-187">Obecné informace o CORS s ASP.NET Core aplikacemi najdete v tématu <xref:security/cors> .</span><span class="sxs-lookup"><span data-stu-id="73e83-187">For general information on CORS with ASP.NET Core apps, see <xref:security/cors>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73e83-188">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="73e83-188">Additional resources</span></span>

* <span data-ttu-id="73e83-189"><xref:blazor/security/webassembly/additional-scenarios>: Obsahuje pokrytí použití <xref:System.Net.Http.HttpClient> pro zabezpečené požadavky webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="73e83-189"><xref:blazor/security/webassembly/additional-scenarios>: Includes coverage on using <xref:System.Net.Http.HttpClient> to make secure web API requests.</span></span>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="73e83-190">Konfigurace koncového bodu HTTPS Kestrel</span><span class="sxs-lookup"><span data-stu-id="73e83-190">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="73e83-191">Sdílení prostředků mezi zdroji (CORS) ve W3C</span><span class="sxs-lookup"><span data-stu-id="73e83-191">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
