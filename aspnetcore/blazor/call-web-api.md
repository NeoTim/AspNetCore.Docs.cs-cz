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
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Volání webového rozhraní API z ASP.NET CoreBlazor

[Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), a Juan De [la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikace WebAssembly volají webová api `HttpClient` pomocí předkonfigurované služby. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Komponovat požadavky, které mohou zahrnovat možnosti Blazor [javascriptu fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pomocí pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>. Služba `HttpClient` v Blazor aplikacích WebAssembly se zaměřuje na vytváření požadavků zpět na server původu. Pokyny v tomto tématu se vymýšleny pouze v Blazor aplikacích WebAssembly.

Serverové aplikace volají <xref:System.Net.Http.HttpClient> webová api pomocí <xref:System.Net.Http.IHttpClientFactory>instancí, které se obvykle vytvářejí pomocí . [ Blazor ](xref:blazor/hosting-models#blazor-server) Pokyny v tomto tématu se nejedná Blazor o serverové aplikace. Při Blazor vývoji serverových aplikací <xref:fundamentals/http-requests>postupujte podle pokynů v aplikaci .

[Zobrazit nebo stáhnout ukázkový](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) kód &ndash; ( jak[stáhnout](xref:index#how-to-download-a-sample)) Vyberte aplikaci *BlazorWebAssemblySample.*

Podívejte se na následující součásti v ukázkové aplikaci *BlazorWebAssemblySample:*

* Call Web API *(Stránky/CallWebAPI.razor)*
* Http Request Tester *(Components/HTTPRequestTester.razor)*

## <a name="packages"></a>Balíčky

Odkaz na balíček [System.Net.Http.Json](https://www.nuget.org/packages/System.Net.Http.Json/) NuGet v souboru projektu.

## <a name="add-the-httpclient-service"></a>Přidání služby HttpClient

V `Program.Main`aplikaci `HttpClient` přidejte službu, pokud ještě neexistuje:

```csharp
builder.Services.AddSingleton(
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>Pomocníci httpclient a json

V Blazor aplikaci WebAssembly [httpclient](xref:fundamentals/http-requests) je k dispozici jako předkonfigurovaná služba pro vytváření požadavků zpět na zdrojový server.

Serverová Blazor aplikace ve výchozím `HttpClient` nastavení neobsahuje službu. Poskytněte aplikaci pomocí [tovární infrastruktury HttpClient](xref:fundamentals/http-requests). `HttpClient`

`HttpClient`a pomocné sypače JSON se také používají k volání koncových bodů webového rozhraní API třetích stran. `HttpClient`je implementována pomocí rozhraní [API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejné zásady původu.

Základní adresa klienta je nastavena na adresu původního serveru. `HttpClient` Vstříkněte `@inject` instanci pomocí směrnice:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech procesy webového rozhraní API todo vytvořit, číst, aktualizovat a odstranit (CRUD) operace. Příklady jsou založeny `TodoItem` na třídě, která ukládá:

* ID`Id`( `long` &ndash; , ) Jedinečné ID položky.
* Název`Name`( `string` &ndash; , ) Název položky.
* Stav`IsComplete`( `bool` &ndash; , ) Označte, zda je položka Todo dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky uri (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* `GetFromJsonAsync`&ndash; Odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON k vytvoření objektu.

  V následujícím kódu `_todoItems` jsou zobrazeny komponenty. Metoda `GetTodoItems` se aktivuje po dokončení vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Úplný příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON k vytvoření objektu.

  V následujícím kódu `_newItemName` je poskytována vázaný prvek komponenty. Metoda `AddItem` se aktivuje výběrem `<button>` prvku. Úplný příklad najdete v ukázkové aplikaci.

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
  
  Volání `PostAsJsonAsync` pro <xref:System.Net.Http.HttpResponseMessage>návrat . Chcete-li dekonstruovat obsah JSON ze `ReadFromJsonAsync<T>` zprávy odpovědi, použijte metodu rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; Odešle požadavek HTTP PUT, včetně obsahu kódovaného protokolem JSON.

  V následujícím kódu `_editItem` jsou `Name` `IsCompleted` hodnoty pro a jsou poskytovány vázané prvky komponenty. Položka `Id` je nastavena, když je položka vybrána v `EditItem` jiné části ui a je volána. Metoda `SaveItem` se spustí výběrem `<button>` Uložit prvek. Úplný příklad najdete v ukázkové aplikaci.

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
  
  Volání `PutAsJsonAsync` pro <xref:System.Net.Http.HttpResponseMessage>návrat . Chcete-li dekonstruovat obsah JSON ze `ReadFromJsonAsync<T>` zprávy odpovědi, použijte metodu rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>obsahuje další metody rozšíření pro odesílání požadavků HTTP a příjem odpovědí HTTP. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) slouží k odeslání požadavku HTTP DELETE do webového rozhraní API.

V následujícím kódu delete `<button>` element `DeleteItem` volá metodu. Vázaný `<input>` prvek dodává `id` položku odstranit. Úplný příklad najdete v ukázkové aplikaci.

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

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení zdrojů mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v podávání žádostí do jiné domény, než je doména, která webovou stránku obsluhovala. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původu brání škodlivému webu ve čtení citlivých dat z jiného webu. Chcete-li vytvořit požadavky z prohlížeče na koncový bod s jiným původem, *koncový bod* musí povolit sdílení prostředků [mezi původem (CORS).](https://www.w3.org/TR/cors/)

[ Blazor Ukázková aplikace WebAssembly (BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstruje použití CORS v komponentě rozhraní API volání web *(Pages/CallWebAPI.razor).*

Pokud chcete jiným webům povolit, aby do vaší aplikace mohly provádět požadavky na sdílení prostředků napříč zdroji (CORS), přečtěte si další informace o tom, že <xref:security/cors>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient a HttpRequestMessage s možnostmi požadavku na načtení rozhraní API

Při spuštění na webassembly v aplikaci Blazor WebAssembly použijte [httpclient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage> přizpůsobit požadavky. Můžete například zadat identifikátor URI požadavku, metodu HTTP a všechny požadované hlavičky požadavku.

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

Další informace o možnostech načtení rozhraní API naleznete v [tématu MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Při odesílání pověření (autorizační ch cookies/záhlaví) na `Authorization` požadavky CORS musí být hlavička povolena zásadami CORS.

Následující zásady zahrnují konfiguraci pro:

* Původ žádosti`http://localhost:5000`( `https://localhost:5001`, ).
* Libovolná metoda (sloveso).
* `Content-Type`a `Authorization` záhlaví. Chcete-li povolit vlastní `x-custom-header`záhlaví (například <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>), uveďte záhlaví při volání .
* Pověření nastavená pomocí kódu JavaScript`credentials` u `include`klienta ( vlastnost nastavená na ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Další informace naleznete <xref:security/cors> v tématu a součást http testeru ukázkové aplikace *(Components/HTTPRequestTester.razor).*

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu Kestrel HTTPS](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Cross Origin Resource Sharing (CORS) ve společnosti W3C](https://www.w3.org/TR/cors/)
