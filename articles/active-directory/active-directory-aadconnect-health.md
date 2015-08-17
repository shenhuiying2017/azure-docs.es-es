<properties 
	pageTitle="Monitorización de la infraestructura de identidad local en la nube" 
	description="En esta página se describe qué es Azure AD Connect Health y por qué debería usarlo." 
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
	ms.topic="get-started-article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Monitorización de la infraestructura de identidad local en la nube

Azure AD Connect Health le ayuda a supervisar y a comprender mejor su infraestructura de identidad local. Ofrece la capacidad de ver alertas, rendimiento, patrones de uso, ajustes de configuración; le permite mantener una conexión confiable a Office 365 y mucho más. Esto se logra mediante un agente instalado en los servidores de destino. Si desea obtener más información sobre los requisitos de Azure AD Connect Health, consulte [Requisitos de Azure AD Connect Health](active-directory-aadconnect-health-requirements.md).

![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Esta información se presenta en el portal de Azure AD Connect Health. Mediante el portal de Azure AD Connect Health puede ver alertas, supervisión del rendimiento y análisis de uso. Esta información se presenta en un mismo lugar, muy fácil de usar, para que no tenga que perder tiempo en la búsqueda de la información que necesita.

![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/usage.png)

Las actualizaciones futuras de Azure AD Connect Health incluirá la supervisión adicional y la información sobre otros componentes de identidad y servicios, como servicios de Azure AD Connect Sync. Por tanto, se proporciona un único panel mediante el modo de identidad, al permitir un entorno más sólido, mantenido e integrado que los usuarios pueden aprovechar para aumentar su capacidad de hacer su trabajo.


![Qué es Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)




## Por qué usar Azure AD Connect Health

La integración de directorios locales con Azure AD hace que los usuarios sean más productivos al proporcionar una identidad común para acceder tanto a los recursos en la nube como a los locales. Sin embargo, esta integración conlleva el desafío de garantizar que este entorno esté bien mantenido para que los usuarios puedan acceder de manera confiable a los recursos tanto a nivel local como en la nube desde cualquier dispositivo. Azure AD Connect Health proporciona un sencillo enfoque basado en la nube para supervisar y obtener información sobre la infraestructura de identidad local, que se utiliza para acceder a Office 365 o a otras aplicaciones de Azure AD. Es tan sencillo como instalar un agente en cada uno de los servidores de identidad locales.

Azure AD Connect Health para AD FS es compatible con AD FS 2.0 en Windows Server 2008/2008 R2, AD FS en Windows Server 2012/2012 R2. También se incluyen los servidores Proxy de AD FS o Proxy de aplicación web que proporcionan compatibilidad de autenticación para el acceso a la extranet. Azure AD Connect Health para AD FS proporciona el siguiente conjunto de capacidades clave:

- Ver y realizar acciones sobre las alertas para un acceso confiable a las aplicaciones protegidas de AD FS, como Azure AD
- Notificaciones de correo electrónico para alertas críticas
- Ver datos de rendimiento para determinar la planificación de capacidad
- Vistas detalladas de los patrones de inicio de sesión de AD FS para determinar las anomalías o establecer líneas de base para la planificación de capacidad

El vídeo siguiente proporcionará información general de Azure AD Connect Health:

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]


## Uso de Azure Active Directory Connect Health por primera vez desde el Portal de Azure
Para empezar a usar Azure Active Directory Connect Health, siga los pasos siguientes. Recuerde que para ver los datos de la instancia de Azure AD Connect Health, deberá instalar el agente de Azure AD Connect Health en los servidores de destino. Para descargar el agente de Azure AD Connect Health, seleccione Inicio rápido y Obtener herramientas en la primera hoja. También puede descargar directamente el agente en el vínculo que aparece a continuación. Para utilizar Azure Active Directory Connect Health, haga lo siguiente:

1. Inicie sesión en el [Portal de Microsoft Azure](https://portal.azure.com/).
2. Para obtener acceso a Azure Active Directory Connect Health, vaya a Marketplace y búsquelo o seleccione Marketplace y, luego, Seguridad + Identidad.
3. En la hoja de introducción (una hoja es una parte de la vista general. Puede pensar en una hoja como una ventana o un menú flotante), haga clic en Crear. Se abrirá otra hoja con la información del directorio.
4. En la hoja del directorio, haga clic en Crear. Si no tiene una licencia de Azure Active Directory Premium, necesitará obtener una para usar Azure AD Connect Health. Para obtener información sobre Azure AD Premium, consulte Introducción a Azure AD Premium.






![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/portal1.png)



## El portal de Azure Active Directory Connect Health
El portal de Azure AD Connect Health le permite ver alertas, información de supervisión del rendimiento y análisis de uso. Después del primer acceso a Azure AD Connect Health, se mostrará la primera hoja. Una hoja es una parte de la vista general. Puede considerar una hoja como una ventana. La primera hoja que verá muestra Inicio rápido, Servicios y Configurar. Debajo de la captura de pantalla aparece una breve explicación de cada una de ellas.

![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/portal2.png)

- **Inicio rápido**: al seleccionar esta opción, se abrirá la hoja Inicio rápido. Aquí podrá descargar el agente de Azure AD Connect Health; para ello, elija Obtener herramientas, obtenga acceso a la documentación y proporcione comentarios.
- **Servicios de federación de Active Directory**: esta opción representa todos los servicios de AD FS que actualmente supervisa Azure AD Connect Health. Las opciones que se aparecen en esta sección se describen en la sección siguiente. Consulte Servicios de Azure Active Directory Connect Health.
- Configurar: esta opción le permite activar o desactivar lo siguiente:
<ol>
1. La actualización automática, para actualizar automáticamente el agente de Azure AD Connect Health a la versión más reciente. Esto significa que actualizará automáticamente a la versión más reciente del agente de Azure AD Connect Health cuando esté disponible. Esta opción está habilitada de manera predeterminada.
2. Permitir el acceso de Microsoft a los datos de mantenimiento del directorio de Azure AD solo con fines de solución de problemas: esto significa que, si esta opción está habilitada, Microsoft podrá ver los mismos datos que usted está viendo. Esto puede ayudar a solucionar problemas. Esta opción está deshabilitada de manera predeterminada.




## Servicios de Azure Active Directory Connect Health
Esta sección representa los servicios activos y las instancias de esos servicios que supervisa Azure AD Connect Health. Si hace clic en el botón de puntos suspensivos, se abrirá una hoja con todas las instancias.

![Servicios de Azure AD Connect Health](./media/active-directory-aadconnect-health/portal3.png)

Si selecciona una de las instancias, Azure AD Connect Health abrirá una hoja con información sobre esa instancia de servicios. Aquí encontrará gran cantidad de información sobre la instancia. Esta información incluye una descripción general, propiedades, alertas, supervisión y análisis de uso. Para obtener información al respecto, consulte los vínculos a las secciones siguientes en la parte superior de esta página.



----------------------------------------------------------------------------------------------------------
## Descargar el agente de Azure AD Connect Health

Para empezar a utilizar Azure AD Connect Health, puede descargar la versión más reciente del agente aquí: [Descargar agente de Azure AD Connect Health](http://go.microsoft.com/fwlink/?LinkID=518973). Asegúrese de que ha agregado el servicio de Marketplace antes de instalar los agentes.

----------------------------------------------------------------------------------------------------------

## Alertas de Azure Active Directory Connect Health
La sección Alertas de Azure AD Connect Health proporciona la lista de alertas activas. Cada alerta incluye información pertinente, pasos de resolución y vínculos a documentación relacionada. Al seleccionar una alerta activa o una alerta resulta, verá una hoja nueva con información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional. También puede ver datos históricos sobre las alertas resueltas en el pasado.

![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/alert1.png)

Al seleccionar una alerta, recibirá información adicional, así como los pasos que puede seguir para resolver la alerta y vínculos a documentación adicional.

![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/alert2.png)

## Supervisión de rendimiento de Azure Active Directory Connect Health
Supervisión de rendimiento de Azure AD Connect Health proporciona información de supervisión sobre métricas. Al activar la casilla Supervisión, se abrirá una hoja con información detallada sobre las métricas.


![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/perf1.png)


Al seleccionar la opción Filtro en la parte superior de la hoja, puede filtrar por servidor para ver las métricas de un servidor individual. Para cambiar las métricas, simplemente haga clic con el botón derecho en el diagrama de supervisión bajo la hoja de supervisión y seleccione Editar gráfico. A continuación, desde la nueva hoja que se abre, puede seleccionar métricas adicionales en la lista desplegable y especificar un intervalo de tiempo para la visualización de los datos de rendimiento.


![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/perf2.png)

## Análisis e informes de uso de Azure Active Directory Connect Health
Análisis de uso de Azure AD Connect Health analiza el tráfico de autenticación de los servidores de federación. Al activar la casilla de análisis de uso se abrirá la hoja de análisis de uso, que le mostrará las métricas y las agrupaciones.

>[AZURE.NOTE]Para poder utilizar el análisis de uso con AD FS, debe asegurarse de que esté habilitada la auditoría de AD FS. Para obtener más información, consulte Requisitos de Azure AD Connect Health.

![Portal de Azure AD Connect Health](./media/active-directory-aadconnect-health/report1.png)

Para seleccionar otras métricas, especifique un intervalo de tiempo. Para cambiar la agrupación, simplemente haga clic con el botón derecho en el gráfico de análisis de uso y seleccione Editar gráfico. A continuación, puede especificar el intervalo de tiempo, cambiar o seleccionar métricas y cambiar la agrupación. Puede ver la distribución del tráfico de autenticación según diferentes "métricas" y agrupar cada métrica con los correspondientes parámetros "Agrupar por" que se describen a continuación

| Métrica | Agrupar por | ¿Qué significa la agrupación y por qué es útil? |
| ------ | -------- | -------------------------------------------- |
| Total de solicitudes: número total de solicitudes procesadas por el servicio de federación | Todo | Esta opción mostrará el recuento total de solicitudes sin agrupación. |
| | Application | Esta opción agrupará el número total de solicitudes en función del usuario de confianza de destino. Esta agrupación es útil para comprender qué aplicación está recibiendo tráfico y qué porcentaje del tráfico total recibe. |
| | Server | Esta opción agrupará el número total de solicitudes en el servidor que procesó la solicitud. Esta agrupación es útil para comprender la distribución de la carga de tráfico total. |
| | Unión al área de trabajo | Esta opción agrupará el número total de solicitudes en función de si las solicitudes proceden o no de dispositivos que están unidos al área de trabajo (conocidos). Esta agrupación es útil para comprender si el acceso a sus recursos se realiza con dispositivos que son desconocidos para la infraestructura de identidades. |
| | Método de autenticación | Esta opción agrupará el número total de solicitudes en función del método de autenticación utilizado para la autenticación. Esta agrupación es útil para comprender el método de autenticación común que se utiliza para la autenticación. A continuación, se indican los métodos de autenticación posibles <ol> <li>Autenticación integrada en Windows (Windows)</li> <li>Autenticación basada en formularios (formularios)</li> <li>SSO (inicio de sesión único)</li> <li>Autenticación de certificados X509 (certificado)</li> <br>Tenga en cuenta que una solicitud se considera como SSO (inicio de sesión único) si los servidores de federación reciben la solicitud con una cookie de SSO. En estos casos, si la cookie es válida, no se pide al usuario que proporcione credenciales y obtiene acceso a la aplicación sin problemas. Esto es habitual si tiene varios usuarios de confianza protegidos por los servidores de federación. |
| | Ubicación de red | Esta opción agrupará el número total de solicitudes en función de la ubicación de red del usuario. Puede ser intranet o extranet. Esta agrupación es útil para saber qué porcentaje del tráfico es de intranet y cuál de extranet. |
| Total de solicitudes con error: número total de solicitudes procesadas por el servicio de federación <br> (Esta métrica solo está disponible en AD FS para Windows Server 2012 R2)| Tipo de error | Se mostrará el número de errores en función de los tipos de error predefinidos. La agrupación es útil para comprender cuáles son los tipos de errores comunes. <ul><li>Nombre de usuario o contraseña incorrectos: errores por nombre de usuario o contraseña incorrectos.</li> <li>"Bloqueo de extranet": errores producidos cuando se reciben solicitudes de un usuario cuyo acceso a la extranet está bloqueado.</li><li> "Contraseña caducada": errores producidos cuando un usuario inicia sesión con una contraseña caducada.</li><li>"Cuenta deshabilitada": errores producidos cuando un usuario inicia sesión con una cuenta deshabilitada.</li><li>"Autenticación de dispositivos": errores producidos cuando un usuario no se puede autenticar mediante la Autenticación de dispositivos.</li><li>"Autenticación de certificado de usuario": errores producidos cuando un usuario no se puede autenticar porque el certificado no es válido.</li><li>"MFA": errores producidos cuando un usuario no se puede autenticar mediante Multi-Factor Authentication.</li><li>"Otra credencial": "Autorización de emisión": problemas producidos por errores de autorización.</li><li>"Delegación de emisión": problemas producidos por errores de delegación de emisión.</li><li>"Aceptación de tokens": errores producidos cuando ADFS rechaza el token de un proveedor de identidades de terceros.</li><li>"Protocolo": error producido por errores de protocolo.</li><li>"Desconocido": detectar todas. Otros errores que no encajan en las categorías definidas.</li> |
| | Server | Esta opción agrupará los errores en función del servidor. Esto es útil para comprender la distribución de errores entre servidores. Una distribución desigual podría indicar que un servidor presenta un estado defectuoso. |
| | Ubicación de red | Esta función agrupará los errores en función de la ubicación de red de las solicitudes (intranet frente a extranet). Esto es útil para comprender qué tipo de solicitud está fallando. |
| | Application | Esta opción agrupará los errores en función de la aplicación de destino (usuario de confianza). Esto es útil para comprender qué aplicación de destino está experimentado una mayor cantidad de errores. |
| Número de usuarios: número medio de usuarios únicos activos en el sistema | Todo | Esta opción proporciona un recuento del número medio de usuarios mediante el servicio de federación en el intervalo de tiempo seleccionado. Los usuarios no están agrupados. <br>El promedio dependerá del intervalo de tiempo seleccionado. |
| | Application | Esta opción agrupará el número medio de usuarios en función de la aplicación de destino (usuario de confianza). Esto es útil para comprender cuántos usuarios utilizan una aplicación y determinar qué aplicación. |

## Pasos siguientes
Para continuar utilizando Azure AD Connect Health, consulte [Requisitos de Azure AD Connect Health](active-directory-aadconnect-health-requirements.md). Una vez que haya instalado el agente y se recolecten datos, consulte [Operaciones de Azure AD Connect Health](active-directory-aadconnect-health-operations.md) para obtener más información sobre cómo configurar Azure AD Connect Health o revise las [Preguntas más frecuentes.](active-directory-aadconnect-health-faq.md)


**Recursos adicionales**

* [Azure AD Connect Health en MSDN](https://msdn.microsoft.com/library/azure/dn906722.aspx)


 

<!---HONumber=August15_HO6-->