* `-F|--no-formatting`

  Příznak, jejichž přítomnost potlačí formátování odpovědi HTTP.

* `-h|--header`

  Nastaví hlavičku požadavku HTTP. Podporují se následující formáty dvě hodnoty:

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  Určuje soubor, do které by měly být napsány celé odpovědi protokolu HTTP (včetně záhlaví a text). Například, `--response "C:\response.txt"`. Soubor je vytvořen, pokud neexistuje.

* `--response:body`

  Určuje soubor, do které by měly být napsány textu odpovědi HTTP. Například, `--response:body "C:\response.json"`. Soubor je vytvořen, pokud neexistuje.

* `--response:headers`

  Určuje soubor, do které by měly být napsány hlavičky HTTP odpovědi. Například, `--response:headers "C:\response.txt"`. Soubor je vytvořen, pokud neexistuje.

* `-s|--streaming`

  Příznak, jejichž přítomnost umožňuje vysílání datového proudu odpovědi HTTP.
