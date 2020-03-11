<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do modelu filmů

Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které se aplikují deklarativně na třídu nebo vlastnost. Tato dataanotace také obsahuje atributy formátování, jako `DataType`, které vám pomůžou s formátováním a neposkytují žádné ověřování.

Aktualizujte třídu `Movie`, abyste mohli využívat předdefinované atributy ověřování `Required`, `StringLength`, `RegularExpression`a `Range`.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověřování určují chování, které chcete vyhovět pro vlastnosti modelu, na které se aplikují:

* Atributy `Required` a `MinimumLength` označují, že vlastnost musí mít hodnotu. ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.
* Atribut `RegularExpression` slouží k omezení znaků, které lze zadat. V předchozím kódu "Žánr":

  * Je nutné použít pouze písmena.
  * První písmeno musí být velkými písmeny. Mezery, číslice a speciální znaky nejsou povoleny.

* `RegularExpression` "hodnocení":

  * Vyžaduje, aby byl první znak velkým písmenem.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.

* Atribut `Range` omezuje hodnotu na v zadaném rozsahu.
* Atribut `StringLength` umožňuje nastavit maximální délku řetězcové vlastnosti a volitelně její minimální délku.
* Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou podstatou požadovány a nepotřebují atribut `[Required]`.

Automatické vynucení ověřovacích pravidel nástrojem ASP.NET Core pomáhá zajistit větší odolnost aplikace. Také zajišťuje, že se nebudete moci zapomenout a neúmyslně ověřit data v databázi.
