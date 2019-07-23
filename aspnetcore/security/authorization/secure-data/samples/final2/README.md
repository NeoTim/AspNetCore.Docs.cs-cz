# <a name="how-to-buildrun-secure-user-data-sample"></a>Jak sestavení nebo spuštění ukázková data zabezpečení uživatele

* Nastavení hesla pomocí nástroje Správce tajný kód:

  `dotnet user-secrets set SeedUserPW <pw>`

* Aktualizace databáze:

  `dotnet ef database update`

* Povolení HTTPS v projektu
