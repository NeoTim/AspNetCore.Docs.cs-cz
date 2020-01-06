# <a name="custom-model-binding-demo"></a>Ukázka vazby vlastního modelu

Otestujte `ByteArrayModelBinder` spuštěním aplikace a odesláním řetězce zakódovaného ve formátu base64 do koncového bodu `ImageController` (`/api/image/`). Zadejte vlastnosti souboru a filename v textu požadavku jako Form-data (pomocí [post](https://www.getpostman.com/) nebo podobného nástroje). [Tento vzorový řetězec](Base64String.txt)lze použít. Výsledek je uložen ve složce *wwwroot/images/upload* se zadaným názvem souboru.

Pokud chcete otestovat příklad vlastní vazby, zkuste tyto koncové body:

* /api/authors/1
* /API/Authors/2 (Nenalezeno)
* /api/boundauthors/1
* /API/boundauthors/2 (Nenalezeno)
* /api/boundauthors/get/1
* /API/boundauthors/Get/2 (žádný obsah) &ndash; Tato akce nekontroluje hodnotu null a vrátí *404, který nebyl nalezen*.
