Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části přidáte třídy pro správu filmy v databázi. Použití těchto tříd s [Entity Framework Core](/ef/core) (EF Core) pro práci s databází. EF Core je objektově relační mapování (ORM) platforma, která zjednodušuje kód přístupu k datům, který musíte napsat.

Tříd modelu, které vytvoříte jsou označovány jako POCO třídy (od "prostý staré CLR objekty"), protože nemají žádné závislosti na EF Core. Definují vlastnosti data, která jsou uložena v databázi.

V tomto kurzu nejprve napsat tříd modelu a EF Core vytvoří databázi. Alternativní není součástí tohoto přístupu je [generování tříd modelu z existující databáze](/ef/core/get-started/aspnetcore/existing-db).

[Zobrazení nebo stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) vzorku.
