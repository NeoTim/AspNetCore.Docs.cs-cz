---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: c64f6e69a19e294a18cc72c860af0a03ef70d444
ms.sourcegitcommit: 357a7120632b20465801c093e4e5bd4a315496a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649196"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="28df3-103">Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="28df3-104">Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="28df3-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="28df3-105">Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.</span><span class="sxs-lookup"><span data-stu-id="28df3-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="28df3-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="28df3-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="28df3-107">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-107">Configure MongoDB</span></span>
> * <span data-ttu-id="28df3-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="28df3-109">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="28df3-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="28df3-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="28df3-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="28df3-111">Vlastní serializace JSON</span><span class="sxs-lookup"><span data-stu-id="28df3-111">Customize JSON serialization</span></span>

<span data-ttu-id="28df3-112">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="28df3-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="28df3-113">Požadavky</span><span class="sxs-lookup"><span data-stu-id="28df3-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28df3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28df3-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="28df3-115">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="28df3-115">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="28df3-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s **vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="28df3-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="28df3-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28df3-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28df3-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="28df3-119">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="28df3-119">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="28df3-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28df3-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="28df3-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28df3-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="28df3-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28df3-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28df3-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="28df3-124">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="28df3-124">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="28df3-125">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="28df3-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="28df3-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="28df3-127">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="28df3-127">Configure MongoDB</span></span>

<span data-ttu-id="28df3-128">Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="28df3-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="28df3-129">Přidat *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* k `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="28df3-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="28df3-130">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="28df3-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="28df3-131">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="28df3-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="28df3-132">Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="28df3-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="28df3-133">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="28df3-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="28df3-134">Například *C:\\BooksData* na Windows.</span><span class="sxs-lookup"><span data-stu-id="28df3-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="28df3-135">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="28df3-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="28df3-136">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="28df3-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="28df3-137">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="28df3-137">Open a command shell.</span></span> <span data-ttu-id="28df3-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="28df3-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="28df3-139">Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="28df3-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="28df3-140">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="28df3-140">Open another command shell instance.</span></span> <span data-ttu-id="28df3-141">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="28df3-141">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="28df3-142">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="28df3-142">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="28df3-143">Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="28df3-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="28df3-144">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="28df3-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="28df3-145">Vytvoření `Books` kolekce pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="28df3-145">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="28df3-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="28df3-146">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="28df3-147">Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="28df3-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="28df3-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="28df3-148">The following result is displayed:</span></span>

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
  > <span data-ttu-id="28df3-149">ID je znázorněno v tomto článku nebudou odpovídat ID při spuštění této ukázky.</span><span class="sxs-lookup"><span data-stu-id="28df3-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="28df3-150">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="28df3-150">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="28df3-151">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="28df3-151">The following result is displayed:</span></span>

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

    <span data-ttu-id="28df3-152">Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="28df3-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="28df3-153">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="28df3-153">The database is ready.</span></span> <span data-ttu-id="28df3-154">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="28df3-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="28df3-155">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="28df3-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="28df3-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="28df3-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="28df3-157">Přejděte na **souboru** > **nové** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="28df3-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="28df3-158">Vyberte **webové aplikace ASP.NET Core** typ projektu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="28df3-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="28df3-159">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="28df3-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="28df3-160">Vyberte **.NET Core** Cílová architektura a **2.2 technologie ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="28df3-160">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="28df3-161">Vyberte **API** šablony projektu a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="28df3-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="28df3-162">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="28df3-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="28df3-163">V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="28df3-164">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="28df3-164">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="28df3-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="28df3-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="28df3-166">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="28df3-166">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="28df3-167">Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="28df3-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="28df3-168">Po stavový řádek OmniSharp bezpečnostní opatření ikona se změní na zelenou, dialogové okno s dotazem **'BooksApi' chybí požadované prostředky pro sestavení a ladění. Přidat?** .</span><span class="sxs-lookup"><span data-stu-id="28df3-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="28df3-169">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="28df3-169">Select **Yes**.</span></span>
1. <span data-ttu-id="28df3-170">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="28df3-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="28df3-171">Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="28df3-172">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="28df3-172">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="28df3-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="28df3-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="28df3-174">Přejděte na **souboru** > **nové řešení** >  **.NET Core** > **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="28df3-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="28df3-175">Vyberte **webového rozhraní API ASP.NET Core** C# šablony projektu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="28df3-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="28df3-176">Vyberte **.NET Core 2.2** z **Cílová architektura** rozevíracího seznamu a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="28df3-176">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="28df3-177">Zadejte *BooksApi* pro **název projektu**a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="28df3-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="28df3-178">V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="28df3-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="28df3-179">Zadejte *MongoDB.Driver* do vyhledávacího pole, vyberte *MongoDB.Driver* balíček a vyberte **přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="28df3-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="28df3-180">Vyberte **přijmout** tlačítko **přijetí licence** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="28df3-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="28df3-181">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="28df3-181">Add an entity model</span></span>

1. <span data-ttu-id="28df3-182">Přidat *modely* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="28df3-183">Přidat `Book` třídu *modely* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="28df3-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

    <span data-ttu-id="28df3-184">Ve třídě předchozí `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="28df3-184">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="28df3-185">Je vyžadován pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="28df3-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="28df3-186">Je opatřen poznámkou [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) se označí jako primární klíč dokumentu této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="28df3-186">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="28df3-187">Je opatřen poznámkou [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) povolit předávání parametru jako typ `string` místo [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) struktury.</span><span class="sxs-lookup"><span data-stu-id="28df3-187">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="28df3-188">Mongo zpracovává server převod z `string` k `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="28df3-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="28df3-189">`BookName` Vlastnost je opatřen poznámkou [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) atribut.</span><span class="sxs-lookup"><span data-stu-id="28df3-189">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="28df3-190">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="28df3-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="28df3-191">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="28df3-191">Add a configuration model</span></span>

1. <span data-ttu-id="28df3-192">Přidejte následující hodnoty konfigurace databáze na *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="28df3-192">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="28df3-193">Přidat *BookstoreDatabaseSettings.cs* do souboru *modely* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="28df3-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="28df3-194">Předchozí `BookstoreDatabaseSettings` třída se používá k ukládání *appsettings.json* souboru `BookstoreDatabaseSettings` hodnot vlastností.</span><span class="sxs-lookup"><span data-stu-id="28df3-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="28df3-195">Ve formátu JSON a C# názvy vlastností jsou pojmenované stejně jako k usnadnění procesu mapování.</span><span class="sxs-lookup"><span data-stu-id="28df3-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="28df3-196">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="28df3-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

    <span data-ttu-id="28df3-197">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="28df3-197">In the preceding code:</span></span>

    * <span data-ttu-id="28df3-198">Instance konfigurace, ke kterému *appsettings.json* souboru `BookstoreDatabaseSettings` oddílu vazby je registrován v kontejnerů Dependency Injection (DI).</span><span class="sxs-lookup"><span data-stu-id="28df3-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="28df3-199">Například `BookstoreDatabaseSettings` objektu `ConnectionString` se vyplní vlastnost `BookstoreDatabaseSettings:ConnectionString` vlastnost *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="28df3-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="28df3-200">`IBookstoreDatabaseSettings` Rozhraní je zaregistrován v DI jednotlivý prvek [doba platnosti služby](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="28df3-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="28df3-201">Při vložený, instanci rozhraní překládá `BookstoreDatabaseSettings` objektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="28df3-202">Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings` odkazy:</span><span class="sxs-lookup"><span data-stu-id="28df3-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="28df3-203">Přidat službu operace CRUD</span><span class="sxs-lookup"><span data-stu-id="28df3-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="28df3-204">Přidat *služby* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="28df3-205">Přidat `BookService` třídu *služby* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="28df3-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="28df3-206">V předchozím kódu `IBookstoreDatabaseSettings` instance je načten z DI prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="28df3-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="28df3-207">Tento postup poskytuje přístup k *appsettings.json* konfigurační hodnoty, které byly přidány [přidat konfigurační model](#add-a-configuration-model) části.</span><span class="sxs-lookup"><span data-stu-id="28df3-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="28df3-208">Přidejte následující zvýrazněný kód do `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="28df3-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

    <span data-ttu-id="28df3-209">V předchozím kódu `BookService` třída je registrována s DI pro podporu vkládání konstruktor přijímací třídy.</span><span class="sxs-lookup"><span data-stu-id="28df3-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="28df3-210">Je nejvhodnější doba platnosti služby typu singleton protože `BookService` převezme přímou závislost `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="28df3-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="28df3-211">Za official je přínosné pro [pokyny pro opakované použití klient Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` by měly být zaregistrovány v DI s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="28df3-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="28df3-212">Přidejte následující kód k hornímu okraji *Startup.cs* přeložit `BookService` odkaz:</span><span class="sxs-lookup"><span data-stu-id="28df3-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="28df3-213">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="28df3-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="28df3-214">[Položky MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; načte instance serveru k provedení operace databáze.</span><span class="sxs-lookup"><span data-stu-id="28df3-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="28df3-215">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="28df3-215">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="28df3-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongodb pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="28df3-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="28df3-217">Tento kurz používá Obecné [GetCollection\<TDocument > (kolekce)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) metoda v rozhraní k získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="28df3-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="28df3-218">Provádění operací CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="28df3-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="28df3-219">V `GetCollection<TDocument>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="28df3-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="28df3-220">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="28df3-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="28df3-221">`TDocument` představuje typ objektu CLR uložená v kolekci.</span><span class="sxs-lookup"><span data-stu-id="28df3-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="28df3-222">`GetCollection<TDocument>(collection)` Vrátí [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="28df3-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="28df3-223">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="28df3-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="28df3-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní odpovídá kritériím hledání zadaný jednotlivý dokument.</span><span class="sxs-lookup"><span data-stu-id="28df3-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="28df3-225">[Najít\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="28df3-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="28df3-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="28df3-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="28df3-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahradí jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.</span><span class="sxs-lookup"><span data-stu-id="28df3-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="28df3-228">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="28df3-228">Add a controller</span></span>

<span data-ttu-id="28df3-229">Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="28df3-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="28df3-230">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="28df3-230">The preceding web API controller:</span></span>

* <span data-ttu-id="28df3-231">Používá `BookService` pro provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="28df3-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="28df3-232">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="28df3-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="28df3-233">Volání <xref:System.Web.Http.ApiController.CreatedAtRoute*> v `Create` metoda akce se vraťte [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) odpovědi.</span><span class="sxs-lookup"><span data-stu-id="28df3-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="28df3-234">Stavový kód 201 je standardní odpověď pro metodu POST protokolu HTTP, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="28df3-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="28df3-235">`CreatedAtRoute` Přidá také `Location` hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="28df3-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="28df3-236">`Location` Hlavičky určuje identifikátor URI nově vytvořeného knihy.</span><span class="sxs-lookup"><span data-stu-id="28df3-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="28df3-237">Test webové rozhraní API</span><span class="sxs-lookup"><span data-stu-id="28df3-237">Test the web API</span></span>

1. <span data-ttu-id="28df3-238">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="28df3-238">Build and run the app.</span></span>

1. <span data-ttu-id="28df3-239">Přejděte do `http://localhost:<port>/api/books` kontroler testů je konstruktor bez parametrů `Get` metody akce.</span><span class="sxs-lookup"><span data-stu-id="28df3-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="28df3-240">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="28df3-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="28df3-241">Přejděte do `http://localhost:<port>/api/books/{id here}` kontroler testů je přetížena `Get` metody akce.</span><span class="sxs-lookup"><span data-stu-id="28df3-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="28df3-242">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="28df3-242">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="28df3-243">Konfigurovat možnosti serializace JSON</span><span class="sxs-lookup"><span data-stu-id="28df3-243">Configure JSON serialization options</span></span>

<span data-ttu-id="28df3-244">Existují dva podrobnosti změnit o vrácená v odpovědi JSON [testování webového rozhraní API](#test-the-web-api) části:</span><span class="sxs-lookup"><span data-stu-id="28df3-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="28df3-245">Velká a malá písmena názvy vlastností ve formátu camelCase výchozí by měla být změněna tak, aby odpovídaly Pascal malá a velká písmena názvů vlastností objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="28df3-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="28df3-246">`bookName` Vlastnost by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="28df3-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="28df3-247">Tím se uspokojí předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="28df3-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="28df3-248">V `Startup.ConfigureServices`, zřetězení následující zvýrazněný kód do `AddMvc` volání metody:</span><span class="sxs-lookup"><span data-stu-id="28df3-248">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

    <span data-ttu-id="28df3-249">Předchozí změny názvy vlastností ve webovém rozhraní API serializovat shoda odpověď JSON odpovídající názvy vlastností v objektu typu CLR.</span><span class="sxs-lookup"><span data-stu-id="28df3-249">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="28df3-250">Například `Book` třídy `Author` vlastnost serializuje jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="28df3-250">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="28df3-251">V *Models/Book.cs*, opatřit poznámkami `BookName` vlastnost s tímto [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) atribut:</span><span class="sxs-lookup"><span data-stu-id="28df3-251">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

    <span data-ttu-id="28df3-252">`[JsonProperty]` Hodnotu atributu `Name` představuje název vlastnosti ve webovém rozhraní API serializovat odpověď JSON.</span><span class="sxs-lookup"><span data-stu-id="28df3-252">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="28df3-253">Přidejte následující kód k hornímu okraji *Models/Book.cs* přeložit `[JsonProperty]` odkaz na atribut:</span><span class="sxs-lookup"><span data-stu-id="28df3-253">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="28df3-254">Opakujte kroky uvedené v [testování webového rozhraní API](#test-the-web-api) oddílu.</span><span class="sxs-lookup"><span data-stu-id="28df3-254">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="28df3-255">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="28df3-255">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="28df3-256">Další kroky</span><span class="sxs-lookup"><span data-stu-id="28df3-256">Next steps</span></span>

<span data-ttu-id="28df3-257">Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="28df3-257">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="28df3-258">Verzi tohoto článku na webu YouTube</span><span class="sxs-lookup"><span data-stu-id="28df3-258">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
