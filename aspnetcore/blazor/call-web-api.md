---
title: Volání webového rozhraní API z ASP.NET Core Blazor
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: 345fb6962e3376c22551eb7914c70c89cb7100d5
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213272"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>Volání webového rozhraní API z ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor aplikace WebAssembly](xref:blazor/hosting-models#blazor-webassembly) volají webová rozhraní API pomocí předkonfigurované služby `HttpClient`. Vytvářené požadavky, které mohou zahrnovat možnosti [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) JavaScriptu, pomocí Blazor pomocníků JSON nebo s <xref:System.Net.Http.HttpRequestMessage>. Služba `HttpClient` v Blazor aplikace WebAssembly se zaměřuje na poskytování požadavků zpět na server původu. Pokyny v tomto tématu se vztahují pouze na Blazor aplikace WebAssembly.

[Blazor serverové](xref:blazor/hosting-models#blazor-server) aplikace volají webová rozhraní API pomocí instancí <xref:System.Net.Http.HttpClient>, obvykle vytvořené pomocí <xref:System.Net.Http.IHttpClientFactory>. Pokyny v tomto tématu se nevztahují na Blazor serverových aplikací. Při vývoji aplikací Blazor serveru postupujte podle pokynů v <xref:fundamentals/http-requests>.

[Zobrazte nebo Stáhněte ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([jak si ho stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .

Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :

* Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)
* Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Balíčky

Odkázat na *experimentální* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) balíček NuGet v souboru projektu `Microsoft.AspNetCore.Blazor.HttpClient` je založen na `HttpClient` a [System. text. JSON](https://www.nuget.org/packages/System.Text.Json/).

Chcete-li použít stabilní rozhraní API, použijte balíček [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) , který používá [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/)/[JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Použití stabilního rozhraní API v `Microsoft.AspNet.WebApi.Client` neposkytuje pomocníkům JSON, které jsou popsány v tomto tématu, které jsou jedinečné pro experimentální `Microsoft.AspNetCore.Blazor.HttpClient` balíček.

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

V aplikaci Blazor WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.

Aplikace Blazor serveru ve výchozím nastavení neobsahuje službu `HttpClient`. Poskytněte `HttpClient` k aplikaci pomocí [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).

`HttpClient` a pomocníky JSON se také používají k volání koncových bodů webového rozhraní API třetích stran. `HttpClient` je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. Vložení instance `HttpClient` pomocí direktivy `@inject`:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD). Příklady jsou založeny na `TodoItem` třídě, která ukládá:

* ID (`Id`, `long`) &ndash; jedinečné ID položky.
* Název (`Name`, `string`) &ndash; název položky.
* Stav (`IsComplete`, `bool`) &ndash; indikaci, zda je položka TODO dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* `GetJsonAsync` &ndash; odešle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu jsou `_todoItems` zobrazeny komponentou. Metoda `GetTodoItems` je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; odešle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty. Metoda `AddItem` je aktivována výběrem `<button>` elementu. Kompletní příklad najdete v ukázkové aplikaci.

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

* `PutJsonAsync` &ndash; odešle požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu `_editItem` hodnoty pro `Name` a `IsCompleted` poskytují vázané prvky komponenty. `Id` položky je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. Metoda `SaveItem` je aktivována výběrem prvku uložit `<button>`. Kompletní příklad najdete v ukázkové aplikaci.

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

<xref:System.Net.Http> zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.

V následujícím kódu element Delete `<button>` volá metodu `DeleteItem`. Vázaný `<input>` element poskytuje `id` položky, která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

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

## <a name="cross-origin-resource-sharing-cors"></a>Sdílení prostředků mezi zdroji (CORS)

Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhuje. Toto omezení se nazývá *zásady stejného původu*. Zásady stejného původce brání škodlivému webu v čtení citlivých dat z jiné lokality. Aby bylo možné podávat požadavky z prohlížeče na koncový bod s jiným zdrojem, musí *koncový bod* umožňovat [sdílení prostředků mezi zdroji (CORS)](https://www.w3.org/TR/cors/).

[Ukázková aplikaceBlazor WebAssembly (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ukazuje použití CORS v součásti webového rozhraní API volání (*Pages/CallWebAPI. Razor*).

Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS), přečtěte si téma <xref:security/cors>.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient a zprávy HttpRequestMessage s možnostmi žádosti o rozhraní API pro načtení

Při spuštění na WebAssembly v Blazor aplikaci WebAssembly použijte k přizpůsobení žádostí [HttpClient](xref:fundamentals/http-requests) a <xref:System.Net.Http.HttpRequestMessage>. Můžete například zadat identifikátor URI žádosti, metodu HTTP a všechny požadované hlavičky požadavků.

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

Další informace o možnostech načtení rozhraní API naleznete v tématu [MDN web Docs: WindowOrWorkerGlobalScope. Fetch ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

Při odesílání přihlašovacích údajů (autorizační soubory cookie/hlavičky) na žádosti CORS musí být záhlaví `Authorization` povoleno zásadami CORS.

Následující zásady zahrnují konfiguraci pro:

* Původ žádosti (`http://localhost:5000`, `https://localhost:5001`).
* Libovolná metoda (příkaz).
* hlavičky `Content-Type` a `Authorization`. Pokud chcete pro vlastní hlavičku (například `x-custom-header`), vypište při volání <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>hlavičku.
* Přihlašovací údaje nastavené kódem JavaScriptu na straně klienta (`credentials` vlastnost nastavenou na `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Další informace najdete v tématu <xref:security/cors> a součásti testera požadavku HTTP ukázkové aplikace (*Components/HTTPRequestTester. Razor*).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
