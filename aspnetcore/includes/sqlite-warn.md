> [!NOTE]
> 
> **Omezení SQLite**
>
> Tento kurz používá funkci *migrace* jádra entity frameworku, kde je to možné. Migrace aktualizuje schéma databáze tak, aby odpovídalo změnám v datovém modelu. Migrace však provádí pouze druhy změn, které podporuje databázový stroj a možnosti změny schématu SQLite jsou omezené. Například přidání sloupce je podporováno, ale odebrání sloupce není podporováno. Pokud je vytvořena migrace k odebrání `ef migrations add` sloupce, příkaz `ef database update` proběhne úspěšně, ale příkaz se nezdaří. 
>
> Řešení pro omezení SQLite je ručně psát migrace kód provést znovu sestavit tabulku při něco v tabulce změní. Kód by šel `Up` v `Down` a metody pro migraci a by zahrnovala:
>
> * Vytvoření nové tabulky.
> * Kopírování dat ze staré tabulky do nové tabulky.
> * Upustil starý stůl.
> * Přejmenování nové tabulky.
>
> Zápis kódu specifického pro databázi tohoto typu je mimo rozsah tohoto kurzu. Místo toho tento kurz klesne a znovu vytvoří databázi vždy, když pokus o použití migrace by se nezdaří. Další informace najdete v následujících materiálech:
>
> * [Omezení zprostředkovatele základní databáze SQLite EF](/ef/core/providers/sqlite/limitations)
> * [Přizpůsobení kódu migrace](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Osávání dat](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE, příkaz](https://sqlite.org/lang_altertable.html)