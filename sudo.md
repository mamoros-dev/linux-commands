# SUDO

## Configuración granular en /etc/sudoers (vía visudo)
+ Para permitir que la usuaria ana ejecute únicamente el comando de reiniciar un servicio específico sin pedirle contraseña (o pidiéndosela solo para eso), abrimos el editor seguro con sudo visudo y añadimos al final del archivo una línea como esta:
```BASH
ana ALL=(ALL) /usr/bin/systemctl restart mi-servicio
```
> Qué significa: El usuario ana, desde cualquier host (ALL), ejecutando como cualquier usuario ((ALL)), puede lanzar exclusivamente la ruta absoluta /usr/bin/systemctl restart mi-servicio. ¡Seguridad milimétrica!

## 📂 Los Ficheros Sagrados de Identidad y Seguridad en Linux
+ Todo SysAdmin N2 debe conocer de memoria dónde vive la información de usuarios y contraseñas:
    - **/etc/passwd:** Contiene la información pública de las cuentas de usuario. Cada línea está separada por dos puntos (:):usuario : x : UID : GID : Comentario/Nombre : Directorio Home : Shell(La x donde iría la contraseña es histórica; hoy en día las contraseñas ya no se guardan aquí por seguridad).
    - **/etc/shadow:** Aquí es donde reside el secreto mejor guardado: las contraseñas cifradas (hashes) de los usuarios y las políticas de caducidad (cuándo expira la pass, días mínimos/máximos). Solo el usuario root tiene permisos para leer este archivo (chmod 000).
    - **/etc/group:** Define los grupos del sistema y qué usuarios pertenecen a cada grupo (grupo:x:GID:usuario1,usuario2).
    - **/etc/sudoers:** El fichero de configuración que dicta quién puede elevar privilegios y qué comandos exactos puede ejecutar mediante sudo. Jamás se edita con un editor normal (nano o vim), siempre debe usarse visudo, ya que valida la sintaxis antes de guardar para evitar que te bloquees el acceso de administrador por un error tipográfico.

## 👥 Gestión de Usuarios, Grupos y Elevación (Chuleta Exprés)

+ Añadir un usuario a un grupo secundario (ej. al grupo sudo o docker):
```Bash
sudo usermod -aG sudo ana
```
> (La bandera -a de append es crucial; si pones solo -g, sobrescribirás su grupo principal y romperás su estructura).

+ Diferencia entre su - y sudo -i / sudo su:
    - **su - (Substitute User):** Te pide la contraseña de root (si está habilitada) y cambia totalmente de usuario cargando el entorno de root.
    - **sudo -i o sudo su: **Te permite elevar privilegios usando tu propia contraseña de usuario (si estás en el grupo sudoers) y te otorga una sesión limpia de root.


## Gestión de Grupos Secundarios y Primarios: Comandos y Ejemplos
+ ¿Cómo añadir un usuario a varios grupos secundarios a la vez? Puedes separar los nombres de los grupos por comas (sin espacios):
```Bash
sudo usermod -aG grupo1,grupo2,grupo3 ana
```
> ¿Se puede tener más de un grupo primario?No. Un usuario en Linux tiene un único grupo primario (también llamado grupo principal), que es el que se asigna por defecto a los archivos y carpetas que el usuario crea. Por lo general, Linux crea un grupo con el mismo nombre del usuario al registrarlo (ej. el grupo ana para la usuaria ana).

+ ¿Cómo cambiar o asignar el grupo primario? Se usa la bandera minúscula -g (sin la a):
```Bash
sudo usermod -g nombre_grupo_primario ana
```

+ ¿Cómo quitar a un usuario de un grupo secundario (ej. quitar a ana del grupo sudo)?
    - El comando más limpio y seguro para gestionar los grupos secundarios de un usuario sobrescribiendo la lista actual es usando la bandera -G (en mayúsculas, sin la -a). Al no poner -a (append), le dices al sistema: "Deja a este usuario únicamente en estos grupos que te paso ahora":
```Bash
sudo usermod -G docker,www-data ana
```
> (Al hacer esto, si ana estaba en el grupo sudo, desaparecerá de él inmediatamente).

## Diferencias clave entre su -, sudo -i, sudo su y el uso cotidiano de sudo
- Si ana NO está en el grupo sudoers:
    + Si hace **su - root** (o simplemente su -), el sistema le pedirá la contraseña de root. Si no la conoce, le dará un error de Authentication failure.
    + Si hace **sudo cat /etc/shadow**, el sistema le dirá: "ana is not in the sudoers file. This incident will be reported." (Error rotundo de permisos).

- ¿Cuándo usar su - frente a sudo -i / sudo su?
    + **sudo -i (o sudo su):** Es el método recomendado en servidores modernos donde el usuario root tiene la contraseña bloqueada o deshabilitada por seguridad. Elevas privilegios usando tu propia contraseña de usuario y abres una sesión completa de root cargando tus variables de entorno de administración.
    + **su - (Substitute User):** Se usa cuando necesitas cambiar formalmente a otra cuenta de usuario del sistema (por ejemplo, su - postgres para entrar con el usuario de la base de datos y realizar tareas de mantenimiento internas). Te pedirá la clave de ese usuario específico.

## Ejemplos Prácticos Resueltos (Chuleta de Usuarios y Grupos)
+ Crear un grupo nuevo en el sistema:
```Bash
sudo groupadd desarrolladores
```

+ Cambiar el grupo principal de un archivo o carpeta existente de forma recursiva:
```Bash
sudo chgrp -R desarrolladores /var/www/html
```

+ Ver a qué grupos pertenece un usuario (muy útil para auditorías rápidas):
```Bash
groups ana
# O de for
```

## El misterio de su - y su - postgres al descubierto
+ El comando **su** viene de Substitute User (Sustituir Usuario). Su función es cambiar de una identidad a otra en la terminal.
    - **su - (A secas):** Al no poner nombre de usuario, asume que quieres cambiar a root. El guion (-) es lo más importante de todo: significa "cargar el entorno de login completo".
    > Al usar el guion, Linux cambia al usuario root y además carga todas sus variables de entorno originales ($PATH, $HOME, $USER), como si te hubieras conectado por SSH directamente con esa cuenta.  
    - **su - postgres:** Hace exactamente lo mismo, pero cambiando al usuario del sistema postgres (el encargado de gestionar el motor de bases de datos PostgreSQL).
    > ¿Por qué es vital en producción? Cuando una base de datos falla y necesitas ejecutar comandos internos de mantenimiento (como psql o reconstruir clústeres), si estás logueado como tu usuario habitual (miguel), el sistema te dará errores de permisos. Haces su - postgres, te conviertes en el dueño legítimo de los ficheros de la base de datos, se carga su entorno en /var/lib/postgresql y puedes operar con total libertad.

+ ¿Qué pasa si omites el guion (su postgres)? Cambias de usuario, pero mantienes las variables de entorno de tu usuario anterior. Esto provoca fallos rarísimos de rutas donde la terminal no encuentra los binarios porque tu $PATH sigue apuntando al sitio equivocado. Por eso los SysAdmins siempre usamos el guion: su -.

## Cómo borrar por completo a un usuario (ana)
+ Si quieres eliminar a la usuaria ana de la faz del servidor, borrando no solo su cuenta de acceso sino todo su rastro físico en el disco duro (su carpeta personal en /home/ana, sus archivos temporales y su buzón de correo), se usa la bandera -r (remove):
```Bash
sudo userdel -r ana
```
+  Qué hace este comando por debajo:
    - Borra la línea de ana en /etc/passwd, /etc/shadow y /etc/group.
    - Elimina de forma recursiva y permanente el directorio /home/ana/ y todo su contenido.
    - Limpia cualquier grupo personal que se hubiera creado exclusivamente para ella.


## 📑 Chuleta: Ejemplos más habituales en /etc/sudoers (vía visudo)
+ En el día a día de un SysAdmin N2 o DevOps, configurar permisos granulares en visudo evita que demos privilegios de root absoluto a quien no lo necesita. Aquí tienes los patrones más utilizados en producción:

+ **Ejecutar un comando específico SIN pedir contraseña (NOPASSWD):** Ideal para scripts de despliegue o herramientas que automatizan tareas.
```Plaintext
ana ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
```

+ **Permitir a todo un grupo de usuarios ejecutar comandos de administración: **Si creas un grupo llamado sysadmins o devops, puedes darles permisos globales sin ir usuario por usuario:
```Plaintext
%devops ALL=(ALL) ALL
```

+ **Limitar la ejecución a comandos de gestión de paquetes (ej. apt o yum):** Para que un usuario pueda actualizar software sin ser root para lo demás:
```Plaintext
deployer ALL=(ALL) /usr/bin/apt-get update, /usr/bin/apt-get install *
```

+ **Ejecutar comandos cohibiéndose como OTRO usuario del sistema (no root):** Permitir que el usuario miguel ejecute el comando psql pero suplantando únicamente al usuario de base de datos postgres:
```Plaintext
miguel ALL=(postgres) /usr/bin/psql
```