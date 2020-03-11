# <a name="visual-studio"></a>[<span data-ttu-id="548fa-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="548fa-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="548fa-102">Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="548fa-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="548fa-103">Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="548fa-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="548fa-104">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="548fa-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="548fa-105">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="548fa-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="548fa-106">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="548fa-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="548fa-107">Když Visual Studio vytvoří webový projekt, pro webový server se použije náhodný port.</span><span class="sxs-lookup"><span data-stu-id="548fa-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="548fa-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="548fa-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="548fa-109">Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="548fa-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="548fa-110">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="548fa-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="548fa-111">V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`.</span><span class="sxs-lookup"><span data-stu-id="548fa-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="548fa-112">Důvodem je, že `localhost` je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="548fa-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="548fa-113">Localhost obsluhuje pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="548fa-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="548fa-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="548fa-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="548fa-115">V aplikaci Visual Studio stiskněte klávesu **opt-cmd-Return** , aby se spouštěla bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="548fa-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="548fa-116">Můžete také přejít na panel nabídek a přejít na **příkaz spustit > spustit bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="548fa-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="548fa-117">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="548fa-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---