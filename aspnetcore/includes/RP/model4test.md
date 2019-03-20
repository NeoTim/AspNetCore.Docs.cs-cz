---
ms.openlocfilehash: 1e848a6779a0c68754215ead35d27df5b371f8ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265380"
---
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
