---
title: Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB
author: prkhandelwal
description: V tomto kurzu se dozvíte, jak vytvořit webové rozhraní API ASP.NET Core pomocí databáze NoSQL v databázi MongoDB.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 0e7881aa93953866c7a90eb62de64c4c000a866c
ms.sourcegitcommit: 40dc9b00131985abcd99bd567647420d798e798a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78935457"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="ab765-103">Vytvoření webového rozhraní API pomocí ASP.NET Core využívající databázi MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="ab765-104">Od [Pratik Khandelwal](https://twitter.com/K2Prk) a [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="ab765-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ab765-105">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="ab765-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="ab765-106">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="ab765-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ab765-107">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-107">Configure MongoDB</span></span>
> * <span data-ttu-id="ab765-108">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="ab765-109">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="ab765-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="ab765-110">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="ab765-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="ab765-111">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="ab765-111">Customize JSON serialization</span></span>

<span data-ttu-id="ab765-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab765-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ab765-113">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="ab765-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab765-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab765-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="ab765-115">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ab765-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="ab765-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="ab765-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ab765-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab765-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ab765-119">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ab765-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="ab765-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ab765-121">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="ab765-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab765-123">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab765-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ab765-124">.NET Core SDK 3.0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ab765-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="ab765-125">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ab765-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="ab765-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="ab765-127">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-127">Configure MongoDB</span></span>

<span data-ttu-id="ab765-128">Pokud používáte systém Windows, MongoDB je nainstalován v *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="ab765-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="ab765-129">Přidejte *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* do proměnné prostředí `Path`.</span><span class="sxs-lookup"><span data-stu-id="ab765-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="ab765-130">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="ab765-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="ab765-131">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="ab765-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="ab765-132">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="ab765-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="ab765-133">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="ab765-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="ab765-134">Například *C:\\BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="ab765-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="ab765-135">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="ab765-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="ab765-136">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="ab765-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="ab765-137">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="ab765-137">Open a command shell.</span></span> <span data-ttu-id="ab765-138">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="ab765-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="ab765-139">Nezapomeňte nahradit `<data_directory_path>` adresářem, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="ab765-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="ab765-140">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="ab765-140">Open another command shell instance.</span></span> <span data-ttu-id="ab765-141">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="ab765-142">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="ab765-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="ab765-143">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="ab765-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="ab765-144">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="ab765-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="ab765-145">Pomocí následujícího příkazu vytvořte kolekci `Books`:</span><span class="sxs-lookup"><span data-stu-id="ab765-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="ab765-146">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="ab765-147">Definujte schéma pro kolekci `Books` a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="ab765-148">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-148">The following result is displayed:</span></span>

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
   > <span data-ttu-id="ab765-149">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="ab765-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="ab765-150">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="ab765-151">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="ab765-152">Schéma přidá do každého dokumentu automaticky generovanou vlastnost `_id` typu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ab765-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="ab765-153">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="ab765-153">The database is ready.</span></span> <span data-ttu-id="ab765-154">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab765-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="ab765-155">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab765-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab765-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab765-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ab765-157">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ab765-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ab765-158">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-159">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-160">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 3,0**.</span><span class="sxs-lookup"><span data-stu-id="ab765-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="ab765-161">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-162">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ab765-163">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="ab765-164">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab765-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ab765-166">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ab765-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="ab765-167">Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ab765-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="ab765-168">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?** .</span><span class="sxs-lookup"><span data-stu-id="ab765-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="ab765-169">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="ab765-169">Select **Yes**.</span></span>
1. <span data-ttu-id="ab765-170">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ab765-171">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="ab765-172">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab765-173">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab765-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ab765-174">Přejít na **soubor** > **nové řešení** > > **aplikaci** **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ab765-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="ab765-175">Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-176">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 3,0** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-177">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-178">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="ab765-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="ab765-179">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="ab765-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="ab765-180">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="ab765-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="ab765-181">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="ab765-181">Add an entity model</span></span>

1. <span data-ttu-id="ab765-182">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="ab765-183">Přidejte třídu `Book` do adresáře *modelů* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ab765-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="ab765-184">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="ab765-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="ab765-185">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="ab765-186">Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="ab765-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="ab765-187">Je opatřen poznámkou [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="ab765-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="ab765-188">Mongo zpracovává převod z `string` na `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ab765-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="ab765-189">Vlastnost `BookName` je označena atributem [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="ab765-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="ab765-190">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="ab765-191">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="ab765-191">Add a configuration model</span></span>

1. <span data-ttu-id="ab765-192">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="ab765-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="ab765-193">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="ab765-194">Předchozí třída `BookstoreDatabaseSettings` slouží k uložení hodnot vlastností `BookstoreDatabaseSettings` souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ab765-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="ab765-195">Názvy JSON a C# Property jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="ab765-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="ab765-196">Přidejte do `Startup.ConfigureServices`následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="ab765-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="ab765-197">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="ab765-197">In the preceding code:</span></span>

   * <span data-ttu-id="ab765-198">Instance konfigurace, na kterou se vazba oddílu `BookstoreDatabaseSettings` souboru *appSettings. JSON* váže, se zaregistruje v kontejneru vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="ab765-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="ab765-199">Například vlastnost `ConnectionString` objektu `BookstoreDatabaseSettings` je naplněna vlastností `BookstoreDatabaseSettings:ConnectionString` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ab765-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="ab765-200">Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="ab765-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ab765-201">Při vložení se instance rozhraní přeloží na objekt `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="ab765-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="ab765-202">Do horní části *Startup.cs* přidejte následující kód pro vyřešení `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings`ch odkazů:</span><span class="sxs-lookup"><span data-stu-id="ab765-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="ab765-203">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="ab765-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="ab765-204">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="ab765-205">Do adresáře *služby* přidejte třídu `BookService` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="ab765-206">V předchozím kódu je instance `IBookstoreDatabaseSettings` načtena z DI prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="ab765-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="ab765-207">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="ab765-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="ab765-208">Přidejte do `Startup.ConfigureServices`následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="ab765-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="ab765-209">V předchozím kódu je třída `BookService` zaregistrována pomocí příkazu DI k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="ab765-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="ab765-210">Životnost služby typu Singleton je nejvhodnější, protože `BookService` přebírá přímo závislost na `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="ab765-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="ab765-211">Podle oficiálních `MongoClient` [pokynů pro opětovné použití klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by mělo být zaregistrováno v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="ab765-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="ab765-212">Přidejte následující kód na začátek *Startup.cs* k vyřešení odkazu `BookService`:</span><span class="sxs-lookup"><span data-stu-id="ab765-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="ab765-213">Třída `BookService` používá následující `MongoDB.Driver` členů k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="ab765-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="ab765-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="ab765-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="ab765-215">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="ab765-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="ab765-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="ab765-217">V tomto kurzu se používá obecná metoda [GetCollection\<TDocument > (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="ab765-218">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="ab765-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="ab765-219">Ve volání metody `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="ab765-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="ab765-220">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="ab765-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="ab765-221">`TDocument` představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="ab765-222">`GetCollection<TDocument>(collection)` vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="ab765-223">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="ab765-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="ab765-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="ab765-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="ab765-225">[Find\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="ab765-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="ab765-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="ab765-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahradí jediný dokument, který odpovídá zadaným kritériím vyhledávání, zadaným objektem.</span><span class="sxs-lookup"><span data-stu-id="ab765-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ab765-228">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="ab765-228">Add a controller</span></span>

<span data-ttu-id="ab765-229">Přidejte třídu `BooksController` do adresáře *Controllers* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="ab765-230">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="ab765-230">The preceding web API controller:</span></span>

* <span data-ttu-id="ab765-231">Používá třídu `BookService` k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="ab765-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="ab765-232">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="ab765-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="ab765-233">Volá <xref:System.Web.Http.ApiController.CreatedAtRoute*> v metodě `Create` akce, která vrátí odpověď [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="ab765-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="ab765-234">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="ab765-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="ab765-235">`CreatedAtRoute` také přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ab765-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="ab765-236">Hlavička `Location` Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="ab765-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="ab765-237">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="ab765-237">Test the web API</span></span>

1. <span data-ttu-id="ab765-238">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ab765-238">Build and run the app.</span></span>

1. <span data-ttu-id="ab765-239">Přejděte na `http://localhost:<port>/api/books` a otestujte metodu `Get` akci bez parametrů řadiče.</span><span class="sxs-lookup"><span data-stu-id="ab765-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="ab765-240">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="ab765-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="ab765-241">Přejděte na `http://localhost:<port>/api/books/{id here}` pro otestování metody `Get` akce, která je přetížena řadičem.</span><span class="sxs-lookup"><span data-stu-id="ab765-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="ab765-242">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="ab765-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="ab765-243">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="ab765-243">Configure JSON serialization options</span></span>

<span data-ttu-id="ab765-244">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="ab765-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="ab765-245">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="ab765-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="ab765-246">Vlastnost `bookName` by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="ab765-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="ab765-247">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="ab765-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="ab765-248">JSON.NET se odebral ze sdílené architektury ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="ab765-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="ab765-249">Přidejte odkaz na balíček [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span><span class="sxs-lookup"><span data-stu-id="ab765-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="ab765-250">V `Startup.ConfigureServices`zřetězit následující zvýrazněný kód na volání metody `AddControllers`:</span><span class="sxs-lookup"><span data-stu-id="ab765-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="ab765-251">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="ab765-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="ab765-252">Například vlastnost `Author` `Book` třídy je serializována jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="ab765-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="ab765-253">V části *Models/Book. cs*přihlaste k vlastnosti `BookName` následující atribut [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="ab765-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="ab765-254">Hodnota atributu `[JsonProperty]` `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ab765-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="ab765-255">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení reference `[JsonProperty]` atributu:</span><span class="sxs-lookup"><span data-stu-id="ab765-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="ab765-256">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="ab765-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="ab765-257">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="ab765-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ab765-258">V tomto kurzu se vytvoří webové rozhraní API, které provádí operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL.</span><span class="sxs-lookup"><span data-stu-id="ab765-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="ab765-259">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="ab765-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ab765-260">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-260">Configure MongoDB</span></span>
> * <span data-ttu-id="ab765-261">Vytvoření databáze MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="ab765-262">Definování kolekce MongoDB a schématu</span><span class="sxs-lookup"><span data-stu-id="ab765-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="ab765-263">Provádění operací MongoDB CRUD z webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="ab765-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="ab765-264">Přizpůsobení serializace JSON</span><span class="sxs-lookup"><span data-stu-id="ab765-264">Customize JSON serialization</span></span>

<span data-ttu-id="ab765-265">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ab765-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ab765-266">Předpoklady</span><span class="sxs-lookup"><span data-stu-id="ab765-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab765-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab765-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="ab765-268">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="ab765-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="ab765-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) s úlohou **vývoje ASP.NET a webu**</span><span class="sxs-lookup"><span data-stu-id="ab765-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="ab765-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab765-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="ab765-272">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="ab765-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="ab765-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="ab765-274">C# pro Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="ab765-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab765-276">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab765-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ab765-277">.NET Core SDK 2,2</span><span class="sxs-lookup"><span data-stu-id="ab765-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="ab765-278">Visual Studio pro Mac verze 7,7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ab765-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="ab765-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="ab765-280">Nakonfigurovat MongoDB</span><span class="sxs-lookup"><span data-stu-id="ab765-280">Configure MongoDB</span></span>

<span data-ttu-id="ab765-281">Pokud používáte systém Windows, MongoDB je nainstalován v *C:\\Program Files\\MongoDB* ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="ab765-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="ab765-282">Přidejte *C:\\Program Files\\MongoDB\\Server\\\<version_number >\\bin* do proměnné prostředí `Path`.</span><span class="sxs-lookup"><span data-stu-id="ab765-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="ab765-283">Tato změna umožňuje MongoDB přístup z libovolného místa na vývojovém počítači.</span><span class="sxs-lookup"><span data-stu-id="ab765-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="ab765-284">Použití prostředí mongo v následujících krocích k vytvoření databáze, ujistěte se, kolekce a ukládat dokumenty.</span><span class="sxs-lookup"><span data-stu-id="ab765-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="ab765-285">Další informace o příkazech prostředí Mongo najdete v tématu [práce s prostředím Mongo](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="ab765-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="ab765-286">Vyberte adresář na vývojovém počítači pro ukládání dat.</span><span class="sxs-lookup"><span data-stu-id="ab765-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="ab765-287">Například *C:\\BooksData* ve Windows.</span><span class="sxs-lookup"><span data-stu-id="ab765-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="ab765-288">Vytvořte adresář, pokud neexistuje.</span><span class="sxs-lookup"><span data-stu-id="ab765-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="ab765-289">Prostředí mongo nebude vytvářet nové adresáře.</span><span class="sxs-lookup"><span data-stu-id="ab765-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="ab765-290">Otevřete příkazové okno.</span><span class="sxs-lookup"><span data-stu-id="ab765-290">Open a command shell.</span></span> <span data-ttu-id="ab765-291">Spusťte následující příkaz pro připojení k MongoDB na výchozím portu 27017.</span><span class="sxs-lookup"><span data-stu-id="ab765-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="ab765-292">Nezapomeňte nahradit `<data_directory_path>` adresářem, který jste zvolili v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="ab765-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="ab765-293">Otevřete jiná instance příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="ab765-293">Open another command shell instance.</span></span> <span data-ttu-id="ab765-294">Připojení k databázi testu výchozí spuštěním následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="ab765-295">Spuštěním následujícího příkazu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="ab765-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="ab765-296">Pokud ještě neexistuje, vytvoří se databáze s názvem *BookstoreDb* .</span><span class="sxs-lookup"><span data-stu-id="ab765-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="ab765-297">Pokud databáze neexistuje, je připojení otevřené transakce.</span><span class="sxs-lookup"><span data-stu-id="ab765-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="ab765-298">Pomocí následujícího příkazu vytvořte kolekci `Books`:</span><span class="sxs-lookup"><span data-stu-id="ab765-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="ab765-299">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="ab765-300">Definujte schéma pro kolekci `Books` a vložte dva dokumenty pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="ab765-301">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-301">The following result is displayed:</span></span>

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
   > <span data-ttu-id="ab765-302">ID zobrazené v tomto článku se při spuštění této ukázky neshoduje s ID.</span><span class="sxs-lookup"><span data-stu-id="ab765-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="ab765-303">Zobrazte dokumenty v databázi pomocí následujícího příkazu:</span><span class="sxs-lookup"><span data-stu-id="ab765-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="ab765-304">Zobrazí se následující výsledek:</span><span class="sxs-lookup"><span data-stu-id="ab765-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="ab765-305">Schéma přidá do každého dokumentu automaticky generovanou vlastnost `_id` typu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ab765-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="ab765-306">Databáze je připravena.</span><span class="sxs-lookup"><span data-stu-id="ab765-306">The database is ready.</span></span> <span data-ttu-id="ab765-307">Můžete začít vytvářet webové rozhraní API ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab765-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="ab765-308">Vytvoření projektu webové rozhraní API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab765-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab765-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab765-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ab765-310">Přejít na **soubor** > **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ab765-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ab765-311">Vyberte ASP.NET Core typ projektu **webové aplikace** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-312">Pojmenujte projekt *BooksApi*a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-313">Vyberte cílové rozhraní **.NET Core** a **ASP.NET Core 2,2**.</span><span class="sxs-lookup"><span data-stu-id="ab765-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="ab765-314">Vyberte šablonu projektu **rozhraní API** a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-315">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ab765-316">V okně **konzoly Správce balíčků** přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="ab765-317">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab765-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab765-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ab765-319">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ab765-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="ab765-320">Nový ASP.NET Core webové rozhraní API projekt cílí na .NET Core je generována a otevřít ve Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="ab765-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="ab765-321">Až se ikona plamene u OmniSharp stavového řádku změní na zelenou, dialogové okno požádá **o požadované prostředky k sestavení a ladění chybí v ' BooksApi '. Přidat je?** .</span><span class="sxs-lookup"><span data-stu-id="ab765-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="ab765-322">Vyberte **Ano**.</span><span class="sxs-lookup"><span data-stu-id="ab765-322">Select **Yes**.</span></span>
1. <span data-ttu-id="ab765-323">Navštivte [galerii NuGet: MongoDB. Driver](https://www.nuget.org/packages/MongoDB.Driver/) a určete nejnovější stabilní verzi ovladače .NET pro MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="ab765-324">Otevřete **integrovaný terminál** a přejděte do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="ab765-325">Spusťte následující příkaz k instalaci ovladače .NET pro MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab765-326">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab765-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ab765-327">Přejít na **soubor** > **nové řešení** > > **aplikaci** **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ab765-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="ab765-328">Vyberte šablonu projektu **ASP.NET Core webového rozhraní API** C# a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-329">V rozevíracím seznamu **cílové rozhraní** vyberte **.NET Core 2,2** a vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="ab765-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="ab765-330">Jako **název projektu**zadejte *BooksApi* a vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="ab765-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="ab765-331">Na panelu **řešení** klikněte pravým tlačítkem myši na uzel **závislosti** projektu a vyberte možnost **Přidat balíčky**.</span><span class="sxs-lookup"><span data-stu-id="ab765-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="ab765-332">Do vyhledávacího pole zadejte *MongoDB. Driver* , vyberte balíček *MongoDB. Driver* a vyberte **Přidat balíček**.</span><span class="sxs-lookup"><span data-stu-id="ab765-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="ab765-333">V dialogu pro **přijetí licence** vyberte tlačítko **přijmout** .</span><span class="sxs-lookup"><span data-stu-id="ab765-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="ab765-334">Přidání modelu entity</span><span class="sxs-lookup"><span data-stu-id="ab765-334">Add an entity model</span></span>

1. <span data-ttu-id="ab765-335">Přidejte adresář *modelů* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="ab765-336">Přidejte třídu `Book` do adresáře *modelů* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ab765-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

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

   <span data-ttu-id="ab765-337">V předchozí třídě `Id` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="ab765-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="ab765-338">Je vyžadován pro mapování objektu modulu CLR (Common Language Runtime) na kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="ab765-339">Je s poznámkami [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) k označení této vlastnosti jako primárního klíče dokumentu.</span><span class="sxs-lookup"><span data-stu-id="ab765-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="ab765-340">Je opatřen poznámkou [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) , aby bylo možné předat parametr jako typ `string` namísto struktury [objectID](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) .</span><span class="sxs-lookup"><span data-stu-id="ab765-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="ab765-341">Mongo zpracovává převod z `string` na `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="ab765-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="ab765-342">Vlastnost `BookName` je označena atributem [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) .</span><span class="sxs-lookup"><span data-stu-id="ab765-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="ab765-343">Hodnota atributu `Name` představuje název vlastnosti v kolekci MongoDB.</span><span class="sxs-lookup"><span data-stu-id="ab765-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="ab765-344">Přidat konfigurační model</span><span class="sxs-lookup"><span data-stu-id="ab765-344">Add a configuration model</span></span>

1. <span data-ttu-id="ab765-345">Do souboru *appSettings. JSON*přidejte následující hodnoty konfigurace databáze:</span><span class="sxs-lookup"><span data-stu-id="ab765-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="ab765-346">Přidejte soubor *BookstoreDatabaseSettings.cs* do adresáře *Models* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="ab765-347">Předchozí třída `BookstoreDatabaseSettings` slouží k uložení hodnot vlastností `BookstoreDatabaseSettings` souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ab765-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="ab765-348">Názvy JSON a C# Property jsou pojmenovány stejně, aby bylo možné zjednodušit proces mapování.</span><span class="sxs-lookup"><span data-stu-id="ab765-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="ab765-349">Přidejte do `Startup.ConfigureServices`následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="ab765-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="ab765-350">V předchozím kódu:</span><span class="sxs-lookup"><span data-stu-id="ab765-350">In the preceding code:</span></span>

   * <span data-ttu-id="ab765-351">Instance konfigurace, na kterou se vazba oddílu `BookstoreDatabaseSettings` souboru *appSettings. JSON* váže, se zaregistruje v kontejneru vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="ab765-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="ab765-352">Například vlastnost `ConnectionString` objektu `BookstoreDatabaseSettings` je naplněna vlastností `BookstoreDatabaseSettings:ConnectionString` v souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ab765-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="ab765-353">Rozhraní `IBookstoreDatabaseSettings` je registrováno v DI s [životností služby](xref:fundamentals/dependency-injection#service-lifetimes)typu singleton.</span><span class="sxs-lookup"><span data-stu-id="ab765-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ab765-354">Při vložení se instance rozhraní přeloží na objekt `BookstoreDatabaseSettings`.</span><span class="sxs-lookup"><span data-stu-id="ab765-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="ab765-355">Do horní části *Startup.cs* přidejte následující kód pro vyřešení `BookstoreDatabaseSettings` a `IBookstoreDatabaseSettings`ch odkazů:</span><span class="sxs-lookup"><span data-stu-id="ab765-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="ab765-356">Přidání služby operace CRUD</span><span class="sxs-lookup"><span data-stu-id="ab765-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="ab765-357">Přidejte adresář *služeb* do kořenového adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="ab765-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="ab765-358">Do adresáře *služby* přidejte třídu `BookService` s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="ab765-359">V předchozím kódu je instance `IBookstoreDatabaseSettings` načtena z DI prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="ab765-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="ab765-360">Tento postup poskytuje přístup k hodnotám konfigurace *appSettings. JSON* , které byly přidány do oddílu [přidat konfigurační model](#add-a-configuration-model) .</span><span class="sxs-lookup"><span data-stu-id="ab765-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="ab765-361">Přidejte do `Startup.ConfigureServices`následující zvýrazněný kód:</span><span class="sxs-lookup"><span data-stu-id="ab765-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="ab765-362">V předchozím kódu je třída `BookService` zaregistrována pomocí příkazu DI k podpoře injektáže konstruktoru v rámci využívání tříd.</span><span class="sxs-lookup"><span data-stu-id="ab765-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="ab765-363">Životnost služby typu Singleton je nejvhodnější, protože `BookService` přebírá přímo závislost na `MongoClient`.</span><span class="sxs-lookup"><span data-stu-id="ab765-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="ab765-364">Podle oficiálních `MongoClient` [pokynů pro opětovné použití klienta Mongo](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)by mělo být zaregistrováno v di s životností služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="ab765-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="ab765-365">Přidejte následující kód na začátek *Startup.cs* k vyřešení odkazu `BookService`:</span><span class="sxs-lookup"><span data-stu-id="ab765-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="ab765-366">Třída `BookService` používá následující `MongoDB.Driver` členů k provádění operací CRUD proti databázi:</span><span class="sxs-lookup"><span data-stu-id="ab765-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="ab765-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; přečte instanci serveru pro provádění databázových operací.</span><span class="sxs-lookup"><span data-stu-id="ab765-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="ab765-368">Konstruktor Tato třída poskytuje připojovacího řetězce MongoDB:</span><span class="sxs-lookup"><span data-stu-id="ab765-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="ab765-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; představuje databázi Mongo pro provádění operací.</span><span class="sxs-lookup"><span data-stu-id="ab765-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="ab765-370">V tomto kurzu se používá obecná metoda [GetCollection\<TDocument > (Collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) na rozhraní pro získání přístupu k datům v určité kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="ab765-371">Provede operace CRUD proti kolekci po volání této metody.</span><span class="sxs-lookup"><span data-stu-id="ab765-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="ab765-372">Ve volání metody `GetCollection<TDocument>(collection)`:</span><span class="sxs-lookup"><span data-stu-id="ab765-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="ab765-373">`collection` představuje název kolekce.</span><span class="sxs-lookup"><span data-stu-id="ab765-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="ab765-374">`TDocument` představuje typ objektu CLR uložený v kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="ab765-375">`GetCollection<TDocument>(collection)` vrátí objekt [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) představující kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="ab765-376">V tomto kurzu jsou vyvolány následující metody na kolekci:</span><span class="sxs-lookup"><span data-stu-id="ab765-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="ab765-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; odstraní jeden dokument, který odpovídá zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="ab765-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="ab765-378">[Find\<TDocument >](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; vrátí všechny dokumenty v kolekci, které odpovídají zadaným kritériím hledání.</span><span class="sxs-lookup"><span data-stu-id="ab765-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="ab765-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Vloží zadaný objekt jako nový dokument v kolekci.</span><span class="sxs-lookup"><span data-stu-id="ab765-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="ab765-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; nahradí jediný dokument, který odpovídá zadaným kritériím vyhledávání, zadaným objektem.</span><span class="sxs-lookup"><span data-stu-id="ab765-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="ab765-381">Přidání kontroleru</span><span class="sxs-lookup"><span data-stu-id="ab765-381">Add a controller</span></span>

<span data-ttu-id="ab765-382">Přidejte třídu `BooksController` do adresáře *Controllers* následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ab765-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="ab765-383">Předchozí kontroler web API:</span><span class="sxs-lookup"><span data-stu-id="ab765-383">The preceding web API controller:</span></span>

* <span data-ttu-id="ab765-384">Používá třídu `BookService` k provádění operací CRUD.</span><span class="sxs-lookup"><span data-stu-id="ab765-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="ab765-385">Obsahuje metody akce, který podporuje požadavky GET, POST, PUT a DELETE HTTP.</span><span class="sxs-lookup"><span data-stu-id="ab765-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="ab765-386">Volá <xref:System.Web.Http.ApiController.CreatedAtRoute*> v metodě `Create` akce, která vrátí odpověď [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) .</span><span class="sxs-lookup"><span data-stu-id="ab765-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="ab765-387">Stavový kód 201 je standardní odpověď pro metodu HTTP POST, která vytvoří nový prostředek na serveru.</span><span class="sxs-lookup"><span data-stu-id="ab765-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="ab765-388">`CreatedAtRoute` také přidá hlavičku `Location` k odpovědi.</span><span class="sxs-lookup"><span data-stu-id="ab765-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="ab765-389">Hlavička `Location` Určuje identifikátor URI nově vytvořené knihy.</span><span class="sxs-lookup"><span data-stu-id="ab765-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="ab765-390">Testování webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="ab765-390">Test the web API</span></span>

1. <span data-ttu-id="ab765-391">Sestavte a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ab765-391">Build and run the app.</span></span>

1. <span data-ttu-id="ab765-392">Přejděte na `http://localhost:<port>/api/books` a otestujte metodu `Get` akci bez parametrů řadiče.</span><span class="sxs-lookup"><span data-stu-id="ab765-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="ab765-393">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="ab765-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="ab765-394">Přejděte na `http://localhost:<port>/api/books/{id here}` pro otestování metody `Get` akce, která je přetížena řadičem.</span><span class="sxs-lookup"><span data-stu-id="ab765-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="ab765-395">Zobrazí se následující odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="ab765-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="ab765-396">Konfigurace možností serializace JSON</span><span class="sxs-lookup"><span data-stu-id="ab765-396">Configure JSON serialization options</span></span>

<span data-ttu-id="ab765-397">Existují dvě podrobnosti o tom, jak můžete změnit informace o odpovědích JSON vrácených v části [Test webového rozhraní API](#test-the-web-api) :</span><span class="sxs-lookup"><span data-stu-id="ab765-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="ab765-398">Názvy vlastností výchozí ve stylu CamelCase velká a malá písmena by se měly změnit tak, aby odpovídaly názvu vlastností objektu CLR na velká písmena Pascal.</span><span class="sxs-lookup"><span data-stu-id="ab765-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="ab765-399">Vlastnost `bookName` by měla být vrácena jako `Name`.</span><span class="sxs-lookup"><span data-stu-id="ab765-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="ab765-400">Chcete-li splnit předchozí požadavky, proveďte následující změny:</span><span class="sxs-lookup"><span data-stu-id="ab765-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="ab765-401">V `Startup.ConfigureServices`zřetězit následující zvýrazněný kód na volání metody `AddMvc`:</span><span class="sxs-lookup"><span data-stu-id="ab765-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="ab765-402">V předchozí změně názvy vlastností v serializovaných odpovědích JSON webového rozhraní API odpovídají jejich odpovídajícím názvům vlastností v typu objektu CLR.</span><span class="sxs-lookup"><span data-stu-id="ab765-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="ab765-403">Například vlastnost `Author` `Book` třídy je serializována jako `Author`.</span><span class="sxs-lookup"><span data-stu-id="ab765-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="ab765-404">V části *Models/Book. cs*přihlaste k vlastnosti `BookName` následující atribut [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) :</span><span class="sxs-lookup"><span data-stu-id="ab765-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="ab765-405">Hodnota atributu `[JsonProperty]` `Name` představuje název vlastnosti v serializované odpovědi JSON webového rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ab765-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="ab765-406">Přidejte následující kód na začátek *modelů/Book. cs* pro vyřešení reference `[JsonProperty]` atributu:</span><span class="sxs-lookup"><span data-stu-id="ab765-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="ab765-407">Opakujte kroky definované v části [Test webového rozhraní API](#test-the-web-api) .</span><span class="sxs-lookup"><span data-stu-id="ab765-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="ab765-408">Všimněte si rozdílu v názvech vlastností JSON.</span><span class="sxs-lookup"><span data-stu-id="ab765-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="ab765-409">Přidání podpory ověřování do webového rozhraní API</span><span class="sxs-lookup"><span data-stu-id="ab765-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="ab765-410">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ab765-410">Next steps</span></span>

<span data-ttu-id="ab765-411">Další informace o vytváření webových rozhraní API ASP.NET Core naleznete na následujících odkazech:</span><span class="sxs-lookup"><span data-stu-id="ab765-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="ab765-412">YouTube verze tohoto článku</span><span class="sxs-lookup"><span data-stu-id="ab765-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
