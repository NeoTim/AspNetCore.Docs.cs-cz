## <a name="troubleshoot"></a><span data-ttu-id="2b174-101">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="2b174-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="2b174-102">Soubory cookie a údaje o webu</span><span class="sxs-lookup"><span data-stu-id="2b174-102">Cookies and site data</span></span>

<span data-ttu-id="2b174-103">Soubory cookie a data webu mohou přetrvávat v rámci aktualizací aplikací a zasahovat do testování a odstraňování problémů.</span><span class="sxs-lookup"><span data-stu-id="2b174-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="2b174-104">Zrušte následující položky při provádění změn kódu aplikace, změn uživatelského účtu u poskytovatele nebo změn konfigurace aplikace zprostředkovatele:</span><span class="sxs-lookup"><span data-stu-id="2b174-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="2b174-105">Soubory cookie pro přihlášení uživatelů</span><span class="sxs-lookup"><span data-stu-id="2b174-105">User sign-in cookies</span></span>
* <span data-ttu-id="2b174-106">Soubory cookie aplikací</span><span class="sxs-lookup"><span data-stu-id="2b174-106">App cookies</span></span>
* <span data-ttu-id="2b174-107">Uložená a uložená data webu uložená v mezipaměti</span><span class="sxs-lookup"><span data-stu-id="2b174-107">Cached and stored site data</span></span>

<span data-ttu-id="2b174-108">Jedním z přístupů, jak zabránit tomu, aby přetrvávající soubory cookie a data webu zasahovaly do testování a odstraňování problémů, je:</span><span class="sxs-lookup"><span data-stu-id="2b174-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="2b174-109">Pro testování, které můžete nakonfigurovat tak, aby při každém zavření prohlížeče odstranili všechna data souborů cookie a webu, použijte prohlížeč, který můžete nakonfigurovat tak, aby odstranil všechna data souborů cookie a webu.</span><span class="sxs-lookup"><span data-stu-id="2b174-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="2b174-110">Zavřete prohlížeč mezi jakoukoli změnou konfigurace aplikace, testovacího uživatele nebo zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="2b174-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="2b174-111">Spuštění aplikace Server</span><span class="sxs-lookup"><span data-stu-id="2b174-111">Run the Server app</span></span>

<span data-ttu-id="2b174-112">Při testování a řešení potíží s hostovkou aplikace Blazor se ujistěte, že aplikaci spouštěte z projektu **Server.**</span><span class="sxs-lookup"><span data-stu-id="2b174-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="2b174-113">Například v sadě Visual Studio před spuštěním aplikace s některou z následujících přístupů zkontrolujte, že projekt serveru je zvýrazněn v **Průzkumníkovi řešení:**</span><span class="sxs-lookup"><span data-stu-id="2b174-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="2b174-114">Vyberte tlačítko **Spustit**.</span><span class="sxs-lookup"><span data-stu-id="2b174-114">Select the **Run** button.</span></span>
* <span data-ttu-id="2b174-115">Použijte **ladění** > **start ladění** z nabídky.</span><span class="sxs-lookup"><span data-stu-id="2b174-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="2b174-116">Stiskněte <kbd>klávesu F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="2b174-116">Press <kbd>F5</kbd>.</span></span>
