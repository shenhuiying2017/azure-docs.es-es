<properties
   pageTitle="Sincronización de Azure Connect de AD: evitar eliminaciones accidentales | Microsoft Azure"
   description="En este tema se describe la característica para evitar eliminaciones accidentales en Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/16/2015"
   ms.author="andkjell"/>

# Sincronización de Azure AD Connect: cómo evitar eliminaciones accidentales
En este tema se describe la característica para evitar eliminaciones accidentales en Azure AD Connect.

Al instalar Azure AD Connect, la característica para evitar eliminaciones accidentales se habilitará de forma predeterminada y se configurará para que no permita ninguna exportación con más de 500 eliminaciones. Esta característica está diseñada para protegerle de los cambios de configuración accidentales y de los cambios al directorio local que afectarían a un gran número de usuarios.

El valor predeterminado de 500 objetos se puede cambiar con PowerShell mediante `Enable-ADSyncExportDeletionThreshold`. Debe configurar este valor para ajustar el tamaño de su organización. Dado que el programador de sincronización se ejecutará cada tres horas, el valor es el número de eliminaciones que hemos visto en tres horas.

Con esta característica habilitada, si hay demasiadas eliminaciones almacenadas provisionalmente para exportarse a Azure AD, la exportación no continuará y recibirá un mensaje similar al siguiente:

![Hola. El servicio de sincronización de identidades detectó que el número de eliminaciones superó el umbral configurado para fabrikam.com. Se envió un total de 1234 objetos para su eliminación en esta ejecución de sincronización de identidades. Esto cumplía o superaba el valor de umbral de eliminación configurado de 500 objetos. Es necesario que confirme que estas eliminaciones deben procesarse para que podamos continuar. Vea la información sobre la prevención de eliminaciones accidentales para más detalles sobre el error que aparece en este mensaje de correo electrónico.](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

Si no es lo esperado, investigue y tome las medidas correctivas oportunas. Para ver los objetos que se van a eliminar, haga lo siguiente:

1. Inicie el **Servicio de sincronización** desde el menú Inicio.
2. Vaya a **Conectores**.
3. Seleccione el conector con el tipo **Azure Active Directory**.
4. En **Acciones** a la derecha, seleccione **Espacio del conector de búsqueda**.
5. En la ventana emergente en **Ámbito** seleccione **Desconectado desde** y elija una hora en el pasado. Haga clic en **Búsqueda**. Esto le ofrecerá una vista de todos los objetos que se van a eliminar. Al hacer clic en cada elemento, puede obtener información adicional sobre el objeto. También puede hacer clic en **Valor de la columna** para agregar atributos adicionales para que sean visibles en la cuadrícula.

![Espacio del conector de búsqueda](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Si se desean todas las eliminaciones, haga lo siguiente:

1. Para deshabilitar temporalmente esta protección y permitir realizar estas eliminaciones, ejecute el cmdlet de PowerShell: `Disable-ADSyncExportDeletionThreshold` Cuando se le pidan las credenciales, proporcione una cuenta y una contraseña de administrador global de Azure AD. ![Credenciales](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Con el Conector Azure Active Directory aún seleccionado, seleccione la acción **Ejecutar** y **Exportar**.
3. Para volver a habilitar la protección, ejecute el cmdlet de PowerShell: `Enable-ADSyncExportDeletionThreshold`

## Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_1217_2015-->