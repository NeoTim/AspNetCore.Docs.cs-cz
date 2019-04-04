---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 95a5f8bdb4b302d6bdae7b5809b54f1b263e6ee4
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012861"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB

Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)

Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurovat MongoDB
> * Vytvoření databáze MongoDB
> * Definování kolekce MongoDB a schématu
> * Provádění operací MongoDB CRUD z webového rozhraní API

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Požadavky

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio 2017 verze 15.9 nebo vyšší](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) s **vývoj pro ASP.NET a web** pracovního vytížení
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# [<a name="visual-studio-code"></a>Visual Studio Code](#tab/visual-studio-code)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET core SDK 2.2 nebo vyšší](https://www.microsoft.com/net/download/all)
* [Visual Studio pro Mac verze 7,7 nebo novější](https://www.visualstudio.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Nakonfigurovat MongoDB

Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\\Program Files\\MongoDB* ve výchozím nastavení. Přidat *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* k `Path` proměnné prostředí. Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.

Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty. Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Vyberte adresář na vývojovém počítači pro ukládání dat. Například *C:\\BooksData* na Windows. Vytvořte adresář, pokud neexistuje. Prostředí mongo nebude vytvářet nové adresáře.
1. Otevřete příkazové okno. Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017. Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Otevřete jiná instance příkazového prostředí. Připojení k databázi testu výchozí spuštěním následujícího příkazu:

    ```console
    mongo
    ```

1. Spuštěním následujícího příkazu v příkazovém řádku:

    ```console
    use BookstoreDb
    ```

    Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří. Pokud databáze neexistuje, je připojení otevřené transakce.

1. Vytvoření `Books` kolekce pomocí následujícího příkazu:

    ```console
    db.createCollection('Books')
    ```

    Zobrazí se následující výsledek:

    ```console
    { "ok" : 1 }
    ```

1. Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:

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

    Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.

Databáze je připravena. Můžete začít vytvářet webové rozhraní API ASP.NET Core.

## <a name="create-the-aspnet-core-web-api-project"></a>Vytvoření projektu webové rozhraní API ASP.NET Core

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

1. Přejděte na **souboru** > **nové** > **projektu**.
1. Vyberte **webové aplikace ASP.NET Core**, pojmenujte projekt *BooksApi*a klikněte na tlačítko **OK**.
1. Vyberte **.NET Core** Cílová architektura a **2.2 technologie ASP.NET Core**. Vyberte **API** šablony projektu a klikněte na tlačítko **OK**:
1. Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB. V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# [<a name="visual-studio-code"></a>Visual Studio Code](#tab/visual-studio-code)

1. V příkazovém řádku spusťte následující příkazy:

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.

1. Klikněte na tlačítko **Ano** při *'BooksApi' chybí požadované prostředky pro sestavení a ladění. Přidat?*  se zobrazí oznámení.
1. Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB. Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu. Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/visual-studio-mac)

1. Přejděte na **souboru** > **nové řešení** > **.NET Core** > **aplikace**.
1. Vyberte **webového rozhraní API ASP.NET Core** C# šablony projektu a klikněte na tlačítko **Další**.
1. Vyberte **.NET Core 2.2** z **Cílová architektura** rozevíracího seznamu a klikněte na tlačítko **Další**.
1. Zadejte *BooksApi* pro **název projektu**a klikněte na tlačítko **vytvořit**.
1. V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.
1. Zadejte *MongoDB.Driver* do vyhledávacího pole, vyberte *MongoDB.Driver* balíček a klikněte na tlačítko **přidat balíček**.
1. Klikněte na tlačítko **přijmout** tlačítko **přijetí licence** dialogového okna.

---

## <a name="add-a-model"></a>Přidání modelu

1. Přidat *modely* adresáře do kořenového adresáře projektu.
1. Přidat `Book` třídu *modely* adresáře s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

Ve třídě předchozí `Id` vlastnost:

* Je vyžadován pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.
* Je opatřen poznámkou `[BsonId]` se označí jako primární klíč dokumentu této vlastnosti.
* Je opatřen poznámkou `[BsonRepresentation(BsonType.ObjectId)]` povolit předávání parametru jako typ `string` místo `ObjectId`. Mongo zpracovává server převod z `string` k `ObjectId`.

Další vlastnosti ve třídě, je opatřen poznámkou `[BsonElement]` atribut. Hodnota atributu představuje název vlastnosti v kolekci MongoDB.

## <a name="add-a-crud-operations-class"></a>Přidejte třídu operace CRUD

1. Přidat *služby* adresáře do kořenového adresáře projektu.
1. Přidat `BookService` třídu *služby* adresáře s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. Přidat připojovací řetězec MongoDB do *appsettings.json*:

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    Předchozí `BookstoreDb` v získat přístup k vlastnosti `BookService` konstruktoru třídy.

1. V `Startup.ConfigureServices`, zaregistrujte `BookService` třídy systémem injektáž závislostí:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    Předchozí registrace služby je nezbytný pro podporu vkládání konstruktor přijímací třídy.

`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:

* `MongoClient` &ndash; Načte instance serveru k provedení operace databáze. Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; Reprezentuje databázi Mongodb pro provádění operací. Tento kurz používá Obecné `GetCollection<T>(collection)` metoda v rozhraní k získání přístupu k datům v určité kolekci. Operace CRUD lze provést proti kolekci, jakmile tato metoda je volána. V `GetCollection<T>(collection)` volání metody:
  * `collection` představuje název kolekce.
  * `T` představuje typ objektu CLR uložená v kolekci.

`GetCollection<T>(collection)` Vrátí `MongoCollection` objekt představující kolekci. V tomto kurzu jsou vyvolány následující metody na kolekci:

* `Find<T>` &ndash; Vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.
* `InsertOne` &ndash; Vloží zadaný objekt jako nový dokument v kolekci.
* `ReplaceOne` &ndash; Nahrazuje jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.
* `DeleteOne` &ndash; Odstraní jeden dokument, který odpovídá kritériím hledání zadaná.

## <a name="add-a-controller"></a>Přidání kontroleru

1. Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    Předchozí kontroler web API:

    * Používá `BookService` pro provádění operací CRUD.
    * Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.
    * <xref:System.Web.Http.ApiController.CreatedAtRoute*> Metoda vrátí odezvě 201, což je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru. `CreatedAtRoute` také přidá do odpovědi hlavičku umístění. Hlavička umístění Určuje identifikátor URI nově vytvořeného úkolu položky. Zobrazit [10.2.2 201 vytvořili](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).
1. Sestavte a spusťte aplikaci.
1. Přejděte na `http://localhost:<port>/api/books` v prohlížeči. Zobrazí se následující odpověď JSON:

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

## <a name="next-steps"></a>Další kroky

Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:

* [Verzi tohoto článku na webu YouTube](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
