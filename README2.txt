 Obligatorio – Administración de Servidores Linux

Este proyecto contiene los archivos de configuración, inventario y playbooks utilizados para administrar dos servidores Linux (Ubuntu 24.04 y CentOS Stream 9) mediante **Ansible**.

El objetivo es que cualquier persona pueda ejecutar los playbooks y reproducir la configuración realizada, incluso en servidores recién instalados.

---

 Estructura del proyecto

├── inventory.ini # Archivo de inventario con grupos y hosts
├── nfs_setup.yml # Playbook para configurar NFS en CentOS
├── hardening.yml # Playbook de seguridad para Ubuntu
├── ansible_basics.md # Respuestas teóricas de la tarea 5
├── Documentos/ # Evidencias y capturas de pantalla
└── README.md # Este archivo

yaml
Copiar
Editar

---

 Requisitos previos

1. **Servidor CentOS Stream 9** y **servidor Ubuntu Server 24.04**, con:
   - Usuario con permisos sudo (en este caso `admin`).
   - Acceso SSH habilitado.
   - Clave pública del bastión copiada al usuario.
2. **Bastión o máquina de control** con Ansible instalado:
   - En Ubuntu/Debian:
     ```bash
     sudo apt update && sudo apt install ansible
     ```
   - En CentOS/RHEL:
     ```bash
     sudo dnf install ansible
     ```
3. Conexión de red entre el bastión y los servidores.

---

 Inventario

El archivo `inventory.ini` define los servidores y sus grupos:

```ini
[ubuntu]
ubuntu-server ansible_host=192.168.100.20 ansible_user=admin

[centos]
centos-server ansible_host=192.168.100.10 ansible_user=admin

[linux:children]
ubuntu
centos

[webserver]
centos-server
ansible_host: IP interna del servidor.

ansible_user: usuario que Ansible utilizará para la conexión SSH.

Ejecución de playbooks
1. Configuración de NFS en CentOS (nfs_setup.yml)
Este playbook realiza:

Instalación del servicio NFS.

Inicio y habilitación del servicio.

Apertura del puerto 2049 en el firewall.

Creación del directorio /var/nfs_shared con permisos 777.

Configuración del archivo /etc/exports para compartir el directorio.

Ejecución:

bash
Copiar
Editar
ansible-playbook -i inventory.ini nfs_setup.yml
Verificación:

bash
Copiar
Editar
sudo systemctl status nfs-server
cat /etc/exports
2. Hardening en Ubuntu (hardening.yml)
Este playbook realiza:

Actualización de todos los paquetes.

Configuración del firewall UFW para bloquear todo excepto SSH.

Configuración de SSH para deshabilitar el login de root y permitir solo clave pública.

Instalación y activación de fail2ban.

Ejecución:

bash
Copiar
Editar
ansible-playbook -i inventory.ini hardening.yml
Verificación:

bash
Copiar
Editar
sudo ufw status
sudo systemctl status fail2ban
 Pruebas de conexión
Para verificar que el inventario está bien configurado:

bash
Copiar
Editar
ansible-inventory -i inventory.ini --list
Para probar la conexión a todos los servidores:

bash
Copiar
Editar
ansible all -i inventory.ini -m ping
