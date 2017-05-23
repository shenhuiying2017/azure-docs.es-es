1. Copie el instalador en una carpeta local (por ejemplo, /tmp) del servidor que desea proteger. En un terminal, ejecute los siguientes comandos:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar Mobility Service, ejecute el siguiente comando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Una vez completada la instalación, Mobility Service debe ser registrado en el servidor de configuración. Ejecute el siguiente comando para registrar Mobility Service en el servidor de configuración.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Línea de comandos del instalador de Mobility Service

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parámetro|Tipo|Descripción|Valores posibles|
|-|-|-|-|
|-r |Obligatorio|Especifica si se debe instalar Mobility Service (MS) o debe instalarse MasterTarget(MT)|MS </br> MT|
|-d |Opcional|Ubicación en que se instalará Mobility Service|/usr/local/ASR|
|-v|Obligatorio|Especifica la plataforma en la que se instala Mobility Service </br> </br>- **VMware**: utilice este valor si va a instalar Mobility Service en una máquina virtual que se ejecuta en *ESXi Hosts de VMware vSphere*, *Hosts de Hyper-V* y *servidores físicos* </br> - **Azure**: utilice este valor si va a instalar el agente en una máquina virtual de IaaS de Azure| VMware </br> Las tablas de Azure|
|-q|Opcional|Especifica que se ejecute el programa de instalación en modo silencioso| N/D|


#### <a name="mobility-service-configuration-command-line"></a>Línea de comandos de configuración de Mobility Service

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parámetro|Tipo|Descripción|Valores posibles|
|-|-|-|-|
|-i |Obligatorio|Dirección IP del servidor de configuración|Cualquier dirección IP válida|
|-P |Obligatorio|Ruta de acceso completa del archivo donde se guarda la frase de contraseña de conexión|Cualquier carpeta válida|
