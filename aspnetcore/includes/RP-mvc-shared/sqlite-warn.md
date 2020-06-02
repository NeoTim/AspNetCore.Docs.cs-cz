
> [!NOTE]
> V tomto kurzu použijete funkci Entity Framework Core *migrace* , pokud je to možné. Migrace aktualizuje schéma databáze tak, aby odpovídalo změnám v datovém modelu. Migrace ale můžou dělat jenom typy změn, které podporuje zprostředkovatel EF Core, a schopnosti poskytovatele SQLite jsou omezené. Například přidání sloupce je podporováno, ale odebrání nebo změna sloupce není podporováno. Pokud se vytvoří migrace pro odebrání nebo změnu sloupce, `ef migrations add` příkaz se zdaří, ale `ef database update` příkaz selže. V souvislosti s těmito omezeními nepoužívá tento kurz migrace pro změny schématu SQLite. Místo toho se při změně schématu vyřadí a znovu vytvoří databáze.
>
>Alternativním řešením pro omezení SQLite je ruční psaní kódu migrace, který provede opakované sestavení tabulky, když se něco v tabulce změní. Nové sestavení tabulky zahrnuje:
>
>* Vytváří se nová tabulka.
>* Kopírování dat z původní tabulky do nové tabulky.
>* Vynechává se stará tabulka.
>* Probíhá přejmenování nové tabulky.
>
>Další informace najdete v následujících materiálech:
>
> * [Omezení zprostředkovatele databáze EF Core SQLite](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení kódu migrace](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Předvyplnění dat](/ef/core/modeling/data-seeding)
  * [Příkaz ALTER TABLE v SQLite](https://sqlite.org/lang_altertable.html)