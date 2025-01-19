---
title: 'CH5 - ABM'
---

# **Cheat Sheet: Configuración de Servidores FTP, FTPS y SFTP**  

---

## ✨ **1. Configuración Básica de un Servidor FTP con vsftpd**  

### **🔧 Instalación**
```bash
sudo apt update
sudo apt install vsftpd -y
```

### **🚀 Iniciar y habilitar el servicio**
```bash
sudo systemctl start vsftpd
sudo systemctl enable vsftpd
```

### **👤 Crear un usuario para FTP**
```bash
sudo adduser ftpuser
sudo passwd ftpuser
sudo mkdir -p /home/ftpuser/ftp/upload
sudo chown nobody:nogroup /home/ftpuser/ftp
sudo chmod a-w /home/ftpuser/ftp
sudo chown ftpuser:ftpuser /home/ftpuser/ftp/upload
```

### **⚙️ Configurar vsftpd**
1. Editar el archivo de configuración:  
   ```bash
   sudo nano /etc/vsftpd.conf
   ```
2. Añadir las siguientes líneas:  
   ```plaintext
   write_enable=YES
   chroot_local_user=YES
   allow_writeable_chroot=YES
   user_sub_token=$USER
   local_root=/home/$USER/ftp
   pasv_enable=YES
   pasv_min_port=1024
   pasv_max_port=1048
   ```
3. Reiniciar el servicio:  
   ```bash
   sudo systemctl restart vsftpd
   ```

---

## 🔒 **2. Configuración de FTPS (FTP Seguro con SSL/TLS)**  

### **🛠 Generar un certificado SSL**
```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/vsftpd.pem -out /etc/ssl/private/vsftpd.pem
```

### **📄 Configurar vsftpd para FTPS**
1. Editar el archivo:  
   ```bash
   sudo nano /etc/vsftpd.conf
   ```
2. Agregar o modificar:  
   ```plaintext
   rsa_cert_file=/etc/ssl/private/vsftpd.pem
   rsa_private_key_file=/etc/ssl/private/vsftpd.pem
   ssl_enable=YES
   allow_anon_ssl=NO
   force_local_data_ssl=YES
   force_local_logins_ssl=YES
   ssl_tlsv1=YES
   ssl_sslv2=NO
   ssl_sslv3=NO
   require_ssl_reuse=NO
   ssl_ciphers=HIGH
   pasv_enable=YES
   pasv_min_port=1027
   pasv_max_port=1030
   pasv_address=<DIRECCIÓN_IP>
   ```
3. Reiniciar el servicio:  
   ```bash
   sudo systemctl restart vsftpd
   ```

---

## 🔑 **3. Configuración de SFTP (FTP Seguro Sobre SSH)**  

### **✅ Verificar que SSH está activo**
```bash
sudo systemctl status ssh
```

### **👤 Crear un usuario para SFTP**
```bash
sudo adduser sftpuser
sudo passwd sftpuser
```

### **📁 Configurar el directorio de SFTP**
1. Crear directorios y establecer permisos:  
   ```bash
   sudo mkdir -p /home/sftpuser/sftp/upload
   sudo chown root:root /home/sftpuser/sftp
   sudo chmod 755 /home/sftpuser/sftp
   sudo chown sftpuser:sftpuser /home/sftpuser/sftp/upload
   ```

2. Editar configuración de SSH:  
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
3. Agregar al final:  
   ```plaintext
   Match User sftpuser
       ChrootDirectory /home/sftpuser/sftp
       ForceCommand internal-sftp
       AllowTcpForwarding no
   ```
4. Reiniciar SSH:  
   ```bash
   sudo systemctl restart ssh
   ```

---

## 🔗 **4. Probar las Conexiones**

- **Conexión FTP**:  
  ```bash
  ftp <DIRECCIÓN_IP>
  ```

- **Conexión FTPS**:  
  1. Usar un cliente como **FileZilla**.  
  2. Configuración:  
     - **Protocolo**: FTPS explícito.  
     - **Puerto**: 21.  
     - **Usuario**: `ftpuser`.  

- **Conexión SFTP**:  
  ```bash
  sftp sftpuser@<DIRECCIÓN_IP>
  ```
