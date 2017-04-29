
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Preguntas más frecuentes: DNS inverso para su dirección IP asignada por Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>¿Cuánto cuestan los registros de DNS inversos?

Son gratuitos.  No existe ningún coste adicional para las consultas o los registros de DNS inversos.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>¿Se resolverán mis registros de DNS inversos desde Internet?

Sí. Cuando haya configurado la propiedad de DNS inverso para su servicio en la nube, Azure administrará todas las delegaciones y zonas DNS necesarias para garantizar que el registro de DNS inverso se resuelve para todos los usuarios de Internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>¿Se creará un registro de DNS inverso predeterminado para mis servicios en la nube?

No. El DNS inverso es una característica opcional. Si decide no configurarla, no se crea ningún registro de DNS inverso predeterminado.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>¿Cuál es el formato del nombre de dominio completo (FQDN)?

Los FQDN se especifican en orden progresivo y deben terminar con un punto (p. ej., "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>¿Qué ocurre si no se superan las comprobaciones de validación para el DNS inverso que he especificado?

Cuando no se supera la comprobación de validación de DNS inverso, se producirá un error en la operación de administración de servicios. Corrija el valor de DNS inverso según sea necesario y vuelva a intentarlo.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>¿Puedo administrar el DNS inverso para mi sitio web de Azure?

No se admite el DNS inverso para los sitios web de Azure. El DNS inverso es compatible con las máquinas virtuales de IaaS y los roles de PaaS de Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>¿Puedo configurar varios registros de DNS inversos para mi servicio en la nube?

No. Azure admite un único registro de DNS inverso por cada servicio en la nube de Azure. Sin embargo, cada servicio en la nube de Azure puede tener su propio registro de DNS inverso.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>¿Puedo enviar correos electrónicos a dominios externos desde mis servicios de proceso de Azure?

No. [Los servicios de proceso de Azure no admiten el envío de correos electrónicos a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
