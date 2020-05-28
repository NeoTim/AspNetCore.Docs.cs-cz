---
<span data-ttu-id="29d0c-101">Název: Autor: Popis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="29d0c-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29d0c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29d0c-102">'Blazor'</span></span>
- <span data-ttu-id="29d0c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29d0c-103">'Identity'</span></span>
- <span data-ttu-id="29d0c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29d0c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="29d0c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29d0c-105">'Razor'</span></span>
- <span data-ttu-id="29d0c-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="29d0c-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="29d0c-107">ASP.NET Core zátěžové nebo zátěžové testování</span><span class="sxs-lookup"><span data-stu-id="29d0c-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="29d0c-108">Zátěžové testování a zátěžové testování jsou důležité k zajištění toho, aby byla webová aplikace výkonná a škálovatelná.</span><span class="sxs-lookup"><span data-stu-id="29d0c-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="29d0c-109">Jejich cíle se liší, i když často sdílejí podobné testy.</span><span class="sxs-lookup"><span data-stu-id="29d0c-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="29d0c-110">**Zátěžové testy**: Otestujte, jestli aplikace dokáže zpracovat zadané zatížení uživatelů pro určitý scénář a přitom stále vyhovuje cíli odpovědi.</span><span class="sxs-lookup"><span data-stu-id="29d0c-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="29d0c-111">Aplikace se spouští za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="29d0c-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="29d0c-112">**Zátěžové testy**: stabilita testovací aplikace při provozu za extrémních podmínek, často po dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="29d0c-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="29d0c-113">Testy zadávají vysoké uživatelské zatížení, buď špičky nebo postupně zvyšují zatížení, v aplikaci, nebo omezují výpočetní prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="29d0c-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="29d0c-114">Zátěžové testy určují, jestli se aplikace v rámci zátěže může zotavit z chyby a řádně se vrátit k očekávanému chování.</span><span class="sxs-lookup"><span data-stu-id="29d0c-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="29d0c-115">V případě zátěže není aplikace spouštěna za běžných podmínek.</span><span class="sxs-lookup"><span data-stu-id="29d0c-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="29d0c-116">Visual Studio 2019 je poslední verzí sady Visual Studio s funkcemi zátěžového testu.</span><span class="sxs-lookup"><span data-stu-id="29d0c-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="29d0c-117">Pro zákazníky, kteří potřebují nástroje pro testování zatížení v budoucnu, doporučujeme použít alternativní nástroje, jako je Apache JMeter, Akamai CloudTest a BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="29d0c-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="29d0c-118">Další informace naleznete v [poznámkách k verzi sady Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="29d0c-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="29d0c-119">Nástroje sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="29d0c-119">Visual Studio tools</span></span>

<span data-ttu-id="29d0c-120">Visual Studio umožňuje uživatelům vytvářet, vyvíjet a ladit testy výkonu a zátěže webu.</span><span class="sxs-lookup"><span data-stu-id="29d0c-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="29d0c-121">Možnost je k dispozici pro vytváření testů pomocí zaznamenávání akcí ve webovém prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="29d0c-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="29d0c-122">Informace o tom, jak vytvořit, nakonfigurovat a spustit projekty zátěžového testu pomocí sady Visual Studio 2017, naleznete v tématu [rychlý Start: vytvoření projektu zátěžového testu](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="29d0c-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="29d0c-123">Zátěžové testy lze nakonfigurovat tak, aby běžely místně nebo běžely v cloudu s využitím Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="29d0c-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="29d0c-124">Nástroje třetích stran</span><span class="sxs-lookup"><span data-stu-id="29d0c-124">Third-party tools</span></span>

<span data-ttu-id="29d0c-125">Následující seznam obsahuje nástroje webového výkonu jiných výrobců s různými sadami funkcí:</span><span class="sxs-lookup"><span data-stu-id="29d0c-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="29d0c-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="29d0c-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="29d0c-127">ApacheBench (AB)</span><span class="sxs-lookup"><span data-stu-id="29d0c-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="29d0c-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="29d0c-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="29d0c-129">k6</span><span class="sxs-lookup"><span data-stu-id="29d0c-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="29d0c-130">Svatojánského</span><span class="sxs-lookup"><span data-stu-id="29d0c-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="29d0c-131">Webwind v západním větru</span><span class="sxs-lookup"><span data-stu-id="29d0c-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="29d0c-132">Netling</span><span class="sxs-lookup"><span data-stu-id="29d0c-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="29d0c-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="29d0c-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

