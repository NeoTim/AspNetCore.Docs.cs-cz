> [!WARNING]
> <span data-ttu-id="139ca-101">Parametr **catch-All** může nesprávně odpovídat trasy z důvodu [chyby](https://github.com/dotnet/aspnetcore/issues/18677) v směrování.</span><span class="sxs-lookup"><span data-stu-id="139ca-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="139ca-102">Aplikace ovlivněné touto chybou mají následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="139ca-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="139ca-103">Například trasa typu catch-ALL.`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="139ca-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="139ca-104">Trasa catch-All nesplňuje požadavky, které by se měla shodovat.</span><span class="sxs-lookup"><span data-stu-id="139ca-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="139ca-105">Při odebrání jiných tras bude vše začít pracovat.</span><span class="sxs-lookup"><span data-stu-id="139ca-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="139ca-106">Příklady přístupů k této chybě najdete v tématu chyby GitHubu [18677](https://github.com/dotnet/aspnetcore/issues/18677) a [16579](https://github.com/dotnet/aspnetcore/issues/16579) .</span><span class="sxs-lookup"><span data-stu-id="139ca-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="139ca-107">Pro tuto chybu se plánuje oprava pro výslovný souhlas.</span><span class="sxs-lookup"><span data-stu-id="139ca-107">An opt-in fix for this bug is planned.</span></span> <span data-ttu-id="139ca-108">Tento dokument bude aktualizován po vydání opravy.</span><span class="sxs-lookup"><span data-stu-id="139ca-108">This doc will be updated when the patch is released.</span></span> <span data-ttu-id="139ca-109">Po vydání opravy bude následující kód nastavit interní přepínač, který tuto chybu vyřeší:</span><span class="sxs-lookup"><span data-stu-id="139ca-109">When the patch is released, the following code will set an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```