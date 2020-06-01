# <a name="aspnet-core-distributed-cache-sample"></a>Ukázka distribuované mezipaměti ASP.NET Core

Tato ukázka znázorňuje použití distribuované mezipaměti. Tato ukázka předvádí Scénář popsaný v článku [práce s distribuovanou mezipamětí v ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) tématu.

V produkčním prostředí je ukázková aplikace nakonfigurovaná tak, aby používala distribuovanou mezipaměť SQL Server. Chcete-li aplikaci znovu nakonfigurovat tak, aby používala distribuovanou mezipaměť Redis, změňte direktivu preprocesoru v horní části souboru *Startup.cs* tak, aby používala Redis ( `#define Redis // SQLServer` ). Další informace naleznete v tématu [direktivy preprocesoru v ukázkovém kódu](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code).
