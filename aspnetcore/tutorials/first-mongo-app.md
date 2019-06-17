---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/10/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 426b4c0dee290153b9b1bf83deec14fa728183cb
ms.sourcegitcommit: f5762967df3be8b8c868229e679301f2f7954679
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048079"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="18dd8-103">Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="18dd8-104">Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="18dd8-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="18dd8-105">Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.</span><span class="sxs-lookup"><span data-stu-id="18dd8-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="18dd8-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="18dd8-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="18dd8-107">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-107">Configure MongoDB</span></span>
> * <span data-ttu-id="18dd8-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="18dd8-109">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="18dd8-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="18dd8-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="18dd8-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="18dd8-111">Vlastní serializace JSON</span><span class="sxs-lookup"><span data-stu-id="18dd8-111">Customize JSON serialization</span></span>

<span data-ttu-id="18dd8-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18dd8-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18dd8-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="18dd8-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="18dd8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18dd8-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="18dd8-115">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="18dd8-115">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="18dd8-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="18dd8-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="18dd8-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="18dd8-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18dd8-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="18dd8-119">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="18dd8-119">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="18dd8-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18dd8-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="18dd8-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18dd8-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="18dd8-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="18dd8-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="18dd8-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="18dd8-124">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="18dd8-124">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="18dd8-125">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="18dd8-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="18dd8-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="18dd8-127">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="18dd8-127">Configure MongoDB</span></span>

<span data-ttu-id="18dd8-128">Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="18dd8-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="18dd8-129">Přidat *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* k `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="18dd8-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="18dd8-130">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="18dd8-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="18dd8-131">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="18dd8-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="18dd8-132">Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="18dd8-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="18dd8-133">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="18dd8-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="18dd8-134">Například *C:\\BooksData* na Windows.</span><span class="sxs-lookup"><span data-stu-id="18dd8-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="18dd8-135">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="18dd8-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="18dd8-136">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="18dd8-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="18dd8-137">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="18dd8-137">Open a command shell.</span></span> <span data-ttu-id="18dd8-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="18dd8-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="18dd8-139">Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="18dd8-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="18dd8-140">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="18dd8-140">Open another command shell instance.</span></span> <span data-ttu-id="18dd8-141">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="18dd8-141">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="18dd8-142">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="18dd8-142">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="18dd8-143">Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="18dd8-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="18dd8-144">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="18dd8-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="18dd8-145">Vytvoření `Books` kolekce pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="18dd8-145">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="18dd8-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="18dd8-146">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="18dd8-147">Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="18dd8-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="18dd8-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="18dd8-148">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="18dd8-149">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="18dd8-149">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="18dd8-150">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="18dd8-150">The following result is displayed:</span></span>

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

    <span data-ttu-id="18dd8-151">Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="18dd8-151">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="18dd8-152">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="18dd8-152">The database is ready.</span></span> <span data-ttu-id="18dd8-153">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18dd8-153">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="18dd8-154">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18dd8-154">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="18dd8-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18dd8-155">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="18dd8-156">Přejděte na **souboru** > **nové** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-156">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="18dd8-157">Vyberte **webové aplikace ASP.NET Core** typ projektu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-157">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="18dd8-158">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-158">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="18dd8-159">Vyberte **.NET Core** Cílová architektura a **2.2 technologie ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-159">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="18dd8-160">Vyberte **API** šablony projektu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-160">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="18dd8-161">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="18dd8-161">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="18dd8-162">V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-162">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="18dd8-163">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="18dd8-163">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="18dd8-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18dd8-164">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="18dd8-165">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="18dd8-165">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="18dd8-166">Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="18dd8-166">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="18dd8-167">Po stavový řádek OmniSharp bezpečnostní opatření ikona se změní na zelenou, dialogové okno s dotazem **'BooksApi' chybí požadované prostředky pro sestavení a ladění. Přidat?** .</span><span class="sxs-lookup"><span data-stu-id="18dd8-167">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="18dd8-168">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-168">Select **Yes**.</span></span>
1. <span data-ttu-id="18dd8-169">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="18dd8-169">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="18dd8-170">Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-170">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="18dd8-171">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="18dd8-171">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="18dd8-172">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="18dd8-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="18dd8-173">Přejděte na **souboru** > **nové řešení** >  **.NET Core** > **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-173">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="18dd8-174">Vyberte **webového rozhraní API ASP.NET Core** C# šablony projektu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-174">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="18dd8-175">Vyberte **.NET Core 2.2** z **Cílová architektura** rozevíracího seznamu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-175">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="18dd8-176">Zadejte *BooksApi* pro **název projektu**a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-176">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="18dd8-177">V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-177">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="18dd8-178">Zadejte *MongoDB.Driver* do vyhledávacího pole, vyberte *MongoDB.Driver* balíček a vyberte **přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="18dd8-178">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="18dd8-179">Vyberte **přijmout** tlačítko **přijetí licence** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="18dd8-179">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="18dd8-180">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="18dd8-180">Add an entity model</span></span>

1. <span data-ttu-id="18dd8-181">Přidat *modely* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-181">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="18dd8-182">Přidat `Book` třídu *modely* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="18dd8-182">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="18dd8-183">Ve třídě předchozí `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="18dd8-183">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="18dd8-184">Je vyžadován pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="18dd8-184">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="18dd8-185">Je opatřen poznámkou [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) se označí jako primární klíč dokumentu této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="18dd8-185">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="18dd8-186">Je opatřen poznámkou [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) povolit předávání parametru jako typ `string` místo [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="18dd8-186">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="18dd8-187">Mongo zpracovává server převod z `string` k `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="18dd8-187">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="18dd8-188">`BookName` Vlastnost je opatřen poznámkou [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atribut.</span><span class="sxs-lookup"><span data-stu-id="18dd8-188">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="18dd8-189">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="18dd8-189">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="18dd8-190">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="18dd8-190">Add a configuration model</span></span>

1. <span data-ttu-id="18dd8-191">Přidejte následující hodnoty konfigurace databáze na *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="18dd8-191">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="18dd8-192">Přidat *BookstoreDatabaseSettings.cs* do souboru *modely* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="18dd8-192">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="18dd8-193">Předchozí `BookstoreDatabaseSettings` třída se používá k ukládání *appsettings.json* souboru `BookstoreDatabaseSettings` hodnot vlastností.</span><span class="sxs-lookup"><span data-stu-id="18dd8-193">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="18dd8-194">Ve formátu JSON a C# názvy vlastností jsou pojmenované stejně jako k usnadnění procesu mapování.</span><span class="sxs-lookup"><span data-stu-id="18dd8-194">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="18dd8-195">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18dd8-195">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

    <span data-ttu-id="18dd8-196">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="18dd8-196">In the preceding code:</span></span>

    * <span data-ttu-id="18dd8-197">Instance konfigurace, ke kterému *appsettings.json* souboru `BookstoreDatabaseSettings` oddílu vazby je registrován v kontejnerů Dependency Injection (DI).</span><span class="sxs-lookup"><span data-stu-id="18dd8-197">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="18dd8-198">Například `BookstoreDatabaseSettings` objektu `ConnectionString` se vyplní vlastnost `BookstoreDatabaseSettings:ConnectionString` vlastnost *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="18dd8-198">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="18dd8-199">`IBookstoreDatabaseSettings` Rozhraní je zaregistrován v DI jednotlivý prvek [doba platnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="18dd8-199">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="18dd8-200">Při vložený, instanci rozhraní překládá `BookstoreDatabaseSettings` objektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-200">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="18dd8-201">Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings` odkazy:</span><span class="sxs-lookup"><span data-stu-id="18dd8-201">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="18dd8-202">Přidat službu operace CRUD</span><span class="sxs-lookup"><span data-stu-id="18dd8-202">Add a CRUD operations service</span></span>

1. <span data-ttu-id="18dd8-203">Přidat *služby* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-203">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="18dd8-204">Přidat `BookService` třídu *služby* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="18dd8-204">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="18dd8-205">V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="18dd8-205">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="18dd8-206">Tento postup poskytuje přístup k *appsettings.json* konfigurační hodnoty, které byly přidány [přidat konfigurační model](#add-a-configuration-model) části.</span><span class="sxs-lookup"><span data-stu-id="18dd8-206">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="18dd8-207">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="18dd8-207">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

    <span data-ttu-id="18dd8-208">V předchozím kódu `BookService` třída je registrována s DI pro podporu vkládání konstruktor přijímací třídy.</span><span class="sxs-lookup"><span data-stu-id="18dd8-208">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="18dd8-209">Je nejvhodnější doba platnosti služby typu singleton protože `BookService` převezme přímou závislost `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="18dd8-209">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="18dd8-210">Za official je přínosné pro [pokyny pro opakované použití klient Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` by měly být zaregistrovány v DI s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="18dd8-210">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="18dd8-211">Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookService` odkaz:</span><span class="sxs-lookup"><span data-stu-id="18dd8-211">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="18dd8-212">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="18dd8-212">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="18dd8-213">[Položky MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; načte instance serveru k provedení operace databáze.</span><span class="sxs-lookup"><span data-stu-id="18dd8-213">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="18dd8-214">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="18dd8-214">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="18dd8-215">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongodb pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="18dd8-215">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="18dd8-216">Tento kurz používá Obecné [GetCollection\<TDocument > (kolekce)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) metoda v rozhraní k získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="18dd8-216">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="18dd8-217">Provádění operací CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="18dd8-217">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="18dd8-218">V `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="18dd8-218">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="18dd8-219">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="18dd8-219">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="18dd8-220">`TDocument` představuje typ objektu CLR uložená v kolekci.</span><span class="sxs-lookup"><span data-stu-id="18dd8-220">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="18dd8-221">`GetCollection<TDocument>(collection)` Vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="18dd8-221">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="18dd8-222">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="18dd8-222">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="18dd8-223">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní odpovídá kritériím hledání zadaný jednotlivý dokument.</span><span class="sxs-lookup"><span data-stu-id="18dd8-223">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="18dd8-224">[Najít\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="18dd8-224">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="18dd8-225">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="18dd8-225">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="18dd8-226">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahradí jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-226">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="18dd8-227">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="18dd8-227">Add a controller</span></span>

<span data-ttu-id="18dd8-228">Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="18dd8-228">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="18dd8-229">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="18dd8-229">The preceding web API controller:</span></span>

* <span data-ttu-id="18dd8-230">Používá `BookService` pro provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="18dd8-230">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="18dd8-231">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="18dd8-231">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="18dd8-232">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metoda akce se vraťte [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18dd8-232">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="18dd8-233">Stavový kód 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="18dd8-233">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="18dd8-234">`CreatedAtRoute` Přidá také `Location` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="18dd8-234">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="18dd8-235">`Location` Hlavičky určuje identifikátor URI nově vytvořeného knihy.</span><span class="sxs-lookup"><span data-stu-id="18dd8-235">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="18dd8-236">Test webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="18dd8-236">Test the web API</span></span>

1. <span data-ttu-id="18dd8-237">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="18dd8-237">Build and run the app.</span></span>

1. <span data-ttu-id="18dd8-238">Přejděte do `http://localhost:<port>/api/books` kontroler testů je konstruktor bez parametrů `Get` metody akce.</span><span class="sxs-lookup"><span data-stu-id="18dd8-238">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="18dd8-239">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="18dd8-239">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="18dd8-240">Přejděte do `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` kontroler testů je přetížena `Get` metody akce.</span><span class="sxs-lookup"><span data-stu-id="18dd8-240">Navigate to `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="18dd8-241">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="18dd8-241">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="18dd8-242">Konfigurovat možnosti serializace JSON</span><span class="sxs-lookup"><span data-stu-id="18dd8-242">Configure JSON serialization options</span></span>

<span data-ttu-id="18dd8-243">Existují dva podrobnosti změnit o vrácená v odpovědi JSON [testování webového rozhraní API](#test-the-web-api) části:</span><span class="sxs-lookup"><span data-stu-id="18dd8-243">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="18dd8-244">Velká a malá písmena názvy vlastností ve formátu camelCase výchozí by měla být změněna tak, aby odpovídaly Pascal malá a velká písmena názvů vlastností objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="18dd8-244">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="18dd8-245">`bookName` Vlastnost by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="18dd8-245">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="18dd8-246">Tím se uspokojí předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="18dd8-246">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="18dd8-247">V `Startup.ConfigureServices`, zřetězení následující zvýrazněný kód do `AddMvc` volání metody:</span><span class="sxs-lookup"><span data-stu-id="18dd8-247">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

    <span data-ttu-id="18dd8-248">Předchozí změny názvy vlastností ve webovém rozhraní API serializovat shoda odpověď JSON odpovídající názvy vlastností v objektu typu CLR.</span><span class="sxs-lookup"><span data-stu-id="18dd8-248">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="18dd8-249">Například `Book` třídy `Author` vlastnost serializuje jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="18dd8-249">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="18dd8-250">V *Models/Book.cs*, opatřit poznámkami `BookName` vlastnost s tímto [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atribut:</span><span class="sxs-lookup"><span data-stu-id="18dd8-250">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

    <span data-ttu-id="18dd8-251">`[JsonProperty]` Hodnotu atributu `Name` představuje název vlastnosti ve webovém rozhraní API serializovat odpověď JSON.</span><span class="sxs-lookup"><span data-stu-id="18dd8-251">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="18dd8-252">Přidejte následující kód k hornímu okraji *Models/Book.cs* přeložit `[JsonProperty]` odkaz na atribut:</span><span class="sxs-lookup"><span data-stu-id="18dd8-252">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="18dd8-253">Opakujte kroky uvedené v [testování webového rozhraní API](#test-the-web-api) oddílu.</span><span class="sxs-lookup"><span data-stu-id="18dd8-253">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="18dd8-254">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="18dd8-254">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="18dd8-255">Další kroky</span><span class="sxs-lookup"><span data-stu-id="18dd8-255">Next steps</span></span>

<span data-ttu-id="18dd8-256">Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="18dd8-256">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="18dd8-257">Verzi tohoto článku na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="18dd8-257">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
