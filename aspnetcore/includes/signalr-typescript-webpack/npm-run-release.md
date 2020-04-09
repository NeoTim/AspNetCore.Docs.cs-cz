```console
npm run release
```

Tento příkaz generuje prostředky na straně klienta, které mají být obsluhovány při spuštění aplikace. Datové zdroje jsou umístěny ve složce *wwwroot.*

Webpack dokončil následující úkoly:

* Vymazal obsah adresáře *wwwroot.*
* Převedl TypeScript do Jazyka JavaScript v procesu známém jako *transpilace*.
* Mangled generované JavaScript ke snížení velikosti souboru v procesu známém jako *minification*.
* Zkopírovali zpracované soubory JavaScript, CSS a HTML z *src* do adresáře *wwwroot.*
* Vstříknuté následující prvky do souboru *wwwroot/index.html:*
  * Značka `<link>` odkazující na *wwwroot/main.\< soubor\>hash .css.* Tato značka je umístěna `</head>` bezprostředně před uzavírací značkou.
  * Značka `<script>` odkazující na minified *wwwroot/main.\< soubor\>hash js.* Tato značka je umístěna `</body>` bezprostředně před uzavírací značkou.