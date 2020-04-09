# <a name="aspnet-core-health-check-sample"></a>ASP.NET základní kontrolní vzorek stavu

Tato ukázka ilustruje použití middlewaru a služeb kontroly stavu. Tato ukázka ukazuje scénář popsaný v [kontrole stavu v ASP.NET základní](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) téma.

Chcete-li spustit ukázkovou aplikaci pro scénář popsaný v tématu, použijte příkaz [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Předá přepínač pro scénář, který zkoumáte. Aplikace se ve `basic` výchozím nastavení vejde do konfigurace, když přepínač není k dispozici `dotnet run`.

| Scénář                                               | Ukázkový příkaz aplikace               | Popis |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Základní sonda stavu (výchozí)                           | `dotnet run --scenario basic`    | Potvrzuje, že aplikace může zpracovávat požadavky HTTP. |
| Databázová sonda                                         | `dotnet run --scenario db`       | Zkontroluje připojení databáze serveru SQL Server. Pokyny naleznete v části [Databázová sonda](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) v tématu. |
| Sondy připravenosti/živosti                              | `dotnet run --scenario liveness` | Provádí kontroly stavu živé aplikace *(živost)* oproti aplikaci, která se připravuje na živé připojení *(připravenost).* |
| Sonda (paměť) založená na metrikách/<br>vlastní zapisovač odpovědí | `dotnet run --scenario writer`   | Zkontroluje proti použití paměti a zapíše vlastní JSON při zaškrtnutí koncového bodu stavu. |
| Filtrovat podle portu                                         | `dotnet run --scenario port`     | Filtruje kontroly stavu na daný port. Pokyny naleznete v části [Filtr podle portu](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) tématu. |

Scénáře databázového sondy a filtru portů vyžadují další konfiguraci. Podrobnosti najdete v tématu [Kontroly stavu.](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks)
