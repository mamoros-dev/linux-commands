# Administrador de Sistemas Linux 

## Instalación de Linux

### Instalación de Ubuntu

1. Descargar la imagen de Ubuntu desde [Ubuntu](https://ubuntu.com/download/desktop)
2. Crear una imagen de disco duro con la imagen de Ubuntu
3. Iniciar la imagen de disco duro
4. Instalar Ubuntu
5. Instalar el sistema de gestión de paquetes `apt` en Ubuntu

### Instalación de Fedora

1. Descargar la imagen de Fedora desde [Fedora](https://getfedora.org/en/workstation/download/)
2. Crear una imagen de disco duro con la imagen de Fedora
3. Iniciar la imagen de disco duro
4. Instalar Fedora
5. Instalar el sistema de gestión de paquetes `dnf` en Fedora

### Instalación de Arch Linux

1. Descargar la imagen de Arch Linux desde [Arch Linux](https://www.archlinux.org/download/)
2. Crear una imagen de disco duro con la imagen de Arch Linux
3. Iniciar la imagen de disco duro
4. Instalar Arch Linux
5. Instalar el sistema de gestión de paquetes `pacman` en Arch Linux

## Configuración de la terminal en Ubuntu, Fedora, Linux, Windows y MacOS

1. Abrir la terminal
2. Instalar `zsh`
3. Instalar `oh-my-zsh`
4. Configurar `zsh`
5. Configurar `oh-my-zsh`

## Configuración de las herramientas de desarrollo

1. Abrir la terminal
2. Instalar `git`
3. Instalar `vim`
4. Configurar `git`
5. Configurar `vim`

## 📑 Chuleta Completa: Categorías Esenciales SysAdmin N2 / DevOps

+ **0. Gestión de paquetes en Linux (`APT / DNF`)**:
    - **Actualizar el índice de repositorios** (¡Hazlo siempre antes de instalar nada!)
        + Debian/Ubuntu: `sudo apt update`
        + RHEL/CentOS: `sudo dnf check-update` (o `sudo dnf update` directo)
    - **Actualizar el sistema operativo completo**
        + Debian/Ubuntu: `sudo apt upgrade -y` (Actualiza paquetes sin romper dependencias complejas) / `sudo apt full-upgrade` (Permite desinstalar paquetes si es necesario para resolver conflictos de dependencias).
        + RHEL/CentOS: `sudo dnf upgrade -y`
    - **Instalar un paquete**
        + Ambos: `sudo apt install -y <paquete>` / `sudo dnf install <paquete>`
    - **Buscar un paquete en los repositorios**
        + Debian/Ubuntu: `apt search <termino>`
        + RHEL/CentOS: `dnf search <termino>`
    - **Desinstalar y limpiar de forma limpia**
        + Desinstalación normal: `sudo apt remove <paquete>` (Borra el binario pero deja los archivos de configuración por si acaso).
        + Desinstalación total (Purga): `sudo apt purge <paquete>` (Borra el paquete y sus ficheros de configuración. ¡Muy recomendado para dejar el servidor limpio!).
        + Limpieza de huérfanos: `sudo apt autoremove` (Elimina dependencias y librerías que se instalaron automáticamente para un programa pero que ya no usa ningún software en el sistema. ¡Libera gigabytes de basura!).
    - **¿Qué paquete instaló este archivo o de qué depende?**
        + Saber qué paquete proporciona un comando o archivo: `dpkg -S /usr/bin/nginx` (Debian) o `dnf provides /usr/bin/nginx` (RHEL).
        + Ver dependencias de un paquete antes de instalarlo: `apt-cache depends <paquete>`.
    - **Búsqueda de rutas**: Herramientas linux para localizar archivos y binarios.
        + `which <comando>`: Te dice la ruta exacta del binario que se ejecutará en tu terminal basándose en tu variable de entorno $PATH (ej. `which systemctl -> /usr/bin/systemctl`). Es el más usado en el día a día.
        + `whereis <comando>`: Busca no solo el binario, sino también el código fuente y las páginas del manual (man pages).
        + `locate <archivo>`: Busca en una base de datos interna indexada de todo el disco duro. Es instantáneo, pero si un archivo se acaba de crear hace 5 minutos, puede que no aparezca hasta que se actualice la base de datos (`sudo updatedb`).

+ **1. Discos, Almacenamiento y Archivos (`df, du, ncdu, lsof, find`):**
    - `df -h`: Muestra el espacio disponible en los sistemas de archivos en formato legible (humano).
    - `df -i`: Revisa el consumo de inodos (muy útil cuando el disco marca espacio pero el sistema operativo dice que no puede crear archivos).
    - `du -sh /ruta/*`: Mide de forma rápida cuánto ocupa cada carpeta en un directorio específico.
    - `ncdu`: Analizador visual interactivo de espacio en disco (navegable por terminal, salvavidas en emergencias).
    - `lsof +L1`: Lista archivos abiertos que han sido borrados de la estructura de directorios pero siguen ocupando espacio porque un proceso los mantiene abiertos.
    - `find /ruta -type f -mtime +30 -name "*.log"`: Busca archivos modificados hace más de 30 días (ideal para políticas de retención de logs).
    - `vmstat`: Es una herramienta fantástica y más ligera para scripts o diagnósticos rápidos de rendimiento general. Te muestra estadísticas de procesos, memoria, paginación, bloques de E/S (I/O blocks), interrupciones y uso de CPU en una sola línea por segundo (ej. ejecutando vmstat 2 5 para verlo cada 2 segundos, 5 veces). Te dice si el servidor está sufriendo por falta de RAM física y tirando de disco (swap).

+ **2. Procesos y Rendimiento (`top, htop, ps, kill, systemctl, journalctl`):**
    - `top / htop` o `uptime`: Monitor de procesos en tiempo real (CPU, Memoria, Carga del sistema).
    - `ps`: Muestra únicamente los procesos asociados a tu sesión actual de terminal (TTY).
    - `ps aux`: Muestra todos los procesos en ejecución en el sistema, sin importar qué usuario los ejecute (a), detallando el usuario propietario (u), y abarcando procesos que no están asociados a una terminal (x, como los demonios del sistema o servicios web)
    - `ps aux --sort=-%mem (o -%cpu) (| head/tail -n5)`: Lista todos los procesos ordenados por consumo de memoria o CPU de mayor a menor.
    - `kill -15 <PID> (SIGTERM)`: Cierre ordenado y elegante de un proceso.
    - `kill -9 <PID> (SIGKILL)`: Matanza forzosa inmediata (último recurso).
    - `systemctl status <servicio> / journalctl -u <servicio> -e`: El tándem perfecto para ver el estado de un servicio del sistema y sus logs recientes.

+ **3. Permisos y Seguridad (`chmod, chown, id, getfacl, setfacl`)**
    - `ls -l file/dir`: ver permisos y propietarios de un fichero/directorio
    - `stat file/dir`: ver permisos y propietarios de un fichero/directorio
    - `chmod 755 <archivo> / chmod -R`: Modifica permisos de lectura, escritura y ejecución (octal o simbólico).
    - `chown usuario:grupo <archivo>`: Cambia el propietario y grupo de un archivo o directorio.
    - `id <usuario>`: Muestra el UID, GID y los grupos secundarios a los que pertenece un usuario.
    - `getfacl <archivo> / setfacl`: Permite gestionar Listas de Control de Acceso (ACLs) para dar permisos granulares más allá del trinomio clásico (usuario/grupo/otros).
        + Las Listas de Control de Acceso permiten añadir permisos específicos a usuarios o grupos adicionales sobre un archivo o directorio sin alterar el propietario ni el grupo principal.
        ```bash
        # Ver las ACLs de un archivo/directorio:
        getfacl /etc/app/config.json

        # Asignar permisos a un usuario específico (ej. dar lectura/escritura a miguel)
        setfacl -m u:miguel:rw /etc/app/config.json

        # Asignar permisos por defecto de forma recursiva en una carpeta (para que los nuevos archivos hereden la regla): m=modify/add u=user d=default/heredable -R=recursivo
        setfacl -R -m d:u:miguel:rwx /var/www/html/logs/

        # Eliminar una ACL específica:
        setfacl -x u:miguel /etc/app/config.json
        ```

+ **4. Redes y Conectividad (`ss, curl, nslookup, traceroute, ip`)**
    - `ss -tulpn`: Reemplazo moderno y rápido de netstat. Muestra los puertos TCP/UDP en escucha y qué proceso los está usando.
        + `-t` (TCP): Muestra únicamente conexiones TCP.
        + `-u` (UDP): Muestra conexiones UDP.
        + `-l` (Listening): Muestra los puertos que están escuchando a la espera de conexiones entrantes.
        + `-n` (Numeric): No resuelve nombres de dominio ni puertos a texto (ej. muestra 5432 en vez de postgresql), lo que hace que el comando sea mucho más rápido.
        + `-p` (Process): Muestra qué proceso o PID está usando ese puerto (requiere privilegios de root/sudo).
        + Ejemplo estrella: `ss -tulpn` (TCP + UDP + Listen + Numeric + Process + Numeric ports).
    - `ip a / ip route`: Muestra interfaces de red, direcciones IP y la tabla de enrutamiento activa.
        + `ip a (o ip address)`: Muestra todas las interfaces de red físicas y virtuales (eth0, lo, contenedores veth...), sus direcciones IP (IPv4 e IPv6) y el estado (UP / DOWN).
        + `ip r (o ip route)`: Muestra la tabla de enrutamiento. Te dice por dónde sale el tráfico hacia internet (default via 192.168.1.1 dev eth0).
    - `curl -Iv [https://api.tuempresa.com](https://api.tuempresa.com)`: Realiza peticiones HTTP de prueba mostrando la traza de cabeceras (ideal para validar SSL, códigos 4xx/5xx y latencia).
        + `-I` (Head): Pide únicamente las cabeceras HTTP (útil para ver códigos de estado 200, 404, 500 o caducidad de certificados SSL sin descargarte toda la página).
        + `-v` (Verbose): Modo detallado. Muestra todo el intercambio de paquetes TCP, TLS/SSL y cabeceras de ida y vuelta (vital para debuggear APIs).
        + `-s` (Silent): Silencioso. Oculta la barra de progreso y errores visuales, ideal para scripts.
        + `-X` (Request): Permite especificar el método HTTP (-X POST, -X PUT, -X DELETE).
    - `nslookup <dominio> o dig <dominio>`: Diagnóstico de resolución DNS.
        + `dig @8.8.8.8 tuweb.com`: Permite consultar a un servidor DNS específico (en este caso Google) saltándote la configuración local de tu máquina.
        + `dig tuweb.com +short`: Devuelve únicamente la dirección IP limpia, sin texto adicional, ideal para automatizar en scripts de Bash.
    - `traceroute <ip> o mtr <ip>` `ping url`: Rastrea los saltos de red hasta un destino para identificar dónde se corta el tráfico.
        + `ping -c 4` (-c de count): Envía exactamente 4 paquetes y se detiene (por defecto en Linux hace ping infinito hasta que pulses Ctrl+C).
        + `traceroute -n`: Muestra las IPs directamente en lugar de intentar resolver los nombres DNS de los routers intermedios, acelerando el diagnóstico.
    - `nc o netcat`: La navaja suiza de redes. `nc -zv <ip> <puerto>` sirve para probar de forma limpia si un puerto TCP está abierto y accesible al otro lado.
        + `-z` (Zero-I/O mode): Se usa para escaneo. Le dice a netcat que no envíe datos reales, sino que solo compruebe si la conexión se establece.
        + `-v` (Verbose): Muestra por pantalla si la conexión tuvo éxito o falló (Connection refused / Succeeded).
        + Ejemplo estrella: `nc -zv 192.168.1.50 80`
    - `telnet <ip> <puerto>`: DEPRECADO, Abre una conexión interactiva de texto plano con el puerto de destino
    - `iftop / nload`: Uso: Monitorizar en tiempo real el ancho de banda y el consumo de tráfico por IP en las interfaces de red.

+ **5. Análisis de Logs y Filtros (`grep, awk, sed, tail, head`)**
    - `tail -f /var/log/syslog`: Sigue en tiempo real las últimas líneas de un archivo de log.
    > `grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' /var/log/auth.log | sort | uniq -c | sort -nr`
    - `grep -i "error" /var/log/nginx/error.log`: Busca coincidencias de texto (ignorando mayúsculas/minúsculas).
    - `awk '{print $1}'`: Extrae columnas específicas de un archivo de texto o log (vital para contar IPs en accesos, por ejemplo).

+ **6. Gestión de Usuarios, Grupos y Privilegios (`useradd, usermod, groupadd, passwd, visudo`)**
    - `id <usuario>` o `groups <usuario>`: ver los grupos que pertenece un usuario.
    - `useradd -m -s /bin/bash <usuario>`: Crea un usuario nuevo con su directorio home y shell por defecto.
    - `useradd -m -d /var/www/ana -s /bin/bash <usuario>`: Sirve para cambiar la ruta por defecto del directorio home
    - `userdel -r <usuario>`: borra un usuario y todo su contenido.
    - `usermod -aG <grupo1>,<grupo2>,<grupo3> <usuario>`: Añade un usuario existente a un grupo secundario (clave para permisos de Docker, sudo, etc.).
    > La bandera -a de append es crucial; si pones solo -g, sobrescribirás su grupo principal y romperás su estructura  
    > Un usuario en Linux tiene un único grupo primario (también llamado grupo principal), que es el que se asigna por defecto a los archivos y carpetas que el usuario crea.  
    - `usermod -g nombre_grupo_primario <usuario>`: cambia el grupo primario.
    - `sudo usermod -G docker,www-data <usuario>`: sobreescribe los grupos secundarios por esos.
    - `sudo usermod -G "" <usuario>`: sobreescribe los grupos secundarios por ninguno.
    - `sudo gpasswd -d <usuario> sudo`: elimina grupo secundario sudo del usuario.
    - `sudo groupadd desarrolladores`: crea un nuevo grupo.
    - `sudo chgrp -R desarrolladores /var/www/html`: ambiar el grupo principal de un archivo o carpeta existente de forma recursiva.
    - `passwd <usuario>`: Cambia o establece la contraseña del usuario.
    - `su -` (Substitute User): Te pide la contraseña de `root` (si está habilitada) y cambia totalmente de usuario cargando el entorno de root.
    - `sudo -i o sudo su`: Te permite elevar privilegios usando tu propia contraseña de usuario (si estás en el grupo sudoers) y te otorga una sesión limpia de root.
    > si no está el usuario metido al grupo `sudo`o `wheel` no podrá entrar.  
    - `visudo`: Edita de forma segura el archivo /etc/sudoers para configurar permisos de elevación de privilegios (sudo).
    ```bash
    # Ejemplo de añadir al usuario ana que tenga sudo solo el comando systemctl
    ana ALL=(ALL) /usr/bin/systemctl restart mi-servicio
    ```
    > El usuario ana, desde cualquier host (ALL), ejecutando como cualquier usuario ((ALL)), puede lanzar exclusivamente la ruta absoluta /usr/bin/systemctl restart mi-servicio
    - `Ficheros críticos:`
        + `/etc/passwd (usuarios)`: Contiene la información pública de las cuentas de usuario. Cada línea está separada por dos puntos (`:`): `usuario : x : UID : GID : Comentario/Nombre : Directorio Home : Shell`(La x donde iría la contraseña es histórica; hoy en día las contraseñas ya no se guardan aquí por seguridad).
        + `/etc/group (grupos)`: Define los grupos del sistema y qué usuarios pertenecen a cada grupo (`grupo:x:GID:usuario1,usuario2`).
        + `/etc/shadow (contraseñas encriptadas)`: Aquí es donde reside el secreto mejor guardado: las contraseñas cifradas (hashes) de los usuarios y las políticas de caducidad (cuándo expira la pass, días mínimos/máximos). Solo el usuario root tiene permisos para leer este archivo (`chmod 000`).
        + `/etc/sudoers`: El fichero de configuración que dicta quién puede elevar privilegios y qué comandos exactos puede ejecutar mediante `sudo`. Jamás se edita con un editor normal (`nano` o `vim`), siempre debe usarse `visudo`, ya que valida la sintaxis antes de guardar para evitar que te bloquees el acceso de administrador por un error tipográfico.

+ **7. Enlaces y Estructura del Sistema de Ficheros (`ln, mount, /etc/fstab`)**
    - `ln -s <archivo_real> <enlace_simbolico>`: Crea un enlace simbólico (shortcut / puntero, como un acceso directo). Muy usado en despliegues web (ej. releases de aplicaciones).
    - `ln <archivo> <enlace_duro>`: Crea un enlace duro (apunta al mismo inodo en disco).
    - `mount /dev/sdb1 /mnt/datos`: Monta un disco o partición en un directorio.
    - `/etc/fstab`: Archivo de configuración para que los discos se monten automáticamente al arrancar el servidor (¡cuidado aquí, un error de sintaxis rompe el arranque!).

+ **8. Compresión y Empaquetado (`tar, gzip, zip`)**
    - `tar -czvf backup.tar.gz /var/www/html`: Empaqueta (-c) y comprime con gzip (-z) un directorio entero en un archivo .tar.gz.
    - `tar -xzvf backup.tar.gz -C /ruta/destino`: Descomprime y extrae un archivo .tar.gz en una ruta específica.
    - `zip -r backup.zip /etc/nginx/`: Comprime en formato ZIP de forma recursiva.

+ **9. Copias de Seguridad y Transferencia Segura (`rsync, scp`)**
    - `rsync -avz -e ssh /origen/ usuario@servidor_remoto:/destino/`: Sincroniza archivos de forma local o remota transfiriendo solo los cambios (la navaja suiza de los backups).
    - `rsync -avzP -e "ssh -p 2222" /var/www/html/ ubuntu@backup-server-02:/backups/html/`: sincroniza a través de puerto concreto.
        - -a (archive): Es el modo archivador. Equivale a -rlptgoD. Preserva recursividad, enlaces simbólicos, permisos, fechas de modificación, propietario y grupo. Es la bandera obligatoria que usarás el 99% de las veces.
        - -v (verbose): Muestra los archivos transferidos en pantalla.
        - -z (compress): Comprime los datos en tránsito durante el envío para ahorrar consumo de ancho de banda.
        - -h (human-readable): Muestra los tamaños en MB/GB comprensibles.
        - --delete: (¡Cuidado extremo con esta!) Borra del destino los archivos que ya no existan en el origen. Convierte la carpeta de destino en un espejo (mirror) idéntico.
        - -P (Progress + Partial): Muestra una barra de progreso en tiempo real y permite reanudar transferencias interrumpidas.
    - `scp archivo.tar.gz user@remoto:/ruta/`: Copia archivos de forma segura a través de SSH entre servidores.
    - `scp -P 2222 backup_uploads.tar.gz ubuntu@backup-server-02:/backups/`: copia a través de puerto concreto.
    - `ssh -p 2222 ubuntu@backup-server-02 "ls -lh /storage/logs/app/"`: se conecta con puerto concreto y hace un comando allí.

+ **10. Identidad y Configuración del Sistema (`hostnamectl, timedatectl, sysctl`)**
    - `hostnamectl set-hostname <nombre>`: Cambia el hostname del servidor de forma persistente.
    - `timedatectl set-timezone Europe/Madrid`: Configura la zona horaria (vital para logs sincronizados).
    - `sysctl -p`: Recarga los parámetros del kernel en caliente (ej. optimizaciones de red o límites de archivos abiertos nofile).

+ **11. SSH: Cómo conectarse por SSH a servidores**:
    - `ssh usuario@ip_o_dominio`: El comando básico de conexión.
    - `ssh -p 2222 admin@192.168.1.50`: 
    - `ssh -i ~/.ssh/id_rsa_produccion ubuntu@api.tuempresa.com`:
    - El archivo de configuración `(~/.ssh/config): Para no escribir IPs y puertos largos, configuras alias en tu máquina local:
    ```bash
    Host prod-web-01
        HostName 203.0.113.50
        User ubuntu
        Port 2222
        IdentityFile ~/.ssh/id_rsa_prod
    ```

+ **12. DMESG(viene de diagnostic message) es un comando que te permite mostrar y controlar el anillo de búfer del kernel de Linux (kernel ring buffer).**
    - Cada vez que enciendes la máquina o ocurre un evento a bajo nivel en el hardware o en el sistema operativo, el kernel registra mensajes en este búfer circular en memoria:
        + Cuando conectas o desconectas un disco USB o una interfaz de red.
        + Cuando un disco duro empieza a fallar (errores de sectores defectuosos o I/O errors).
        + Cuando el Kernel ejecuta la OOM Killer porque te has quedado sin memoria RAM.
        + Cuando un driver de red o de vídeo crashea.
    - `dmesg -T | grep -i oom`: si una aplicación muere misteriosamente, para confirmar si el sistema mató al proceso por falta de RAM.
    - `dmesg | grep -i error o dmesg | grep -i sd`: Diagnosticar fallos de hardware/almacenamiento, te dirá si el disco físico está dando errores de lectura/escritura (I/O error).
    - `dmesg | less`: para revisar todo lo que pasó desde que la máquina encendió el primer segundo.

## 🗺️ Roadmap de Laboratorios (SysAdmin N2 / DevOps)

## Escenario 0: Gestión de paquetes

+ Imagina que entras a un servidor en producción y necesitas instalar una herramienta de diagnóstico urgente (htop o curl), pero al hacer el apt install el sistema explota por culpa de dependencias rotas dejadas por una actualización anterior.

+ Flujo de resolución paso a paso:
    - Actualizar índices:
    ```Bash
    sudo apt update
    ```
    - Intentar la instalación limpia:
    ```Bash
    sudo apt install htop -y
    ```
    - Si salta un error de dependencias rotas (broken dependencies), aplicas la solución de emergencia:
    ```Bash
    sudo apt --fix-broken install
    (Este comando obliga al gestor de paquetes a reparar o desinstalar los paquetes inconsistentes que bloqueaban el sistema).
    ```
    - Limpieza total de paquetes huérfanos para liberar espacio y evitar conflictos:
    ```Bash
    sudo apt autoremove --purge
    ```

## Escenario 1: Gestión de Disco y Logs

+ Son las 03:00 AM. La aplicación web principal ha caído porque el volumen /var está al 100% y el servicio no puede arrancar.
    - **Pregunta 1: ¿Con qué comando exacto compruebas de forma rápida el estado de uso de todos los sistemas de archivos y revisas los inodos?** `df -h / df -i`
        + **Un sistema de ficheros (Filesystem)** es el método y estructura de datos que utiliza el sistema operativo para controlar cómo se almacenan y recuperan los datos en un disco duro o partición. Sin él, el disco sería una masa informe de ceros y unos. (Ext4 - Debian/Ubuntu, XFS - RHEL/CENTOS, tmpfs - RAM)
        + **El inodo** es una estructura de datos que almacena toda la metainformación de un archivo o directorio (propietario, permisos, tamaño, fechas de modificación y la ubicación física exacta de los bloques de datos en el disco), excepto su nombre y su contenido.
            - Cada archivo o carpeta consume exactamente un inodo.
            - Puedes tener un disco duro de 500 GB con el 50% de espacio libre, pero si tu aplicación genera millones de archivos diminutos (ej. sesiones de PHP, colas de correo en /var/spool, caché web), agotaras los inodos disponibles
    
    - **Pregunta 2: ¿Cómo rastreas progresivamente las carpetas que más ocupan dentro de /var sin colgar la terminal?** `ncdu /var`
        + **ncdu (NCurses Disk Usage)** escanea el árbol de directorios de forma inteligente y te presenta una interfaz visual interactiva en la terminal ordenada de mayor a menor consumo.
        
        + **Cómo se usa en producción:** Te desplazas con las flechas del teclado, entras en carpetas con Enter, y si encuentras una carpeta basura o un log antiguo que no sirve para nada, puedes borrarlo pulsando la tecla d directamente dentro de ncdu. Pulsa q para salir. Te ahorra hacer cálculos mentales con du -sh y comandos recursivos lentos.

    - **Pregunta 3: Borraste un log gigante pero el espacio sigue al 100% (df -h). ¿Qué comando usas para identificar qué proceso mantiene pillado ese archivo y cómo lo liberas?** `lsof +L1 (o lsof | grep deleted` + `systemctl reload <servicio> o systemctl restart <servicio>)`
        + **Cómo identificarlo**: Usamos lsof +L1 (o lsof | grep deleted), que lista los archivos abiertos que tienen un enlace duro de 0 (NLINK 0), es decir, están borrados pero retenidos por un proceso. Te dirá el PID y el nombre del proceso culpable.
        + **Cómo solucionarlo de forma limpia:** En lugar de hacer un kill -9 a lo bruto (que puede corromper datos de la app), lo profesional es reiniciar o recargar el servicio de forma ordenada (systemctl reload <servicio> o systemctl restart <servicio>), lo que obliga al proceso a cerrar los descriptores y liberar el espacio al instante.

+ **TIPS**:
    - **Nunca vacíes un log con rm si está activo:** Si quieres vaciar un archivo de log que sigue activo en caliente sin reiniciar el proceso, utiliza el operador de truncado: `> /ruta/al/archivo.log` o `truncate -s 0 /ruta/al/archivo.log`. Esto vacía el contenido a 0 bytes manteniendo el archivo intacto para el proceso.
    - **Implementa Logrotate:** Asegúrate siempre de que las aplicaciones en producción tengan configurada la rotación de logs en `/etc/logrotate.d/` para comprimir, rotar y purgar logs antiguos automáticamente.

## Escenario 2: Procesos y Rendimiento

+ El servidor va lentísimo (CPU al 100% y carga disparada)
+ Alerta P1: Son las 11:30 AM en plena hora punta. Los clientes reportan que la plataforma va extremadamente lenta. Intentas hacer SSH al servidor y la terminal se queda congelada durante 30 segundos antes de darte acceso. Una vez dentro, notas que el sistema responde con muchísimo retardo.
+ Tienes acceso SSH como sudo.
    - **Pregunta 1: La foto rápida de salud: Una vez dentro de la terminal, ¿qué comandos rápidos ejecutarías para ver de inmediato la carga del sistema (Load Average) y el consumo global de CPU y Memoria RAM sin entrar en interfaces interactivas?** `top` o `uptime`
    ```bash
    top
    top - 19:41:11 up 12:05,  1 user,  load average: 0.08, 0.15, 0.13
    Tasks:  56 total,   2 running,  54 sleeping,   0 stopped,   0 zombie
    %Cpu(s):  0.2 us,  0.5 sy,  0.0 ni, 99.1 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st 
    MiB Mem :   7608.1 total,   5188.7 free,   2030.5 used,    598.5 buff/cache     
    MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   5577.6 avail Mem 
    ```
    - **Pregunta 2: Caza de brujas (Procesos pesados): Necesitas identificar exactamente qué proceso o script está reventando la CPU o la memoria. ¿Qué comando usarías con ps para listar los procesos ordenados de mayor a menor consumo de CPU o Memoria?** `ps aux --sort=-%mem` o `ps aux --sort=-%cpu`

    - **Pregunta 3: El misterio de los Zombis: En la lista de procesos ves varios con estado Z (Zombie).**
        + **¿Qué es exactamente un proceso zombi y está consumiendo recursos de CPU o RAM?** Un proceso zombi no es un proceso vivo que consume CPU o RAM. Es un proceso que ya ha terminado su ejecución (exit), pero su proceso padre todavía no ha leído su estado de salida en la tabla de procesos del kernel. Sigue apareciendo en la lista con estado Z simplemente para dejar constancia de su código de salida. No consume recursos (su memoria y CPU ya fueron liberadas), solo ocupa una entrada numérica minúscula (un PID).

        + **Si intentas hacerles un kill -9 <PID>, ¿funcionará? ¿Por qué?** NO. Como el proceso ya está muerto y no existe en el sistema operativo (solo queda su "fantasma administrativo" esperando al padre), la señal SIGKILL no tiene efecto alguno. Tienes que **matar al proceso padre** que se ha quedado colgado sin recoger a sus hijos. Al morir el padre, el sistema operativo (init/systemd) adopta y limpia automáticamente al zombi.

+ **TIPS**:
    - El indicador Load Average: Cuando mires la carga del sistema (uptime o top), verás tres números (ej. 1.50, 0.80, 0.45). Representan la carga media en los últimos 1, 5 y 15 minutos.
    > Si tienes un servidor con 4 CPUs y el Load Average sube de 4.00, el servidor está sufriendo saturación (hay más tareas pidiendo CPU de las que los núcleos físicos pueden procesar simultáneamente).  
    - Cuidado con la OOM (Out Of Memory) Killer: Si la RAM física llega al 100% y el sistema se queda sin memoria de intercambio (swap), el Kernel de Linux entra en modo pánico y ejecuta la OOM Killer, matando de forma automática al proceso que más RAM esté consumiendo (a veces una base de datos o Nginx) para salvar el sistema operativo. Puedes ver los rastros de esto ejecutando: `dmesg -T | grep -i oom`.

## Escenario 3: Permisos y Seguridad

+ El error maldito de "Permission Denied"
+ Alerta P1: Son las 16:00 PM. Despliegas una nueva versión de la aplicación web a través de un script de CI/CD o de forma manual, pero al intentar arrancar el servicio o acceder a la web, Nginx o la propia aplicación devuelven un `error HTTP 500` y en los logs aparece repetidamente:`FATAL: Failed to open configuration file /etc/app/config.json: Permission denied o (13: Permission denied) while reading upstream`.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Inspección de permisos básicos: Estás logueado como tu usuario habitual (miguel), pero el servicio corre bajo otro usuario del sistema (ej. www-data o nginx). ¿Qué comandos usarías para verificar el propietario, grupo y los permisos numéricos (octales) exactos tanto del archivo de configuración como de su directorio padre? `ls -l file/dir` o `stat file/dir`
    ```bash
    ls -l /var/
    total 44
    drwxr-xr-x  2 root root   4096 Oct 17 00:00 backups

    stat /var
    File: /var
    size: 4096            Blocks: 8          IO Block: 4096   directory
    Device: 8,48    Inode: 33554433    Links: 13
    Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2026-10-25 00:13:29.210459350 +0200
    ```

    - **Pregunta 2:** Cambio de propiedad y permisos: Descubres que el archivo pertenece a root:root con permisos 600, por lo que el usuario de la aplicación no puede leerlo.
        + ¿Qué comando usas para cambiar el propietario y grupo de forma recursiva o específica? `chown -R usuario:grupo file/dir`
        + ¿Qué combinación de permisos (en formato octal, ej. 755 o 644) aplicarías de forma segura a un archivo de configuración para que el dueño pueda leer/escribir y el grupo/otros solo leer? `chmod 644 file/dir`

    - **Pregunta 3:** El nivel avanzado (ACLs): Imagina que tienes un escenario complejo donde tres usuarios de diferentes departamentos necesitan escribir en una misma carpeta de logs, pero el sistema clásico de permisos de Linux (propietario/grupo/otros) se te queda corto. ¿Qué herramienta y comandos usarías para añadir permisos granulares a un usuario específico sin cambiar el grupo principal?
        + Las Listas de Control de Acceso permiten añadir permisos específicos a usuarios o grupos adicionales sobre un archivo o directorio sin alterar el propietario ni el grupo principal.
        ```bash
        # Ver las ACLs de un archivo/directorio:
        getfacl /etc/app/config.json

        # Asignar permisos a un usuario específico (ej. dar lectura/escritura a miguel)
        setfacl -m u:miguel:rw /etc/app/config.json

        # Asignar permisos por defecto de forma recursiva en una carpeta (para que los nuevos archivos hereden la regla):
        setfacl -R -m d:u:miguel:rwx /var/www/html/logs/

        # Eliminar una ACL específica:
        setfacl -x u:miguel /etc/app/config.json
        ```

+ **FLUJO DE TRABAJO EN PRODUCCIÓN**:
    - Conexión al servidor de producción (vía SSH con alias o IP):
    ```bash
    # (vía SSH con alias o IP
    ssh prod-app-01

    # Si usaras puerto y llave privada específica
    ssh -p 2222 -i ~/.ssh/id_rsa_prod ubuntu@203.0.113.50
    ```
    - Comprobación inicial de identidad y permisos actuales: `whoami`
    - Te diriges al directorio donde está el fallo y listos los detalles:
    ```bash
    cd /etc/app/
    ls -l config.json

    # Salida típica del error: 
    -rw------- 1 root root  423 Oct 26 10:00 config.json (Solo root puede leerlo, por eso la app crashea).
    ```
    - Elevación de privilegios y corrección (Uso de sudo):
    ```bash
    sudo chown www-data:www-data config.json
    sudo chmod 644 config.json

    # Verificas los cambios
    ls -l config.json
    # Debe mostrar: -rw-r--r-- 1 www-data www-data 423 Oct 26 10:00 config.json
    ```
    - Validación del servicio:
    ```bash
    sudo systemctl restart mi-aplicacion.service
    sudo systemctl status mi-aplicacion.service
    ```
    - Revisar los logs en tiempo real: `journalctl -u mi-aplicacion.service -n 20 --no-pager`
    - Salir: `exit`

+ **TIPS**:
    - **Cuidado con los permisos recursivos (chmod -R):** Ejecutar un chmod -R 755 /var/www a lo loco puede romper la seguridad de archivos privados o claves SSH dentro de los directorios de usuario. Úsalo siempre con cautela y delimitando rutas.
    - **El permiso especial chmod +x (Ejecutable):** Recuerda que en Linux un script de Bash o un binario no se puede ejecutar a menos que tenga explícitamente el permiso de ejecución (chmod +x script.sh). Es una causa común de fallos en despliegues de CI/CD.


## Escenario 4: Redes y Conectividad

+ El servidor no puede comunicarse con la Base de Datos externa
+ Alerta P1: Son las 21:00 PM. La aplicación arranca correctamente, pero al intentar iniciar sesión o cargar datos, la página se queda en blanco. Revisas los logs y ves un error de timeout: `Connection timed out to database host db.prod.internal:5432`. El servidor de aplicaciones no puede hablar con la base de datos PostgreSQL externa.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Resolución DNS y Red básica: Antes de culpar a la red, ¿con qué comandos compruebas si el servidor es capaz de resolver el nombre de dominio (db.prod.internal) a una dirección IP correcta, y cómo validas si hay respuesta a nivel de IP (ping)? `nslookup db.prod.internal y ping db.prod.internal`

    - **Pregunta 2:** El estado del puerto y los sockets: Sospechas que el puerto 5432 podría estar cerrado en destino o bloqueado por un firewall local. ¿Qué comando moderno usarías para listar los sockets de red activos y comprobar si hay conexiones salientes o si el puerto está accesible? `ss -tn sport = :5432 or dport = :5432`  o forma general `ss -t`
    > El comando ss -tulpn solo muestra puertos en escucha local (LISTEN)  
    > En la columna State. Si ves ESTAB (Established), la conexión está viva y abierta. Si ves SYN-SENT, significa que tu servidor está intentando conectar pero nadie le responde (el paquete se queda atascado, síntoma claro de un firewall o puerto cerrado en destino).

    - **Pregunta 3:** Prueba de fuego final: Necesitas hacer una prueba rápida desde la terminal para ver si el servicio remoto responde activamente a una petición TCP o HTTP antes de que salte el timeout. ¿Qué comandos usas para probar la conectividad contra ese puerto específico o simular una petición? `traceroute db.prod.internal`
    > Si llega al final (como en tu salto 10 fra16s20-in-f3.1e100.net): La ruta está completa y abierta. El destino responde.  

    > Si se queda en bucle de asteriscos hasta el salto 30 (* * * * *): El tráfico se ha cortado en seco en algún punto (un firewall intermedio, una ruta estática mal configurada o el servidor de destino está apagado).

+ **FLUJO**:
    ```bash
    # Conexión al servidor de aplicaciones
    ssh prod-app-01

    # Diagnóstico de resolución DNS y conectividad IP básica
    nslookup db.prod.internal
    # O con dig que es más detallado:
    dig db.prod.internal +short

    # Lanzas un ping rápido para ver si responde:
    ping -c 3 db.prod.internal

    # Comprobación del puerto remoto (La prueba de fuego con nc o telnet/curl)
    ## Si responde "Connected to...", el puerto está abierto y el firewall de red deja pasar el tráfico.
    ## Si da "Connection timed out", hay un firewall (Security Group de AWS, iptables, etc.) bloqueando el puerto.
    nc -zv db.prod.internal 5432

    # Análisis de rutas si hay bloqueo (traceroute / mtr)
    traceroute db.prod.internal

    exit
    ```
    > Una vez detectado que el Security Group del servidor de base de datos bloqueaba la IP de tu app, se corrige la regla de red, vuelves a probar con nc, verificas que la app ya conecta en los logs (`journalctl -u mi-app -f`), y cierras sesión limpiamente:


## Escenario 5: Análisis de Logs y Filtros

+ Alerta P1: El servidor de autenticación o SSH está recibiendo miles de peticiones extrañas. Sospechas que hay una IP externa intentando entrar por fuerza bruta. Los logs de autenticación (/var/log/auth.log o journalctl) escupen líneas sin parar.
+ Tienes acceso SSH como sudo.
    - **Pregunta 1:** Seguimiento en vivo: ¿Qué comando usas para ver en tiempo real las últimas líneas que van llegando al registro de autenticación del sistema? `tail -f /var/log/syslog`
    > ¿En qué debes fijarte? En los niveles de severidad (CRIT, ERR, WARNING, pam_unix(sshd:auth): authentication failure), en las marcas de tiempo repetitivas para ver si hay un bucle, y en procesos que mueran repentinamente (segfault, OOM-killer).

    - **Pregunta 2:** Filtrado y Conteo de IPs: Tienes un archivo de log lleno de intentos fallidos. ¿Cómo combinarías grep, awk y sort para extraer únicamente las direcciones IP que están fallando y contar cuántas veces lo ha intentado cada una, ordenándolas de mayor a menor? `grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' /var/log/auth.log | sort | uniq -c | sort -nr`
    > Esto es lo que busca el formato de una dirección IP (cuatro bloques de números separados por puntos, ej. 192.168.1.1).  
    + Vamos a leerla por dentro:
        - `\b`: Significa límite de palabra (boundary). Evita que coja números que formen parte de fechas o palabras más largas.
        - `[0-9]{1,3}`: Busca un dígito del 0 al 9, que se repita entre 1 y 3 veces (esto cubre bloques como 5, 45 o 192).
        - `\.`: Es un punto literal (el punto en regex significa "cualquier carácter", por eso la barra invertida \ lo convierte en un punto normal).
        - `(...) y {3}`: Todo lo que está entre paréntesis (un bloque de 1 a 3 números seguido de un punto) se repite exactamente 3 veces (.3). Es decir, busca bloque.bloque.bloque..
        - `[0-9]{1,3}`: Como una IP tiene 4 bloques pero el último no lleva punto al final (ej. 192.168.1.1), esto añade el cuarto y último bloque de números.
        - `\b`: Cierra el límite de la palabra.


## Escenario 6: Gestión de Usuarios, Grupos y Privilegios

+ Alerta P1: Se incorpora un nuevo desarrollador (ana) al equipo de infraestructura. Necesita acceso al servidor mediante su propia cuenta de usuario, pero por políticas de seguridad corporativa no puede tener acceso de root total, aunque sí debe poder reiniciar un servicio específico de la aplicación web usando sudo sin que le pida la contraseña completa de administrador para todo lo demás.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Creación y entorno: ¿Con qué comando creas el usuario ana asegurándote de que se genere automáticamente su directorio personal (home) y se le asigne la shell de comandos por defecto (/bin/bash)? `useradd -m -s /bin/bash ana`
    > -m (Create Home): Le dice al sistema: "Crea el directorio personal (/home/usuario) de forma automática si no existe". Si no pones -m, el usuario se crea en el sistema pero no tendrá su carpeta home, lo cual suele dar problemas al iniciar sesión.  

    > -d (Directory): Sirve para cambiar la ruta por defecto del directorio home. Por defecto, Linux los crea en /home/usuario. Si por motivos de arquitectura o diseño quieres que el home de ana esté en /var/www/ana, harías: `useradd -m -d /var/www/ana -s /bin/bash ana`  

    - **Pregunta 2:** Elevación granular (visudo): ¿Cómo configurarías el archivo de privilegios para permitir que la usuaria ana pueda ejecutar exclusivamente el comando systemctl restart mi-servicio mediante sudo sin otorgarle permisos de superusuario absoluto?
    ```bash
    # creamos el usuario
    tail -n1 /etc/passwd
    ana:x:1001:1001::/home/ana:/bin/bash

    # vemos cual es binario que ejecuta ese comando
    which systemctl
    /usr/bin/systemctl

    # editamos /etc/sudoers para especificar el permiso sudo a cierto comando
    ana ALL=(ALL) /usr/bin/systemctl restart mi-servicio
    ```
    > Qué significa: El usuario ana, desde cualquier host (ALL), ejecutando como cualquier usuario ((ALL)), puede lanzar exclusivamente la ruta absoluta /usr/bin/systemctl restart mi-servicio



## Escenario 7: Enlaces y Estructura del Sistema de Ficheros

+ Alerta P1: Estás realizando un despliegue de una nueva versión de la aplicación web en `/var/www/releases/v2.0/`. Sin embargo, Nginx está configurado para leer siempre desde la ruta estática `/var/www/current/`. Si modificas la configuración de Nginx cada vez que hay deploy, tardarás demasiado y habrá cortes de servicio.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Tipos de enlaces: ¿Qué diferencia conceptual existe entre un enlace simbólico (symlink) y un enlace duro (hard link) en Linux?
        + **Enlace Simbólico (Symlink / Soft Link):**
            - Qué es: Es exactamente un acceso directo (como los de Windows). Es un archivo especial que contiene únicamente la ruta de texto hacia el archivo o carpeta original.
            - Características: Puede apuntar a directorios (carpetas) y puede cruzar diferentes particiones o discos duros.
            - El peligro: Si borras o mueves el archivo u origen, el enlace simbólico se rompe inmediatamente y queda huérfano (da error de No such file or directory).

        + **Enlace Duro (Hard Link):**
            - Qué es: Es un puntero directo al mismo inodo físico en el disco duro. No es una ruta de texto; es una copia de la "etiqueta" que apunta a los mismos bloques de datos.
            - Características: Comparten exactamente el mismo inodo y tamaño. Si modificas el archivo desde el enlace duro o desde el original, los cambios se reflejan al instante en ambos porque son el mismo archivo físicamente con dos nombres distintos.
            - Restricciones: No se pueden hacer enlaces duros a directorios (solo a ficheros) y no pueden cruzar sistemas de ficheros distintos.
            - Ventaja clave: Si borras el archivo original, el contenido no se pierde mientras quede al menos un enlace duro vivo apuntando al inodo.

    - **Pregunta 2:** Creación del enlace: Sabiendo que quieres que /var/www/current apunte de forma dinámica a la carpeta de la nueva versión /var/www/releases/v2.0/, ¿con qué comando exacto creas este enlace y compruebas que está apuntando al sitio correcto? `sudo ln -s /var/www/releases/v2.0/ /var/www/current`  
    > lrwxrwxrwx 1 root root 26 Oct 26 21:00 /var/www/current -> /var/www/releases/v2.0/  
    > ln -s [ruta_real_o_destino] [nombre_del_enlace_que_quieres_crear]  


## Escenario 8: Compresión y Empaquetado

+ Alerta P1: El disco secundario donde guardamos los backups históricos está al límite de su capacidad. Tienes que empaquetar y comprimir una carpeta gigante con los datos estáticos de la web (/var/www/html/uploads/) en un único archivo .tar.gz para poder transferirlo a un almacenamiento en la nube (S3) y liberar espacio de inmediato de forma limpia.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Empaquetar y Comprimir: ¿Qué combinación exacta de banderas del comando tar usarías para crear un archivo comprimido llamado backup_uploads.tar.gz a partir del directorio /var/www/html/uploads/? `tar -czvf backup_uploads.tar.gz /var/www/html/uploads/`
    > -c (create) / -z (gzip) / -f (file)

    - **Pregunta 2:** Extracción y Descompresión: Si en lugar de empaquetar necesitas extraer un backup comprimido que te han pasado en formato .tar.gz y volcarlo de forma limpia dentro de la carpeta /var/www/html/, ¿qué comando ejecutas? `tar -xzvf file.tar.gz -C /var/www/html/`  
    > -x (extract) / -C (directory)

+ **FLUJO EN PRODUCCION**:
Imagina la situación: te salta una alerta de Disk Space Critical en /var (98% usado). Necesitas liberar espacio de inmediato empaquetando logs u uploads antiguos y moviéndolos/borrándolos de forma limpia.  
```bash
# Conexión y verificación de espacio antes de comprimir
ssh prod-app-01
df -h /var

# Empaquetado y compresión mostrando el progreso
cd /var/www/html/
sudo tar -czvf backup_uploads_$(date +%Y%m%d).tar.gz uploads/

# Verificación de integridad del archivo creado (sin extraer)
tar -tzvf backup_uploads_20261026.tar.gz | head -n 10

# Movimiento al directorio de backups o storage secundario y limpieza de originales
sudo mv backup_uploads_20261026.tar.gz /mnt/backups/
# Una vez a salvo en el backup, recién limpias o borras la carpeta pesada
sudo rm -rf /var/www/html/uploads_old/

# Validación final del espacio liberado y salida
df -h /var
exit
```

+ **TIPS**:
    - El poder de gzip vs bzip2 / xz:
        + `tar.gz` es el estándar rápido y equilibrado.
        + Si necesitas una compresión brutalmente pequeña (para ahorrar mucho espacio en backups fríos) a costa de consumir más CPU y tardar más tiempo, usa `tar.jvf` (bzip2) o `tar.Jvf` (xz).

    - Ver el contenido sin extraer (Muy útil):
        + Si te pasan un `tar.gz` gigante y quieres ver qué hay dentro antes de descomprimirlo todo a lo loco en el servidor, usa una t en lugar de x:
        `tar -tzvf backup_uploads.tar.gz`  


## Escenario 9: Copias de Seguridad y Transferencia Segura

+ Alerta P1: Tienes un archivo de backup gigantesco (`backup_uploads.tar.gz`) en tu servidor de aplicaciones (`prod-app-01`) y necesitas transferirlo de forma segura al servidor de almacenamiento secundario (`backup-server-02`) antes de medianoche. Además, el directorio de logs del servidor necesita ser sincronizado incrementalmente con otro nodo remoto sin duplicar datos que ya existan allí.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Transferencia Segura de Ficheros (scp): ¿Con qué comando exacto enviarías un archivo local llamado backup_uploads.tar.gz al directorio remoto /backups/ del servidor backup-server-02 usando el puerto SSH personalizado 2222 y autenticándote con tu usuario ubuntu?  
    `scp -P 2222 backup_uploads.tar.gz ubuntu@backup-server-02:/backups/`  
    > -P: especificar el puerto.  
    > -p: sirve para preservar las fechas de modificación y permisos del archivo original (preserve).

    - **Pregunta 2:** Sincronización Inteligente (rsync): ¿Por qué se prefiere rsync frente a scp para hacer copias de seguridad incrementales en redes de producción, y qué flags o parámetros básicos usarías para sincronizar una carpeta local /var/www/html/ con un destino remoto manteniendo permisos y comprimiendo al vuelo?  
    `rsync -avz -e ssh /var/www/html/ ubuntu@backup-server-02:/backups/`  
        + Banderas más usadas en rsync:
            - -a (archive): Es el modo archivador. Equivale a -rlptgoD. Preserva recursividad, enlaces simbólicos, permisos, fechas de modificación, propietario y grupo. Es la bandera obligatoria que usarás el 99% de las veces.
            - -v (verbose): Muestra los archivos transferidos en pantalla.
            - -z (compress): Comprime los datos en tránsito durante el envío para ahorrar consumo de ancho de banda.
            - -h (human-readable): Muestra los tamaños en MB/GB comprensibles.
            - --delete: (¡Cuidado extremo con esta!) Borra del destino los archivos que ya no existan en el origen. Convierte la carpeta de destino en un espejo (mirror) idéntico.
            - -P (Progress + Partial): Muestra una barra de progreso en tiempo real y permite reanudar transferencias interrumpidas.


+ **TIPS**:
    - ¿Por qué se prefiere rsync sobre scp en producción?
        + Transferencia Incremental: rsync utiliza el algoritmo delta. Si transfieres una carpeta de 50 GB y al día siguiente cambia solo un archivo de 2 KB, rsync solo enviará esos 2 KB, mientras que scp volvería a copiar los 50 GB enteros desde cero.
        + Reanudación de descargas rotas: Si la conexión de red se corta al 90%, rsync puede reanudar la copia donde se quedó (usando --partial o -P), mientras que scp obliga a empezar todo de nuevo.

+ **FLUJO EN PRODUCCIÓN**:
```BASH
# Conexión previa para validar conectividad por el puerto 2222
nc -zv backup-server-02 2222

# Ejecución de la sincronización incremental (con barra de progreso)
rsync -avzP -e "ssh -p 2222" /var/log/app/ ubuntu@backup-server-02:/storage/logs/app/

# Verificación rápida en el servidor de destino
ssh -p 2222 ubuntu@backup-server-02 "ls -lh /storage/logs/app/"

exit
```

## Escenario 10: Identidad y Configuración del Sistema

+ Alerta P1: Tras un despliegue de una plantilla de máquina virtual en AWS/Cloud, la instancia se ha levantado con el nombre genérico localhost. Además, la hora del sistema está desfasada por 5 minutos respecto al servidor de base de datos, provocando fallos en la validación de tokens JWT y registros de logs desincronizados.
+ Tienes acceso SSH como usuario con privilegios de sudo.
    - **Pregunta 1:** Hostname del Sistema: ¿Con qué comando cambias de forma permanente el nombre del servidor (hostname) a prod-app-server-01 sin necesidad de reiniciar la máquina? `hostnamectl set-hostname prod-app-server-01`  
    > /etc/hostname y cambiar el texto a mano también es válido, pero no surte efecto de manera inmediata en el kernel hasta que reinicias

    - - **Pregunta 2:** Sincronización Horaria y Zonas: ¿Qué comando usas para verificar el estado de la hora del sistema, ver si el servicio de sincronización NTP está activo y cambiar la zona horaria a Europe/Madrid?
    ```bash
    timedatectl
        Local time: Mon 2026-10-26 21:08:35 CET
        Universal time: Mon 2026-10-26 20:08:35 UTC
        RTC time: Mon 2026-10-26 20:08:35
        Time zone: Europe/Madrid (CET, +0100)
        System clock synchronized: yes
        NTP service: active
        RTC in local TZ: no

    timedatectl set-timezone Europe/Madrid
    ```

+ **FLUJO EN PRODUCCIÓN**:
```bash
# Conexión al servidor y comprobación de identidad y reloj
ssh prod-app-01
hostname
timedatectl status

# Corrección del Hostname en caliente
sudo hostnamectl set-hostname prod-app-server-01

# Ajuste de la zona horaria y sincronización NTP
sudo timedatectl set-timezone Europe/Madrid
# Forzar la sincronización horaria si estuviera desactivada:
sudo timedatectl set-ntp true

# Verificación final y salida
timedatectl
exit
```

+ **TIPS**:
    - El fichero `/etc/hosts local`: Cuando cambies el hostname de un servidor, recuerda revisar siempre el fichero /etc/hosts. Muchas aplicaciones locales buscan su propio nombre (`127.0.0.1 prod-app-server-01`) para resolver conexiones internas; si no lo actualizas, algunas herramientas de monitorización o bases de datos locales pueden dar errores extraños de resolución de bucle invertido (loopback).

    - Cuidado con el reloj de la BIOS (RTC): Asegúrate siempre de que la opción `RTC in local TZ`: no esté en "no" (como se ve en tu salida). Los servidores Linux profesionales deben mantener siempre la hora del hardware en UTC universal, dejando que sea el sistema operativo el que calcule la zona horaria local por encima. Esto evita desajustes catastróficos al cambiar el horario de verano/invierno o al mover máquinas virtuales entre regiones geográficas de la nube (AWS/Azure).

## Tu Primer Día: Ticket de Onboarding

```bash
Ticket #ONBOARDING-01
Prioridad: Normal / Pruebas de Acceso
Descripción: Configuración inicial, despliegue de seguridad, resolución de conectividad y auditoría del nodo de pruebas staging-core-01.
```

### 🛠️ Misión 1: Identidad y Sincronización del Sistema
El servidor que te han asignado se llama genéricamente ip-10-0-0-15 y va con el reloj desfasado.
+ Cambia el hostname del servidor de forma permanente a staging-core-01 sin reiniciar. `sudo hostnamectl set-hostname staging-core-01`

+ Verifica el estado del reloj con timedatectl, asegúrate de que la zona horaria sea Europe/Madrid y comprueba que el servicio NTP esté activo para que la hora atómica sincronice sola. `timedatectl`

### 👥 Misión 2: Ciclo de Vida de Usuarios y Permisos Granulares
Un nuevo sysadmin junior, carlos, se incorpora al equipo.
+ Crea el usuario carlos asegurándote de que tenga su directorio /home/carlos y la shell por defecto /bin/bash. `sudo useradd -m -s /bin/bash carlos`

+ Añade a Carlos al grupo de administradores (sudo), pero luego demuestra tu control sacándolo de ese grupo y dándole permisos exclusivos vía visudo para que únicamente pueda ejecutar el comando de reinicio de Nginx (/usr/bin/systemctl restart nginx) sin que le pida contraseña (NOPASSWD). `sudo usermod -aG sudo carlos` / `sudo gpasswd -d carlos sudo` / `carlos ALL=(ALL) /usr/bin/systemctl restart nginx`

+ Borra la cuenta por completo (incluyendo su directorio personal) de forma limpia cuando termines de probarlo. `sudo userdel -r carlos`

### 🌐 Misión 3: Diagnóstico de Red y Puertos
La aplicación web instalada en el servidor no logra comunicarse con un servicio de métricas externo (metrics.techcorp.internal en el puerto 9090).
+ Comprueba si el dominio resuelve correctamente su IP usando dig o nslookup. `nslookup metrics.techcorp.internal`

+ Realiza una prueba rápida y silenciosa con Netcat (nc) para verificar si el puerto TCP 9090 está abierto y accesible al otro lado. `nc -zv metrics.techcorp.internal 9090` 

+ Si el puerto estuviera bloqueado, usa traceroute o mtr para analizar los saltos de red y comprobar que no hay pérdida de paquetes en la pasarela local. `traceroute metrics.techcorp.internal`

### 📄 Misión 4: Forense de Logs y Expresiones Regulares
El equipo de seguridad sospecha que hay bots escaneando el puerto SSH del servidor.

+ Abre el flujo en tiempo real de los logs de autenticación con tail -f. `tail -f /var/log/syslog`

+ Utiliza la combinación de comandos con grep y expresiones regulares que aprendimos para escanear el fichero de log, extraer únicamente las direcciones IP que están intentando autenticarse, contarlas y ordenarlas de mayor a menor número de intentos para identificar posibles atacantes. `grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' /var/log/auth.log | sort | uniq -c | sort -nr`

### 📦 Misión 5: Empaquetado, Compresión y Sincronización
Necesitas empaquetar una carpeta de logs antiguos (/var/log/nginx/old/) y transferirla al servidor de respaldos.

+ Crea un archivo comprimido .tar.gz llamado nginx_backup_$(date +%Y%m%d).tar.gz de dicha carpeta, mostrando en pantalla el proceso con la bandera verbose. `tar -czvf nginx_backup_$(date +%Y%m%d).tar.gz /var/log/nginx/old/`

+ Simula la transferencia segura de ese archivo hacia un servidor remoto de backups usando scp por un puerto SSH personalizado (ej. puerto 2222). `scp -P 2222 nginx_backup_$(date +%Y%m%d).tar.gz ubuntu@ip-server-backup:/backups/`

+ Explica mentalmente (o ejecuta) cómo harías esa misma transferencia de forma incremental usando rsync si el volumen de datos fuera de varios Gigabytes. `rsync -avzP -e "ssh -p 2222" nginx_backup_$(date +%Y%m%d).tar.gz ubuntu@ip-server-backup:/backups/`


## 🚨 Incidencia de Producción: INC-8842 (Sev 1)
```bash
Hora del incidente: 03:14 AM
Alerta de Monitorización:
    - HTTP 502 Bad Gateway masivo en la pasarela de pagos.
    - Alerta de disco lleno: El volumen /var del clúster de aplicaciones principal (prod-app-01) se encuentra al 100% de uso.
    - El servicio de Nginx y el motor de Node.js/Python de la API han dejado de responder y se están colgando al intentar escribir en los logs locales.
```
> Tienes acceso SSH como usuario con privilegios de sudo. Estás de guardia (on-call).

+ **🛠️ Tus Retos de Resolución de Incidencias (Troubleshooting Real):**
    - Analiza los pasos lógicos que darías y escribe los comandos exactos para resolver este caos en producción:
        + **Paso 1 (Diagnóstico Rápido de Espacio e Inodos):** Te conectas al servidor. ¿Qué comandos combinas para ver qué partición está llena (df) y cuáles son las 10 carpetas o archivos más pesados dentro de /var que están devorando todo el espacio (du)? `df -h` / `du -sh /var`

        + **Paso 2 (Archivos borrados pero retenidos por procesos):** Imagina que has borrado logs antiguos, pero el comando df -h sigue marcando el 100% porque un proceso zombi o un servicio caído sigue teniendo el archivo abierto en segundo plano. ¿Qué comando usas para listar ficheros borrados pero con los descriptores de archivos aún abiertos por un PID vivo, y cómo liberarías ese espacio sin reiniciar toda la máquina? `lsof +L1` / `reiniciar el servicio`

        + **Paso 3 (Análisis de Puertos y Procesos):** Verificas que Nginx y el servicio de la aplicación han muerto. ¿Qué comando usas para comprobar si los procesos están caídos o si hay puertos colgados en estado CLOSE_WAIT / TIME_WAIT que estén bloqueando el reinicio del servicio? `ss -tulpn`

        + **Paso 4 (Recuperación y Validación):** Reinicias los servicios afectados de forma limpia y validas mediante curl -Iv que la API vuelve a devolver códigos HTTP 200 OK. `systemctl restart mi-servicio` / `curl -Iv urlAPI`