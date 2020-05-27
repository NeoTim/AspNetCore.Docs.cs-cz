---
title: ' volání webového rozhraní API z ASP.NET Coreho Blazor WebAssembly ' Author: Description: ' Naučte se volat webové rozhraní API z Blazor aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS). '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Volání webového rozhraní API z ASP.NET CoreBlazor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)

Aplikace [ Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) volají webová rozhraní API pomocí předem nakonfigurované <xref:System.Net.Http.HttpClient> služby. Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, použití Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage> . <xref:System.Net.Http.HttpClient>Služba v Blazor aplikacích pro WebAssembly se zaměřuje na poskytování požadavků zpět na server původu. Pokyny v tomto tématu se vztahují pouze na Blazor aplikace WebAssembly.

[ Blazor Serverové](xref:blazor/hosting-models#blazor-server) aplikace volají webová rozhraní API pomocí <xref:System.Net.Http.HttpClient> instancí, které se obvykle vytvářejí pomocí <xref:System.Net.Http.IHttpClientFactory> . Pokyny v tomto tématu se nevztahují na Blazor serverové aplikace. Při vývoji Blazor serverových aplikací postupujte podle pokynů v části <xref:fundamentals/http-requests> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)): vyberte aplikaci *BlazorWebAssemblySample* .

Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :

* Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)
* Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Balíčky

V souboru projektu se odkázat na balíček NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) .

## <a name="add-the-httpclient-service"></a>Přidání služby HttpClient

V nástroji `Program.Main` přidejte <xref:System.Net.Http.HttpClient> službu, pokud ještě neexistuje:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

V Blazor aplikaci WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.

BlazorServerová aplikace <xref:System.Net.Http.HttpClient> ve výchozím nastavení nezahrnuje službu. Poskytněte <xref:System.Net.Http.HttpClient> aplikaci s využitím [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).

<xref:System.Net.Http.HttpClient>a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran. <xref:System.Net.Http.HttpClient>je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. Vložení <xref:System.Net.Http.HttpClient> instance pomocí [`@inject`](xref:mvc/views/razor#inject) direktivy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD). Příklady jsou založeny na `TodoItem` třídě, která ukládá:

* ID ( `Id` , `long` ) &ndash; jedinečné ID položky
* Název ( `Name` , `string` ) &ndash; název položky.
* Stav ( `IsComplete` , `bool` ) &ndash; označuje, zda je položka TODO dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>&ndash;Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu se `todoItems` zobrazí součást. `GetTodoItems`Metoda je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] todoItems;

      protected override async Task OnInitializedAsync() => 
          todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A>&ndash;Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `newItemName` je poskytován vázaným prvkem komponenty. `AddItem`Metoda je aktivována výběrem `<button>` prvku. Kompletní příklad najdete v ukázkové aplikaci.

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
  
  Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PostAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> . K deserializaci obsahu JSON ze zprávy odpovědi použijte `ReadFromJsonAsync<T>` metodu rozšíření:
  
  ```csharp
  var content = response.Content.ReadFromJsonAsync<WeatherForecast>();
  ```

* <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A>&ndash;Odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu `editItem` jsou hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti. Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. `SaveItem`Metoda je aktivována výběrem možnosti Uložit `<button>` element. Kompletní příklad najdete v ukázkové aplikaci.

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
  
  Volání <xref:System.Net.Http.Json.HttpClientJsonExtensions.PutAsJsonAsync%2A> vrátí <xref:System.Net.Http.HttpResponseMessage> . K deserializaci obsahu JSON ze zprávy odpovědi použijte <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> metodu rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. <xref:System.Net.Http.HttpClient.DeleteAsync%2A?displayProperty=nameWithType>slouží k odeslání požadavku HTTP DELETE webovému rozhraní API.

V následujícím kódu `<button>` element Delete volá `DeleteItem` metodu. Vázaný `<input>` element poskytuje položku, `id` která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

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

## <a name="named-httpclient-with-ihttpclientfactory"></a>S názvem HttpClient s IHttpClientFactory

<xref:System.Net.Http.IHttpClientFactory>podporují se služby a konfigurace s názvem <xref:System.Net.Http.HttpClient> .

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

`FetchData`součást (*Pages/FetchData. Razor*):

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

## <a name="typed-httpclient"></a>Typové HttpClient

<xref:System.Net.Http.HttpClient> <xref:System.Net.Http.HttpClient> Pro návratová data z jednoho nebo více koncových bodů webového rozhraní API používá typ jednu nebo více instancí aplikace, výchozí nebo pojmenované.

*WeatherForecastClient.cs*:

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

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Komponenty vkládají typ <xref:System.Net.Http.HttpClient> pro volání webového rozhraní API.

`FetchData`součást (*Pages/FetchData. Razor*):

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

## <a name="handle-errors"></a>Ošetření chyb

Když při interakci s webovým rozhraním API dojde k chybám, mohou být zpracovány vývojářským kódem. Například <xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A> očekává odpověď JSON z rozhraní API serveru s `Content-Type` `application/json` . Pokud odpověď není ve formátu JSON, ověření obsahu vyvolá <xref:System.NotSupportedException> .

V následujícím příkladu je koncový bod identifikátoru URI pro požadavek na data předpovědi počasí špatně napsaný. Identifikátor URI by měl být, `WeatherForecast` ale zobrazí se ve volání jako `WeatherForcast` (chybí "e").

<xref:System.Net.Http.Json.HttpClientJsonExtensions.GetFromJsonAsync%2A>Volání očekává vrácení formátu JSON, ale server vrátí kód HTML pro neošetřenou výjimku na serveru s `Content-Type` `text/html` . Neošetřená výjimka probíhá na serveru, protože nebyla nalezena cesta a middleware nemůže pro požadavek obsluhovat stránku nebo zobrazení.

V <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> klientovi <xref:System.NotSupportedException> je vyvolána, když je obsah odpovědi ověřen jako jiný než JSON. Výjimka je zachycena v `catch` bloku, kde vlastní logika by mohla protokolovat chybu nebo prezentovat uživateli popisnou chybovou zprávu:

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
> Předchozí příklad je pro demonstrační účely. Aplikaci webového rozhraní API je možné nakonfigurovat tak, aby vracela JSON i v případě, že koncový bod neexistuje nebo když dojde k neošetřené výjimky na serveru.

Další informace naleznete v tématu <xref:blazor/handle-errors>.

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

[ Blazor Ukázková aplikace WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti webového rozhraní API volání (*Pages/CallWebAPI. Razor*).

Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS), přečtěte si téma <xref:security/cors> .

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/webassembly/additional-scenarios>&ndash;Zahrnuje pokrytí použití <xref:System.Net.Http.HttpClient> pro zabezpečené požadavky webového rozhraní API.
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
