# <a name="aspnet-core-distributed-cache-sample"></a>Ukázka distribuované mezipaměti ASP.NET Core

Tento příklad ukazuje použití metody distribuované mezipaměti. V této ukázce podle scénáře popsaného v [práce s distribuovanou mezipamětí v ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed) tématu.

V produkčním prostředí ukázkovou aplikaci konfigurován pro použití distribuované mezipaměti serveru SQL Server. Chcete-li znovu nakonfigurovat aplikaci, aby používala distribuované mezipaměti redis cache, změňte direktivy preprocesoru v horní části *Startup.cs* soubor pro použití Redis (`#define Redis // SQLServer`). Další informace najdete v tématu [direktivy preprocesoru ve vzorovém kódu](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code).
