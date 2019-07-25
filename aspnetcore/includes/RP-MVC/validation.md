<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>Přidání ověřovacích pravidel do modelu filmů

Obor názvů DataAnnotations poskytuje sadu předdefinovaných ověřovacích atributů, které se aplikují deklarativně na třídu nebo vlastnost. Tato dataanotace také obsahuje atributy formátování `DataType` , jako jsou tyto informace užitečné při formátování a neposkytují žádné ověřování.

`Range` `RegularExpression` `StringLength` `Required`Aktualizujte tříduprovyužitívestavěnýchatributůověřování,,a.`Movie`

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

Atributy ověřování určují chování, které chcete vyhovět pro vlastnosti modelu, na které se aplikují:

* Atributy `Required` a`MinimumLength` označují, že vlastnost musí mít hodnotu, ale nic nebrání uživateli v zadání prázdného místa pro splnění tohoto ověření.
* `RegularExpression` Atribut slouží k omezení znaků, které lze zadat. V předchozím kódu "Žánr":

  * Je nutné použít pouze písmena.
  * První písmeno musí být velkými písmeny. Mezery, číslice a speciální znaky nejsou povoleny.

* `RegularExpression` Hodnocení:

  * Vyžaduje, aby byl první znak velkým písmenem.
  * Umožňuje speciální znaky a čísla v následujících mezerách. "PG-13" je platné pro hodnocení, ale pro "Žánr" se nezdařilo.

* `Range` Atribut omezuje hodnotu na v zadaném rozsahu.
* `StringLength` Atribut umožňuje nastavit maximální délku řetězcové vlastnosti a volitelně její minimální délku.
* Typy hodnot (například `decimal`, `int`, `float`, `DateTime`) jsou podstatně požadovány a nepotřebují `[Required]` atribut.

Automatické vynucení ověřovacích pravidel nástrojem ASP.NET Core pomáhá zajistit větší odolnost aplikace. Také zajišťuje, že se nebudete moci zapomenout a neúmyslně ověřit data v databázi.
