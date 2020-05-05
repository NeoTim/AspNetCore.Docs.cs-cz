---
title: Volání webového rozhraní API z ASP.NET Core Blazor WebAssembly
author: guardrex
description: Naučte se volat webové rozhraní API z Blazor aplikace WebAssembly pomocí pomocníků JSON, včetně vytváření žádostí o sdílení prostředků mezi zdroji (CORS).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: d823db3688e05f6befefacc9f390e0dcdbf329a7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767145"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>Volání webového rozhraní API z ASP.NET CoreBlazor

Od [Luke Latham](https://github.com/guardrex), [Daniel Skořepa](https://github.com/danroth27)a [Juan de la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikace WebAssembly volají webová rozhraní API pomocí předem nakonfigurované `HttpClient` služby. [ Blazor ](xref:blazor/hosting-models#blazor-webassembly) Požadavky na sestavení, které mohou zahrnovat možnosti [rozhraní API](https://developer.mozilla.org/docs/Web/API/Fetch_API) pro načtení Blazor JavaScriptu, použití pomocníků <xref:System.Net.Http.HttpRequestMessage>JSON nebo s. `HttpClient` Služba v aplikacích Blazor pro WebAssembly se zaměřuje na poskytování požadavků zpět na server původu. Pokyny v tomto tématu se vztahují pouze na Blazor aplikace WebAssembly.

Serverové aplikace volají webová rozhraní API <xref:System.Net.Http.HttpClient> pomocí instancí, které se <xref:System.Net.Http.IHttpClientFactory>obvykle vytvářejí pomocí. [ Blazor ](xref:blazor/hosting-models#blazor-server) Pokyny v tomto tématu se nevztahují Blazor na serverové aplikace. Při vývoji Blazor serverových aplikací postupujte podle pokynů v <xref:fundamentals/http-requests>části.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample)) &ndash; vyberte aplikaci *BlazorWebAssemblySample* .

Podívejte se na následující komponenty v ukázkové aplikaci *BlazorWebAssemblySample* :

* Volání webového rozhraní API (*Pages/CallWebAPI. Razor*)
* Tester požadavků HTTP (*Components/HTTPRequestTester. Razor*)

## <a name="packages"></a>Balíčky

V souboru projektu se odkázat na balíček NuGet [System .NET. http. JSON](https://www.nuget.org/packages/System.Net.Http.Json/) .

## <a name="add-the-httpclient-service"></a>Přidání služby HttpClient

V `Program.Main`nástroji přidejte `HttpClient` službu, pokud ještě neexistuje:

```csharp
builder.Services.AddTransient(sp => 
    new HttpClient
    {
        BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
    });
```

## <a name="httpclient-and-json-helpers"></a>HttpClient a pomocníky JSON

Blazor V aplikaci WebAssembly je [HttpClient](xref:fundamentals/http-requests) k dispozici jako předkonfigurovaná služba pro podání požadavků zpět na zdrojový server.

Blazor Serverová aplikace ve výchozím nastavení `HttpClient` nezahrnuje službu. `HttpClient` Poskytněte aplikaci s využitím [infrastruktury HttpClient Factory](xref:fundamentals/http-requests).

`HttpClient`a pomocníkům JSON se taky používají k volání koncových bodů webového rozhraní API třetích stran. `HttpClient`je implementováno pomocí [rozhraní API pro načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API) prohlížeče a podléhá jeho omezením, včetně vynucení stejných zásad původu.

Základní adresa klienta je nastavena na adresu původního serveru. Vložení `HttpClient` instance pomocí `@inject` direktivy:

```razor
@using System.Net.Http
@inject HttpClient Http
```

V následujících příkladech zpracovává webové rozhraní API TODO operace vytvoření, čtení, aktualizace a odstranění (CRUD). Příklady jsou založeny na `TodoItem` třídě, která ukládá:

* ID (`Id`, `long`) &ndash; jedinečné ID položky
* Název (`Name`, `string`) &ndash; název položky.
* Stav (`IsComplete`, `bool`) &ndash; označuje, zda je položka TODO dokončena.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

Pomocné metody JSON odesílají požadavky na identifikátor URI (webové rozhraní API v následujících příkladech) a zpracovávají odpověď:

* `GetFromJsonAsync`&ndash; Pošle požadavek HTTP GET a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_todoItems` se zobrazí součást. `GetTodoItems` Metoda je aktivována při vykreslování komponenty ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). Kompletní příklad najdete v ukázkové aplikaci.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetFromJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostAsJsonAsync`&ndash; Pošle požadavek HTTP POST, včetně obsahu kódovaného JSON, a analyzuje tělo odpovědi JSON pro vytvoření objektu.

  V následujícím kódu `_newItemName` je poskytován vázaným prvkem komponenty. `AddItem` Metoda je aktivována výběrem `<button>` prvku. Kompletní příklad najdete v ukázkové aplikaci.

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
  
  Volání `PostAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>. K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

* `PutAsJsonAsync`&ndash; ODEŠLE požadavek HTTP PUT, včetně obsahu kódovaného JSON.

  V následujícím kódu jsou `_editItem` hodnoty pro `Name` a `IsCompleted` poskytovány pomocí vázaných prvků součásti. Položka `Id` je nastavena, když je položka vybrána v jiné části uživatelského rozhraní a `EditItem` je volána. `SaveItem` Metoda je aktivována výběrem možnosti Uložit `<button>` element. Kompletní příklad najdete v ukázkové aplikaci.

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
  
  Volání `PutAsJsonAsync` vrátí <xref:System.Net.Http.HttpResponseMessage>. K deserializaci obsahu JSON ze zprávy odpovědi použijte metodu `ReadFromJsonAsync<T>` rozšíření:
  
  ```csharp
  var content = response.content.ReadFromJsonAsync<WeatherForecast>();
  ```

<xref:System.Net.Http>zahrnuje další metody rozšíření pro posílání požadavků HTTP a příjem odpovědí HTTP. [HttpClient. DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) se používá k odeslání požadavku HTTP Delete webovému rozhraní API.

V následujícím kódu element Delete `<button>` volá `DeleteItem` metodu. Vázaný `<input>` element poskytuje `id` položku, která se má odstranit. Kompletní příklad najdete v ukázkové aplikaci.

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

[Ukázková aplikace WebAssembly (BlazorWebAssemblySample) ukazuje použití CORS v součásti webového rozhraní API volání (Pages/CallWebAPI. Razor). Blazor ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) *Pages/CallWebAPI.razor*

Pokud chcete jiným webům umožnit, aby vaše aplikace provedla požadavky na sdílení prostředků mezi zdroji (CORS <xref:security/cors>), přečtěte si téma.

## <a name="additional-resources"></a>Další zdroje

* <xref:security/blazor/webassembly/index>
* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Konfigurace koncového bodu HTTPS Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [Sdílení prostředků mezi zdroji (CORS) ve W3C](https://www.w3.org/TR/cors/)
