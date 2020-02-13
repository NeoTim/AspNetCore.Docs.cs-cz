```console
npm run release
```

Tento příkaz vygeneruje prostředky na straně klienta, které mají být obsluhovány při spuštění aplikace. Prostředky se umístí do složky *wwwroot* .

Sada Webpack dokončila následující úlohy:

* Vymazal se obsah adresáře *wwwroot* .
* V procesu známém jako *transpilation*je převedený TypeScript na JavaScript.
* Byl změněn vygenerovaný JavaScript za účelem zmenšení velikosti souboru v procesu známém jako *minifikace*.
* Zkopírovány zpracované soubory JavaScript, CSS a HTML z *Src* do adresáře *wwwroot* .
* Do souboru *wwwroot/index.html* byly vloženy následující prvky:
  * Značka `<link>` s odkazem na soubor *wwwroot/Main.\<hash\>. CSS* . Tato značka je umístěna bezprostředně před uzavírací `</head>` značku.
  * Značka `<script>` s odkazem na soubor minifikovaného *wwwroot/Main.\<hash\>. js* . Tato značka je umístěna bezprostředně před uzavírací `</body>` značku.