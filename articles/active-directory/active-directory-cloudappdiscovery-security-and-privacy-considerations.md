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
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Consideraciones de seguridad y privacidad de Cloud App Discovery

Microsoft se compromete a proteger su privacidad y sus datos, al tiempo que ofrece software y servicios que le ayudan a administrar la seguridad de su organización. <br> Reconocemos que cuando confía sus datos a otros usuarios, esa confianza requiere rigurosas inversiones en ingeniería de seguridad y pericia para soportarla. Microsoft cumple estrictas instrucciones de seguridad y cumplimiento de normas, desde prácticas de ciclo de vida en el desarrollo de software hasta la operación de un servicio. <br> La seguridad y la protección de los datos son prioritarias en Microsoft.

En este tema se explica cómo se recopilan, procesan y protegen los datos dentro de Cloud App Discovery de Azure Active Directory.




##Información general

Cloud App Discovery es una característica de Azure AD y se hospeda en Microsoft Azure. <br> Los agentes de extremo de Cloud App Discovery se usan para recopilar datos de detección de aplicaciones de equipos de TI administrados. <br> Los datos recopilados se envían de forma segura sobre un canal cifrado al servicio Cloud App Discovery de Azure AD. <br> Después, los datos de Cloud App Discovery de una organización pasan a estar visibles en el portal de Azure.


<center>![How Cloud App Discovery Works](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center>


En las siguientes secciones se describe el flujo de información y cómo se protege en su desplazamiento desde la organización hasta el servicio Cloud App Discovery y, por último, al portal de Cloud App Discovery.



## Recopilación de datos de su organización

A fin de poder usar la característica de Cloud App Discovery de Azure Active Directory para obtener información sobre las aplicaciones que usan los empleados de su organización, primero deberá implementar agentes de extremo de Cloud App Discovery de Azure AD en las máquinas de su organización.

Los administradores del inquilino de Azure Active Directory (o su delegado) pueden descargar el paquete de instalación del agente desde el portal de Azure. Los agentes se pueden instalar manualmente o se pueden instalar en varias máquinas de la organización mediante SCCM o directiva de grupo.

Para obtener más instrucciones sobre las opciones de implementación, vea [Guía de implementación de la directiva de grupo de Policy Deployment Guide](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).


### Datos recopilados por el agente

Cuando se realizan conexiones a las aplicaciones web, el agente recopila la información que se indica en la lista siguiente. La información solo se recopila para aquellas aplicaciones que el administrador ha configurado para la detección de la aplicación. <br> La lista de aplicaciones para las que elige recopilar metadatos se puede configurar en el portal, en la pestaña Configuración.



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

A medida que el tráfico web se envía y se recibe en la máquina, Cloud App Discovery lo analiza y extrae los metadatos relevantes (vea la tabla anterior). <br> Cada minuto, el agente carga los datos recopilados en el servicio Cloud App Discovery a través de un canal cifrado.

Para las conexiones cifradas, se realiza un paso adicional en un esfuerzo por mejorar la precisión de los datos recopilados.<br> Esto se conoce como una inspección en profundidad. <br> El componente de controlador intercepta el tráfico cifrado y se inserta en la secuencia cifrada. Para ello crea un certificado raíz autofirmado en el equipo, lo que hace que la aplicación cliente confíe en el agente de Cloud App Discovery. Este certificado raíz autofirmado está marcado como no exportable y se agrega a la ACL para los administradores. Está pensado para no salir nunca de la máquina en la que se creó.


### Respeto de la privacidad de los usuarios

Nuestro objetivo es proporcionar a los administradores las herramientas necesarias para establecer un equilibrio entre la óptica detallada en el uso de las aplicaciones y la privacidad de los usuarios que sea apropiado para su organización. Para ello, proporcionamos los siguientes botones en la página de configuración del portal:

- **Recopilación de datos**: los administradores pueden especificar de qué aplicaciones o categorías de aplicaciones quieren obtener datos de detección.


- **Inspección en profundidad**: los administradores pueden especificar si el agente recopilará el tráfico HTTP para las conexiones SSL/TLS.



El agente de extremo de Cloud App Discovery solo recopila la información que se describe en la tabla anterior.



> [AZURE.NOTE]Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 



## Envío de datos a Cloud App Discovery

Una vez que el agente ha recopilado los datos, estos se almacenan en caché en la máquina durante un máximo de un minuto o hasta que los datos en caché alcanzan un tamaño de 5 MB. Después se comprimen y se envían a través de una conexión segura al servicio Cloud App Discovery.

Si el agente no puede comunicarse con el servicio Cloud App Discovery por algún motivo, los datos recopilados se almacenan en una caché de archivos local a la que solo pueden acceder usuarios con privilegios en la máquina (por ejemplo, el grupo Administradores). <br> El agente intenta reenviar automáticamente los metadatos en caché hasta que el servicio Cloud App Discovery los recibe correctamente.



## Recepción de los datos en el extremo del servicio

Los agentes se autentican en el servicio Cloud App Discovery con el certificado de autenticación de cliente específico de la máquina que se mencionó anteriormente, y reenvía los datos a través de un canal cifrado. <br> La canalización de análisis del servicio Cloud App Discovery procesa los metadatos de cada cliente por separado realizando particiones lógicas de los mismos en todas las etapas de dicha canalización. Los datos analizados conforman los distintos informes del portal.

Los datos no procesados y los metadatos analizados se almacenan durante 180 días. Además, los clientes pueden optar por capturar los metadatos analizados en una cuenta de almacenamiento de blobs de Azure de su preferencia. Esto es útil para realizar análisis sin conexión de los metadatos y para conservar los datos más tiempo.

## Acceso a los datos mediante el portal de Azure

Con el fin de proteger los datos recopilados, de forma predeterminada, solo los administradores globales del inquilino tienen acceso a la característica Cloud App Discovery en el portal de Azure. <br> Sin embargo, los administradores pueden optar por delegar este acceso a otros usuarios o grupos.



> [AZURE.NOTE]Para obtener más información, vea [Introducción a Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx).
 


Cualquier usuario que acceda a los datos del portal debe tener una licencia Premium de Azure AD.



**Recursos adicionales**


* [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=July15_HO5-->