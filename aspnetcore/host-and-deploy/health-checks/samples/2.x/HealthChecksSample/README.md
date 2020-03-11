# <a name="aspnet-core-health-check-sample"></a>Ukázka kontroly stavu ASP.NET Core

Tato ukázka znázorňuje použití middlewaru a služeb kontroly stavu. Tato ukázka předvádí Scénář popsaný v části [kontroly stavu v ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) tématu.

Chcete-li spustit ukázkovou aplikaci pro scénář popsaný v tématu, použijte příkaz [dotnet Run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Předejte přepínač pro scénář, který zkoumáte. Aplikace se nastaví na výchozí konfiguraci `basic`, když není `dotnet run`k dispozici přepínač.

| Scénář                                               | Příkaz ukázkové aplikace               | Popis |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Základní sonda stavu (výchozí)                           | `dotnet run --scenario basic`    | Potvrdí, že aplikace dokáže zpracovat požadavky HTTP. |
| Test databáze                                         | `dotnet run --scenario db`       | Kontroluje připojení k databázi SQL Server. Pokyny najdete v části [sonda databáze](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) v tématu. |
| Testy připravenosti/živých                              | `dotnet run --scenario liveness` | Provede kontrolu stavu živé aplikace (*živý*) oproti aplikaci, která se připraví na živý (*připravenost*). |
| Sonda založená na metrikách (paměť)/<br>vlastní zapisovač odpovědí | `dotnet run --scenario writer`   | Kontroluje použití paměti a zapisuje vlastní JSON při kontrole koncového bodu stavu. |
| Filtrovat podle portu                                         | `dotnet run --scenario port`     | Filtruje kontroly stavu na daný port. Pokyny najdete v části [Filter by port](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) v tématu. |

Scénář testu databáze a filtru portů vyžadují další konfiguraci. Podrobnosti najdete v tématu [kontroly stavu](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) .
