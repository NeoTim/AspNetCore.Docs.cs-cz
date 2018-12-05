# <a name="aspnet-core-health-check-sample"></a>Ukázka kontroly stavu ASP.NET Core

Tento příklad ukazuje použití metody stavu zkontrolujte Middleware a služby. V této ukázce podle scénáře popsaného v [doplněk pro kontroly stavu v ASP.NET Core](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) tématu.

Chcete-li spustit ukázkovou aplikaci pro scénář popsaný v tématu, použijte [dotnet spustit](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) příkaz ze složky projektu v příkazovém řádku. Předání přepínače pro scénáře, který zkoumáte. Výchozí nastavení aplikace `basic` konfigurace, pokud není k dispozici přepínač na `dotnet run`.

| Scénář                                               | Příkaz ukázkové aplikace               | Popis |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| Sonda stavu základní (výchozí)                           | `dotnet run --scenario basic`    | Potvrzuje se tím, že aplikace může zpracovávat požadavky HTTP. |
| Test databáze                                         | `dotnet run --scenario db`       | Kontroluje se připojení k databázi systému SQL Server. Najdete v článku [databáze sondy](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe) tématu pokyny. |
| Testy připravenosti/aktivity                              | `dotnet run --scenario liveness` | Provádí kontroly stavu živé aplikace (*aktivity*) a připravuje se stanou živé aplikace (*připravenosti*). |
| Na základě metrik test paměti (paměť) /<br>Zapisovač vlastní odpovědi | `dotnet run --scenario writer`   | Ověří proti využití paměti a zapíše vlastní JSON je zaškrtnuta možnost Stav koncového bodu. |
| Filtrovat podle portu                                         | `dotnet run --scenario port`     | Filtruje kontroly stavu pro zadaný port. Najdete v článku [filtrovat podle port](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port) tématu pokyny. |

Scénáře filtrování testu a port databáze vyžadovat dodatečnou konfiguraci. Zobrazit [doplněk pro kontroly stavu](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks) podrobnosti.
