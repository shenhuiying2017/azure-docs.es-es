<properties 
	pageTitle="Preguntas más frecuentes de Azure AD Connect Health" 
	description="Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015"
	ms.author="billmath"/>


# Preguntas más frecuentes sobre Azure AD Connect Health

Las preguntas más frecuentes son preguntas y respuestas sobre Azure AD Connect Health. Estas preguntas más frecuentes cubre las preguntas acerca de cómo utilizar el servicio, incluido el modelo de facturación, las capacidades, las limitaciones y la compatibilidad.

## Preguntas generales



**P: Tengo varios inquilinos en Azure Active Directory. ¿Cómo puedo cambiar al inquilino que tiene Azure Active Directory Premium?**

Para cambiar al inquilino de Azure AD, seleccione "Inicio" en la barra de navegación de la izquierda, seleccione el nombre de usuario conectado actualmente en la esquina superior derecha y luego elija la cuenta de inquilino correcta. Si la cuenta de inquilino no aparece, seleccione Cerrar sesión y vuelva a iniciar sesión con las credenciales de administrador de inquilino global del inquilino de Azure Active Directory Premium.


## Preguntas sobre la instalación



**P: ¿Qué impacto tiene la instalación del agente de Azure AD Connect Health en los servidores individuales?**

El impacto de la instalación del agente de Microsoft Identity Health en los servidores de ADFS es mínimo con respecto a la CPU, al consumo de memoria, al ancho de banda y al almacenamiento.

Los números siguientes son una aproximación.

- Consumo de CPU: aumento de ~1%
- Consumo de memoria: hasta 10 % de la memoria total del sistema
- Uso de ancho de banda de la red: ~1 MB por cada 1000 solicitudes de ADFS
>[AZURE.NOTE]

- Almacenamiento en búfer local para el agente de AD Health: ~20 MB
- Almacenamiento de datos requerido para canal de auditoría


Se recomienda que aprovisione un espacio en disco de 1024 MB (1 GB) para que el canal de auditoría de AD FS para agentes de AD Health procese todos los datos.

**P: ¿Tendré que reiniciar los servidores durante la instalación de los agentes de Azure AD Connect Health?**

No. La instalación de los agentes no requerirá que reinicie el servidor. Sin embargo, la instalación de algunos de los requisitos previos podría requerir reiniciar el servidor.

En Windows Server 2008 R2, por ejemplo, la instalación de .Net 4.5 Framework requiere un reinicio del servidor.


**P: ¿Los Servicios de Azure AD Connect Health funcionan mediante un proxy HTTP de paso a través?**

Sí, el proceso de registro y el funcionamiento normal pueden funcionar mediante un proxy explícito configurado para desviar las solicitudes HTTP salientes. "Netsh WinHttp set Proxy" no funciona en este caso, porque el agente usa System.Net para realizar solicitudes web en lugar de los servicios HTTP de Microsoft Windows.

Hágalo en cualquier momento antes de ejecutar Register-AdHealthAgent (el paso final de la instalación)


- Paso 1: agregar una entrada en el archivo machine.config


Busque el archivo machine.config El archivo se encuentra en in%windir%\\Microsoft.NET\\Framework64[versión]\\config\\machine.config</li>

Agregue la entrada siguiente en el elemento <configuration></configuration> en el archivo machine.config.
 
		
	<system.net>  
			<defaultProxy useDefaultCredentials="true">
       		<proxy 
        usesystemdefault="true" 
        proxyaddress="http://YOUR.PROXY.HERE.com"  
        bypassonlocal="true"/>
		</defaultProxy>
	</system.net> 

 

Puede encontrar información <defaultProxy> adicional [aquí](https://msdn.microsoft.com/library/kd3cf2ex(v=vs.110).aspx).

Este ajuste configura las aplicaciones .NET en todo el sistema para usar el proxy explícitamente definido cuando se realizan solicitudes .NET HTTP. No se recomienda modificar cada archivo app.config individual, porque esta acción se podría deshacer durante la actualización automática. Solo debe cambiar un archivo, el que se mantendrá a través de las actualizaciones si solo modifica machine.config.

- Paso 2: configurar el servidor proxy en las Opciones de Internet

Abra Internet Explorer -> Configuración -> Opciones de Internet -> Conexiones -> Configuración de LAN.

Seleccione Usar un servidor proxy para la LAN

Seleccione Opciones avanzadas si tiene distintos puertos de proxy para HTTP y HTTPS/Secure




**P: ¿Servicios de Azure AD Connect Health admiten la autenticación básica cuando se conectan con servidores proxy de HTTP?**

No. Actualmente, no se admite un mecanismo para especificar un nombre de usuario/contraseña arbitrarios para la autenticación básica.


## Preguntas sobre las operaciones



**P: ¿Es necesario habilitar la auditoría en los servidores proxy de aplicación de AD FS o de aplicación web?**

No. No es necesario habilitar la auditoría en los servidores proxy de aplicación de AD FS o de aplicación web. Solo es necesario habilitarla en los servidores federados de AD FS.



**P: ¿Cómo se resuelven las alertas de Azure AD Connect Health?**

Las alertas de Azure AD Connect Health se resuelven con una condición de acierto. Los agentes de Azure AD Connect Health detectan e informan las condiciones de acierto al servicio de manera periódica. En el caso de algunas alertas, la supresión depende del tiempo. Es decir, si la misma condición de error no se observa dentro de 48 horas desde la generación de la alerta, esta se resuelve de forma automática.




**P: ¿Qué puertos de firewall debo abrir para que funcione el agente de Azure AD Connect Health?**

Deberá abrir los puertos 80 y 443 TCP/UDP para que el agente de Azure AD Connect Health pueda comunicarse con los extremos de servicio de Azure AD Health.

<!---HONumber=July15_HO3-->