<properties 
   pageTitle="Microsoft Azure AD Connect - Actualización desde la Herramienta de sincronización de Windows Azure AD (DirSync)" 
   description="Aprenda a actualizar desde DirSync a Azure AD Connect. En este artículo se describen los pasos para actualizar la Herramienta de sincronización de Microsoft Azure AD actual (DirSync) a Azure AD Connect." 
   services="active-directory" 
   documentationCenter="" 
   authors="shoatman" 
   manager="terrylanfear" 
   editor="billmath"/>

<tags 
   ms.service="active-directory" 
   ms.workload="identity" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="05/26/2015" 
   ms.author="shoatman"/>

# Actualización de la sincronización de Microsoft Azure Active Directory (DirSync) con Azure Active Directory Connect

La siguiente documentación le ayudará a actualizar la instalación existente de DirSync con Azure AD Connect

## Descarga de Azure AD Connect

Para empezar a utilizar Azure AD Connect puede descargar la versión más reciente mediante [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).


## Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect y actualizar desde DirSync, a continuación se indican algunas cosas que necesitará:

- La contraseña de la cuenta de administrador global existente para la instancia de Azure AD (la instalación le recordará de qué cuenta se trata)
- Una cuenta de administrador de empresa para su Active Directory local
- Opcional: si configuró DirSync para usar una versión completa de SQL Server, la información de esa instancia de base de datos.

### Implementación paralela

Si están sincronizando actualmente más de 50.000 objetos, se le ofrecerá una opción para realizar una implementación paralela. La implementación paralela requiere un servidor independiente o un conjunto de servidores (si necesita un servidor independiente para SQL Server). La ventaja de la implementación paralela es la oportunidad para evitar el tiempo de inactividad de la sincronización. La instalación de Azure AD Connect intentará estimar el tiempo de inactividad que esperamos, pero si ha actualizado DirSync en el pasado, probablemente su propia experiencia sea la mejor referencia.

## Instalación de Azure AD Connect

Descargue Azure AD Connect y cópielo en el servidor DirSync existente.

1. Navegue hasta el archivo AzureADConnect.msi y haga doble clic en él.
2. Empiece a recorrer el asistente.

Para la actualización local se producen los siguientes pasos de alto nivel:

1. Bienvenida a Azure AD Connect
2. Análisis de la configuración actual de DirSync
3. Recopilación de la contraseña de administrador global de Azure AD
4. Recopilación de credenciales para una cuenta de administrador de empresa (solo se usa durante la instalación de Azure AD Connect)
5. Instalación de AAD Connect
    * Desinstalar DirSync
	* Instalar Azure AD Connect
	* Opcionalmente, iniciar la sincronización

Se requiere información y pasos adicionales cuando:

* Actualmente usar SQL Server completo (local o remoto)
* Dispone de más de 50.000 objetos en el ámbito para sincronización

## Actualización local: menos de 50.000 - SQL Express (tutorial)

0. Inicie el instalador de Azure AD Connect (MSI).

1. Revise y acepte los términos de licencia y el aviso de privacidad.

![Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)

2. Haga clic en Siguiente para realizar el análisis de la instalación de DirSync existente.

![Análisis de la instalación de Directory Sync existente](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)

3. Cuando el análisis se complete, le recomendaremos cómo proceder. En este escenario (menos de 50.000 objetos que usan SQL Express) se muestra la pantalla siguiente.

![Análisis completado listo para actualizar desde DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)

4. Proporcione la contraseña para la cuenta que utiliza actualmente para conectarse a Azure AD.

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)

5. Proporcione una cuenta de administrador de empresa para Active Directory.

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)

6. Ahora está preparado para realizar la configuración. Cuando haga clic en Siguiente, DirSync se desinstalará y Azure AD Connect se configurará y se iniciará la sincronización.  

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)


## Actualización local: más de 50.000 objetos
En el paso 3 verá un mensaje diferente si dispone de más de 50.000 objetos en el ámbito de sincronización. Se mostrará una pantalla similar a la siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

En este caso, le recomendamos que considere la posibilidad de una actualización paralela en un servidor independiente. La razón de esta recomendación es que, dependiendo del tamaño de su organización, una actualización local puede afectar a los acuerdos de nivel de servicio con su negocio en lo que respecta a la rapidez con la que los cambios de Active Directory local se reflejan en Azure AD / Office 365. Intentamos calcular cuánto tiempo tardará la primera sincronización usando Azure AD Connect. Como se mencionó anteriormente, su propia experiencia con la instalación original de DirSync o con las actualizaciones a DirSync suele ser el mejor indicador.

La implementación paralela requiere uno o varios servidores independientes (si necesita ejecutar SQL Server en un servidor independiente desde Azure AD Connect). Por ese motivo es completamente razonable considerar una actualización local si se puede programar de forma que se eviten efectos negativos dentro de su organización.

Para proceder con un actualización local, haga clic en la casilla de verificación situada junto al mensaje: "Continuar actualizando DirSync en este equipo".

## Actualización local - SQL Server completo

En el paso 3 verá un mensaje diferente si la instalación de DirSync usa una versión completa local o remota de SQL Server. Se mostrará una pantalla similar a la siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)

Se muestra la información relacionada con el servidor de base de datos existente de SQL Server utilizando por DirSync. Si es necesario, realice los ajustes adecuados. Haga clic en "Siguiente" para continuar con la instalación.

## Implementación paralela: más de 50.000 objetos

En el paso 3, si tiene más de 50.000 objetos, la instalación de Azure AD Connect recomendará una implementación paralela. Vea "Actualización local: más de 50.000 objetos" más arriba para obtener información sobre si debe elegir una implementación local o paralela de Azure AD Connect . Se mostrará una pantalla similar a la siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Si desea continuar con la implementación paralela, será necesario realizar los pasos siguientes:

- Haga clic en el botón "Exportar configuración". Cuando instala Azure AD Connect en un servidor independiente, se importarán estas configuraciones para migrar cualquier configuración desde su DirSync actual a la nueva instalación de Azure AD Connect.

Una vez exportada la configuración correctamente, puede salir del asistente de Azure AD Connect en el servidor DirSync.

### Instalación de Azure AD Connect en un servidor independiente

Cuando instala Azure AD Connect en un nuevo servidor, no encontrará DirSync y supondrá que desea realizar una instalación limpia de Azure AD Connect. En este caso hay un par de pasos especiales:

1. Ejecute el instalador de Azure AD Connect (MSI).
2. Cuando aparezca la pantalla " Azure AD Connect", salga del asistente haciendo clic en la "X" en la esquina superior derecha de las ventanas.
3. Abra un símbolo del sistema.
4. Desde la ubicación de instalación de Azure AD Connect (predeterminada: C:\Archivos de programa\Microsoft Azure Active Directory Connect), ejecute el siguiente comando:
    * AzureADConnect.exe /migrate

Azure AD se conecta y le muestra la interfaz de usuario siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)

5. Seleccione el archivo de configuración exportado desde la instalación de DirSync.
6. Configure las opciones avanzadas, que se incluyen:
    * Una ubicación de instalación personalizada para Azure AD Connect
	* Una instancia existente de SQL Server (predeterminada: Azure AD Connect instala SQL Server 2012 Express)
	* Una cuenta de servicio usada para conectarse a SQL Server (si la base de datos de SQL Server es remota, esta cuenta debe ser una cuenta de servicio de dominio)

Vea esas opciones en la interfaz de usuario siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)

7. Haga clic en Siguiente. 
8. En la página "Listo para configurar" deje la opción "Iniciar el proceso de configuración tan pronto como se complete la configuración" seleccionada.[AZURE.NOTE]Se iniciará la sincronización entre Windows Server Active Directory y Azure Active Directory, pero no se exportará ningún cambio a Azure AD. Solo una herramienta de sincronización puede a exportar activamente los cambios de una vez.
9. Haga clic en "Instalar".

[AZURE.NOTE]Desactive la casilla de sincronización de inicio para asegurarse de que DirSync, que sigue estando instalado y en ejecución, y Azure AD Connect no intentan escribir en Azure AD al mismo tiempo.

### Comprobación de que Azure AD Connect está listo para comenzar la sincronización.

Para determinar si Azure AD Connect está listo o no para asumir el control de DirSync, necesitará abrir Synchronization Service Manager de Azure AD Connect. La búsqueda del término "Sincronización" en el menú Inicio de Windows mostrará esta aplicación.

Dentro de la aplicación se debe ver la ficha "Operaciones". En esta pestaña desea confirmar que se han completado las siguientes operaciones:

- Importación en el Agente de administración de AD
- Importación en el Agente de administración de Azure AD
- Sincronización completa en el Agente de administración de AD
- Sincronización completa en el Agente de administración de Azure AD

Una vez que se hayan completado estas 4 operaciones, estará listo para desinstalar DirSync y habilitar la sincronización de Azure AD Connect.

### Desinstalación de DirSync (servidor antiguo)

- En "Agregar o quitar programas" busque "Herramienta de sincronización de Microsoft Azure Active Directory".
- Desinstale "Herramienta de sincronización de Microsoft Azure Active Directory".

### Apertura de Azure AD Connect (nuevo servidor)
Después de la instalación, al volver a abrir Azure AD Connect disfrutará de una experiencia de configuración. Abra Azure AD Connect.

Verá lo siguiente:

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

* Seleccione "Configurar modo de almacenamiento provisional".
    * La actualización desde DirSync usando la configuración exportada automáticamente pone Azure AD Connect en modo provisional. Modo provisional básicamente significa que la sincronización se producirá dentro de Azure AD Connect, pero los cambios no se exportarán a Azure AD ni a AD.
* Deshabilite el modo provisional desactivando la casilla "Modo provisional habilitado".

![Escriba sus credenciales de Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

* Haga clic en el botón Instalar.

¡Enhorabuena! Ha migrado correctamente a Azure AD Connect mediante la implementación paralela.

## Componentes de soporte de Azure AD Connect

La siguiente es una lista de requisitos previos y componentes de soporte que Azure AD Connect instalará en el servidor en el que configuró Azure AD Connect. Esta lista es para una instalación rápida básica. Si decide usar un servidor SQL Server diferente en la página Instalar servicios de sincronización, los componentes de SQL Server 2012 enumerados a continuación no se instalarán.

- Forefront Identity Manager Azure Active Directory Connector
- Utilidades de línea de comandos de Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Native Client
- Microsoft SQL Server 2012 Express LocalDB
- Módulo de Active Directory para Windows PowerShell
- Microsoft Online Services - Ayudante para el inicio de sesión para profesionales de TI
- Paquete de redistribución de Microsoft Visual C++ 2013


**Recursos adicionales**

* [Uso de la infraestructura de identidad local en la nube](active-directory-aadconnect.md)
* [Cómo funciona Azure AD Connect](active-directory-aadconnect-how-it-works.md)
* [Qué sigue en Azure AD Connect](active-directory-aadconnect-whats-next.md)
* [Más información](active-directory-aadconnect-learn-more.md)
* [Azure AD Connect en MSDN](https://msdn.microsoft.com/library/azure/dn832695.aspx)
 

<!---HONumber=July15_HO4-->