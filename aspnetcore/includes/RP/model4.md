<a name="codegenerator"></a><span data-ttu-id="8609c-101">Následující tabulka podrobně popisuje ASP.NET Core parametry generátoru kódu:</span><span class="sxs-lookup"><span data-stu-id="8609c-101">The following table details the ASP.NET Core code generator parameters:</span></span>

| <span data-ttu-id="8609c-102">Parametr</span><span class="sxs-lookup"><span data-stu-id="8609c-102">Parameter</span></span>               | <span data-ttu-id="8609c-103">Popis</span><span class="sxs-lookup"><span data-stu-id="8609c-103">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="8609c-104">-m</span><span class="sxs-lookup"><span data-stu-id="8609c-104">-m</span></span>  | <span data-ttu-id="8609c-105">Název modelu.</span><span class="sxs-lookup"><span data-stu-id="8609c-105">The name of the model.</span></span> |
| <span data-ttu-id="8609c-106">-dc</span><span class="sxs-lookup"><span data-stu-id="8609c-106">-dc</span></span>  | <span data-ttu-id="8609c-107">`DbContext` Třída, která se má použít</span><span class="sxs-lookup"><span data-stu-id="8609c-107">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="8609c-108">– UDL</span><span class="sxs-lookup"><span data-stu-id="8609c-108">-udl</span></span> | <span data-ttu-id="8609c-109">Použijte výchozí rozložení.</span><span class="sxs-lookup"><span data-stu-id="8609c-109">Use the default layout.</span></span> |
| <span data-ttu-id="8609c-110">– outDir</span><span class="sxs-lookup"><span data-stu-id="8609c-110">-outDir</span></span> | <span data-ttu-id="8609c-111">Relativní cesta k výstupní složce pro vytvoření zobrazení</span><span class="sxs-lookup"><span data-stu-id="8609c-111">The relative output folder path to create the views.</span></span> |
| <span data-ttu-id="8609c-112">--referenceScriptLibraries</span><span class="sxs-lookup"><span data-stu-id="8609c-112">--referenceScriptLibraries</span></span> | <span data-ttu-id="8609c-113">Přidá `_ValidationScriptsPartial` k úpravám a vytváření stránek.</span><span class="sxs-lookup"><span data-stu-id="8609c-113">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="8609c-114">Nápovědu k příkazu získáte pomocí `h`přepínače `aspnet-codegenerator razorpage` :</span><span class="sxs-lookup"><span data-stu-id="8609c-114">Use the `h` switch to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="8609c-115">Další informace najdete v tématu [dotnet ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span><span class="sxs-lookup"><span data-stu-id="8609c-115">For more information, see [dotnet aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)</span></span> 