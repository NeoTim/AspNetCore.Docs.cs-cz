## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Dopředné informace o žádosti pomocí proxy serveru nebo nástroje pro vyrovnávání zatížení

Pokud je aplikace nasazená za proxy server nebo nástrojem pro vyrovnávání zatížení, můžou se některé původní informace žádosti přeslat do aplikace v hlavičkách žádostí. Tyto informace obvykle zahrnují schéma zabezpečeného požadavku (`https`), hostitele a IP adresu klienta. Aplikace nečtou tyto hlavičky požadavků automaticky ke zjištění a používání původních informací o požadavku.

Schéma se používá v generaci odkazů, které má vliv na tok ověřování s externími poskytovateli. Výsledkem ztráty zabezpečeného schématu`https`() je, že aplikace generuje nesprávné adresy URL nezabezpečeného přesměrování.

Použijte middleware předaných hlaviček k zpřístupnění původních informací o požadavku aplikaci pro zpracování žádostí.

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.
