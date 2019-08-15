Příkaz aktualizace databáze generuje následující upozornění: 

   Pro desetinný sloupec ' Price ' pro typ entity ' video ' nebyl zadán žádný typ. To způsobí, že se hodnoty tiše zkrátí, pokud se nevejdou do výchozí přesnosti a rozsahu. Explicitně zadejte typ sloupce SQL serveru, který může obsahovat všechny hodnoty pomocí ' HasColumnType () '.

Toto upozornění můžete ignorovat, bude opraveno v pozdějším kurzu.

Schéma databáze je založené na modelu určeném ve `MvcMovieContext` třídě (v souboru *data/MvcMovieContext. cs* ). `InitialCreate` Argument je název migrace. Můžete použít libovolný název, ale podle konvence je vybraný název, který popisuje migraci.

`Update-Database` Příkaz spustí `Up` metodu *migrace / {časové razítko} _InitialCreate.cs* soubor, který vytvoří databázi.