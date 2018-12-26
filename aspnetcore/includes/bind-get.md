> [!WARNING]
> Z bezpečnostních důvodů musíte výslovný souhlas se vazba `GET` požadovat data na stránce Vlastnosti modelu. Ověření vstupu uživatele před mapování na vlastnosti. K přidání na `GET` vazby je užitečné, když adresování scénáře, které jsou závislé na řetězec nebo trasy hodnoty dotazu.
>
> Vytvořit vazbu vlastnosti `GET` nastavit požadavky [[BindProperty]](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) atributu `SupportsGet` vlastnost `true`: `[BindProperty(SupportsGet = true)]`
