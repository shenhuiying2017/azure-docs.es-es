<properties
	pageTitle="Operaciones de Azure AD Connect Health."
	description="Este artículo describe las operaciones adicionales que pueden realizarse una vez que haya implementado Azure AD Connect Health."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>

# Operaciones de Azure AD Connect Health

El tema siguiente describe las distintas operaciones que se pueden realizar con Azure AD Connect Health.

## Habilitación de notificaciones de correo electrónico
Puede configurar el Servicio de Azure AD Connect Health para enviar notificaciones por correo electrónico cuando se generen alertas que indiquen que el estado de la infraestructura de identidad no es correcto. Esto ocurrirá cuando se genere una alerta y también cuando se marque como resuelta. Siga las instrucciones que aparecen a continuación para configurar las notificaciones de correo electrónico. Tenga en cuenta que las notificaciones de correo electrónico están deshabilitadas de manera predeterminada.


### Para habilitar las notificaciones de correo electrónico de Azure AD Connect Health

1. Abra la hoja Alertas del servicio para el que desea recibir una notificación de correo electrónico.
2. Haga clic en el botón "Configuración de notificaciones" de la barra de acciones.
3. Ponga el modificador de la opción Notificación de correo electrónico en la posición ON (Activado).
4. Active la casilla para establecer que todos los administradores globales reciban notificaciones de correo electrónico.
5. Si desea recibir notificaciones de correo electrónico en otras direcciones de correo electrónico, puede especificarlas en el cuadro Destinatario de correo electrónico adicional. Para quitar una dirección de correo electrónico de esta lista, haga clic con el botón derecho en la entrada y seleccione Eliminar.
6. Para finalizar los cambios, haga clic en "Guardar". Los cambios entrarán en vigor solo después de seleccionar "Guardar".






## Eliminación de un servidor del Servicio de Azure AD Connect Health

En algunos casos, es posible que desee dejar de supervisar un servidor. Siga las instrucciones siguientes para quitar un servidor del Servicio de Azure AD Connect Health.

Cuando elimine un servidor, tenga en cuenta lo siguiente:

- Esta acción DETENDRÁ cualquier recopilación de datos de ese servidor. Este servidor se quitará del servicio de supervisión. Después de esta acción, no podrá ver nuevas alertas ni datos de análisis de uso o supervisión de este servidor.
- Esta acción NO desinstalará ni quitará el agente de mantenimiento del servidor. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en el servidor relacionados con el agente de mantenimiento.
- Esta acción NO eliminará los datos que ya se recopilaron desde este servidor. Los datos se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure.
- Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo.


### Para eliminar un servidor del Servicio de Azure AD Connect Health

1. Seleccione el nombre del servidor que se va a quitar para abrir la hoja Servidor en la hoja Lista de servidores.
2. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirme la acción para eliminar el servidor; para ello, escriba el nombre del servidor en el cuadro de confirmación.
4. Haga clic en el botón "Eliminar".







## Eliminación de una instancia de servicio del Servicio de Azure AD Connect Health

En algunos casos, es posible que desee quitar una instancia de servicio. Siga las instrucciones siguientes para quitar una instancia de servicio del Servicio de Azure AD Connect Health.

Cuando elimine una instancia de servicio, tenga en cuenta lo siguiente:

- Esta acción quitará la instancia de servicio actual del servicio de supervisión.
- Esta acción NO desinstalará ni quitará el agente de mantenimiento de ninguno de los servidores que se supervisaron como parte de esta instancia de servicio. Si no ha desinstalado el agente de mantenimiento antes de realizar este paso, es posible que vea eventos de error en los servidores relacionados con el agente de mantenimiento.
- Todos los datos de esta instancia de servicio se eliminarán según lo estipulado en la Directiva de retención de datos de Microsoft Azure.
- Después de realizar esta acción, si desea comenzar a supervisar el servicio, desinstale el agente de mantenimiento en todos los servidores que se supervisarán y vuelva a instalarlo. Después de realizar esta acción, si desea comenzar a supervisar nuevamente el mismo servidor, deberá desinstalar el agente de mantenimiento en este servidor y volver a instalarlo.


### Para eliminar una instancia de servicio del Servicio de Azure AD Connect Health

1. Seleccione el identificador del servicio (nombre de la granja) que desea quitar para abrir la Hoja Servicio en la hoja Lista de Servicios.
2. En la hoja Servidor, haga clic en el botón "Eliminar" de la barra de acciones.
3. Confirme el nombre del servicio; para ello, escríbalo en el cuadro de confirmación (por ejemplo: sts.contoso.com).
4. Haga clic en el botón "Eliminar".

## Habilitación de la auditoría de AD FS

Para que la característica Análisis de uso pueda recopilar datos y analizarlos, el Agente de Azure AD Connect Health necesita la información de los registros de auditoría de AD FS, que no están habilitados de forma predeterminada. Esto solo se aplica a los servidores de federación de AD FS. No es necesario habilitar la auditoría en los servidores proxy de AD FS o de aplicación web. Use los procedimientos siguientes para habilitar la auditoría de AD FS y localizar los registros de auditoría de AD FS obtenidos.

#### Para habilitar la auditoría de AD FS 2.0

1. Haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en **Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Administración de derechos de usuario** y haga doble clic en Generar auditorías de seguridad.
3. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS 2.0. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Cierre Directiva de seguridad local y luego abra el complemento de administración. Para abrir el complemento de administración, haga clic en **Inicio**, seleccione **Programas**, **Herramientas administrativas** y luego haga clic en Administración de AD FS 2.0.
6. En el panel Acciones, haga clic en Editar propiedades del servicio de federación.
7. En el cuadro de diálogo **Propiedades del servicio de federación**, haga clic en la pestaña **Eventos**.
8. Active las casillas **Auditorías de aciertos** y **Auditorías de errores**.
9. Haga clic en **Aceptar**.

#### Para habilitar la auditoría de AD FS en Windows Server 2012 R2

1. Abra **Directiva de seguridad local**; para ello, abra **Administrador del servidor** en la pantalla Inicio o Administrador del servidor en la barra de tareas del escritorio y luego haga clic en **Herramientas/Directiva de seguridad local**.
2. Navegue hasta la carpeta **Configuración de seguridad\\Directivas locales\\Asignación de derechos de usuario** y haga doble clic en **Generar auditorías de seguridad**.
3. En la pestaña **Configuración de seguridad local**, compruebe que aparezca la cuenta de servicio de AD FS. Si no aparece, haga clic en **Agregar usuario o grupo**, agréguela a la lista y luego haga clic en **Aceptar**.
4. Abra un símbolo del sistema con privilegios elevados y ejecute el siguiente comando para habilitar la auditoría.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Cierre **Directiva de seguridad local** y luego abra el complemento **Administración de AD FS** (en Administrador del servidor, haga clic en Herramientas y luego seleccione Administración de AD FS).
6. En el panel Acciones, haga clic en **Editar propiedades del servicio de federación**.
7. En el cuadro de diálogo Propiedades del servicio de federación, haga clic en la pestaña **Eventos**.
8. Active las casillas **Autorías de aciertos y Auditorías de errores** y luego haga clic en **Aceptar**.






#### Para buscar los registros de auditoría de AD FS


1. Abra **Visor de eventos**.
2. Vaya a Registros de Windows y seleccione **Seguridad**.
3. A la derecha, haga clic en **Filtrar registros actuales**.
4. En Origen de evento, seleccione **Auditoría de AD FS**.

![Registros de auditoría de AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Si tiene una directiva de grupo que deshabilita la auditoría de AD FS, el agente de Azure AD Connect Health no podrá recopilar información. Asegúrese de no tener ninguna directiva de grupo de este tipo.


## Vínculos relacionados

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalación del agente de Azure AD Connect Health para AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Uso de Azure AD Connect Health con AD FS](active-directory-aadconnect-health-adfs.md)
* [Preguntas más frecuentes de Azure AD Connect Health](active-directory-aadconnect-health-faq.md)

<!---HONumber=Oct15_HO2-->