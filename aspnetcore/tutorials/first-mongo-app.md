---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: pratik-khandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/26/2018
uid: tutorials/first-mongo-app
ms.openlocfilehash: c4e00eeb2c4aecde9c70c6902e21d06853be7696
ms.sourcegitcommit: e7fafb153b9de7595c2558a0133f8d1c33a3bddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52458490"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="8d738-103">Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB</span><span class="sxs-lookup"><span data-stu-id="8d738-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="8d738-104">Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8d738-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8d738-105">Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.</span><span class="sxs-lookup"><span data-stu-id="8d738-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="8d738-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="8d738-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8d738-107">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="8d738-107">Configure MongoDB</span></span>
> * <span data-ttu-id="8d738-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="8d738-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="8d738-109">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="8d738-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="8d738-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="8d738-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="8d738-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8d738-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8d738-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="8d738-112">Prerequisites</span></span>

* [<span data-ttu-id="8d738-113">.NET core SDK 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="8d738-113">.NET Core SDK 2.1 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="8d738-114">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8d738-114">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)
* <span data-ttu-id="8d738-115">[Visual Studio 2017](https://www.visualstudio.com/downloads/) verze 15.7.3 nebo novější s následujícími sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="8d738-115">[Visual Studio 2017](https://www.visualstudio.com/downloads/) version 15.7.3 or later with the following workloads:</span></span>
  * <span data-ttu-id="8d738-116">**Vývoj pro různé platformy .NET core**</span><span class="sxs-lookup"><span data-stu-id="8d738-116">**.NET Core cross-platform development**</span></span>
  * <span data-ttu-id="8d738-117">**Vývoj pro ASP.NET a web**</span><span class="sxs-lookup"><span data-stu-id="8d738-117">**ASP.NET and web development**</span></span>

## <a name="configure-mongodb"></a><span data-ttu-id="8d738-118">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="8d738-118">Configure MongoDB</span></span>

<span data-ttu-id="8d738-119">Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\Program Files\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="8d738-119">If using Windows, MongoDB is installed at *C:\Program Files\MongoDB* by default.</span></span> <span data-ttu-id="8d738-120">Přidat *C:\Program Files\MongoDB\Server\<version_number > \bin* k `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="8d738-120">Add *C:\Program Files\MongoDB\Server\<version_number>\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="8d738-121">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="8d738-121">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="8d738-122">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="8d738-122">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="8d738-123">Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="8d738-123">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="8d738-124">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="8d738-124">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="8d738-125">Například *C:\BooksData* na Windows.</span><span class="sxs-lookup"><span data-stu-id="8d738-125">For example, *C:\BooksData* on Windows.</span></span> <span data-ttu-id="8d738-126">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="8d738-126">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="8d738-127">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="8d738-127">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="8d738-128">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="8d738-128">Open a command shell.</span></span> <span data-ttu-id="8d738-129">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="8d738-129">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="8d738-130">Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="8d738-130">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="8d738-131">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="8d738-131">Open another command shell instance.</span></span> <span data-ttu-id="8d738-132">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="8d738-132">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="8d738-133">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="8d738-133">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="8d738-134">Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="8d738-134">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="8d738-135">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="8d738-135">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="8d738-136">Vytvoření `Books` kolekce pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="8d738-136">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="8d738-137">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="8d738-137">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="8d738-138">Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="8d738-138">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="8d738-139">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="8d738-139">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="8d738-140">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="8d738-140">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="8d738-141">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="8d738-141">The following result is displayed:</span></span>

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

    <span data-ttu-id="8d738-142">Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="8d738-142">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="8d738-143">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="8d738-143">The database is ready.</span></span> <span data-ttu-id="8d738-144">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8d738-144">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="8d738-145">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d738-145">Create the ASP.NET Core web API project</span></span>

1. <span data-ttu-id="8d738-146">V sadě Visual Studio, přejděte na **souboru** > **nový** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="8d738-146">In Visual Studio, go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="8d738-147">Vyberte **webové aplikace ASP.NET Core**, pojmenujte projekt *BookMongo*a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="8d738-147">Select **ASP.NET Core Web Application**, name the project *BookMongo*, and click **OK**.</span></span>
1. <span data-ttu-id="8d738-148">Vyberte **.NET Core** Cílová architektura a **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="8d738-148">Select the **.NET Core** target framework and **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="8d738-149">Vyberte **API** šablony projektu a klikněte na tlačítko **OK**:</span><span class="sxs-lookup"><span data-stu-id="8d738-149">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="8d738-150">V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="8d738-150">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="8d738-151">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8d738-151">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version 2.7.2
    ```

## <a name="add-a-model"></a><span data-ttu-id="8d738-152">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="8d738-152">Add a model</span></span>

1. <span data-ttu-id="8d738-153">Přidat *modely* složku do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="8d738-153">Add a *Models* folder to the project root.</span></span>
1. <span data-ttu-id="8d738-154">Přidat `Book` třídu *modely* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8d738-154">Add a `Book` class to the *Models* folder with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Models/Book.cs)]

<span data-ttu-id="8d738-155">Ve třídě předchozí `Id` vlastnost se vyžaduje pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8d738-155">In the preceding class, the `Id` property is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span> <span data-ttu-id="8d738-156">Další vlastnosti ve třídě jsou vybaveny `[BsonElement]` atribut.</span><span class="sxs-lookup"><span data-stu-id="8d738-156">Other properties in the class are decorated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="8d738-157">Hodnota atributu představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="8d738-157">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="8d738-158">Přidejte třídu operace CRUD</span><span class="sxs-lookup"><span data-stu-id="8d738-158">Add a CRUD operations class</span></span>

1. <span data-ttu-id="8d738-159">Přidat *služby* složku do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="8d738-159">Add a *Services* folder to the project root.</span></span>
1. <span data-ttu-id="8d738-160">Přidat `BookService` třídu *služby* složka s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="8d738-160">Add a `BookService` class to the *Services* folder with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="8d738-161">Přidat připojovací řetězec MongoDB do *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8d738-161">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/appsettings.json?highlight=2-4)]

    <span data-ttu-id="8d738-162">Předchozí `BookstoreDb` v získat přístup k vlastnosti `BookService` konstruktoru třídy.</span><span class="sxs-lookup"><span data-stu-id="8d738-162">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="8d738-163">V `Startup.ConfigureServices`, zaregistrujte `BookService` třídy systémem injektáž závislostí:</span><span class="sxs-lookup"><span data-stu-id="8d738-163">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="8d738-164">Předchozí registrace služby je nezbytný pro podporu vkládání konstruktor přijímací třídy.</span><span class="sxs-lookup"><span data-stu-id="8d738-164">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="8d738-165">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="8d738-165">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="8d738-166">`MongoClient` &ndash; Načte instance serveru k provedení operace databáze.</span><span class="sxs-lookup"><span data-stu-id="8d738-166">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="8d738-167">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="8d738-167">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="8d738-168">`IMongoDatabase` &ndash; Reprezentuje databázi Mongodb pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="8d738-168">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="8d738-169">Tento kurz používá Obecné `GetCollection<T>(collection)` metoda v rozhraní k získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="8d738-169">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="8d738-170">Operace CRUD lze provést proti kolekci, jakmile tato metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="8d738-170">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="8d738-171">V `GetCollection<T>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="8d738-171">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="8d738-172">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="8d738-172">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="8d738-173">`T` představuje typ objektu CLR uložená v kolekci.</span><span class="sxs-lookup"><span data-stu-id="8d738-173">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="8d738-174">`GetCollection<T>(collection)` Vrátí `MongoCollection` objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="8d738-174">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="8d738-175">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="8d738-175">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="8d738-176">`Find<T>` &ndash; Vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="8d738-176">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="8d738-177">`InsertOne` &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="8d738-177">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="8d738-178">`ReplaceOne` &ndash; Nahrazuje jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.</span><span class="sxs-lookup"><span data-stu-id="8d738-178">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="8d738-179">`DeleteOne` &ndash; Odstraní jeden dokument, který odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="8d738-179">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8d738-180">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="8d738-180">Add a controller</span></span>

1. <span data-ttu-id="8d738-181">Klikněte pravým tlačítkem myši *řadiče* složky v **Průzkumníka řešení**.</span><span class="sxs-lookup"><span data-stu-id="8d738-181">Right-click the *Controllers* folder in **Solution Explorer**.</span></span> <span data-ttu-id="8d738-182">Vyberte **přidat** > **řadič**.</span><span class="sxs-lookup"><span data-stu-id="8d738-182">Select **Add** > **Controller**.</span></span>
1. <span data-ttu-id="8d738-183">Zvolte **prázdný kontroler API –** šablony položky a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="8d738-183">Choose the **API Controller - Empty** item template, and click **Add**.</span></span>
1. <span data-ttu-id="8d738-184">Zadejte *BooksController* v **názvu Kontroleru** textového pole a klikněte na tlačítko **přidat**.</span><span class="sxs-lookup"><span data-stu-id="8d738-184">Enter *BooksController* in the **Controller name** text box, and click **Add**.</span></span>
1. <span data-ttu-id="8d738-185">Přidejte následující kód, který *BooksController.cs*:</span><span class="sxs-lookup"><span data-stu-id="8d738-185">Add the following code to *BooksController.cs*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BookstoreAPI/Controllers/BooksController.cs)]

    <span data-ttu-id="8d738-186">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="8d738-186">The preceding web API controller:</span></span>

    * <span data-ttu-id="8d738-187">Používá `BookService` pro provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="8d738-187">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="8d738-188">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="8d738-188">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
1. <span data-ttu-id="8d738-189">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="8d738-189">Build and run the app.</span></span>
1. <span data-ttu-id="8d738-190">Přejděte na `http://localhost:<port>/api/books` v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="8d738-190">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="8d738-191">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="8d738-191">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="8d738-192">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8d738-192">Next steps</span></span>

<span data-ttu-id="8d738-193">Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="8d738-193">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
