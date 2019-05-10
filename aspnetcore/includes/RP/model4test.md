<a name="test"></a>

### <a name="test-the-app"></a>Testování aplikace

* Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).
* Test **vytvořit** odkaz.

  ![Vytvoření stránky](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* Test **upravit**, **podrobnosti**, a **odstranit** odkazy.

Pokud se zobrazí následující chyba, zkontrolujte máte spusťte migrace a aktualizovat databázi:

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```
