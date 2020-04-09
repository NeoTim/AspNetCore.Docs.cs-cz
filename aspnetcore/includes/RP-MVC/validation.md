<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do filmového modelu

Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které jsou deklarativně použity na třídu nebo vlastnost. DataAnnotations také obsahuje atributy `DataType` formátování, jako je to, které pomáhají s formátováním a neposkytují žádné ověření.

Aktualizujte `Movie` třídu, abyste využili `Required`výhod `StringLength` `RegularExpression`předdefinovaných atributů , a `Range` validation.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověření určují chování, které chcete vynutit u vlastností modelu, na které se použijí:

* `Required` Atributy `MinimumLength` a označují, že vlastnost musí mít hodnotu; ale nic nebrání uživateli v zadání prázdného místa k uspokojení tohoto ověření.
* Atribut `RegularExpression` se používá k omezení, jaké znaky lze zadat. V předchozím kódu "Žánr":

  * Musí používat pouze písmena.
  * První písmeno musí být velkými písmeny. Prázdné znaky, čísla a speciální znaky nejsou povoleny.

* "Hodnocení": `RegularExpression`

  * Vyžaduje, aby první znak byl velké písmeno.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platný pro hodnocení, ale selže pro "Žánr".

* Atribut `Range` omezuje hodnotu v konkrétním rozsahu.
* Atribut `StringLength` umožňuje nastavit maximální délku vlastnosti řetězce a volitelně její minimální délku.
* Typy `decimal`hodnot (například `float` `DateTime`, `int`, ) jsou ze své `[Required]` podstaty povinné a nepotřebují atribut.

Díky tomu, že se ověřovací pravidla automaticky vynucují pomocí ASP.NET Core, vaše aplikace bude robustnější. Také zajišťuje, že nemůžete zapomenout na ověření něco a nechtěně nechat špatná data do databáze.
