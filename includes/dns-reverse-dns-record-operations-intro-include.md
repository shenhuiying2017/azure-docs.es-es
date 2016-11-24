## <a name="what-is-reverse-dns"></a>¿Qué es un DNS inverso?

Los registros de DNS convencionales permiten la asignación de un nombre de DNS (por ejemplo, "www.contoso.com") a una dirección IP (por ejemplo, 64.4.6.100).  Un DNS inverso permite traducir una dirección IP (64.4.6.100) a un nombre ("www.contoso.com").

Los registros de DNS inversos se utilizan en distintas situaciones, entre ellas, en la validación de servidores y en la autenticación de solicitudes de servidor. Por ejemplo, los registros de DNS inverso se usan mucho en la lucha contra el correo basura; para ello, se comprueba el remitente del mensaje de correo electrónico.  El servidor de correo receptor recupera el registro de DNS inverso de la dirección IP del servidor de envío y comprueba si ese host está autorizado para enviar un correo electrónico desde el dominio de origen. (Sin embargo, tenga en cuenta que [los servicios de proceso de Azure no admiten el envío de correos electrónicos a dominios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)).

## <a name="how-reverse-dns-works"></a>Cómo funciona un DNS inverso

Los registros de DNS inverso se hospedan en zonas DNS especiales conocidas como zonas “ARPA”.  Estas zonas forman una jerarquía de DNS independiente paralela a la jerarquía normal que hospeda dominios tales como "contoso.com".

Por ejemplo, el registro de DNS “www.contoso.com” se implementa mediante un registro “A” de DNS con el nombre "www" en la zona “contoso.com”.  Este registro A apunta a la dirección IP correspondiente, en este caso 64.4.6.100.  La búsqueda inversa se implementa por separado, usando un registro “PTR” llamado “100” en la zona “6.4.64.in-addr.arpa” (tenga en cuenta que las direcciones IP se invierten en las zonas ARPA).  Este registro PTR, si se ha configurado correctamente, apunta al nombre "www.contoso.com".

Cuando se asigna un bloque de direcciones IP a una organización, adquiere también el derecho a administrar la zona ARPA correspondiente. Microsoft hospeda y administra las zonas ARPA correspondientes a los bloques de direcciones IP usados por Azure. Su ISP puede hospedar la zona ARPA para sus propias direcciones IP automáticamente, o le puede permitir hospedar la zona ARPA en un servicio DNS de su elección, por ejemplo, el DNS de Azure.

> [!NOTE]
> Las búsquedas DNS directas e inversas se implementan en jerarquías de DNS independientes y paralelas. La búsqueda inversa de "www.contoso.com" **no** se hospeda en la zona "contoso.com"; en su lugar se hospeda en la zona ARPA para el bloque de direcciones IP correspondiente.

Para más información sobre los registros de DNS inverso, consulte [Búsqueda DNS inversa](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Soporte técnico de Azure para DNS inverso

Azure admite dos escenarios independientes con relación al DNS inverso:

1. Hospedar la zona ARPA correspondiente a su bloque de direcciones IP.
2. Permitirle configurar el registro de DNS inverso para la dirección IP asignada a su servicio de Azure.

Para admitir esto, se puede usar el DNS de Azure para hospedar las zonas ARPA y administrar los registros PTR para cada búsqueda de DNS inverso.  El proceso de creación de la zona ARPA, configuración de la delegación y configuración de los registros PTR es el mismo que para las zonas DNS normales.  Las únicas diferencias son que la delegación se debe configurar mediante su ISP en lugar del registrador de DNS, y solo se debe usar el tipo de registro PTR.

Para esto último, Azure le permite configurar la búsqueda inversa de las direcciones IP asignadas a su servicio.  Azure configura esta búsqueda inversa como un registro PTR en la zona ARPA correspondiente.  Microsoft hospeda y administra estas zonas ARPA correspondientes a todos los intervalos de direcciones IP usados por Azure. **El resto de este artículo describe este escenario con detalle.**


<!--HONumber=Nov16_HO3-->


