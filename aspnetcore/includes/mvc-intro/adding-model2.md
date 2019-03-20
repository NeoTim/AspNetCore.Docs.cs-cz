---
ms.openlocfilehash: de093954fedc0fef1f945e881e1c7a6a24178bdb
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265411"
---
## <a name="add-initial-migration-and-update-the-database"></a><span data-ttu-id="754ca-101">Přidat počáteční migraci a aktualizaci databáze</span><span class="sxs-lookup"><span data-stu-id="754ca-101">Add initial migration and update the database</span></span>

* <span data-ttu-id="754ca-102">Otevřete příkazový řádek a přejděte do adresáře projektu.</span><span class="sxs-lookup"><span data-stu-id="754ca-102">Open a command prompt and navigate to the project directory.</span></span> <span data-ttu-id="754ca-103">(Adresáře, který obsahuje *Startup.cs* souboru).</span><span class="sxs-lookup"><span data-stu-id="754ca-103">(The directory containing the *Startup.cs* file).</span></span>

* <span data-ttu-id="754ca-104">V příkazovém řádku spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="754ca-104">Run the following commands in the command prompt:</span></span>

  ```console
  dotnet restore
  dotnet ef migrations add Initial
  dotnet ef database update
  ```

  <span data-ttu-id="754ca-105">[.NET core](/dotnet/core/tools/index) je na více platforem implementace rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="754ca-105">[.NET Core](/dotnet/core/tools/index) is a cross-platform implementation of .NET.</span></span> <span data-ttu-id="754ca-106">Zde je, co dělat tyto příkazy:</span><span class="sxs-lookup"><span data-stu-id="754ca-106">Here is what these commands do:</span></span>

  * <span data-ttu-id="754ca-107">[DotNet restore](/dotnet/core/tools/dotnet-restore): Stáhne balíčky NuGet, který je zadán v *.csproj* souboru.</span><span class="sxs-lookup"><span data-stu-id="754ca-107">[dotnet restore](/dotnet/core/tools/dotnet-restore): Downloads the NuGet packages specified in the *.csproj* file.</span></span>
  * <span data-ttu-id="754ca-108">`dotnet ef migrations add Initial` Spustí příkaz migrace Entity Framework .NET Core CLI a vytvoří počáteční migraci.</span><span class="sxs-lookup"><span data-stu-id="754ca-108">`dotnet ef migrations add Initial` Runs the Entity Framework .NET Core CLI migrations command and creates the initial migration.</span></span> <span data-ttu-id="754ca-109">Parametr po "Přidání" je název, který přiřadíte k migraci.</span><span class="sxs-lookup"><span data-stu-id="754ca-109">The parameter after "add" is a name that you assign to the migration.</span></span> <span data-ttu-id="754ca-110">Tady pojmenováváte migrace "Počáteční" protože se počáteční migraci databáze.</span><span class="sxs-lookup"><span data-stu-id="754ca-110">Here you're naming the migration "Initial" because it's the initial database migration.</span></span> <span data-ttu-id="754ca-111">Tato operace vytvoří *Data/Migrations/\<datum a čas > _Initial.cs* soubor, který obsahuje příkazy migrace pro přidání *film* tabulky v databázi.</span><span class="sxs-lookup"><span data-stu-id="754ca-111">This operation creates the *Data/Migrations/\<date-time>_Initial.cs* file containing the migration commands to add the *Movie* table to the database.</span></span>
  * <span data-ttu-id="754ca-112">`dotnet ef database update`  Aktualizuje databázi pomocí migrace, kterou jsme právě vytvořili.</span><span class="sxs-lookup"><span data-stu-id="754ca-112">`dotnet ef database update`  Updates the database with the migration we just created.</span></span>

<span data-ttu-id="754ca-113">V dalším kurzu získáte informace o databázi a připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="754ca-113">You'll learn about the database and connection string in the next tutorial.</span></span> <span data-ttu-id="754ca-114">Získáte informace o změn datových modelů v [přidat pole](xref:tutorials/first-mvc-app/new-field) kurzu.</span><span class="sxs-lookup"><span data-stu-id="754ca-114">You'll learn about data model changes in the [Add a field](xref:tutorials/first-mvc-app/new-field) tutorial.</span></span>
