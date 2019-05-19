## <a name="add-validation-rules-to-the-movie-model"></a>Přidání pravidel ověřování modelu movie

Otevřít *Movie.cs* souboru. Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributy, pomocí kterých jsou deklarativně třída nebo vlastnost. DataAnnotations taky obsahuje atributy formátování, jako je `DataType` , pomoct při formátování a neposkytují žádné ověřování.

Aktualizace `Movie` třídy, které chcete využít výhod integrovaného `Required`, `StringLength`, `RegularExpression`, a `Range` atributů ověření.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Ověření atributy určují chování, které chcete vynutit na vlastnosti projektu, které se použijí:

* `Required` a `MinimumLength` atributy ukazují, že vlastnost musí mít hodnotu, ale nic zabraňuje uživateli v zadávání prázdnými znaky až splňovat toto ověření.
* `RegularExpression` Atribut se používá k omezení znaků, může být vstupní. V předchozím kódu "Žánr":

  * Smí používat jen písmena.
  * Musí být velké písmeno první písmeno. Prázdné znaky, číslice a speciální znaky nejsou povoleny.

* `RegularExpression` "Hodnocení":

  * Vyžaduje, aby první znak velkého písmene.
  * Umožňuje speciální znaky a čísla v následných mezery. "PG-13" je platný pro hodnocení, ale selže "Žánr".

* `Range` Atribut omezuje hodnotu do zadaného rozsahu.
* `StringLength` Atribut umožňuje nastavit maximální délka vlastnosti typu string a volitelně jeho minimální délka.
* Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou ze své podstaty povinné a nemusíte `[Required]` atribut.

Ověřovací pravidla automaticky vynucuje sada ASP.NET Core s pomáhá vytvářet aplikace pro více robustní. Také zajistí, že nelze zapomenete něco ověření a neúmyslně nechat chybná data do databáze.
