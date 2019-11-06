# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1757-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1757-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d1757-102">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="d1757-102">Create a new project.</span></span>
1. <span data-ttu-id="d1757-103">Vyberte **službu pracovního procesu**.</span><span class="sxs-lookup"><span data-stu-id="d1757-103">Select **Worker Service**.</span></span> <span data-ttu-id="d1757-104">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d1757-104">Select **Next**.</span></span>
1. <span data-ttu-id="d1757-105">Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu.</span><span class="sxs-lookup"><span data-stu-id="d1757-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d1757-106">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d1757-106">Select **Create**.</span></span>
1. <span data-ttu-id="d1757-107">V dialogovém okně **vytvořit novou službu pracovního procesu** vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d1757-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1757-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="d1757-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d1757-109">Vytvořte nový projekt.</span><span class="sxs-lookup"><span data-stu-id="d1757-109">Create a new project.</span></span>
1. <span data-ttu-id="d1757-110">Na bočním panelu vyberte **aplikaci** v části **.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="d1757-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="d1757-111">V části **ASP.NET Core**vyberte **Worker** .</span><span class="sxs-lookup"><span data-stu-id="d1757-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="d1757-112">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d1757-112">Select **Next**.</span></span>
1. <span data-ttu-id="d1757-113">Pro **cílovou architekturu**vyberte **.NET Core 3,0** nebo novější.</span><span class="sxs-lookup"><span data-stu-id="d1757-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="d1757-114">Vyberte **Další**.</span><span class="sxs-lookup"><span data-stu-id="d1757-114">Select **Next**.</span></span>
1. <span data-ttu-id="d1757-115">Do pole **název projektu** zadejte název.</span><span class="sxs-lookup"><span data-stu-id="d1757-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="d1757-116">Vyberte **vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="d1757-116">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d1757-117">Rozhraní příkazového řádku .NET Core</span><span class="sxs-lookup"><span data-stu-id="d1757-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d1757-118">Pomocí příkazu [dotnet New](/dotnet/core/tools/dotnet-new) v příkazovém prostředí použijte šablonu Worker Service (`worker`).</span><span class="sxs-lookup"><span data-stu-id="d1757-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="d1757-119">V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorker`.</span><span class="sxs-lookup"><span data-stu-id="d1757-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="d1757-120">Složka pro aplikaci `ContosoWorker` se vytvoří automaticky při spuštění příkazu.</span><span class="sxs-lookup"><span data-stu-id="d1757-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
