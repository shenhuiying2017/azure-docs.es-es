Todos los equipos cliente que se conecten a una red virtual mediante una conexión de punto a sitio debe tener instalado un certificado de cliente. El certificado de cliente se genera a partir del certificado raíz y se instala cada equipo cliente. Si no se ha instalado ningún certificado de cliente válido y el cliente intenta conectarse a la red virtual, la autenticación no se realiza.

Puede generar un certificado único para cada cliente o puede usar el mismo para varios clientes. La ventaja de generar certificados de cliente únicos es la capacidad de revocar un solo certificado. De lo contrario, si varios clientes usan el mismo certificado de cliente y hace falta revocarlo, es preciso generar e instalar certificados para todos los clientes que usan dicho certificado para autenticarlos.

Para generar certificados de cliente se pueden emplear los métodos siguientes:

- **Certificado de empresa:**

  - Si usa una solución de certificación de empresa, genere un certificado de cliente con el formato de valor de nombre común "name@yourdomain.com", en lugar del formato "nombreDeDominio\nombreDeUsuario".
  - Asegúrese de que el certificado de cliente se base en la plantilla de certificado "Usuario" que tenga "Autenticación de cliente" como primer elemento de la lista de uso, y no Inicio de sesión de tarjeta inteligente, etc. Para comprobar el certificado, haga doble clic en el certificado de cliente y vea *Detalles > Uso mejorado de claves*.

- **Certificado raíz autofirmado:** si genera un certificado de cliente desde un certificado raíz autofirmado mediante las instrucciones del artículo [Creación de un certificado raíz autofirmado para conexiones de punto a sitio mediante PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert), este se instala automáticamente en el equipo que utilizó para generarlo. Si desea instalar un certificado de cliente en otro equipo cliente, es preciso que lo exporte. Siga las instrucciones que aparecen en el artículo para [exportar el certificado](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).