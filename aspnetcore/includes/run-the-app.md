# <a name="visual-studio"></a>[<span data-ttu-id="0718a-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0718a-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0718a-102">Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="0718a-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0718a-103">Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0718a-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0718a-104">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="0718a-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0718a-105">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="0718a-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="0718a-106">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="0718a-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="0718a-107">Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.</span><span class="sxs-lookup"><span data-stu-id="0718a-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="0718a-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0718a-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="0718a-109">Stisknutím **klávesCtrl-F5** spusťte bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="0718a-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0718a-110">Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="0718a-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="0718a-111">Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako .</span><span class="sxs-lookup"><span data-stu-id="0718a-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0718a-112">To proto, `localhost` že je standardní název hostitele pro místní počítač.</span><span class="sxs-lookup"><span data-stu-id="0718a-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0718a-113">Localhost slouží pouze webové požadavky z místního počítače.</span><span class="sxs-lookup"><span data-stu-id="0718a-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0718a-114">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="0718a-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0718a-115">V sadě Visual Studio stiskněte **opt-cmd-return** pro spuštění bez ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="0718a-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="0718a-116">Případně přejděte na řádek nabídek a přejděte na **spustit>start bez ladění**.</span><span class="sxs-lookup"><span data-stu-id="0718a-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="0718a-117">Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .</span><span class="sxs-lookup"><span data-stu-id="0718a-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---