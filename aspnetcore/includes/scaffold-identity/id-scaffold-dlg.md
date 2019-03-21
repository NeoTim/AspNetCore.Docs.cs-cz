---
ms.openlocfilehash: 648ed8087c64eaf80fd055fa7dd08041ab9a8752
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320288"
---
<span data-ttu-id="62d8a-101">Spusťte generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="62d8a-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="62d8a-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="62d8a-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="62d8a-103">Z **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt > **přidat** > **novou vygenerovanou položku**.</span><span class="sxs-lookup"><span data-stu-id="62d8a-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="62d8a-104">V levém podokně **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **Identity** > **přidat**.</span><span class="sxs-lookup"><span data-stu-id="62d8a-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="62d8a-105">V **ADD Identity** dialogového okna, vyberte požadované možnosti.</span><span class="sxs-lookup"><span data-stu-id="62d8a-105">In the **ADD Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="62d8a-106">Vyberte existující stránku rozložení nebo rozložení souboru budou přepsány nesprávný kód.</span><span class="sxs-lookup"><span data-stu-id="62d8a-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="62d8a-107">Například `~/Pages/Shared/_Layout.cshtml` pro stránky Razor `~/Views/Shared/_Layout.cshtml` pro projekty MVC</span><span class="sxs-lookup"><span data-stu-id="62d8a-107">For example `~/Pages/Shared/_Layout.cshtml` for Razor Pages `~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
  * <span data-ttu-id="62d8a-108">Vyberte **+** tlačítko pro vytvoření nového **třída kontextu dat**.</span><span class="sxs-lookup"><span data-stu-id="62d8a-108">Select the **+** button to create a new **Data context class**.</span></span>
* <span data-ttu-id="62d8a-109">Vyberte **přidat**.</span><span class="sxs-lookup"><span data-stu-id="62d8a-109">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="62d8a-110">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="62d8a-110">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="62d8a-111">Pokud jste nenainstalovali dříve generátor ASP.NET Core, nainstalujte ho:</span><span class="sxs-lookup"><span data-stu-id="62d8a-111">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="62d8a-112">Přidat odkaz na balíček pro [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do projektu (\*.csproj) souboru.</span><span class="sxs-lookup"><span data-stu-id="62d8a-112">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (\*.csproj) file.</span></span> <span data-ttu-id="62d8a-113">Spusťte následující příkaz v adresáři projektu:</span><span class="sxs-lookup"><span data-stu-id="62d8a-113">Run the following command in the project directory:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="62d8a-114">Spusťte následující příkaz k výpisu možností generátor Identity:</span><span class="sxs-lookup"><span data-stu-id="62d8a-114">Run the following command to list the Identity scaffolder options:</span></span>

```cli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="62d8a-115">Ve složce projektu spusťte s možnostmi, které chcete generátor Identity.</span><span class="sxs-lookup"><span data-stu-id="62d8a-115">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="62d8a-116">Například nastavit identitu pomocí výchozího uživatelského rozhraní a minimální počet souborů, spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="62d8a-116">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```cli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
