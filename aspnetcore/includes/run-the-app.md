# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Stisknutím kombinace kláves CTRL + F5 spustíte bez ladicího programu.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio spustí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) a spustí aplikaci. V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače. Když Visual Studio vytvoří webový projekt, použije se pro webový server náhodný port.
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Stisknutím **kombinace kláves CTRL + F5** spustíte bez ladicího programu.

  Visual Studio Code spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`. V adresním řádku se zobrazí `localhost:port#` a ne něco jako `example.com`. Důvodem je, že `localhost` je standardní název hostitele pro místní počítač. Localhost obsluhuje pouze webové požadavky z místního počítače.

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pro Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* V aplikaci Visual Studio stiskněte klávesu **opt-cmd-Return** , aby se spouštěla bez ladicího programu. Můžete také přejít na panel nabídek a přejít na **příkaz spustit > spustit bez ladění**.

  Visual Studio spustí [Kestrel](xref:fundamentals/servers/kestrel), spustí prohlížeč a přejde na `http://localhost:5001`.

<!-- End of VS tabs -->

---