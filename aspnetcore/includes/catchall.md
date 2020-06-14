> [!WARNING]
> <span data-ttu-id="af8bf-101">Parametr **catch-All** může nesprávně odpovídat trasy z důvodu [chyby](https://github.com/dotnet/aspnetcore/issues/18677) v směrování.</span><span class="sxs-lookup"><span data-stu-id="af8bf-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="af8bf-102">Aplikace ovlivněné touto chybou mají následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="af8bf-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="af8bf-103">Například trasa typu catch-ALL.`{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="af8bf-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="af8bf-104">Trasa catch-All nesplňuje požadavky, které by se měla shodovat.</span><span class="sxs-lookup"><span data-stu-id="af8bf-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="af8bf-105">Při odebrání jiných tras bude vše začít pracovat.</span><span class="sxs-lookup"><span data-stu-id="af8bf-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="af8bf-106">Příklady přístupů k této chybě najdete v tématu chyby GitHubu [18677](https://github.com/dotnet/aspnetcore/issues/18677) a [16579](https://github.com/dotnet/aspnetcore/issues/16579) .</span><span class="sxs-lookup"><span data-stu-id="af8bf-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="af8bf-107">Oprava pro tuto chybu je obsažená v [sadě .NET Core 3.1.301 SDK a novější](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="af8bf-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="af8bf-108">Následující kód nastaví interní přepínač, který vyřeší tuto chybu:</span><span class="sxs-lookup"><span data-stu-id="af8bf-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```