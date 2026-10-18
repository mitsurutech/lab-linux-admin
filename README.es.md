# Laboratorio de administración de Linux: usuarios, permisos, cron y systemd en una máquina virtual Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md)

[![check](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-linux-admin/actions/workflows/check.yml)

Un **laboratorio de administración de Linux** listo para el aula, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde escuelas y centros dan a cada alumno su propia máquina Ubuntu y un terminal en el navegador. El alumno es el administrador de un pequeño servidor y hace cuatro trabajos propios de un administrador de sistemas de primer año: **añadir un usuario y un grupo**, **establecer los permisos de una carpeta compartida**, **programar una tarea con cron** y **escribir y activar un servicio de systemd**. No se instala nada en el ordenador del alumno, y el laboratorio lo califica un script, así que el profesor no tiene que leer cada máquina.

## Las cuatro tareas

| Tarea | Qué hace el alumno |
| --- | --- |
| 1. Un usuario y un grupo | Crear un grupo normal `devs` y un usuario normal `maria` con carpeta personal y bash, dentro de ese grupo y que no sea administrador |
| 2. Una carpeta compartida | Hacer de `/srv/projects` la carpeta del equipo: los miembros del grupo pueden leer, escribir y entrar en ella, los archivos que se creen dentro pertenecen automáticamente al grupo, y nadie más tiene acceso |
| 3. Una tarea programada | Ejecutar el `backup.sh` del laboratorio como root todos los días a las 02:30 desde un archivo de `/etc/cron.d`, escrito como cron lo necesita |
| 4. Un servicio | Escribir un archivo de unidad de systemd para el `heartbeat.sh` del laboratorio, que se reinicie si falla, y activarlo para que arranque en cada inicio |

El laboratorio deja un README en la carpeta personal del alumno que dice qué hacer y no da ninguna de las respuestas: ni comandos, ni modos, ni archivo de unidad. Es pequeño a propósito, de modo que encaja en una primera lección sobre **usuarios y permisos, cron y systemd** y se puede ampliar con discos, LVM o un cortafuegos copiando el archivo.

## Cómo se califica

El script `check` se ejecuta como root sobre una copia de la máquina guardada tras un arranque en limpio, así que solo ve lo que se **guardó**, nunca un comando que se escribió y se perdió al reiniciar. Repasa las cuatro tareas en orden y se detiene en la primera que está mal, y su primera línea dice cuál y por qué, por ejemplo `NOT PASSED: task 3: the user field is /usr/local/bin/backup.sh, it must be root`. Lee el estado real: el usuario y el grupo en la base de datos de cuentas, el propietario y el modo de la carpeta, y un archivo real creado por el nuevo usuario y por alguien ajeno al grupo; el propietario, los permisos y el final del archivo de cron, y su única línea de tarea; el `ExecStart`, el `Restart` y el `WantedBy` del archivo de unidad, y el enlace que crea `systemctl enable`. Un crontab de usuario, una carpeta abierta de par en par con `chmod 777`, un servicio que se inició pero nunca se activó y una copia del archivo de unidad en lugar del enlace fallan todos.

## Qué hay en el archivo

`linux-admin.lab.yaml` es una **definición de laboratorio de máquina virtual** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la máquina virtual de cada alumno: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es el terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la máquina virtual de cada alumno: instala `cron` desde el archivo de Ubuntu, crea los dos scripts pequeños y la carpeta compartida, y escribe el README del laboratorio |
| `check` | el script de calificación descrito arriba; su estado de salida es el resultado y `0` significa aprobado |

## Usarlo en la plataforma

**Para ponerlo como trabajo a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio de máquina virtual Ubuntu**.
2. Pulsa el ejemplo, o pega el contenido de `linux-admin.lab.yaml`. Usar un ejemplo sustituye el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada alumno recibe una máquina virtual propia, que se conserva entre sesiones.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un alumno en la lista de la clase: arranca una copia de su máquina guardada que no se vuelve a guardar, y muestra si aprobó y qué imprimió.

La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-linux-admin) del manual de ayuda, su [guía de los laboratorios de máquina virtual Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) cuentan el resto. ¿Aún no tienes una cuenta de centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) para hasta 50 alumnos, y [registrarse](https://mitsurutech.co.uk/es/signup) solo pide una dirección de correo electrónico.

## Comprobación del archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es YAML válido con las claves de arriba y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna máquina virtual: eso se hace en las máquinas de la propia plataforma.

## Qué no funciona en un laboratorio

La máquina virtual llega al archivo de paquetes de Ubuntu, a PyPI, a npm y a GitHub, y a nada más, así que un laboratorio no puede apuntar a un servidor de internet. Es una sola máquina virtual, así que no hay una segunda máquina que administrar a través de una red.

## Acerca de

Hecho por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para escuelas y centros, con laboratorios de contenedores, laboratorios de máquina virtual, aplicaciones web desplegadas desde GitHub y cuestionarios que se califican solos. Prueba código sin cuenta en el [entorno de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea) y [lab-sample](https://github.com/mitsurutech/lab-sample).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

`cron` se instala desde el archivo de Ubuntu cuando arranca una máquina virtual; nada de él se distribuye aquí.
