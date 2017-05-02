
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Preguntas más frecuentes: DNS inverso para su dirección IP asignada por Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>¿Cuánto cuestan los registros de DNS inversos?

Son gratuitos.  No existe ningún coste adicional para las consultas o los registros de DNS inversos.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>¿Los registros DNS inversos para mi dirección IP pública asignada por Azure se resolverá desde Internet?

Sí. Cuando haya configurado la propiedad de DNS inverso para su dirección IP pública, Azure administrará todas las delegaciones y zonas DNS necesarias para garantizar que el registro de DNS inverso se resuelve para todos los usuarios de Internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>¿Se creará un registro de DNS inverso predeterminado para mis direcciones IP públicas?

No. El DNS inverso es una característica opcional. Si decide no configurarla, no se crea ningún registro de DNS inverso predeterminado.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>¿Cuál es el formato del nombre de dominio completo (FQDN)?

Los FQDN se especifican en orden progresivo y deben terminar con un punto (p. ej., "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>¿Qué ocurre si no se superan las comprobaciones de validación para el DNS inverso que he especificado?

Cuando no se supera la comprobación de validación de DNS inverso, se producirá un error en la operación de administración de servicios. Corrija el valor de DNS inverso según sea necesario y vuelva a intentarlo.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>¿Puedo administrar el DNS inverso para mi sitio web de Azure?

No se admite el DNS inverso para los sitios web de Azure. El DNS inverso se admite para máquinas virtuales de Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>¿Puedo configurar varios registros de DNS inversos para mi dirección IP pública?

No. Azure admite un único registro de DNS inverso para cada dirección IP pública. Sin embargo, cada dirección IP pública puede tener su propio registro de DNS inverso.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>¿Puedo configurar registros de DNS inverso para una dirección IP pública IPv6?

No.  En este momento, los registros de DNS inverso solo se admiten para las direcciones IP públicas IPv4.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>¿Puedo configurar un registro de DNS inverso para mi dirección IP pública sin tener especificado un valor DomainNameLabel?

No. Si quiere utilizar los registros de DNS inverso para sus direcciones IP públicas, tendrá que especificar la propiedad DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>¿Puedo enviar correos electrónicos a dominios externos desde mis servicios de proceso de Azure?

No. [Los servicios de proceso de Azure no admiten el envío de correos electrónicos a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
