```console
npm run release
```

Tento příkaz provede na straně klienta prostředků ke zpracování při spuštění aplikace. Prostředky jsou umístěné v *wwwroot* složky.

Webpacku dokončit následující úkoly:

* Vymazat obsah *wwwroot* adresáře.
* Převést TypeScript pro JavaScript&mdash;tento proces se označuje jako *transpilation*.
* Pozměnění generovaný JavaScript, který chcete zmenšit velikost souboru&mdash;tento proces se označuje jako *připravenost k minifikaci*.
* Zpracované soubory jazyka JavaScript, CSS a HTML z zkopírovány *src* k *wwwroot* adresáře.
* Vloží do následující prvky *wwwroot/index.html* souboru:
  * A `<link>` označit, odkazující *wwwroot/main.\< Hodnota hash\>.css* souboru. Tato značka je umisťovaný bezprostředně před uzavírací `</head>` značky.
  * A `<script>` značky, odkazující minifikovaný *wwwroot/main.\< Hodnota hash\>js* souboru. Tato značka je umisťovaný bezprostředně před uzavírací `</body>` značky.
