<properties
	pageTitle="Configuración de filtrado de Sincronización de Azure AD Connect"
	description="Explica cómo configurar el filtrado en Sincronización de Azure AD Connect"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Sincronización de Azure AD Connect: configuración de filtrado

En Sincronización de Azure AD Connect Sync, puede habilitar el filtrado en cualquier momento. Si ya ha ejecutado las configuraciones predeterminadas de sincronización de directorios y, a continuación, ha configurado el filtrado, los objetos que se filtran ya no se sincronizan con Azure AD. Esto provoca que los objetos en Azure AD que se sincronizaron previamente pero que se filtraron después se eliminen en Azure AD. Si los objetos se eliminaron involuntariamente porque se ha producido un error de filtrado, puede volver a crear los objetos en Azure AD quitando las configuraciones de filtrado y, a continuación, sincronizar de nuevo los directorios.

> [AZURE.IMPORTANT]Microsoft no admite la modificación ni el funcionamiento de Sincronización de Azure AD Connect que no se limite a estas acciones documentadas formalmente. Cualquiera de estas acciones puede dar lugar a un estado incoherente o no admitido de Sincronización de Azure AD Connect y, como resultado, Microsoft no ofrece soporte técnico para las implementaciones de este tipo.
 
Con la excepción del filtrado basado en atributos de salida, se conservarán las configuraciones cuando instale o actualice a una versión más reciente de Azure AD Connect. Es siempre una práctica recomendada comprobar que la configuración no se haya cambiado involuntariamente después de una actualización a una nueva versión antes de ejecutar el primer ciclo de sincronización.


## Opciones de filtrado

> [AZURE.NOTE]En este tema, SourceAD se usa como nombre del Conector de servicio de dominio de Active Directory. Si dispone d varios bosques, tendrá un conector por bosque y la configuración debe repetirse para cada bosque.
 
Los tres tipos de configuración de filtrado siguientes pueden aplicarse a la herramienta Sincronización de directorios:

- **Basado en dominio**: puede usar este tipo de filtrado para administrar las propiedades del Conector SourceAD en Sincronización de Azure AD Connect. Este tipo permite seleccionar qué dominios se pueden sincronizar con Azure AD.

- **Configuración de filtrado basado en unidad organizativa**: puede usar este tipo de filtrado para administrar las propiedades del Conector SourceAD en Sincronización de Azure AD Connect. Este tipo de filtrado permite seleccionar qué unidades organizativas se pueden sincronizar con Azure AD.

- **Basado en atributos**: puede usar este método de filtrado para especificar filtros basados en atributos. Esto le permite controlar qué objetos deben sincronizarse con la nube.

## Configuración del filtrado basado en dominios

En esta sección se proporcionan los pasos necesarios para configurar el filtro de dominios.


> [AZURE.NOTE]Si ha modificado el filtro de dominios, también tiene que actualizar los perfiles de ejecución.
 

**Para configurar el filtro de dominio, realice los pasos siguientes:**

1. Inicie sesión en el equipo que ejecuta Sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins**.

2. En **Inicio**, seleccione o haga clic en **Servicio de sincronización** para abrir el **Synchronization Service Manager**. <br>

3. Para abrir la vista de conectores, haga clic en **Conectores** en el menú **Herramientas**.

4. En la lista **Conectores**, seleccione el conector que tenga **Servicios de dominio de Active Directory ** como **Tipo**.

5. Para abrir el cuadro de diálogo **Propiedades**, haga clic en **Propiedades** en el menú **Acciones**.

6. Haga clic en **Configurar particiones de directorio**.

7. En la lista **Seleccionar particiones de directorio**, compruebe que se seleccionan solo las particiones que desea sincronizar. <br>>[AZURE.Note]Para quitar un dominio del proceso de sincronización, desactive la casilla del dominio.

8. Para cerrar el cuadro de diálogo **Propiedades**, haga clic en **Aceptar**.


Si ha actualizado el filtro de dominios, también tiene que actualizar los siguientes perfiles de ejecución:

- Importación completa
- Sincronización completa
- Importación delta
- Sincronización delta
- Exportación


Si ha quitado una partición de la lista de particiones de directorio, tiene que asegurarse de que también se quitan todos los pasos de perfil de ejecución que hacen referencia a esta partición.

**Para quitar un paso de un archivo de ejecución, realice los siguientes pasos:**

1. En la lista **Conectores**, seleccione el conector que tenga **Servicios de dominio de Active Directory ** como **Tipo**.

2. Para abrir el cuadro de diálogo **Configurar perfiles de ejecución para**, haga clic en **Configurar perfiles de ejecución** en el menú **Acciones**.

3. En la lista **Perfiles de ejecución de conector**, seleccione el perfil de ejecución que desee configurar.

4. En cada paso de la lista de información de pasos, realice lo siguiente:

     4\.1. Si es necesario, haga clic en el paso para ampliar la información del paso.

     4\.2. Si el **valor** del atributo **Partition** es una GUID, haga clic en Eliminar paso.

5. Para cerrar el cuadro de diálogo **Configurar perfiles de ejecución para**, haga clic en **Aceptar**.

Si ha agregado una partición a la lista de particiones del directorio, tiene que asegurarse de que hay disponible un paso de perfil de ejecución para esa partición en cada uno de los perfiles de ejecución en la lista anterior.

**Para agregar un paso a un perfil de ejecución, realice los siguientes pasos:**

1. En la lista **Conectores**, seleccione el conector que tenga **Servicios de dominio de Active Directory ** como **Tipo**.

2. Para abrir el cuadro de diálogo **Configurar perfiles de ejecución para**, haga clic en **Configurar perfiles de ejecución** en el menú **Acciones**.

3. En la lista **Perfiles de ejecución de conector**, seleccione el perfil de ejecución que desee configurar.

4. Para abrir el cuadro de diálogo **Configurar perfil de ejecución**, haga clic en **Nuevo paso**.

5. En la página **Paso de configuración**, en la lista de tipo de pasos, seleccione el tipo de paso y, a continuación, haga clic en **Siguiente**.

6. En la página **Configuración de conector**, en la lista **Partición**, seleccione el nombre de la partición que ha agregado al filtro de dominio.

7. Para cerrar el cuadro de diálogo **Configurar perfiles de ejecución**, haga clic en **Finalizar**.

8. Para cerrar el cuadro de diálogo **Configurar perfiles de ejecución para**, haga clic en **Aceptar**.

 

## Configuración del filtrado basado en unidad organizativa

**Para configurar el filtrado basado en unidad organizativa, realice los pasos siguientes:**

1. Inicie sesión en el equipo que ejecuta Sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad **ADSyncAdmins**.

2. En **Inicio**, seleccione o haga clic en **Servicio de sincronización** para abrir el **Synchronization Service Manager**.<br>

3. En **Synchronization Service Manager**, haga clic en **Conectores** y, a continuación, haga doble clic en **SourceAD**.

4. Haga clic en **Configurar particiones de directorio**, seleccione el dominio que desee configurar y, a continuación, haga clic en **Contenedores**.

5. Cuando se le solicite, especifique las credenciales de dominio para el bosque de Active Directory local.<br> >[AZURE.NOTE]Cuando aparezca el cuadro de diálogo de credenciales, aparecerá la cuenta que se usa para la importación y exportación de AD DS. Si no conoce la contraseña para la cuenta, puede especificar otra contraseña que usar. La cuenta que use debe disponer de permisos de lectura para el dominio configurado actualmente.

6. En el cuadro de diálogo **Seleccionar contenedores**, borre las unidades organizativas que no desee sincronizar con el directorio en la nube y, a continuación, haga clic en **Aceptar**.

7. Haga clic en **Aceptar** en la página **Propiedades de SourceAD**.

8. Ejecute una importación completa y una sincronización delta completando los siguientes pasos:

     8\.1. En la lista de conectores, seleccione **SourceAD**.

     8\.2. Para abrir el cuadro de diálogo **Ejecutar conector**, seleccione **Ejecutar** en el menú **Acciones**.

     8\.3. En la lista **Perfiles de ejecución**, seleccione **Importación completa** y, a continuación, espere a que el archivo de ejecución se complete.

     8\.4. Para abrir el cuadro de diálogo **Ejecutar conector**, seleccione **Ejecutar** en el menú **Acciones**.

     8\.5. En la lista **Perfiles de ejecución**, seleccione **Sincronización delta** y, a continuación, espere a que el archivo de ejecución se complete.




## Configuración del filtrado basado en atributos

Existen varias formas de configurar el filtrado basado en atributos. Se recomienda la configuración entrante desde AD porque estos valores de configuración se mantendrán incluso después de actualizar a una versión más reciente. Es compatible la configuración saliente hacia AAD, pero esta configuración no se guardará después de una actualización a una versión más reciente y solo debe usarse cuando sea necesario para observar el objeto combinado en el metaverso para determinar el filtrado.

### Filtrado entrante

El filtrado entrante aprovecha la configuración predeterminada en la que los objetos que se dirigen a AAD no tengan el atributo de metaverso cloudFiltered establecido en un valor y el atributo de metaverso sourceObjectType se encuentre establecido en “Usuario” o “Contacto”.

El atributo cloudFiltered debe establecerse en True cuando el objeto no debe sincronizarse con Azure AD y mantenerse vacío en los demás casos. Este método se usa cuando podemos observar un objeto y decir que no queremos sincronizarlo (filtrado negativo).

En el siguiente ejemplo filtraremos todos los usuarios en los que extensionAttribute15 tenga el valor NoSync:

1. Inicie sesión en el equipo que ejecuta Sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad ADSyncAdmins.
2. Abra el **Editor de reglas de sincronización** del menú **Inicio**.
3. Asegúrese de que **Entrante** esté seleccionado y haga clic en **Agregar nueva regla**.
4. Asigne un nombre descriptivo a una regla, como "*In from AD – User DoNotSyncFilter*", seleccione el bosque correcto, **Usuario** como **Tipo de objeto CS**, y **Persona** como **Tipo de objeto MV**. Como **Tipo de vínculo**, seleccione **Unirse** y en el tipo de precedencia un valor que no haya utilizado actualmente otra regla de sincronización (por ejemplo: 50) y, a continuación, haga clic en **Siguiente**.
5. En **Filtro de ámbito**, haga clic en **Agregar grupo**, luego en **Agregar cláusula** y en Atributo, seleccione **ExtensionAttribute15**. Asegúrese de que el operador esté configurado en **EQUAL** y **escriba** el valor NoSync en el cuadro Valor. Haga clic en **Next**.
6. Deje las reglas de **unión** vacías y, a continuación, haga clic en **Siguiente**.
7. Haga clic en **Agregar transformación**, seleccione **FlowType** en **Constante**, seleccione el atributo de destino cloudFiltered y en el cuadro de texto de origen, escriba True. Haga clic en Agregar para guardar la regla.
8. Realice una sincronización completa: en la pestaña **Conectores**, haga clic con el botón secundario en **SourceAD**, haga clic en **Ejecutar**, en **Sincronización completa** y en **Aceptar**. 


El atributo **sourceObjectType** realizará el aprovisionamiento de un **Usuario** o **Contacto** en Azure AD si este atributo tiene el valor **Usuario** o **Contacto** respectivamente. Al crear una regla de sincronización con una precedencia superior que las listas para su aplicación, puede sobrescribir el comportamiento predeterminado. Este método también le ofrece una oportunidad de expresar las reglas positivas y negativas.

En el ejemplo siguiente solo se sincronizarán usuarios cuando el atributo del departamento sea “*Sales*” o esté vacío.

1. Inicie sesión en el equipo que ejecuta Sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad ADSyncAdmins.
2. Abra el **Editor de reglas de sincronización** del menú **Inicio**.
3. Asegúrese de que **Entrante** esté seleccionado y, a continuación, haga clic en **Agregar nueva regla**.
4. Asigne un nombre descriptivo a una regla (por ejemplo: "*In from AD – User DoNotSyncFilter*"), seleccione el bosque correcto, **Usuario** como **Tipo de objeto CS**, y **Persona** como **Tipo de objeto MV**. Como **Tipo de vínculo**, seleccione **Unirse** y en el **tipo de precedencia** un valor que no haya utilizado actualmente otra regla de sincronización (por ejemplo: 60). Haga clic en **Siguiente**.
5. Deje el **filtro de ámbito** y las **reglas de unión** vacías y haga clic en **Siguiente** dos veces.
6. Haga clic en **Agregar transformación**, establezca **FlowType** en **Expresión** y **Atributo de destino** en **sourceObjectType**. En **Origen**, escriba la siguiente expresión:<br>`IIF(IsNullOrEmpty([department]),NULL,IIF([department]<>”Sales”,”DoNotSync”,NULL))`
7. Haga clic en Agregar para guardar la regla.
8. Realice una sincronización completa: en la pestaña **Conectores**, haga clic con el botón secundario en **SourceAD**, haga clic en **Ejecutar**, en **Sincronización completa** y en **Aceptar**. A continuación se muestra la apariencia que debería tener:<br>

> [AZURE.NOTE]Tenga en cuenta que estamos usando una mezcla de cloudFiltered y sourceObjectType para determinar qué objetos queremos sincronizar con AAD.
 
Con el uso de expresiones dispone de opciones de filtrado muy eficaz. En la expresión anterior, tenga en cuenta que proporcionamos el valor NULL literal cuando el departamento no está presente y cuando el departamento era Sales. Esto indica que este atributo no colaboró con un valor y que se evaluarán las reglas listas para su aplicación. Es el procedimiento deseado para que puedan determinar si debemos crear un **Usuario** o **Contacto** en Azure AD.


## Filtrado saliente

En algunos casos es necesario realizar el filtrado solo después de que los objetos se hayan unido en metaverso. Por ejemplo, podría ser necesario observar el atributo mail del bosque de recursos y el atributo userPrincipalName del bosque de cuentas para determinar si debe sincronizarse un objeto. En estos casos, crearemos el filtrado en la regla de salida.

> [AZURE.NOTE]Este método requiere un cambio en las reglas de sincronización listas para su aplicación. El cambio del ámbito de una regla de sincronización es compatible, pero el cambio puede no conservarse después de actualizar a una versión más reciente de Azure AD Connect. Si usa el filtrado saliente, anote los cambios que realizar y, después de la actualización, asegúrese de que se conserva el filtrado y vuelva a aplicarlo si fuese necesario.
 

En este ejemplo, cambiaremos el filtrado de manera que se sincronicen solo los usuarios en los que mail y userPrincipalName terminen en @contoso.com:

1. Inicie sesión en el equipo que ejecuta Sincronización de Azure AD Connect con una cuenta que sea miembro del grupo de seguridad ADSyncAdmins.
2. Abra el Editor de reglas de sincronización y búsquelo en el menú Inicio.
3. En Tipos de reglas, haga clic en Salida.
4. Busque la regla con el nombre Out to AAD – User Join. Haga clic en Editar.
5. Haga clic en el filtro de ámbito en el panel de navegación izquierdo. Haga clic en Agregar cláusula y, en Atributo, seleccione mail, en Operador, seleccione ENDSWITH y en Value, escriba @contoso.com. Haga clic en Agregar cláusula y, en Atributo, seleccione userPrincipalName, en Operador, seleccione ENDSWITH y en Value, escriba @contoso.com.
6. Haga clic en Guardar.
7. Realice una sincronización completa: en la pestaña Conectores, haga clic con el botón secundario en SourceAD, haga clic en Ejecutar, en Sincronización completa y en Aceptar.



## Recursos adicionales

* [Sincronización de Azure AD Connect: personalización de las opciones de sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

 

<!---HONumber=July15_HO5-->