
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Preguntas más frecuentes: hospedaje de la zona ARPA en el DNS de Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>¿Puedo hospedar zonas ARPA para mis bloques IP asignados por el ISP en el DNS de Azure?

Sí. El hospedaje de zonas ARPA para sus propios intervalos de IP en DNS de Azure está totalmente permitido.

Simplemente [cree la zona en el DNS de Azure](../articles/dns/dns-getstarted-create-dnszone.md) y trabaje con su ISP para [delegar la zona](../articles/dns/dns-domain-delegation.md).  Después puede administrar los registros PTR de cada búsqueda inversa igual que otros tipos de registros.

También puede [importar una zona de búsqueda inversa existente mediante la CLI de Azure](../articles/dns/dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>¿Cuánto cuesta hospedar una zona ARPA?

El hospedaje de una zona ARPA para su bloque de direcciones IP asignadas por el ISP en el DNS de Azure se cobra según las [tarifas estándar del DNS de Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Puedo hospedar zonas ARPA para direcciones IPv4 e IPv6 en el DNS de Azure?

Sí.


<!--HONumber=Nov16_HO3-->


