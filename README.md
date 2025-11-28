# Mis Ejercicios de Despliegue: FTP, FTPS y SFTP 🚀

¡Hola! 👋 En este repositorio he subido las prácticas que he estado haciendo para desplegar diferentes tipos de servidores de transferencia de archivos. Todo está montado con **Docker** y **Docker Compose** para que sea súper fácil de levantar y probar.

He configurado tres escenarios distintos:

1.  **FTP**: El de toda la vida, básico y sin encriptar.
2.  **FTPS**: Un poco más seguro, añadiéndole SSL/TLS.
3.  **SFTP**: El más seguro, usando SSH y claves en lugar de contraseñas.

## ¿Qué necesitáis?

Simplemente tener instalado **Docker** y **Docker Compose** en vuestra máquina. Con eso ya estaría todo listo para funcionar.

---

## 1. Servidor FTP (Básico) 📂

Está en la carpeta `FTP/`. Aquí he montado un servidor simple usando `vsftpd`. Lo he configurado para que el usuario esté "enjaulado" en su directorio y no pueda cotillear por el resto del sistema.

*   **Imagen que he usado**: `fauria/vsftpd`
*   **Usuario**: `myuser`
*   **Contraseña**: `mypass`
*   **Puerto**: 21

### ¿Cómo lo arranco?
Entráis en la carpeta y levantáis el compose:
```bash
cd FTP
docker-compose up -d
```

Para probarlo, podéis usar FileZilla o conectaros por terminal a `localhost` con el usuario y contraseña de arriba.

---

## 2. Servidor FTPS (Con SSL) 🔒

Este lo tenéis en la carpeta `FTPS/`. Es básicamente igual que el anterior, pero le he metido certificados SSL para que la conexión vaya cifrada.

*   **Certificados**: He dejado los archivos `vsftpd.crt` y `vsftpd.key` en la carpeta `certs/`.
*   **Configuración**: He forzado el uso de SSL (`ssl_enable=YES`).

### ¿Cómo lo arranco?
Igual que el anterior:
```bash
cd FTPS
docker-compose up -d
```

**Ojo al conectar**: En vuestro cliente FTP (como FileZilla), tenéis que elegir la opción de **"Requiere FTP explícito sobre TLS"**, si no, os dará error porque el servidor rechaza conexiones inseguras.

---

## 3. Servidor SFTP (SSH) 🔑

Este es mi favorito, está en la carpeta `SFTP/`. Aquí no usamos contraseñas, sino claves SSH, que es mucho más pro y seguro.

*   **Imagen**: `atmoz/sftp`
*   **Puerto**: He mapeado el puerto **2222** de mi máquina al 22 del contenedor.
*   **Archivos**: Lo que subáis caerá en la carpeta `datos_compartidos`.

### El tema de las claves
He generado un par de claves en `ssh_keys/`.
*   `sftp_key.pub`: Es la llave pública que ya he puesto dentro del contenedor.
*   `sftp_key`: Es la llave privada que **tú** necesitas para conectarte.

### ¿Cómo lo arranco?
```bash
cd SFTP
docker-compose up -d
```

### ¿Cómo conectar?
Si usáis terminal, el comando es este (fijaos que indico dónde está la clave privada):
```bash
sftp -P 2222 -i SFTP/ssh_keys/sftp_key myuser@localhost
```
Si usáis FileZilla, acordaos de cambiar el protocolo a **SFTP** y en el tipo de logueo seleccionad "Archivo de claves" para cargar el fichero `sftp_key`.

---

¡Espero que os sirva de ayuda si estáis trasteando con esto! Cualquier cosa o mejora, me decís. 😉
