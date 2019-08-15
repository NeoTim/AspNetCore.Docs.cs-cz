<span data-ttu-id="94f32-101">Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:</span><span class="sxs-lookup"><span data-stu-id="94f32-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="94f32-102">Parametr</span><span class="sxs-lookup"><span data-stu-id="94f32-102">Parameter</span></span>               | <span data-ttu-id="94f32-103">Popis</span><span class="sxs-lookup"><span data-stu-id="94f32-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="94f32-104">-m</span><span class="sxs-lookup"><span data-stu-id="94f32-104">-m</span></span>  | <span data-ttu-id="94f32-105">Název modelu.</span><span class="sxs-lookup"><span data-stu-id="94f32-105">The name of the model.</span></span> |
| <span data-ttu-id="94f32-106">-dc</span><span class="sxs-lookup"><span data-stu-id="94f32-106">-dc</span></span>  | <span data-ttu-id="94f32-107">Kontext dat</span><span class="sxs-lookup"><span data-stu-id="94f32-107">The data context.</span></span> |
| <span data-ttu-id="94f32-108">– UDL</span><span class="sxs-lookup"><span data-stu-id="94f32-108">-udl</span></span> | <span data-ttu-id="94f32-109">Použijte výchozí rozložení.</span><span class="sxs-lookup"><span data-stu-id="94f32-109">Use the default layout.</span></span> |
| <span data-ttu-id="94f32-110">--relativeFolderPath</span><span class="sxs-lookup"><span data-stu-id="94f32-110">--relativeFolderPath</span></span> | <span data-ttu-id="94f32-111">Relativní cesta k výstupní složce pro vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="94f32-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="94f32-112">--useDefaultLayout</span><span class="sxs-lookup"><span data-stu-id="94f32-112">--useDefaultLayout</span></span> | <span data-ttu-id="94f32-113">Pro zobrazení je nutné použít výchozí rozložení.</span><span class="sxs-lookup"><span data-stu-id="94f32-113">The default layout should be used for the views.</span></span> |
| <span data-ttu-id="94f32-114">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="94f32-114">--referenceScriptLibraries</span></span> | <span data-ttu-id="94f32-115">Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek.</span><span class="sxs-lookup"><span data-stu-id="94f32-115">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="94f32-116">Nápovědu k příkazu získáte pomocí `h`přepínače `aspnet-codegenerator controller` :</span><span class="sxs-lookup"><span data-stu-id="94f32-116">Use the `h` switch to get help on the `aspnet-codegenerator controller` command:</span></span>

```console
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="94f32-117">Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span><span class="sxs-lookup"><span data-stu-id="94f32-117">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span></span>