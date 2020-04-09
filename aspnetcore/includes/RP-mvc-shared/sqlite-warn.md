
> [!NOTE]
> Pro účely tohoto kurzu můžete použít funkci *migrace* core entity frameworku, kde je to možné. Migrace aktualizuje schéma databáze tak, aby odpovídalo změnám v datovém modelu. Migrace však můžete provést pouze druhy změn, které podporuje zprostředkovatele EF Core a možnosti zprostředkovatele SQLite jsou omezené. Například přidání sloupce je podporováno, ale odebrání nebo změna sloupce není podporována. Pokud je vytvořena migrace k odebrání nebo `ef migrations add` změně sloupce, `ef database update` příkaz proběhne úspěšně, ale příkaz se nezdaří. Z důvodu těchto omezení tento kurz nepoužívá migrace pro změny schématu SQLite. Místo toho při změně schématu přetažení a znovu vytvořit databázi.
>
>Řešení pro omezení SQLite je ručně psát migrace kód provést znovu sestavit tabulku při něco v tabulce změní. Opětovné sestavení tabulky zahrnuje:
>
>* Vytvoření nové tabulky.
>* Kopírování dat ze staré tabulky do nové tabulky.
>* Upustil starý stůl.
>* Přejmenování nové tabulky.
>
>Další informace najdete v následujících materiálech:
>
> * [Omezení zprostředkovatele základní databáze SQLite EF](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení kódu migrace](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Osávání dat](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE, příkaz](https://sqlite.org/lang_altertable.html)