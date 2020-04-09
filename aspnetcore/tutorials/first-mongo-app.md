---
title: Vytvoření webového rozhraní API s ASP.NET Core a MongoDB
author: prkhandelwal
description: Tento kurz ukazuje, jak vytvořit ASP.NET základní webové rozhraní API pomocí databáze MongoDB NoSQL.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: d5ce4a1dc3c00b2b12edc12e26f482caa97df6b3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511415"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API s ASP.NET Core a MongoDB

Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace mongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce a schématu MongoDB
> * Provádění operací MongoDB CRUD z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 nebo novější](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio pro Mac verze 7.7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejděte do **souboru** > **nový** > **projekt**.
1. Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 3.0**. Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.
1. Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nový ASP.NET core web API projekt cílení .NET Core je generována a otevřena v kódu Visual Studio.

1. Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v rozhraní BooksApi. Přidat je?**. Vyberte **ano**.
1. Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB. Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Přejděte na soubor > **novéřešení** > **.NET Core** > **File** **App**.
1. Vyberte šablonu projektu **core webového rozhraní API** C# ASP.NET a vyberte **Další**.
1. V rozevíracím seznamu **Cílová architektura** vyberte **.NET Core 3.0** a vyberte **Další**.
1. Zadejte *BooksApi* pro **název projektu**a vyberte **Vytvořit**.
1. Na panelu **Řešení** klepněte pravým tlačítkem myši na uzel **Závislosti** projektu a vyberte přidat **balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB.Driver,* vyberte balíček *MongoDB.Driver* a vyberte **Přidat balíček**.
1. V dialogovém okně **Přijetí licence** vyberte tlačítko **Přijmout.**

---

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

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.* Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI). `BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.
   * Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.

1. Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání provozní služby CRUD

1. Přidejte adresář *služby* do kořenového adresáře projektu.
1. Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru. Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách. Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na . Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.

1. Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

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

1. V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddControllers` kód na volání metody:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` `Author` vlastnost třídy serializuje jako `Author`.

1. V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api) Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Tento kurz vytvoří webové rozhraní API, které provádí operace Vytvoření, Čtení, Aktualizace a Odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace mongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce a schématu MongoDB
> * Provádění operací MongoDB CRUD z webového rozhraní API
> * Přizpůsobení serializace JSON

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Požadavky

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Sada .NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **ASP.NET a zatížením vývoje webu**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Sada .NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

* [Sada .NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio pro Mac verze 7.7 nebo novější](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

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

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Přejděte do **souboru** > **nový** > **projekt**.
1. Vyberte typ projektu **základní webové aplikace ASP.NET** a vyberte **další**.
1. Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.
1. Vyberte cílovou architekturu **.NET Core** a **ASP.NET Core 2.2**. Vyberte šablonu projektu **rozhraní API** a vyberte **Vytvořit**.
1. Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB. V okně **Konzola Správce balíčků** přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Spusťte v příkazovém prostředí následující příkazy:

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   Nový ASP.NET core web API projekt cílení .NET Core je generována a otevřena v kódu Visual Studio.

1. Poté, co ikona plamene OmniSharp na stavovém řádku zezelenaje, dialogové okno požádá **požadované datové zdroje k sestavení a ladění v rozhraní BooksApi. Přidat je?**. Vyberte **ano**.
1. Navštivte [Galerii NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) a zjistěte nejnovější stabilní verzi ovladače .NET pro MongoDB. Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spuštěním následujícího příkazu nainstalujte ovladač .NET pro MongoDB:

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

1. Přejděte na soubor > **novéřešení** > **.NET Core** > **File** **App**.
1. Vyberte šablonu projektu **core webového rozhraní API** C# ASP.NET a vyberte **Další**.
1. V rozevíracím seznamu **Cílová architektura** vyberte **.NET Core 2.2** a vyberte **Další**.
1. Zadejte *BooksApi* pro **název projektu**a vyberte **Vytvořit**.
1. Na panelu **Řešení** klepněte pravým tlačítkem myši na uzel **Závislosti** projektu a vyberte přidat **balíčky**.
1. Do vyhledávacího pole zadejte *MongoDB.Driver,* vyberte balíček *MongoDB.Driver* a vyberte **Přidat balíček**.
1. V dialogovém okně **Přijetí licence** vyberte tlačítko **Přijmout.**

---

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

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. Přidejte *soubor BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   Předchozí `BookstoreDatabaseSettings` třída se používá k uložení hodnot `BookstoreDatabaseSettings` vlastností souboru *appsettings.json.* Názvy vlastností JSON a C# jsou pojmenovány identicky pro usnadnění procesu mapování.

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   V předchozím kódu:

   * Instance konfigurace, na kterou se váže `BookstoreDatabaseSettings` oddíl souboru *appsettings.json,* je registrována v kontejneru DI vkládání závislostí (DI). `BookstoreDatabaseSettings` Například `ConnectionString` vlastnost objektu je naplněna `BookstoreDatabaseSettings:ConnectionString` vlastností v *souboru appsettings.json*.
   * Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností](xref:fundamentals/dependency-injection#service-lifetimes)singleton . Při vložení instance rozhraní překládá `BookstoreDatabaseSettings` na objekt.

1. Přidejte následující kód na *Startup.cs* začátek `BookstoreDatabaseSettings` Startup.cs `IBookstoreDatabaseSettings` chcete vyřešit a odkazy:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>Přidání provozní služby CRUD

1. Přidejte adresář *služby* do kořenového adresáře projektu.
1. Přidejte `BookService` třídu do *adresáře Služby* s následujícím kódem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím vkládání konstruktoru. Tato technika poskytuje přístup k hodnotám konfigurace *appsettings.json,* které byly přidány v části [Přidat model konfigurace.](#add-a-configuration-model)

1. Do položky přidejte `Startup.ConfigureServices`následující zvýrazněný kód :

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   V předchozím kódu je `BookService` třída registrována u DI pro podporu vkládání konstruktoru ve spotřebních třídách. Životnost služby singleton je `BookService` nejvhodnější, protože `MongoClient`má přímou závislost na . Podle oficiálních pokynů pro opakované `MongoClient` použití [klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by měly být registrovány v DI s životností singleton.

1. Chcete-li `BookService` odkaz vyřešit, přidejte na začátek *Startup.cs* následující kód:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

Třída `BookService` používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Přečte instanci serveru pro provádění databázových operací. Konstruktor této třídy je k dispozici připojovací řetězec MongoDB:

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

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

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

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

1. V `Startup.ConfigureServices`, řetěz následující zvýrazněný `AddMvc` kód na volání metody:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   S předchozí změnou názvy vlastností v serializované odpovědi JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR. Například `Book` `Author` vlastnost třídy serializuje jako `Author`.

1. V *části Models/Book.cs*opatří `BookName` vlastnost poznámkami s následujícím [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atributem:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   Hodnota `[JsonProperty]` atributu `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.

1. Přidejte následující kód do horní části *Models/Book.cs* vyřešit odkaz na `[JsonProperty]` atribut:

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. Opakujte kroky definované v části [Otestovat webové rozhraní API.](#test-the-web-api) Všimněte si rozdílu v názvech vlastností JSON.

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>Přidání podpory ověřování do webového rozhraní API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>Další kroky

Další informace o vytváření ASP.NET základní webová api naleznete v následujících zdrojích:

* [Verze tohoto článku na YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
