> [!NOTE]
> 
> **Omezení SQLite**
>
> V tomto kurzu se používá funkce *migrace* Entity Framework Core, pokud je to možné. Migrace aktualizuje schéma databáze tak, aby odpovídalo změnám v datovém modelu. Nicméně migrace provádí pouze typy změn, které podporuje databázový stroj, a možnosti změny schématu SQLite jsou omezené. Například přidání sloupce je podporováno, ale odebrání sloupce se nepodporuje. Pokud je vytvořená migrace pro odebrání sloupce, příkaz se `ef migrations add` zdaří, `ef database update` ale příkaz selže. 
>
> Alternativním řešením pro omezení SQLite je ruční psaní kódu migrace, který provede opakované sestavení tabulky, když se něco v tabulce změní. Kód by přešel do `Up` metod a `Down` pro migraci a měl by zahrnovat:
>
> * Vytváří se nová tabulka.
> * Kopírování dat z původní tabulky do nové tabulky.
> * Vynechává se stará tabulka.
> * Probíhá přejmenování nové tabulky.
>
> Zápis kódu specifického pro databázi tohoto typu je mimo rozsah tohoto kurzu. Místo toho tento kurz ponechá a znovu vytvoří databázi vždy, když pokus o použití migrace selže. Další informace naleznete v následujících materiálech:
>
> * [Omezení zprostředkovatele databáze EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení kódu migrace](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Osazení dat](/ef/core/modeling/data-seeding)
> * [Příkaz ALTER TABLE v SQLite](https://sqlite.org/lang_altertable.html)