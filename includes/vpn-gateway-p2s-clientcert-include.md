Todos los equipos cliente que se conecten a una red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. El certificado de cliente se genera a partir del certificado raíz y se instala cada equipo cliente. Si no se ha instalado ningún certificado de cliente válido y el cliente intenta conectarse a la red virtual, la autenticación no se realiza.

Puede generar un certificado único para cada cliente o puede usar el mismo para varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado. De lo contrario, si varios clientes usan el mismo certificado de cliente y hace falta revocarlo, es preciso generar e instalar certificados para todos los clientes que usan dicho certificado para autenticarlos.

Para generar certificados de cliente se pueden emplear los métodos siguientes:

- **Certificado de empresa:**

  - Si usa una solución de certificación de empresa, genere un certificado de cliente con el formato de valor de nombre común "name@yourdomain.com", en lugar del formato "nombreDeDominio\nombreDeUsuario".
  - Asegúrese de que el certificado de cliente se base en la plantilla de certificado "Usuario" que tenga "Autenticación de cliente" como primer elemento de la lista de uso, y no Inicio de sesión de tarjeta inteligente, etc. Para comprobar el certificado, haga doble clic en el certificado de cliente y vea **Detalles > Uso mejorado de claves**.

- **Certificado raíz autofirmado:** es importante que siga los pasos de uno de los artículos sobre certificado P2S a continuación. En caso contrario, los certificados de cliente que cree no serán compatibles con las conexiones de P2S y los clientes reciben un error al intentar conectarse. Los pasos descritos en cualquiera de los siguientes artículos generan un certificado de cliente compatible: 

  * [Instrucciones para Windows 10 y PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): estas instrucciones requieren Windows 10 y PowerShell para generar certificados. Los certificados que se generan pueden instalarse en cualquier cliente P2S compatible.
  * [Instrucciones para MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): use MakeCert para generar certificados si no tiene acceso a un equipo con Windows 10. MakeCert está en desuso, pero todavía puede usar MakeCert para generar certificados. Los certificados que se generan pueden instalarse en cualquier cliente P2S compatible.

  Si genera un certificado de cliente desde un certificado raíz autofirmado mediante las instrucciones anteriores, este se instala automáticamente en el equipo que utilizó para generarlo. Si desea instalar un certificado de cliente en otro equipo cliente, debe exportarlo como .pfx junto con toda su cadena. Esto crea un archivo .pfx que contiene la información del certificado raíz que se requiere para que el cliente se autentique correctamente. Para conocer los pasos necesarios para exportar un certificado, consulte [Certificados: exportar un certificado de cliente](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).