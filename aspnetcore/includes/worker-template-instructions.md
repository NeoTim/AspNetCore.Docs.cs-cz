# <a name="visual-studio"></a>[<span data-ttu-id="b0680-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b0680-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b0680-102">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="b0680-102">Create a new project.</span></span>
1. <span data-ttu-id="b0680-103">Vyberte **položku Pracovní služba**.</span><span class="sxs-lookup"><span data-stu-id="b0680-103">Select **Worker Service**.</span></span> <span data-ttu-id="b0680-104">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="b0680-104">Select **Next**.</span></span>
1. <span data-ttu-id="b0680-105">Zadejte název projektu do pole **Název projektu** nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="b0680-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b0680-106">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b0680-106">Select **Create**.</span></span>
1. <span data-ttu-id="b0680-107">V **dialogovém** okně Vytvořit novou pracovní službu vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b0680-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="b0680-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="b0680-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="b0680-109">Vytvoření nového projektu</span><span class="sxs-lookup"><span data-stu-id="b0680-109">Create a new project.</span></span>
1. <span data-ttu-id="b0680-110">V postranním panelu vyberte **Aplikaci** pod **.NET Core.**</span><span class="sxs-lookup"><span data-stu-id="b0680-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="b0680-111">V části **ASP.NET jádrem**vyberte **Možnost Pracovník** .</span><span class="sxs-lookup"><span data-stu-id="b0680-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="b0680-112">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="b0680-112">Select **Next**.</span></span>
1. <span data-ttu-id="b0680-113">Vyberte **rozhraní .NET Core 3.0** nebo novější pro **cílovou architekturu**.</span><span class="sxs-lookup"><span data-stu-id="b0680-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="b0680-114">Vyberte **další**.</span><span class="sxs-lookup"><span data-stu-id="b0680-114">Select **Next**.</span></span>
1. <span data-ttu-id="b0680-115">Zadejte název do pole **Název projektu.**</span><span class="sxs-lookup"><span data-stu-id="b0680-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="b0680-116">Vyberte **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b0680-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="b0680-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="b0680-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b0680-118">Použijte šablonu`worker`Pracovní služba ( ) s novým příkazem [dotnet](/dotnet/core/tools/dotnet-new) z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="b0680-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="b0680-119">V následujícím příkladu je vytvořena aplikace `ContosoWorker`Pracovní služba s názvem .</span><span class="sxs-lookup"><span data-stu-id="b0680-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="b0680-120">Složka aplikace `ContosoWorker` se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="b0680-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
