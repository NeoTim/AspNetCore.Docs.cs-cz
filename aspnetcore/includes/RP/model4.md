<a name="codegenerator"></a><span data-ttu-id="090c4-101">Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:</span><span class="sxs-lookup"><span data-stu-id="090c4-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="090c4-102">Parametr</span><span class="sxs-lookup"><span data-stu-id="090c4-102">Parameter</span></span>               | <span data-ttu-id="090c4-103">Popis</span><span class="sxs-lookup"><span data-stu-id="090c4-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="090c4-104">-m</span><span class="sxs-lookup"><span data-stu-id="090c4-104">-m</span></span>  | <span data-ttu-id="090c4-105">Název modelu.</span><span class="sxs-lookup"><span data-stu-id="090c4-105">The name of the model.</span></span> |
| <span data-ttu-id="090c4-106">– DC</span><span class="sxs-lookup"><span data-stu-id="090c4-106">-dc</span></span>  | <span data-ttu-id="090c4-107">Třída `DbContext`, která se má použít</span><span class="sxs-lookup"><span data-stu-id="090c4-107">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="090c4-108">– UDL</span><span class="sxs-lookup"><span data-stu-id="090c4-108">-udl</span></span> | <span data-ttu-id="090c4-109">Použijte výchozí rozložení.</span><span class="sxs-lookup"><span data-stu-id="090c4-109">Use the default layout.</span></span> |
| <span data-ttu-id="090c4-110">– outDir</span><span class="sxs-lookup"><span data-stu-id="090c4-110">-outDir</span></span> | <span data-ttu-id="090c4-111">Relativní cesta k výstupní složce pro vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="090c4-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="090c4-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="090c4-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="090c4-113">Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek.</span><span class="sxs-lookup"><span data-stu-id="090c4-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="090c4-114">Nápovědu k příkazu `aspnet-codegenerator razorpage` získáte pomocí `h`ovém přepínači:</span><span class="sxs-lookup"><span data-stu-id="090c4-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="090c4-115">Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="090c4-115">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
