<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="e38b1-101">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e38b1-101">Test the app</span></span>

* <span data-ttu-id="e38b1-102">Spusťte aplikaci a připojit `/Movies` na adresu URL v prohlížeči (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="e38b1-102">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>
* <span data-ttu-id="e38b1-103">Test **vytvořit** odkaz.</span><span class="sxs-lookup"><span data-stu-id="e38b1-103">Test the **Create** link.</span></span>

  ![Vytvoření stránky](../../tutorials/razor-pages/model/_static/conan.png)

<a name="scaffold"></a>

* <span data-ttu-id="e38b1-105">Test **upravit**, **podrobnosti**, a **odstranit** odkazy.</span><span class="sxs-lookup"><span data-stu-id="e38b1-105">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="e38b1-106">Pokud se zobrazí následující chyba, zkontrolujte máte spusťte migrace a aktualizovat databázi:</span><span class="sxs-lookup"><span data-stu-id="e38b1-106">If you get the following error, verify you have run migrations and updated the database:</span></span>

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)
```
