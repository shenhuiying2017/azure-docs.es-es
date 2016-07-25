



En función del entorno y las opciones, el script puede crear toda la infraestructura del clúster, como la red virtual de Azure, las cuentas de almacenamiento, los servicios en la nube, los controladores de dominio, las bases de datos SQL locales o remotas, el nodo principal y los nodos de clúster adicionales. Como alternativa, el script puede usar la infraestructura de Azure ya existente y crear solo los nodos del clúster HPC.


Para obtener información general acerca de cómo diseñar un clúster de HPC Pack, consulte el contenido de [Product Evaluation and Planning](https://technet.microsoft.com/library/jj899596.aspx) (Planeación y evaluación del producto) y de [Getting Started](https://technet.microsoft.com/library/jj899590.aspx) (Introducción) en la Biblioteca de TechNet de HPC Pack.



## Requisitos previos

* **Suscripción de Azure**: puede usar una suscripción en el servicio Azure Global o Azure China. Los límites de su suscripción afectarán al número y al tipo de nodos de clúster que puede implementar. Para obtener información, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../articles/azure-subscription-service-limits.md).


* **Equipo cliente de Windows con Azure PowerShell 0.8.7 o posterior instalado y configurado**: consulte [Cómo instalar y configurar Azure PowerShell](../articles/powershell-install-configure.md) para ver instrucciones de instalación y pasos para conectarse a su suscripción de Azure.


* **Script de implementación de IaaS de HPC Pac **: descargue y desempaquete la versión más reciente del script desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Compruebe la versión del script ejecutando `New-HPCIaaSCluster.ps1 –Version`. Este artículo se basa en la versión 4.4.1 del script.

* **Archivo de configuración del script**: necesitará crear un archivo XML que el script usará para configurar el clúster de HPC. Para más información, junto con ejemplos, vea las secciones más adelante en este artículo y el archivo Manual.rtf que acompaña al script de implementación.


## Sintaxis

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]Debe ejecutar el script como administrador.

### Parámetros

* **ConfigFile**: especifica la ruta de acceso del archivo de configuración para describir el clúster de HPC. Puede leer más sobre el archivo de configuración en este tema, o en el archivo Manual.rtf de la carpeta que contiene el script.

* **AdminUserName**: especifica el nombre de usuario. Si el script crea el bosque de dominio, esto se convierte en el nombre de usuario del administrador local para todas las máquinas virtuales, así como el nombre del administrador de dominio. Si ya existe un bosque de dominio, esto especifica el usuario de dominio como nombre de usuario del administrador local para instalar HPC Pack.

* **AdminPassword**: especifica la contraseña del administrador. Si no se especifica en la línea de comandos, el script le solicitará que escriba la contraseña.

* **HPCImageName** (opcional): especifica el nombre de imagen de la máquina virtual de HPC Pack que se usa para implementar el clúster de HPC. Debe ser una imagen de HPC Pack proporcionada por Microsoft desde Azure Marketplace. Si no se especifica (recomendado en la mayoría de los casos), el script elige la [imagen de HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) publicada más reciente. La última imagen se basa en Windows Server 2012 R2 Datacenter con HPC Pack 2012 R2 Update 3 instalado.

    >[AZURE.NOTE] Se producirá un error en la implementación si no se indica una imagen de HPC Pack válida.

* **LogFile** (opcional): especifica la ruta de acceso del archivo de registro de implementación. Si no se especifica, el script creará un archivo de registro en el directorio temporal del equipo que ejecuta el script.

* **Force** (opcional): suprime todos los mensajes de confirmación.

* **NoCleanOnFailure** (opcional): especifica que no se quitarán las máquinas virtuales de Azure que no se implementaron correctamente. Debe quitar manualmente estas máquinas virtuales antes de volver a ejecutar el script para seguir con la implementación o puede producirse un error en la implementación.

* **PSSessionSkipCACheck** (opcional): para cada servicio en la nube con máquinas virtuales implementadas mediante este script, Azure genera automáticamente un certificado autofirmado y todas las máquinas virtuales del servicio en la nube lo usan como certificado predeterminado de Administración remota de Windows (WinRM). Para implementar características de HPC en estas máquinas virtuales de Azure, el script predeterminado instala temporalmente estos certificados en el equipo local\\almacén de entidades de certificación raíz de confianza del equipo cliente para suprimir el error de seguridad de "la entidad de certificación no es de confianza" durante la ejecución del script; los certificados se eliminan al finalizar el script. Si se especifica este parámetro, los certificados no se instalan en el equipo cliente y se suprime la advertencia de seguridad.

    >[AZURE.IMPORTANT] No se recomienda el uso de este parámetro para implementaciones de producción.

### Ejemplo

En el ejemplo siguiente se crea un nuevo clúster de HPC Pack mediante el archivo de configuración *MyConfigFile.xml* y se especifican las credenciales administrativas para instalar el clúster.

```
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### Consideraciones adicionales



* El script puede, opcionalmente, habilitar el envío de trabajos mediante el portal web de HPC Pack o la API de REST de HPC Pack.

* El script puede, opcionalmente, ejecutar scripts personalizados de configuración previa y posterior en el nodo principal si desea instalar software adicional o configurar otras opciones.


## Archivo de configuración

El archivo de configuración para el script de implementación es un archivo XML. El archivo de esquema HPCIaaSClusterConfig.xsd está en la carpeta de scripts de implementación de HPC Pack IaaS. **IaaSClusterConfig** es el elemento raíz del archivo de configuración que contiene los elementos secundarios descritos en detalle en el archivo Manual.rtf de la carpeta de scripts de implementación.

<!---HONumber=AcomDC_0713_2016-->