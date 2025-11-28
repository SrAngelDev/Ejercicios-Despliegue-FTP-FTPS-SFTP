# Despliegue de Servidores FTP, FTPS y SFTP con Docker

Este repositorio contiene la configuración necesaria para desplegar tres tipos de servidores de transferencia de archivos utilizando Docker y Docker Compose:

1.  **FTP** (File Transfer Protocol) - Básico, sin encriptación.
2.  **FTPS** (FTP Secure) - FTP con capa de seguridad SSL/TLS.
3.  **SFTP** (SSH File Transfer Protocol) - Transferencia de archivos sobre SSH.

## Requisitos

*   [Docker](https://www.docker.com/get-started) instalado.
*   [Docker Compose](https://docs.docker.com/compose/install/) instalado.

---

## 1. Servidor FTP (Básico)

Ubicado en la carpeta `FTP/`. Este despliegue configura un servidor FTP simple utilizando `vsftpd`.

### Características
*   **Imagen**: `fauria/vsftpd`
*   **Usuario**: `myuser`
*   **Contraseña**: `mypass`
*   **Puertos**: 21 (Comando), 21100-21110 (Pasivo)
*   **Configuración**: Jaula chroot habilitada (los usuarios no pueden salir de su directorio home).

### Despliegue
```bash
cd FTP
docker-compose up -d
```

### Conexión
Puedes conectarte usando cualquier cliente FTP (como FileZilla) o vía terminal:
*   **Host**: `localhost` (o tu IP)
*   **Puerto**: `21`
*   **Usuario**: `myuser`
*   **Contraseña**: `mypass`

---

## 2. Servidor FTPS (Seguro con SSL/TLS)

Ubicado en la carpeta `FTPS/`. Añade una capa de seguridad al servidor FTP estándar mediante certificados SSL.

### Características
*   **Imagen**: `fauria/vsftpd`
*   **Usuario**: `myuser`
*   **Contraseña**: `mypass`
*   **Puertos**: 21, 21100-21110
*   **Seguridad**: SSL explícito habilitado (`ssl_enable=YES`). Requiere certificados.

### Certificados
Los certificados deben estar ubicados en `FTPS/certs/`:
*   `vsftpd.crt`
*   `vsftpd.key`

### Despliegue
```bash
cd FTPS
docker-compose up -d
```

### Conexión
En tu cliente FTP, asegúrate de seleccionar:
*   **Protocolo**: FTP
*   **Cifrado**: Requiere FTP explícito sobre TLS (Use explicit FTP over TLS)
*   **Usuario/Pass**: `myuser` / `mypass`

---

## 3. Servidor SFTP (SSH File Transfer)

Ubicado en la carpeta `SFTP/`. Utiliza el protocolo SSH para la transferencia segura de archivos.

### Características
*   **Imagen**: `atmoz/sftp`
*   **Usuario**: `myuser`
*   **Puerto**: `2222` (mapeado al 22 del contenedor)
*   **Autenticación**: Mediante clave pública SSH (sin contraseña).
*   **Volumen**: Los archivos se guardan en `SFTP/datos_compartidos`.

### Configuración de Claves
La clave pública debe estar en `SFTP/ssh_keys/sftp_key.pub`.
La clave privada (`sftp_key`) se usa para conectar desde el cliente.

### Despliegue
```bash
cd SFTP
docker-compose up -d
```

### Conexión
Usando terminal:
```bash
sftp -P 2222 -i SFTP/ssh_keys/sftp_key myuser@localhost
```

O en FileZilla:
*   **Protocolo**: SFTP
*   **Host**: `localhost`
*   **Puerto**: `2222`
*   **Usuario**: `myuser`
*   **Archivo de claves**: Selecciona tu clave privada (`sftp_key`).

---

## Notas Generales

*   Para detener cualquier servicio, ejecuta `docker-compose down` dentro de la carpeta correspondiente.
*   Asegúrate de que los puertos no estén ocupados por otros servicios en tu máquina anfitriona.
