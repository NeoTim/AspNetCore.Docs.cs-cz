---
page_type: sample
description: Tento kurz ukazuje, jak vytvořit ASP.NET základní webové rozhraní API pomocí databáze MongoDB NoSQL.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
urlFragment: aspnetcore-webapi-mongodb
ms.openlocfilehash: 09d73e25667822b8748a00cc76ad6d4f0e5fe290
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511402"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API s ASP.NET Core a MongoDB

Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu se naučíte:

* Konfigurace mongoDB
* Vytvoření databáze MongoDB
* Definování kolekce a schématu MongoDB
* Provádění operací MongoDB CRUD z webového rozhraní API
* Přizpůsobení serializace JSON

## <a name="prerequisites"></a>Požadavky

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019 Preview](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&ch=pre&rel=16&utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019preview) s **ASP.NET a zatížením pro vývoj webů**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

## <a name="configure-mongodb"></a>Konfigurace mongoDB

Pokud používáte systém Windows, MongoDB je nainstalován na *C:\\Program Files\\MongoDB* ve výchozím nastavení. Přidat *C:\\\\Program Files\\MongoDB\\\<Server version_number>\\přihrádku* `Path` do proměnné prostředí. Tato změna umožňuje mongoDB přístup z libovolného místa na vývojovém počítači.

Pomocí prostředí mongo v následujících krocích vytvořte databázi, vytvořte kolekce a uložte dokumenty. Další informace o příkazech mongo Shell naleznete [v tématu Práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Zvolte adresář ve vývojovém počítači pro ukládání dat. Například *C:\\BooksData v* systému Windows. Vytvořte adresář, pokud neexistuje. Mongo Shell nevytváří nové adresáře.
1. Otevřete příkazové prostředí. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Otevřete jinou instanci příkazového prostředí. Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:

    ```console
    mongo
    ```

1. V příkazovém prostředí spusťte následující:

    ```console
    use BookstoreDb
    ```

    Pokud ještě neexistuje, je vytvořena databáze s názvem *BookstoreDb.* Pokud databáze existuje, její připojení je otevřen pro transakce.

1. Vytvořte `Books` kolekci pomocí následujícího příkazu:

    ```console
    db.createCollection('Books')
    ```

    Zobrazí se následující výsledek:

    ```console
    { "ok" : 1 }
    ```

1. Definujte schéma kolekce `Books` a vložte dva dokumenty pomocí následujícího příkazu:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    Zobrazí se následující výsledek:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

  > [!NOTE]
  > ID je uvedeno v tomto článku nebude odpovídat ID při spuštění této ukázky.

1. Dokumenty v databázi můžete zobrazit pomocí následujícího příkazu:

    ```console
    db.Books.find({}).pretty()
    ```

    Zobrazí se následující výsledek:

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    Schéma přidá automaticky vygenerované `_id` vlastnosti typu `ObjectId` pro každý dokument.

Databáze je připravena. Můžete začít vytvářet ASP.NET základní webové rozhraní API.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webového rozhraní API ASP.NET Core

1. Přejděte do **souboru** > **nový** > **projekt**.
1. Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 3.0**. Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.
1. Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

## <a name="add-an-entity-model"></a>Přidání modelu entity

1. Přidejte adresář *Models* do kořenového adresáře projektu.
1. Přidejte `Book` třídu do adresáře *Models* s následujícím kódem:

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;

    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }

            [BsonElement("Name")]
            public string BookName { get; set; }

            public decimal Price { get; set; }

            public string Category { get; set; }

            public string Author { get; set; }
        }
    }
    ```

    V předchozí třídě `Id` vlastnost:

    * Je vyžadováno pro mapování objektu CLR (Common Language Runtime) do kolekce MongoDB.
    * Je anotován [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) s určit tuto vlastnost jako primární klíč dokumentu.
    * Je anotován [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) s povolit předávání `string` parametru jako typ namísto [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury. Mongo zpracovává převod `string` z `ObjectId`do .

    Vlastnost `BookName` je anotována [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) s atributem. Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidání konfiguračního modelu

1. Přidejte do *souboru appsettings.json*následující hodnoty konfigurace databáze :

    ```javascript
    {
      "BookstoreDatabaseSettings": {
        "BooksCollectionName": "Books",
        "ConnectionString": "mongodb://localhost:27017",
        "DatabaseName": "BookstoreDb"
      },

    ```

1. Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

    ```csharp
    namespace BooksApi.Models
    {
        public class BookstoreDatabaseSettings : IBookstoreDatabaseSettings
        {
            public string BooksCollectionName { get; set; }
            public string ConnectionString { get; set; }
            public string DatabaseName { get; set; }
        }

        public interface IBookstoreDatabaseSettings
        {
            string BooksCollectionName { get; set; }
            string ConnectionString { get; set; }
            string DatabaseName { get; set; }
        }
    }
    ```

    Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.* Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddControllers();
    }
    ```

    V předchozím kódu:

    * Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI). `BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.
    * Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.

1. Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:

    ```csharp
    using BooksApi.Models;
    ```

## <a name="add-a-crud-operations-service"></a>Přidání provozní služby CRUD

1. Přidejte adresář *služby* do kořenového adresáře projektu.
1. Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:

    ```csharp
    using BooksApi.Models;
    using MongoDB.Driver;
    using System.Collections.Generic;
    using System.Linq;

    namespace BooksApi.Services
    {
        public class BookService
        {
            private readonly IMongoCollection<Book> _books;

            public BookService(IBookstoreDatabaseSettings settings)
            {
                var client = new MongoClient(settings.ConnectionString);
                var database = client.GetDatabase(settings.DatabaseName);

                _books = database.GetCollection<Book>(settings.BooksCollectionName);
            }

            public List<Book> Get() =>
                _books.Find(book => true).ToList();

            public Book Get(string id) =>
                _books.Find<Book>(book => book.Id == id).FirstOrDefault();

            public Book Create(Book book)
            {
                _books.InsertOne(book);
                return book;
            }

            public void Update(string id, Book bookIn) =>
                _books.ReplaceOne(book => book.Id == id, bookIn);

            public void Remove(Book bookIn) =>
                _books.DeleteOne(book => book.Id == bookIn.Id);

            public void Remove(string id) =>
                _books.DeleteOne(book => book.Id == id);
        }
    }
    ```

    V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru. Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers();
    }
    ```

    V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách. Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na . Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.

1. Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:


    ```csharp
    using BooksApi.Services;
    ```

Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:

    ```csharp
    public BookService(IBookstoreDatabaseSettings settings)
    {
        var client = new MongoClient(settings.ConnectionString);
        var database = client.GetDatabase(settings.DatabaseName);

        _books = database.GetCollection<Book>(settings.BooksCollectionName);
    }
    ```

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Představuje databázi Mongo pro provádění operací. Tento kurz používá obecnou metodu [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) v rozhraní k získání přístupu k datům v konkrétní kolekci. Proveďte operace CRUD proti kolekci po volání této metody. Ve `GetCollection<TDocument>(collection)` volání metody:
  * `collection`představuje název kolekce.
  * `TDocument`představuje typ objektu CLR uložené v kolekci.

`GetCollection<TDocument>(collection)`vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci. V tomto kurzu jsou v kolekci vyvolány následující metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Odstraní jeden dokument odpovídající zadaným kritériím hledání.
* [Najít\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Vrátí všechny dokumenty v kolekci odpovídající zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Nahradí jeden dokument odpovídající zadaným kritériím hledání s poskytnutým objektem.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidejte `BooksController` třídu do *adresáře Řadiče* s následujícím kódem:

```csharp
using BooksApi.Models;
using BooksApi.Services;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;

namespace BooksApi.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class BooksController : ControllerBase
    {
        private readonly BookService _bookService;

        public BooksController(BookService bookService)
        {
            _bookService = bookService;
        }

        [HttpGet]
        public ActionResult<List<Book>> Get() =>
            _bookService.Get();

        [HttpGet("{id:length(24)}", Name = "GetBook")]
        public ActionResult<Book> Get(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            return book;
        }

        [HttpPost]
        public ActionResult<Book> Create(Book book)
        {
            _bookService.Create(book);

            return CreatedAtRoute("GetBook", new { id = book.Id.ToString() }, book);
        }

        [HttpPut("{id:length(24)}")]
        public IActionResult Update(string id, Book bookIn)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Update(id, bookIn);

            return NoContent();
        }

        [HttpDelete("{id:length(24)}")]
        public IActionResult Delete(string id)
        {
            var book = _bookService.Get(id);

            if (book == null)
            {
                return NotFound();
            }

            _bookService.Remove(book.Id);

            return NoContent();
        }
    }
}
```

Předchozí řadič webového rozhraní API:

* Používá `BookService` třídu k provádění operací CRUD.
* Obsahuje metody akce pro podporu požadavků HTTP GET, POST, PUT a DELETE.
* Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě akce vrátit odpověď [HTTP 201.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`také přidá `Location` záhlaví odpovědi. Záhlaví `Location` určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte `http://localhost:<port>/api/books` na otestovat ovladač `Get` bez parametry akce metody. Zobrazí se následující odpověď JSON:

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. Přejděte `http://localhost:<port>/api/books/{id here}` na otestovat přetížené `Get` metody akce řadiče. Zobrazí se následující odpověď JSON:

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a>Konfigurace možností serializace JSON

Existují dva podrobnosti změnit o odpovědi JSON vrácené v [části Test webové rozhraní API:](#test-the-web-api)

* Výchozí camel ové pouzdro názvů vlastností by mělo být změněno tak, aby odpovídalo pouzdrům Pascal názvů vlastností objektu CLR.
* Vlastnost `bookName` by měla `Name`být vrácena jako .

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. JSON.NET byla odebrána ze sdíleného rámce ASP.NET. Přidejte odkaz na balíček [microsoft.aspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).

1. V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddMvc` kód na volání metody:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<BookstoreDatabaseSettings>(
            Configuration.GetSection(nameof(BookstoreDatabaseSettings)));

        services.AddSingleton<IBookstoreDatabaseSettings>(sp =>
            sp.GetRequiredService<IOptions<BookstoreDatabaseSettings>>().Value);

        services.AddSingleton<BookService>();

        services.AddControllers()
            .AddNewtonsoftJson(options => options.UseMemberCasing());
    }
    ```

    S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` `Author` vlastnost třídy serializuje jako `Author`.

1. V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

    ```csharp
    [BsonElement("Name")]
    [JsonProperty("Name")]
    public string BookName { get; set; }
    ```

    Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:

    ```csharp
    using Newtonsoft.Json;
    ```

1. Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api) Všimněte si rozdílu v názvech vlastností JSON.

## <a name="next-steps"></a>Další kroky

Další informace o vytváření ASP.NET základní webová api naleznete v následujících zdrojích:

* [Verze tohoto článku na YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* [Vytváření webových api s ASP.NET jádrem](https://docs.microsoft.com/aspnet/core/web-api/index?view=aspnetcore-3.0)
