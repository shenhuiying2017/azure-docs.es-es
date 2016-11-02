El servicio Azure Backup tiene dos tipos de almacenes: el almacén de Backup y el almacén de Recovery Services. El almacén de Backup fue el primero. A continuación, surgió el almacén de Recovery Services para admitir las implementaciones expandidas de Resource Manager. Microsoft recomienda utilizar las implementaciones de Resource Manager, a menos que se requiera específicamente una implementación mediante el método clásico.

| **Implementación** | **Portal** | **Almacén** |
|-----------|------|-----|
|Clásico|[Clásico](https://manage.windowsazure.com)|Copia de seguridad|
|Resource Manager|[Las tablas de Azure](https://portal.azure.com)|Servicios de recuperación|

> [AZURE.NOTE] Los almacenes de copia de seguridad no pueden proteger soluciones implementadas con Resource Manager. Sin embargo, puede usar un almacén de Servicios de recuperación para proteger los servidores y las máquinas virtuales implementados con el modelo clásico.

<!---HONumber=AcomDC_0921_2016-->