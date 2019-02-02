---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: Tento kurz ukazuje vytvoření webového rozhraní ASP.NET Core API pomocí databáze MongoDB NoSQL.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 5e146261fdc8354fc9f4295a8af317e5cc36332f
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667333"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="44bf1-103">Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="44bf1-104">Podle [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="44bf1-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="44bf1-105">Tento kurz vytvoří webového rozhraní API, který provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) [MongoDB](https://www.mongodb.com/what-is-mongodb) databáze NoSQL.</span><span class="sxs-lookup"><span data-stu-id="44bf1-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="44bf1-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="44bf1-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="44bf1-107">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-107">Configure MongoDB</span></span>
> * <span data-ttu-id="44bf1-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="44bf1-109">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="44bf1-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="44bf1-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="44bf1-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="44bf1-111">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44bf1-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44bf1-112">Požadavky</span><span class="sxs-lookup"><span data-stu-id="44bf1-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44bf1-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44bf1-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="44bf1-114">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="44bf1-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="44bf1-115">[Visual Studio 2017 verze 15.9 nebo vyšší](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) s **vývoj pro ASP.NET a web** pracovního vytížení</span><span class="sxs-lookup"><span data-stu-id="44bf1-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="44bf1-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44bf1-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44bf1-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="44bf1-118">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="44bf1-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="44bf1-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44bf1-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="44bf1-120">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44bf1-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="44bf1-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44bf1-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44bf1-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="44bf1-123">.NET core SDK 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="44bf1-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="44bf1-124">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="44bf1-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="44bf1-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="44bf1-126">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="44bf1-126">Configure MongoDB</span></span>

<span data-ttu-id="44bf1-127">Pokud používáte Windows, databáze MongoDB nainstaluje na *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="44bf1-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="44bf1-128">Přidat *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* k `Path` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="44bf1-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="44bf1-129">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="44bf1-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="44bf1-130">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="44bf1-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="44bf1-131">Další informace o příkazech prostředí mongo naleznete v tématu [práce s mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="44bf1-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="44bf1-132">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="44bf1-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="44bf1-133">Například *C:\\BooksData* na Windows.</span><span class="sxs-lookup"><span data-stu-id="44bf1-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="44bf1-134">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="44bf1-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="44bf1-135">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="44bf1-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="44bf1-136">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="44bf1-136">Open a command shell.</span></span> <span data-ttu-id="44bf1-137">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="44bf1-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="44bf1-138">Nezapomeňte nahradit `<data_directory_path>` s adresáři, kterou jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="44bf1-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="44bf1-139">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="44bf1-139">Open another command shell instance.</span></span> <span data-ttu-id="44bf1-140">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="44bf1-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="44bf1-141">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="44bf1-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="44bf1-142">Pokud ještě neexistuje, databázi s názvem *BookstoreDb* se vytvoří.</span><span class="sxs-lookup"><span data-stu-id="44bf1-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="44bf1-143">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="44bf1-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="44bf1-144">Vytvoření `Books` kolekce pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="44bf1-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="44bf1-145">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="44bf1-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="44bf1-146">Definovat schéma pro `Books` kolekce a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="44bf1-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="44bf1-147">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="44bf1-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="44bf1-148">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="44bf1-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="44bf1-149">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="44bf1-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="44bf1-150">Přidá automaticky generované schéma `_id` vlastnost typu `ObjectId` pro každý dokument.</span><span class="sxs-lookup"><span data-stu-id="44bf1-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="44bf1-151">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="44bf1-151">The database is ready.</span></span> <span data-ttu-id="44bf1-152">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="44bf1-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="44bf1-153">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44bf1-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="44bf1-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="44bf1-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="44bf1-155">Přejděte na **souboru** > **nové** > **projektu**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="44bf1-156">Vyberte **webové aplikace ASP.NET Core**, pojmenujte projekt *BooksApi*a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="44bf1-157">Vyberte **.NET Core** Cílová architektura a **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-157">Select the **.NET Core** target framework and **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="44bf1-158">Vyberte **API** šablony projektu a klikněte na tlačítko **OK**:</span><span class="sxs-lookup"><span data-stu-id="44bf1-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="44bf1-159">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="44bf1-159">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="44bf1-160">V **Konzola správce balíčků** okno, přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="44bf1-160">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="44bf1-161">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="44bf1-161">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="44bf1-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="44bf1-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="44bf1-163">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="44bf1-163">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="44bf1-164">Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="44bf1-164">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="44bf1-165">Klikněte na tlačítko **Ano** při *'BooksApi' chybí požadované prostředky pro sestavení a ladění. Přidat?*  se zobrazí oznámení.</span><span class="sxs-lookup"><span data-stu-id="44bf1-165">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="44bf1-166">Přejděte [Galerie NuGet: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) určit nejnovější stabilní verze ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="44bf1-166">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="44bf1-167">Otevřít **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="44bf1-167">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="44bf1-168">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="44bf1-168">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="44bf1-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="44bf1-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="44bf1-170">Přejděte na **souboru** > **nové řešení** > **.NET Core** > **aplikace**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-170">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="44bf1-171">Vyberte **webového rozhraní API ASP.NET Core** C# šablony projektu a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-171">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="44bf1-172">Vyberte **.NET Core 2.2** z **Cílová architektura** rozevíracího seznamu a klikněte na tlačítko **Další**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-172">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="44bf1-173">Zadejte *BooksApi* pro **název projektu**a klikněte na tlačítko **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-173">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="44bf1-174">V **řešení** panel, klikněte pravým tlačítkem projekt **závislosti** uzel a vyberte možnost **přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-174">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="44bf1-175">Zadejte *MongoDB.Driver* do vyhledávacího pole, vyberte *MongoDB.Driver* balíček a klikněte na tlačítko **přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="44bf1-175">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="44bf1-176">Klikněte na tlačítko **přijmout** tlačítko **přijetí licence** dialogového okna.</span><span class="sxs-lookup"><span data-stu-id="44bf1-176">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="44bf1-177">Přidání modelu</span><span class="sxs-lookup"><span data-stu-id="44bf1-177">Add a model</span></span>

1. <span data-ttu-id="44bf1-178">Přidat *modely* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="44bf1-178">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="44bf1-179">Přidat `Book` třídu *modely* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44bf1-179">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="44bf1-180">Ve třídě předchozí `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="44bf1-180">In the preceding class, the `Id` property:</span></span>

* <span data-ttu-id="44bf1-181">Je vyžadován pro mapování objektu Common Language Runtime (CLR) ke kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="44bf1-181">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
* <span data-ttu-id="44bf1-182">Je opatřen poznámkou `[BsonId]` se označí jako primární klíč dokumentu této vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="44bf1-182">Is annotated with `[BsonId]` to designate this property as the document's primary key.</span></span>
* <span data-ttu-id="44bf1-183">Je opatřen poznámkou `[BsonRepresentation(BsonType.ObjectId)]` povolit předávání parametru jako typ `string` místo `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="44bf1-183">Is annotated with `[BsonRepresentation(BsonType.ObjectId)]` to allow passing the parameter as type `string` instead of `ObjectId`.</span></span> <span data-ttu-id="44bf1-184">Mongo zpracovává server převod z `string` k `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="44bf1-184">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

<span data-ttu-id="44bf1-185">Další vlastnosti ve třídě, je opatřen poznámkou `[BsonElement]` atribut.</span><span class="sxs-lookup"><span data-stu-id="44bf1-185">Other properties in the class are annotated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="44bf1-186">Hodnota atributu představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="44bf1-186">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="44bf1-187">Přidejte třídu operace CRUD</span><span class="sxs-lookup"><span data-stu-id="44bf1-187">Add a CRUD operations class</span></span>

1. <span data-ttu-id="44bf1-188">Přidat *služby* adresáře do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="44bf1-188">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="44bf1-189">Přidat `BookService` třídu *služby* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44bf1-189">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="44bf1-190">Přidat připojovací řetězec MongoDB do *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="44bf1-190">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="44bf1-191">Předchozí `BookstoreDb` v získat přístup k vlastnosti `BookService` konstruktoru třídy.</span><span class="sxs-lookup"><span data-stu-id="44bf1-191">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="44bf1-192">V `Startup.ConfigureServices`, zaregistrujte `BookService` třídy systémem injektáž závislostí:</span><span class="sxs-lookup"><span data-stu-id="44bf1-192">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="44bf1-193">Předchozí registrace služby je nezbytný pro podporu vkládání konstruktor přijímací třídy.</span><span class="sxs-lookup"><span data-stu-id="44bf1-193">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="44bf1-194">`BookService` Třída používá následující `MongoDB.Driver` členy k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="44bf1-194">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="44bf1-195">`MongoClient` &ndash; Načte instance serveru k provedení operace databáze.</span><span class="sxs-lookup"><span data-stu-id="44bf1-195">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="44bf1-196">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="44bf1-196">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="44bf1-197">`IMongoDatabase` &ndash; Reprezentuje databázi Mongodb pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="44bf1-197">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="44bf1-198">Tento kurz používá Obecné `GetCollection<T>(collection)` metoda v rozhraní k získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="44bf1-198">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="44bf1-199">Operace CRUD lze provést proti kolekci, jakmile tato metoda je volána.</span><span class="sxs-lookup"><span data-stu-id="44bf1-199">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="44bf1-200">V `GetCollection<T>(collection)` volání metody:</span><span class="sxs-lookup"><span data-stu-id="44bf1-200">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="44bf1-201">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="44bf1-201">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="44bf1-202">`T` představuje typ objektu CLR uložená v kolekci.</span><span class="sxs-lookup"><span data-stu-id="44bf1-202">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="44bf1-203">`GetCollection<T>(collection)` Vrátí `MongoCollection` objekt představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="44bf1-203">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="44bf1-204">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="44bf1-204">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="44bf1-205">`Find<T>` &ndash; Vrátí všechny dokumenty v kolekci odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="44bf1-205">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="44bf1-206">`InsertOne` &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="44bf1-206">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="44bf1-207">`ReplaceOne` &ndash; Nahrazuje jeden dokument, který odpovídá kritériím hledání zadaná pomocí zadaného objektu.</span><span class="sxs-lookup"><span data-stu-id="44bf1-207">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="44bf1-208">`DeleteOne` &ndash; Odstraní jeden dokument, který odpovídá kritériím hledání zadaná.</span><span class="sxs-lookup"><span data-stu-id="44bf1-208">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="44bf1-209">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="44bf1-209">Add a controller</span></span>

1. <span data-ttu-id="44bf1-210">Přidat `BooksController` třídu *řadiče* adresáře s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="44bf1-210">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="44bf1-211">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="44bf1-211">The preceding web API controller:</span></span>

    * <span data-ttu-id="44bf1-212">Používá `BookService` pro provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="44bf1-212">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="44bf1-213">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="44bf1-213">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
1. <span data-ttu-id="44bf1-214">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="44bf1-214">Build and run the app.</span></span>
1. <span data-ttu-id="44bf1-215">Přejděte na `http://localhost:<port>/api/books` v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="44bf1-215">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="44bf1-216">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="44bf1-216">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="44bf1-217">Další kroky</span><span class="sxs-lookup"><span data-stu-id="44bf1-217">Next steps</span></span>

<span data-ttu-id="44bf1-218">Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="44bf1-218">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
