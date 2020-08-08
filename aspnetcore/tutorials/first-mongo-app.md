---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB
author: prkhandelwal
description: V tomto kurzu se dozvíte, jak vytvořit webové rozhraní API ASP.NET Core pomocí databáze NoSQL v databázi MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: fac4adb6ffa487c7ca9650a8ebd063dd344d84a7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020246"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core a MongoDB

Od [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce a schématu MongoDB
> * Provádění operací CRUD MongoDB z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurace MongoDB

Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* . Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` proměnné prostředí. Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.

Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty. Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Pro uložení dat vyberte adresář na vývojovém počítači. Například *C: \\ BooksData* ve Windows. Pokud adresář neexistuje, vytvořte ho. Prostředí Mongo nevytváří nové adresáře.
1. Otevřete příkazové prostředí. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otevřete jinou instanci příkazového prostředí. Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:

   ```console
   mongo
   ```

1. V příkazovém prostředí spusťte následující příkaz:

   ```console
   use BookstoreDb
   ```

   Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* . Pokud databáze existuje, připojení je otevřeno pro transakce.

1. Vytvořte `Books` kolekci pomocí následujícího příkazu:

   ```console
   db.createCollection('Books')
   ```

   Zobrazí se následující výsledek:

   ```console
   { "ok" : 1 }
   ```

1. Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:

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
   > ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.

1. Zobrazte dokumenty v databázi pomocí následujícího příkazu:

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

   Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .

Databáze je připravena. Můžete začít vytvářet ASP.NET Core webového rozhraní API.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webového rozhraní API ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejít na **soubor** > **Nový** > **projekt**.
1. Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**. Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.
1. Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém prostředí spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.

1. Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**. Vyberte **Ano**.
1. Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB. Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V Visual Studio pro Mac starších než verze 8,6 vyberte **soubor**  >  **Nový řešení**  >  **.NET Core**  >  **aplikace** z tohoto bočního panelu. Ve verzi 8,6 nebo novější vyberte v **File**  >  postranním panelu soubor**nové řešení**  >  **web a konzolová**  >  **aplikace** .
1. Vyberte šablonu projektu **ASP.NET Core** > **API** jazyka C# a vyberte **Další**.
1. V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 3,1** a vyberte **Další**.
1. Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.
1. Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.
1. V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .

---

## <a name="add-an-entity-model"></a>Přidání modelu entity

1. Přidejte adresář *modelů* do kořenového adresáře projektu.
1. Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:

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

   * Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.
   * Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.
   * Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo zpracovává převod z `string` na `ObjectId` .

   `BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem. Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Přidejte následující hodnoty konfigurace databáze, které chcete *appsettings.js*:

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení *appsettings.js* do `BookstoreDatabaseSettings` hodnot vlastností souboru. Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se *appsettings.jsu* oddílu souboru `BookstoreDatabaseSettings` váže, se registruje v kontejneru vkládání závislostí (di). Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *appsettings.js*.
   * `IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton. Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.

1. Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání služby operace CRUD

1. Přidejte adresář *služeb* do kořenového adresáře projektu.
1. `BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru. Tento postup poskytuje přístup k *appsettings.js* hodnot konfigurace, které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd. Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` . Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.

1. Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): přečte instanci serveru pro provádění databázových operací. K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): představuje databázi Mongo pro provádění operací. V tomto kurzu se používá obecná metoda [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci. Provede operace CRUD proti kolekci po volání této metody. Ve `GetCollection<TDocument>(collection)` volání metody:

  * `collection`představuje název kolekce.
  * `TDocument`představuje typ objektu CLR uložený v kolekci.

`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci. V tomto kurzu jsou v kolekci vyvolány následující metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): nahradí jediný dokument, který odpovídá zadaným kritériím hledání, dodanému objektu.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

Předchozí kontroler webového rozhraní API:

* Používá `BookService` třídu k provádění operací CRUD.
* Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.
* Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi. `Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` . Zobrazí se následující odpověď JSON:

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

1. Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` . Zobrazí se následující odpověď JSON:

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

Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :

* Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.
* `bookName`Vlastnost by měla být vrácena jako `Name` .

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. JSON.NET se odebral ze sdílené architektury ASP.NET. Přidejte odkaz na balíček do [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) .

1. V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddControllers` volání metody:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` vlastnost třídy je `Author` serializována jako `Author` .

1. V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) . Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce a schématu MongoDB
> * Provádění operací CRUD MongoDB z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurace MongoDB

Pokud používáte Windows, MongoDB se nainstaluje ve výchozím nastavení v *C: \\ Program Files \\ MongoDB* . Přidejte *C: \\ Program Files \\ MongoDB \\ Server \\ \<version_number> \\ bin* do `Path` proměnné prostředí. Tato změna umožňuje přístup k MongoDB odkudkoli na svém vývojovém počítači.

Pomocí prostředí Mongo v následujících krocích můžete vytvořit databázi, vytvořit kolekce a uložit dokumenty. Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Pro uložení dat vyberte adresář na vývojovém počítači. Například *C: \\ BooksData* ve Windows. Pokud adresář neexistuje, vytvořte ho. Prostředí Mongo nevytváří nové adresáře.
1. Otevřete příkazové prostředí. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` adresář, který jste zvolili v předchozím kroku.

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. Otevřete jinou instanci příkazového prostředí. Připojte se k výchozí testovací databázi spuštěním následujícího příkazu:

   ```console
   mongo
   ```

1. V příkazovém prostředí spusťte následující příkaz:

   ```console
   use BookstoreDb
   ```

   Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* . Pokud databáze existuje, připojení je otevřeno pro transakce.

1. Vytvořte `Books` kolekci pomocí následujícího příkazu:

   ```console
   db.createCollection('Books')
   ```

   Zobrazí se následující výsledek:

   ```console
   { "ok" : 1 }
   ```

1. Definujte schéma pro `Books` kolekci a vložte dva dokumenty pomocí následujícího příkazu:

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
   > ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.

1. Zobrazte dokumenty v databázi pomocí následujícího příkazu:

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

   Schéma přidá do každého dokumentu automaticky generovanou `_id` vlastnost typu `ObjectId` .

Databáze je připravena. Můžete začít vytvářet ASP.NET Core webového rozhraní API.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webového rozhraní API ASP.NET Core

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejít na **soubor** > **Nový** > **projekt**.
1. Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 2,2**. Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.
1. Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém prostředí spusťte následující příkazy:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Vygeneruje se nový projekt ASP.NET Core webového rozhraní API cílící na .NET Core a otevře se v Visual Studio Code.

1. Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?**. Vyberte **Ano**.
1. Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB. Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. V Visual Studio pro Mac starších než verze 8,6 vyberte **soubor**  >  **Nový řešení**  >  **.NET Core**  >  **aplikace** z tohoto bočního panelu. Ve verzi 8,6 nebo novější vyberte v **File**  >  postranním panelu soubor**nové řešení**  >  **web a konzolová**  >  **aplikace** .
1. Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** jazyka C# a vyberte **Další**.
1. V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 2,2** a vyberte **Další**.
1. Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.
1. Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.
1. V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .

---

## <a name="add-an-entity-model"></a>Přidání modelu entity

1. Přidejte adresář *modelů* do kořenového adresáře projektu.
1. Přidejte `Book` třídu do adresáře *modelů* s následujícím kódem:

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

   * Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.
   * Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.
   * Je opatřen s poznámkami, [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) . Mongo zpracovává převod z `string` na `ObjectId` .

   `BookName`Vlastnost je označena [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atributem. Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-configuration-model"></a>Přidat konfigurační model

1. Přidejte následující hodnoty konfigurace databáze, které chcete *appsettings.js*:

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` Třída se používá k uložení *appsettings.js* do `BookstoreDatabaseSettings` hodnot vlastností souboru. Názvy vlastností JSON a C# jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se *appsettings.jsu* oddílu souboru `BookstoreDatabaseSettings` váže, se registruje v kontejneru vkládání závislostí (di). Například `BookstoreDatabaseSettings` `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *appsettings.js*.
   * `IBookstoreDatabaseSettings`Rozhraní je zaregistrované v di s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton. Při vložení se instance rozhraní překládá na `BookstoreDatabaseSettings` objekt.

1. Pro vyřešení odkazů a přidejte následující kód na začátek *Startup.cs* `BookstoreDatabaseSettings` `IBookstoreDatabaseSettings` :

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání služby operace CRUD

1. Přidejte adresář *služeb* do kořenového adresáře projektu.
1. `BookService`Do adresáře *služby* přidejte třídu s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` je instance načtena z di přes injektáže konstruktoru. Tento postup poskytuje přístup k *appsettings.js* hodnot konfigurace, které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .

1. Přidejte následující zvýrazněný kód do `Startup.ConfigureServices` :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu `BookService` je třída registrována pomocí příkazu di k podpoře injektáže konstruktoru v rámci využívání tříd. Doba životnosti služby singleton je nejvhodnější `BookService` , protože používá přímou závislost na `MongoClient` . Podle oficiálních [zásad opětovného použití pro klienty Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) `MongoClient` by se měla registrovat v di s životností služby typu singleton.

1. Přidejte následující kód na začátek *Startup.cs* k vyřešení `BookService` odkazu:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService`Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): přečte instanci serveru pro provádění databázových operací. K konstruktoru této třídy je poskytnutý připojovací řetězec MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): představuje databázi Mongo pro provádění operací. V tomto kurzu se používá obecná metoda [GetCollection \<TDocument> (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci. Provede operace CRUD proti kolekci po volání této metody. Ve `GetCollection<TDocument>(collection)` volání metody:

  * `collection`představuje název kolekce.
  * `TDocument`představuje typ objektu CLR uložený v kolekci.

`GetCollection<TDocument>(collection)`Vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci. V tomto kurzu jsou v kolekci vyvolány následující metody:

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Odstraní jeden dokument, který odpovídá zadaným kritériím hledání.
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Vloží zadaný objekt jako nový dokument v kolekci.
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): nahradí jediný dokument, který odpovídá zadaným kritériím hledání, dodanému objektu.

## <a name="add-a-controller"></a>Přidání kontroleru

Přidejte `BooksController` třídu do adresáře *Controllers* pomocí následujícího kódu:

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

Předchozí kontroler webového rozhraní API:

* Používá `BookService` třídu k provádění operací CRUD.
* Obsahuje metody akcí, které podporují požadavky HTTP GET, POST, PUT a DELETE.
* Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metodě Action vrátí odpověď [http 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) . Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru. `CreatedAtRoute`Přidá také `Location` hlavičku do odpovědi. `Location`Hlavička Určuje identifikátor URI nově vytvořené knihy.

## <a name="test-the-web-api"></a>Testování webového rozhraní API

1. Sestavte a spusťte aplikaci.

1. Přejděte na `http://localhost:<port>/api/books` k otestování metody akce bez parametrů řadiče `Get` . Zobrazí se následující odpověď JSON:

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

1. Přejděte na `http://localhost:<port>/api/books/{id here}` k otestování metody akce, která je přetížena řadičem `Get` . Zobrazí se následující odpověď JSON:

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

Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :

* Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.
* `bookName`Vlastnost by měla být vrácena jako `Name` .

Chcete-li splnit předchozí požadavky, proveďte následující změny:

1. V `Startup.ConfigureServices` , řetězení následujícího zvýrazněného kódu k `AddMvc` volání metody:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` vlastnost třídy je `Author` serializována jako `Author` .

1. V části *Models/Book. cs*poznámku k `BookName` vlastnosti s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]`Hodnota atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení `[JsonProperty]` odkazu na atribut:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) . Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Další kroky

Další informace o vytváření ASP.NET Core webových rozhraní API najdete v následujících zdrojích informací:

* [YouTube verze tohoto článku](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
