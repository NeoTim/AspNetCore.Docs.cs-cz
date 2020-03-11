* `-F|--no-formatting`

  Příznak, jehož přítomnost potlačuje formátování odpovědi HTTP.

* `-h|--header`

  Nastaví hlavičku požadavku HTTP. Podporují se tyto dva formáty hodnot:

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  Určuje soubor, do kterého by se měla zapsat celá odpověď HTTP (včetně hlaviček a textu). například `--response "C:\response.txt"`. Soubor se vytvoří, pokud neexistuje.

* `--response:body`

  Určuje soubor, do kterého se má zapsat tělo odpovědi HTTP. například `--response:body "C:\response.json"`. Soubor se vytvoří, pokud neexistuje.

* `--response:headers`

  Určuje soubor, do kterého se mají zapsat hlavičky odpovědí HTTP. například `--response:headers "C:\response.txt"`. Soubor se vytvoří, pokud neexistuje.

* `-s|--streaming`

  Příznak, jehož přítomnost umožňuje streamování odpovědi HTTP.
