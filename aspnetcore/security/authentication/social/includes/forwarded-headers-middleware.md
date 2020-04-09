## <a name="forward-request-information-with-a-proxy-or-load-balancer"></a>Předávání informací o požadavku pomocí proxy serveru nebo vyrovnávání zatížení

Pokud je aplikace nasazená za proxy serverem nebo vyrovnáváním zatížení, některé z původních informací o požadavku mohou být předány aplikaci v hlavičkách požadavku. Tyto informace obvykle zahrnují zabezpečené`https`schéma požadavků ( ), hostitele a IP adresu klienta. Aplikace tyto hlavičky požadavků automaticky nečtou, aby zjistily a používaly původní informace o žádosti.

Schéma se používá v generování propojení, které ovlivňuje tok ověřování s externími zprostředkovateli. Ztráta zabezpečeného`https`schématu ( ) má za následek, že aplikace vygeneruje nesprávné adresy URL přesměrování nezabezpečených.

Pomocí middlewaru s předaných záhlaví zpřístupníte původní informace o požadavku aplikaci pro zpracování požadavků.

Další informace naleznete v tématu <xref:host-and-deploy/proxy-load-balancer>.
