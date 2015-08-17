<properties 
	pageTitle="Consideraciones de seguridad y privacidad de Cloud App Discovery" 
	description="En este tema se describen las consideraciones de seguridad y privacidad relacionadas Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="markusvi"/>

# Consideraciones de seguridad y privacidad de Cloud App Discovery

Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayudan a administrar la seguridad de su organización. <br> Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere rigurosas inversiones en ingeniería de seguridad y pericia para soportarla. Microsoft cumple estrictas instrucciones de seguridad y cumplimiento de normas, desde prácticas de ciclo de vida en el desarrollo de software hasta la operación de un servicio. <br> La seguridad y la protección de los datos son prioritarias en Microsoft.

En este tema se explica cómo se recopilan, procesan y protegen los datos dentro de Cloud App Discovery de Azure Active Directory.




##Información general

Cloud App Discovery es una característica de Azure AD y se hospeda en Microsoft Azure. <br> Cloud App Discovery Endpoint Agent se usa para recopilar datos de detección de aplicaciones de máquinas administradas por TI. <br> Los datos recopilados se envían de forma segura sobre un canal cifrado al servicio Cloud App Discovery de Azure AD. <br> Después, los datos de Cloud App Discovery de una organización pasan a estar visibles en el portal de Azure.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


En las siguientes secciones se describe el flujo de información y cómo se protege en su desplazamiento desde la organización hasta el servicio Cloud App Discovery y, por último, al portal de Cloud App Discovery.



## Recopilación de datos de su organización

A fin de poder usar la característica Cloud App Discovery de Azure Active Directory para obtener información sobre las aplicaciones que usan los empleados de su organización, primero deberá implementar Cloud App Discovery Endpoint Agent de Azure AD en las máquinas de su organización.

Los administradores del inquilino de Azure Active Directory (o su delegado) pueden descargar el paquete de instalación del agente desde el portal de Azure. El agente se puede instalar manualmente o se puede instalar en varias máquinas de la organización mediante SCCM o la directiva de grupo.

Para obtener más instrucciones sobre las opciones de implementación, vea [Guía de implementación de la directiva de grupo de Policy Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Datos recopilados por el agente

La información descrita en esta lista la recopila el agente cuando se realiza una conexión a una aplicación web. La información solo se recopila para aquellas aplicaciones que el administrador ha configurado para detección. <br> Puede editar la lista de aplicaciones en la nube que el agente supervisa a través de la hoja Cloud App Discovery en el [Portal de Microsoft Azure](https://portal.azure.com), en **Configuración**->**Recopilación de datos**->**Lista de recopilación de aplicaciones**.


> [AZURE.NOTE]Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).

**Categoría de información**: información del usuario<br> **Descripción**:<br> el nombre de usuario de Windows del proceso que realizó una solicitud a la aplicación web de destino (por ejemplo: DOMINIO\\nombre de usuario), así como el identificador de seguridad de Windows (SID) del usuario.


**Información de categoría**: información del proceso <br> **Descripción**:<br> el nombre del proceso que realizó la solicitud a la aplicación web de destino (por ejemplo: "iexplore.exe").

**Información de categoría**: información de la máquina<br> **Descripción**:<br> el nombre NetBIOS de la máquina en la que está instalado el agente.

**Información de categoría**: información del tráfico de la aplicación<br> **Descripción**: <br>

La siguiente información de conexión:

- Las direcciones IP de origen (equipo local) y de destino, y los números de puerto.

- La dirección IP pública de la organización a través de la cual sale la solicitud.

- La hora de la solicitud.

- El volumen de tráfico enviado y recibido.

- La versión de IP (4 o 6).

- Solo para las conexiones TLS: el nombre de host de destino de la extensión de Indicación de nombre de servidor o del certificado de servidor.

La siguiente información de HTTP:

- Método (GET, POST, etc.).

- Protocolo (HTTP/1.1, etc.).

- Cadena de agente de usuario

- Nombre de host.

- URI de destino (sin incluir la cadena de consulta).

- Información del tipo de contenido.

- Información de dirección URL del origen de referencia (sin incluir la cadena de consulta).



> [AZURE.NOTE]La información HTTP anterior se recopila para todas las conexiones no cifradas. Para las conexiones TLS, esta información solo se captura cuando está activada la inspección en profundidad en el portal. El valor es ‘ON’ (activado) de forma predeterminada. Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).



### Cómo funciona el agente

La instalación del agente incluye dos componentes:

- Un componente de modo de usuario

- Un componente de controlador de modo kernel (controlador de Plataforma de filtrado de Windows)



Cuando se instala por primera vez, el agente almacena un certificado de confianza específico del equipo en la máquina que después usa para establecer una conexión segura con el servicio Cloud App Discovery. <br> Periódicamente, el agente recupera la configuración de directiva del servicio Cloud App Discovery en esta conexión segura. <br> La directiva incluye información sobre qué aplicaciones en la nube se deben supervisar y si la actualización automática debe estar habilitada, entre otras cosas.

A medida que el tráfico web se envía y se recibe en la máquina desde Chrome o Internet Explorer, Cloud App Discovery Agent lo analiza y extrae los metadatos relevantes (vea la tabla anterior). <br> Cada minuto, el agente carga los metadatos recopilados en el servicio Cloud App Discovery a través de un canal cifrado.

El componente de controlador intercepta el tráfico cifrado y se inserta en la secuencia cifrada. Para ello crea un certificado raíz autofirmado en el equipo, lo que hace que la aplicación cliente confíe en el agente de Cloud App Discovery. Este certificado raíz autofirmado está marcado como no exportable y se agrega a la ACL para los administradores. Está pensado para no salir nunca de la máquina en la que se creó.


### Respeto de la privacidad de los usuarios

Nuestro objetivo es proporcionar a los administradores las herramientas necesarias para establecer un equilibrio entre la óptica detallada en el uso de las aplicaciones y la privacidad de los usuarios que sea apropiado para su organización. Para ello, proporcionamos los siguientes botones en la página de configuración del portal:

- **Recopilación de datos**: los administradores pueden especificar de qué aplicaciones o categorías de aplicaciones quieren obtener datos de detección.

- **Inspección en profundidad**: los administradores pueden elegir especificar si el agente recopilará el tráfico HTTP para las conexiones SSL/TLS (lo que se conoce como **’Inspección en profundidad’**). Puede obtener más información al respecto en la siguiente sección.

- **Opciones de consentimiento**: los administradores pueden elegir si notificar a los usuarios la recopilación de datos por el agente, y si pedir al usuario que de su consentimiento antes de que el agente comience a recopilar datos de los usuarios.

El agente de extremo de Cloud App Discovery solo recopila la información que se describe en la tabla anterior.



> [AZURE.NOTE]Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).

### Interceptación de datos de conexiones cifradas
Como mencionamos anteriormente, los administradores pueden configurar el agente para que supervise datos de conexiones cifradas ('inspección en profundidad'). TLS ([Seguridad de la capa de transporte](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) es uno de los protocolos más comunes que se utilizan hoy en día en Internet. Con el cifrado de la comunicación con TLS, un cliente puede establecer un canal de comunicación seguro y privado con un servidor web. Con TLS podemos ofrecer una protección esencial para pasar las credenciales de autenticación y evitar la revelación de información confidencial.

Si bien el canal cifrado seguro de extremo a extremo que proporciona TLS permite una protección importante de la privacidad y la seguridad, con frecuencia el protocolo se usa de forma indebida con fines malintencionados. Tanto es así que, de hecho, ese HTTPS se conoce a menudo como "protocolo de omisión de firewall universal". La raíz del problema es que la mayoría de los firewalls no son capaces de inspeccionar la comunicación TLS porque los datos de la capa de aplicación están cifrados con SSL. Sabiendo esto, los atacantes aprovechan con frecuencia TLS para entregar cargas malintencionadas a un usuario seguro de que incluso los firewalls de capa de aplicación más inteligentes son completamente ciegos a TLS y simplemente deben retransmitir la comunicación TLS entre los hosts. Con frecuencia, los usuarios finales aprovechan TLS para omitir los controles de acceso aplicados por sus firewalls y servidores proxy corporativos, y lo usan para conectarse a servidores proxy públicos y para la tunelización de protocolos no TLS a través del firewall que, de lo contrario, podrían ser bloqueados por la directiva.

La inspección en profundidad permite a Cloud App Discovery Agent actuar como un intermediario de confianza. Cuando se realiza una solicitud para obtener acceso a un recurso protegido con HTTPS, el agente establece una nueva conexión al servidor de destino y recupera su certificado SSL. Cloud App Discovery Endpoint Agent copia a continuación la información del certificado y crea su propio certificado mediante estos detalles y se lo proporciona al cliente. Puesto que el cliente confía en el certificado raíz de Cloud App Discovery Endpoint Agent, el proceso es, principalmente, transparente para el usuario final.


### Desventajas y problemas conocidos
Existen algunos casos donde la interceptación de TLS puede afectar a la experiencia del usuario final: los certificados de validación extendida (EV) representan la barra de direcciones del explorador web en verde para que sirva como una indicación visual de que está visitando un sitio web de confianza. La interceptación de TLS no puede duplicar la validación extendida en el certificado que emite al cliente, así que los sitios web que usan certificados EV funcionarán con normalidad pero la barra de direcciones no se mostrará en verde. La asignación de claves pública (lo que también se conoce como asignación de certificados) está concebida para ayudar a proteger a los usuarios de ataques de tipo «Man in the middle» y entidades emisoras de certificados falsas. Cuando el certificado raíz para un sitio asignado no coincide con el de una CA válida conocida, el explorador rechaza la conexión con un error. Puesto que la interceptación de TLS es, de hecho, un ataque de tipo «Man in the middle», se producirá un error en estas conexiones.

Para reducir la ocurrencia de estos problemas, hacemos todo lo posible para mantener el seguimiento de las aplicaciones conocidas que usan validación extendida o asignación de claves públicas y así evitar la interceptación de su conexión cifrada. Se seguirán enviando informes de uso de estas aplicaciones y el volumen de datos, pero dado que no se interceptan, no hay detalles acerca de cómo se utilizan.

Al habilitar la inspección de TLS, Cloud App Discovery Endpoint Agent puede descifrar e inspeccionar las comunicaciones cifradas con TLS, lo que permite al servicio reducir el ruido y proporcionar información detallada sobre el uso de las aplicaciones en la nube cifradas.


### Unas palabras de advertencia
Antes de activar una inspección en profundidad, se recomienda encarecidamente que comunique sus intenciones a los departamentos de Recursos Humanos y jurídico y obtenga su consentimiento. La inspección de la comunicación cifrada privada del usuario final puede ser un asunto delicado, por razones obvias. Antes de poner en marcha en producción la inspección de TLS, asegúrese de que se han actualizado las directivas de seguridad corporativa y de uso aceptable para indicar que se inspeccionará la comunicación cifrada. La notificación a los usuarios y la exención de sitios que se consideran confidenciales (por ejemplo, sitios médicos y bancarios) puede ser también necesario si configura Cloud App Discovery para supervisarlos.


## Envío de datos a Cloud App Discovery

Una vez que el agente ha recopilado los datos, estos se almacenan en caché en la máquina durante un máximo de un minuto o hasta que los datos en caché alcanzan un tamaño de 5 MB. Después se comprimen y se envían a través de una conexión segura al servicio Cloud App Discovery.

Si el agente no puede comunicarse con el servicio Cloud App Discovery por algún motivo, los datos recopilados se almacenan en una caché de archivos local a la que solo pueden acceder usuarios con privilegios en la máquina (por ejemplo, el grupo Administradores). <br> El agente intenta reenviar automáticamente los metadatos almacenados en caché hasta que el servicio Cloud App Discovery los recibe correctamente.



## Recepción de los datos en el extremo del servicio

Los agentes se autentican en el servicio Cloud App Discovery con el certificado de autenticación de cliente específico de la máquina que se mencionó anteriormente, y reenvía los datos a través de un canal cifrado. <br> La canalización de análisis del servicio Cloud App Discovery procesa los metadatos de cada cliente por separado realizando particiones lógicas de los mismos en todas las etapas de dicha canalización. Los datos analizados conforman los distintos informes del portal.

Los datos no procesados y los metadatos analizados se almacenan durante 180 días. Además, los clientes pueden optar por capturar los metadatos analizados en una cuenta de almacenamiento de blobs de Azure de su preferencia. Esto es útil para realizar análisis sin conexión de los metadatos y para conservar los datos más tiempo.

## Acceso a los datos mediante el portal de Azure

Con el fin de proteger los datos recopilados, de forma predeterminada, solo los administradores globales del inquilino tienen acceso a la característica Cloud App Discovery en el portal de Azure. <br> Sin embargo, los administradores pueden optar por delegar este acceso a otros usuarios o grupos.



> [AZURE.NOTE]Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).



Cualquier usuario que acceda a los datos del portal debe tener una licencia Premium de Azure AD.



**Recursos adicionales**


* [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md)

<!---HONumber=August15_HO6-->