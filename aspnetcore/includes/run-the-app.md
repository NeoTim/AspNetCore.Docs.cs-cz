# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kláves Ctrl+F5 spusťte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [iis express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **klávesCtrl-F5** spusťte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na . Adresní řádek `localhost:port#` zobrazuje a `example.com`ne něco jako . To proto, `localhost` že je standardní název hostitele pro místní počítač. Localhost slouží pouze webové požadavky z místního počítače.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* V sadě Visual Studio stiskněte **opt-cmd-return** pro spuštění bez ladicího programu. Případně přejděte na řádek nabídek a přejděte na **spustit>start bez ladění**.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč `http://localhost:5001`a přejde na .

<!-- End of VS tabs -->

---