Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de un dominio, debe crear una zona DNS. Todos los registros DNS creados para un dominio concreto se ubicarán dentro de una zona DNS del dominio.

Por ejemplo, el dominio "contoso.com" puede contener una serie de registros DNS como "mail.contoso.com" (para un servidor de correo) y "www.contoso.com" (para un sitio web).

## <a name="names"></a>Acerca de los nombres de la zona DNS
* El nombre de la zona debe ser único en el grupo de recursos y la zona no debe existir aún. De lo contrario, la operación presentará un error.
* El mismo nombre de zona podrá reutilizarse en un grupo de recursos distinto o en una suscripción a Azure diferente.
* Cuando varias zonas comparten el mismo nombre, a cada instancia se le asignarán distintas direcciones de servidores de nombres, y solo se podrá delegar una instancia desde el dominio primario. Para más información, consulte [Delegación de dominios en DNS de Azure](../articles/dns/dns-domain-delegation.md).

