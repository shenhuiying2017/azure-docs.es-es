<properties
	pageTitle="Visualización de los informes de acceso y uso"
	description="Tema que explica cómo ver informes de acceso y uso para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización."
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="TerryLan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/23/2015"
	ms.author="kenhoff;Justinha"/>

#  Visualización de los informes de acceso y uso

Puede usar los informes de acceso y uso de Active Directory de Azure para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización. Con esta información, un administrador de directorios puede determinar mejor dónde puede haber posibles riesgos de seguridad de modo que pueda planear adecuadamente la mitigación de estos riesgos.

En el Portal de administración de Azure, los informes se clasifican de la manera siguiente:

- Informes de anomalías: contienen eventos de inicio de sesión que consideramos anómalos. Nuestro objetivo es que sea consciente de dicha actividad y que pueda tomar una decisión sobre si un evento es sospechoso.
- Informes de aplicaciones integradas: proporciona información sobre cómo se usan en su organización aplicaciones en la nube. Azure Active Directory ofrece integración con miles de aplicaciones en la nube.
- Informes de errores: indican errores que se pueden producir al aprovisionar cuentas a aplicaciones externas.
- Informes específicos del usuario: muestran los datos de actividad de dispositivo/inicio de sesión para un usuario específico.
- Registros de actividad: contienen un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días, así como los cambios de la actividad de grupo y la actividad de registro y restablecimiento de contraseña.

> [AZURE.NOTE]
>
- Algunos informes de uso de recursos y de anomalías avanzados solo están disponibles cuando se habilita [Azure Active Directory Premium](active-directory-get-started-premium.md). Los informes avanzados le ayudan a mejorar la seguridad de acceso, responder a amenazas potenciales y obtener acceso a análisis sobre el uso de aplicaciones y el acceso a dispositivos.
- Las ediciones Premium y Básico de Azure Active Directory están disponibles para los clientes de China que utilizan la instancia de Azure Active Directory en todo el mundo. Las ediciones Premium y Básico de Azure Active Directory no se admiten actualmente en el servicio de Microsoft Azure operado por 21Vianet en China. Para obtener más información, póngase en contacto con nosotros en el [foro de Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory).

##  Descripciones de los informes

|	Informe |	Descripción |
|	------												|	-----																						|
|	Inicios de sesión desde orígenes desconocidos |	Puede indicar un intento de iniciar sesión sin realizar ningún seguimiento. |
|	Inicios de sesión tras varios errores |	Puede indicar un ataque por fuerza bruta realizado con éxito. |
|	Inicios de sesión desde varias ubicaciones geográficas |	Puede indicar que varios usuarios inician una sesión con la misma cuenta. |
|	Inicios de sesión desde direcciones IP con actividad sospechosa |	Puede indicar un inicio de sesión correcto después de un intento de intrusión sostenido. |
|	Inicios de sesión desde dispositivos posiblemente infectados |	Puede indicar un intento de iniciar sesión desde dispositivos posiblemente infectados. |
|	Actividad de inicio de sesión irregular |	Puede indicar eventos incorrectos en los patrones de inicio de sesión de los usuarios. |
|	Usuarios con actividad de inicio de sesión erróneo. |	Indica los usuarios cuyas cuentas se han comprometido. |
|	Usuarios con credenciales perdidas |	Usuarios con credenciales perdidas |
|	Informe de auditoría |	Eventos auditados en el directorio |
|	Actividad de restablecimiento de contraseña |	Proporciona una vista detallada de los restablecimientos de contraseña que se producen en su organización. |
|	Actividad de registro de restablecimiento de contraseñas |	Proporciona una vista detallada de los registros de restablecimiento de contraseña que se producen en su organización. |
|	Actividad de los grupos de autoservicio |	Proporciona un registro de actividad para la actividad de autoservicio de todo el grupo en su directorio |
|	Uso de la aplicación |	Proporciona un resumen de uso para todas las aplicaciones de SaaS integradas con su directorio. |
|	Actividad de aprovisionamiento de cuentas |	Proporciona un historial de intentos para aprovisionar cuentas en aplicaciones externas. |
|	Estado de la sustitución de contraseña |	Proporciona información detallada del estado de sustitución automática de contraseñas de aplicaciones SaaS. |
|	Errores de aprovisionamiento de cuentas |	Indica un impacto en el acceso de los usuarios a aplicaciones externas. |
|	Uso de RMS |	Proporciona un resumen de uso de Rights Management |
|	Usuarios de RMS más activos |	Enumera los 1.000 usuarios activos principales que han accedido a archivos protegidos mediante RMS |
|	Uso de dispositivos RMS |	Enumera los dispositivos utilizados para obtener acceso a los archivos protegidos mediante RMS |
|	Uso de aplicaciones habilitadas para RMS |	Permite usar aplicaciones habilitadas para RMS |

##  Ediciones de los informes

|	Informe |	Gratuito |	Básica |	Premium |
|	------												|	----	|	-----	|	--------	|
|	Inicios de sesión desde orígenes desconocidos |	✓ |	✓ |	✓ |
|	Inicios de sesión tras varios errores |	✓ |	✓ |	✓ |
|	Inicios de sesión desde varias ubicaciones geográficas |	✓ |	✓ |	✓ |
|	Inicios de sesión desde direcciones IP con actividad sospechosa | | |	✓ |
|	Inicios de sesión desde dispositivos posiblemente infectados | | |	✓ |
|	Actividad de inicio de sesión irregular | | |	✓ |
|	Usuarios con actividad de inicio de sesión erróneo. | | |	✓ |
|	Usuarios con credenciales perdidas | | |	✓ |
|	Informe de auditoría | | |	✓ |
|	Actividad de restablecimiento de contraseña | | |	✓ |
|	Actividad de registro de restablecimiento de contraseñas | | |	✓ |
|	Actividad de los grupos de autoservicio | | |	✓ |
|	Uso de la aplicación | | |	✓ |
|	Actividad de aprovisionamiento de cuentas |	✓ |	✓ |	✓ |
|	Estado de la sustitución de contraseña | | |	✓ |
|	Errores de aprovisionamiento de cuentas |	✓ |	✓ |	✓ |
|	Uso de RMS | | |	Solo RMS |
|	Usuarios de RMS más activos | | |	Solo RMS |
|	Uso de dispositivos RMS | | |	Solo RMS |
|	Uso de aplicaciones habilitadas para RMS | | |	Solo RMS |

##  Detalles de los informes

###  Inicios de sesión desde orígenes desconocidos

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe indica a los usuarios que han iniciado sesión correctamente en su directorio y se les ha asignado una dirección IP de cliente que Microsoft les ha reconocido como dirección IP de proxy anónimo. Estos servidores proxy los usan a menudo los usuarios que desean ocultar la dirección IP del equipo y es posible que se usen con fines malintencionados, como ocurre a veces con los hackers. </p><p> Los resultados de este informe mostrarán el número de veces que un usuario ha iniciado sesión correctamente en su directorio desde esa dirección y la dirección IP de proxy.</p> | Directorio > pestaña Informes |

![Inicios de sesión desde orígenes desconocidos](./media/active-directory-view-access-usage-reports/signInsFromUnknownSources.PNG)]

###  Inicios de sesión tras varios errores

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Este informe indica los usuarios que han iniciado sesión correctamente después de varios intentos del inicio de sesión consecutivos. Las posibles causas son: <ul><li>El usuario había olvidado su contraseña</li><li>El usuario es víctima de una ataque por fuerza bruta para averiguar la contraseña con éxito</li></ul><p>Los resultados de este informe mostrarán el número de intentos de inicio de sesión consecutivos fallidos realizados antes del inicio de sesión correcto y una marca de tiempo asociada al primer inicio de sesión correcto.</p><p><b>Configuración de informes</b>: puede configurar el número mínimo de intentos de inicio de sesión fallidos consecutivos que se deben producir antes de que se puedan mostrar en el informe. Al realizar cambios en esta configuración, es importante tener en cuenta que estos cambios no se aplicarán a cualquier inicio de sesión erróneo existente que se muestra actualmente en el informe existente. Sin embargo, se aplicarán a todos los inicios de sesión futuros. Los cambios realizados en este informe solo los pueden llevar a cabo los administradores con licencia. | Directorio > pestaña Informes |

![Inicios de sesión tras varios errores](./media/active-directory-view-access-usage-reports/signInsAfterMultipleFailures.PNG)]


###  Inicios de sesión desde varias ubicaciones geográficas

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe incluye actividades de inicio de sesión correcto de un usuario en las que parece que dos inicios de sesión se originaron desde distintas regiones y que, según el tiempo pasado entre los inicios de sesión, parece imposible que el usuario haya viajado entre dichas regiones. Las causas posibles son:</p><ul><li>El usuario comparte su contraseña</li><li>El usuario está usando un escritorio remoto para iniciar un explorador web para el inicio de sesión</li><li>Un hacker ha iniciado sesión en la cuenta de un usuario desde otro país.</li></ul><p>Los resultados de este informe mostrarán los eventos de inicio de sesión correcto, así como el tiempo entre los inicios de sesión, las regiones desde donde parece que se originaron los inicios de sesión y el tiempo estimado de viaje entre las regiones.</p><p>El tiempo de viaje mostrado solo es una estimación y puede ser distinto del tiempo de viaje real entre las ubicaciones. Además, no se genera ningún evento para inicios de sesión entre regiones vecinas.</p> | Directorio > pestaña Informes |

![Inicios de sesión desde varias ubicaciones geográficas](./media/active-directory-view-access-usage-reports/signInsFromMultipleGeographies.PNG)]


###  Inicios de sesión desde direcciones IP con actividad sospechosa

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe incluye intentos de inicio de sesión que se han ejecutado desde direcciones IP en que se ha detectado actividad sospechosa. Las actividades sospechosas incluyen muchos intentos de inicio de sesión fallido desde la misma dirección IP en un corto período de tiempo y otras actividades que se consideran sospechosas. Esto puede indicar que un hacker ha intentado iniciar sesión desde esta dirección IP.</p><p>Los resultados de este informe mostrarán intentos de inicio de sesión que se originaron desde una dirección IP en la que se detectó actividad sospechosa, junto con la marca de tiempo asociada al inicio de sesión.</p> | Directorio > pestaña Informes |

![Inicios de sesión desde direcciones IP con actividad sospechosa](./media/active-directory-view-access-usage-reports/signInsFromIPAddressesWithSuspiciousActivity.PNG)]


###  Actividad de inicio de sesión erróneo.

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Este informe incluye inicios de sesión que nuestros algoritmos de aprendizaje automático han identificado como "erróneos". Los motivos para marcar como irregular un intento de inicio de sesión incluyen ubicaciones de inicio de sesión inesperadas, la hora del día y las ubicaciones o una combinación de ambas cosas. Esto puede indicar que un hacker ha intentado iniciar sesión con esta cuenta. El algoritmo de aprendizaje automático clasifica los eventos como "erróneos" o "sospechosos", donde "sospechoso" indica una mayor probabilidad de una infracción de seguridad.</p><p>Los resultados de este informe mostrarán estos inicios de sesión, junto con la clasificación, la ubicación y una marca de tiempo asociada a cada inicio de sesión.</p><p>Enviaremos una notificación por correo electrónico a los administradores globales si encontramos 10 o más eventos de inicio de sesión erróneo dentro de un intervalo de 30 días o menos. Asegúrese de incluir aad-alerts-noreply@mail.windowsazure.com en la lista de remitentes seguros.</p> | Directorio > pestaña Informes |



###  Inicios de sesión desde dispositivos posiblemente infectados

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Use este informe si desea ver inicios de sesión desde dispositivos en los que puede que se esté ejecutando malware (software malintencionado). Correlacionamos las direcciones IP de inicios de sesión frente a las direcciones IP desde las que se realizó un intento de ponerse en contacto con un servidor de malware.</p><p>Recomendación: como este informe supone que una dirección IP se asoció con el mismo dispositivo en ambos casos, recomendamos que se ponga en contacto con el usuario y analice el dispositivo del usuario para estar seguro.</p><p>Para obtener más información sobre cómo tratar infecciones de malware, consulte el [Centro de protección contra malware](http://go.microsoft.com/fwlink/?linkid=335773). </p> | Directorio > pestaña Informes |

![Inicios de sesión desde dispositivos posiblemente infectados](./media/active-directory-view-access-usage-reports/signInsFromPossiblyInfectedDevices.PNG)]


###  Usuarios con actividad de inicio de sesión erróneo.

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| <p>Use este informe para ver todas las cuentas de usuario respecto a las cuales se ha identificado actividad de inicio de sesión erróneo. Este informe incluye datos de todos los informes de actividad errónea. Los resultados de este informe mostrarán detalles sobre el usuario, el motivo por el que el evento de inicio de sesión se ha identificado como erróneo, la fecha y hora y otra información relevante sobre el evento.</p> | Directorio > pestaña Informes |

![Usuarios con actividad de inicio de sesión erróneo.](./media/active-directory-view-access-usage-reports/usersWithAnomalousSignInActivity.PNG)]


##  Informes de aplicaciones integradas

###  Uso de la aplicación: resumen

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe cuando quiera ver el uso de todas las aplicaciones SaaS en su directorio. Este informe se basa en el número de veces que los usuarios hacen clic en la aplicación en el Panel de acceso. | Directorio > pestaña Informes |

![Resumen de uso de la aplicación](./media/active-directory-view-access-usage-reports/applicationUsage.PNG)]


###  Uso de la aplicación: detallado

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe cuando quiera ver cuánto se está usando una aplicación SaaS específica. Este informe se basa en el número de veces que los usuarios hacen clic en la aplicación en el Panel de acceso. | Directorio > pestaña Informes |

###  Panel de la aplicación

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Este informe indica los inicios de sesión acumulados en la aplicación por los usuarios de su organización en un intervalo de tiempo seleccionado. El gráfico de la página Panel le ayudará a identificar las tendencias de uso de la aplicación. | Directorio > Aplicación > pestaña Panel |

##  Informes de errores

###  Errores de aprovisionamiento de cuentas

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Úselo para supervisar los errores que se producen durante la sincronización de cuentas desde las aplicaciones SaaS a Azure Active Directory. | Directorio > pestaña Informes |

![Errores de aprovisionamiento de cuentas](./media/active-directory-view-access-usage-reports/accountProvisioningErrors.PNG)]


##  Informes específicos del usuario

###  Dispositivos

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Use este informe si desea ver la dirección IP y la ubicación geográfica de los dispositivos que un usuario específico ha usado para tener acceso a Active Directory de Azure. | Directorio > Usuarios > <i>Usuario</i> > pestaña Dispositivos |

###  Actividad

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra la actividad de inicio de sesión de un usuario. El informe incluye información como la aplicación en la que se ha iniciado sesión, el dispositivo usado, la dirección IP y la ubicación. No recopilamos el historial de los usuarios que inician sesión con una cuenta de Microsoft. | Directorio > Usuarios > <i>Usuario</i> > pestaña Actividad |

####  Eventos de inicio de sesión incluidos en el informe de actividad

Solo determinados tipos de eventos de inicio de sesión aparecerán en el informe de actividad del usuario.

| Tipo de evento | ¿Se incluye? |
| ----------------------								| ---------		|
| Inicios de sesión en el [Panel de acceso](http://myapps.microsoft.com/) | Sí |
| Inicios de sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/) | Sí |
| Inicios de sesión en el [Portal de Microsoft Azure](http://portal.azure.com/) | Sí |
| Inicios de sesión en el [portal de Office 365](http://portal.office.com/) | Sí |
| Inicios de sesión en una aplicación nativa, como Outlook (consulte la excepción a continuación) | Sí |
| Inicios de sesión en una aplicación federada/aprovisionada a través del Panel de acceso, como Salesforce | Sí |
| Inicios de sesión en una aplicación basada en contraseña a través del Panel de acceso, como Twitter | Sí |
| Inicios de sesión en una aplicación empresarial personalizada que se ha agregado al directorio | No (próximamente) |
| Inicios de sesión en una aplicación de Proxy de aplicación de Azure AD que se ha agregado al directorio | No (próximamente) |

> Nota: para reducir la cantidad de ruido en este informe, no se muestran los inicios de sesión en la aplicación nativa [Lync/Skype para empresas](http://products.office.com/es-es/skype-for-business/online-meetings) y el [Asistente para el inicio de sesión de Microsoft Online Services](http://community.office365.com/es-es/w/sso/534.aspx).

##  Registros de actividad

###  Informe de auditoría

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra un registro de todos los eventos auditados en las últimas 24 horas, los últimos 7 días o los últimos 30 días. <br /> Para obtener más información, consulte [Eventos del informe de auditoría de Azure Active Directory](active-directory-reporting-audit-events.md). | Directorio > pestaña Informes |

![Informe de auditoría](./media/active-directory-view-access-usage-reports/auditReport.PNG)]


###  Informe de actividad de grupos

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra toda la actividad de los grupos administrados de autoservicio en el directorio. | Directorio > Usuarios > <i>Usuario</i> > pestaña Dispositivos |

![Actividad de los grupos de autoservicio](./media/active-directory-view-access-usage-reports/selfServiceGroupsActivity.PNG)]


###  Informe de actividad de registro de restablecimiento de contraseña

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra los registros de restablecimiento de contraseña que se han producido en su organización. | Directorio > pestaña Informes |

![Actividad de registro de restablecimiento de contraseñas](./media/active-directory-view-access-usage-reports/passwordResetRegistrationActivity.PNG)]


###  Actividad de restablecimiento de contraseña

| Descripción | Ubicación del informe |
| :-------------     | :-------        |
| Muestra los intentos de restablecimiento de contraseña que se han producido en su organización. | Directorio > pestaña Informes |

![Actividad de restablecimiento de contraseña](./media/active-directory-view-access-usage-reports/passwordResetActivity.PNG)]


##  Aspectos que se deben tener en cuenta si sospecha de una infracción de seguridad

Si sospecha que una cuenta de usuario puede estar en peligro o que cualquier tipo de actividad de usuario sospechosa puede dar lugar a una infracción de seguridad de los datos del directorio en la nube, considere la posibilidad de realizar una o varias de las acciones siguientes:

- Póngase en contacto con el usuario para comprobar la actividad.
- Restablezca la contraseña del usuario.
- [Habilite Multi-factor Authentication](http://go.microsoft.com/fwlink/?linkid=335774) para obtener seguridad adicional

##  Visualización o descarga de un informe

1. En el Portal de administración de Azure, haga clic en **Active Directory**, haga clic en el nombre del directorio de la organización y, a continuación, haga clic en **Informes**.
2. En la página Informes, haga clic en el informe que desea ver o descargar.
    >
    > [AZURE.NOTE]

3. Haga clic en el menú desplegable situado al lado de Intervalo y, a continuación, seleccione uno de los siguientes intervalos de tiempo que se debe usar al generar este informe:
    - Últimas 24 horas
    - Últimos 7 días
    - Últimos 30 días
4. Haga clic en el icono de marca de verificación para ejecutar el informe.
	- Se mostrarán hasta 1000 eventos en el Portal de administración de Azure.
5. Si corresponde, haga clic en **Descargar** para descargar el informe en un archivo comprimido en formato de valores separados por comas (CSV) para verlo sin conexión o con fines de archivo.
	- Hasta 75000 eventos se incluirán en el archivo descargado.

##  Ignorar un evento

Si está viendo informes de anomalías, observará que puede ignorar varios eventos que se muestran en informes relacionados. Para ignorar un evento, resáltelo en el informe y, a continuación, haga clic en **Ignorar**. El botón **Ignorar** quitará de forma permanente el evento resaltado del informe y solo lo pueden usar administradores globales con licencia.

##  Notificaciones de correo electrónico automáticas

###  Qué informes generan notificaciones de correo electrónico

En este momento, solo el informe de inicio actividad de inicio de sesión erróneo y el informe de usuarios con actividad de inicio de sesión erróneo usan el sistema de notificación de correo electrónico.

###  ¿Qué desencadena el envío de la notificación de correo electrónico?

De forma predeterminada, Azure Active Directory se establece para enviar automáticamente notificaciones de correo electrónico a todos los administradores globales. El correo electrónico se envía en las siguientes condiciones para cada informe.

En el caso del informe de actividad de inicio de sesión erróneo.

- Orígenes desconocidos: 10 eventos
- Varios errores: 10 eventos
- Direcciones IP con actividad sospechosa: 10 eventos
- Dispositivos infectados: 10 eventos

En el caso del informe de usuarios con actividad de inicio de sesión erróneo:

- Orígenes desconocidos: 10 eventos
- Varios errores: 10 eventos
- Direcciones IP con actividad sospechosa: 10 eventos
- Dispositivos infectados: 5 eventos
- Informe de inicios de sesión erróneos: 15 eventos

El correo electrónico se envía si se cumple alguna de las condiciones anteriores en un período de 30 días o desde que se envió el último correo electrónico si es menos de 30 días.

Los inicios de sesión erróneos son aquellos que nuestros algoritmos de aprendizaje automático han identificado como "erróneos" debido a ubicaciones de inicio de sesión inesperadas, la hora del día y las ubicaciones o una combinación de ambas cosas. Esto puede indicar que un hacker ha intentado iniciar sesión con esta cuenta. Puede encontrar más información sobre el informe en la tabla anterior.

###  ¿Quién recibe las notificaciones de correo electrónico?

El correo electrónico se envía a todos los administradores globales a los que se ha asignado una licencia de Active Directory Premium. Para asegurarnos de que se entrega, también lo enviamos a la dirección de correo electrónico alternativa de los administradores. Los administradores deben incluir aad-alerts-noreply@mail.windowsazure.com en su lista de remitentes seguros para no perder el correo electrónico.

###  ¿Con qué frecuencia se envían los correos electrónicos?

Una vez que se envía un correo electrónico, el siguiente se enviará solo cuando se detecten 10 o más eventos de inicio de sesión erróneo en el período de 30 días tras el envío de dicho correo electrónico. ¿Cómo puedo tener acceso al informe mencionado en el correo electrónico?

Al hacer clic en el vínculo, se le redirigirá a la página del informe en el Portal de administración de Azure. Para tener acceso al informe, deberá ser:

- Administrador o coadministrador de su suscripción de Azure
- Administrador global en el directorio y tener asignada una licencia de Active Directory Premium. Para obtener más información, consulte Ediciones de Azure Active Directory.

###  ¿Puedo desactivar los correos electrónicos?

Sí, para desactivar las notificaciones relacionadas con inicios de sesión erróneos dentro del Portal de administración de Azure, haga clic en **Configurar**, y, a continuación, seleccione **Deshabilitadas** en la sección **Notificaciones**.

##  Pasos siguientes

- [Introducción a Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Incorporación de la información de marca de empresa a sus páginas de inicio de sesión y del panel de acceso](active-directory-add-company-branding.md)

<!--HONumber=62-->