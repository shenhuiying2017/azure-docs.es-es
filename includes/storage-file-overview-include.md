## <a name="overview"></a>Información general
Almacenamiento de archivos de Azure es un servicio que ofrece recursos compartidos de archivos en la nube mediante el [protocolo Bloque de mensajes del servidor (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)estándar. Se admiten SMB 2.1 y SMB 3.0. Con Almacenamiento de archivos de Azure puede migrar aplicaciones heredadas basadas en recursos compartidos de archivos a Azure con rapidez y sin necesidad de costosas reescrituras. Las aplicaciones que se ejecutan en máquinas virtuales de Azure o en servicios en la nube o desde clientes locales pueden montar un recurso compartido de archivos en la nube, igual que una aplicación de escritorio monta un recurso compartido SMB típico. Cualquier número de componentes de aplicación puede montar y acceder simultáneamente al recurso compartido de Almacenamiento de archivos.

Puesto que un recurso compartido de almacenamiento de archivos es un recurso compartido de archivos SMB estándar, las aplicaciones que se ejecutan en Azure pueden obtener acceso a los datos del recurso compartido a través de las API de E/S del sistema de archivos. Por tanto, los desarrolladores pueden aprovechar el código y los conocimientos que ya tienen para migrar las aplicaciones actuales. Los profesionales de TI pueden usar cmdlets de PowerShell para crear, montar y administrar recursos compartidos de Almacenamiento de archivos como parte de la administración de aplicaciones de Azure.

Puede crear recursos compartidos de archivos de Azure mediante el [Portal de Azure](https://portal.azure.com), los cmdlets de PowerShell de Almacenamiento de Azure, las bibliotecas de cliente de Almacenamiento de Azure o la API de REST de Almacenamiento de Azure. Además, dado que esos recursos compartidos de archivos son recursos compartidos del protocolo SMB, puede tener acceso a ellos a través de la API del sistema de archivos estándar y familiar.



<!--HONumber=Jan17_HO3-->


