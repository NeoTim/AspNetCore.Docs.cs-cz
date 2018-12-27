## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Vpřed žádost o informace o proxy serveru nebo nástroj pro vyrovnávání zatížení

Pokud je aplikace nasazena za proxy server nebo nástroje pro vyrovnávání zatížení, některé z původní informace o požadavku může být přeposílán aplikace v záhlaví požadavku. Tyto informace obvykle obsahuje schéma požadavku zabezpečení (`https`), hostitele a IP adresu klienta. Aplikace si automaticky tyto hlavičky žádosti mohli objevit a používat původní informace o žádostech.

Schéma se používá při generování odkazů, které má vliv na tok ověřování u externích poskytovatelů. Zabezpečený režim ztráty (`https`) výsledkem generování adresy URL pro přesměrování nesprávné nezabezpečené aplikace.

Předané Middleware záhlaví využívat k zpřístupňování původní informace o požadavku do aplikace pro zpracování požadavku.

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.
