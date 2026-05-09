# 📦 Blackbox Composerized

[![Repositorio en GitHub](https://img.shields.io/badge/GitHub-blackbox-181717?style=for-the-badge&logo=github)](https://github.com/maxjb-xyz/blackbox)

**Blackbox** es *el Diario Forense (Forensic Journal) para tu Infraestructura*. Desarrollado en Go, está diseñado para ofrecer el máximo impacto con el mínimo consumo de recursos.

Este repositorio proporciona un entorno listo para usar mediante Docker Compose para desplegar rápidamente tanto el Servidor como el Agente de Blackbox.

## 🌟 ¿Para qué sirve?

Blackbox actúa como una capa de inteligencia centralizada para tu infraestructura. Se encarga de monitorear y registrar eventos críticos de manera continua, tales como:
- Registros del sistema (journald / systemd)
- Eventos y estados de contenedores Docker
- Cambios en el sistema de archivos (ej. `/etc`)

Al funcionar como una "caja negra" o "registro forense", Blackbox te asegura que, cuando algo falle en tu servidor o infraestructura, tendrás una línea de tiempo inmutable y completa de los eventos para poder investigar la causa raíz.

## 🔑 Generación de Tokens de Seguridad

Para que el servidor y el agente se comuniquen de forma segura, es necesario definir tokens criptográficos en el archivo `.env`.

Puedes generar tokens aleatorios y seguros fácilmente desde tu terminal ejecutando el siguiente comando (generará una cadena hexadecimal de 64 caracteres):

```bash
openssl rand -hex 32
```

Ejecuta este comando varias veces para generar claves distintas para las diferentes variables de tu archivo `.env`: `JWT_SECRET`, `WEBHOOK_SECRET` y los tokens del agente.

**Ejemplo de configuración en tu archivo `.env`:**
```env
# Variables Generales
JWT_SECRET=tu_token_generado_1
WEBHOOK_SECRET=tu_token_generado_2

# Tokens permitidos en el Servidor (Formato: NODO=TOKEN)
AGENT_TOKENS="homelab=tu_token_generado_3"

# Configuración del Agente
AGENT_TOKEN=tu_token_generado_3
NODE_NAME=homelab
```

## 🚀 Tutorial: Levantar el proyecto en entorno local

Sigue estos pasos para desplegar Blackbox en tu máquina local y acceder a su interfaz gráfica.

### 1. Preparar la configuración

Dirígete a la carpeta `docker-compose` de este proyecto:
```bash
cd docker-compose
```

Asegúrate de haber configurado los tokens en el archivo `.env` tal como se explicó en el paso anterior.

### 2. Exponer el puerto del Servidor

En el archivo `docker-compose.yml`, los puertos de la interfaz web suelen venir comentados por defecto. Para poder acceder desde tu navegador local, edita el archivo `docker-compose.yml` y **descomenta** la sección de `ports` en el servicio `blackbox-server`:

```yaml
  blackbox-server:
    image: ghcr.io/mdambro/blackbox-server:latest
    container_name: blackbox-server
    restart: unless-stopped
    ports:               # <-- Descomentar esta línea
      - "8080:8080"      # <-- Descomentar esta línea
    volumes:
      - blackbox_data:/data
# ...
```

### 3. Iniciar los contenedores

Una vez listo el archivo `.env` y expuesto el puerto, levanta el proyecto en modo "detached" (segundo plano) ejecutando:

```bash
docker compose up -d
```

Si deseas ver que todo está funcionando correctamente, puedes revisar los logs:
```bash
docker compose logs -f
```

### 4. Acceder a la Interfaz Gráfica

¡Todo listo! Abre tu navegador web favorito y accede a la siguiente dirección:

👉 **[http://localhost:8080](http://localhost:8080)**

Ahí podrás ver la interfaz gráfica de Blackbox, la cual empezará a mostrar toda la telemetría recolectada por el agente que acabas de desplegar.

---
*Para más información técnica, documentación detallada y código fuente original, visita el [repositorio oficial de Blackbox en GitHub](https://github.com/maxjb-xyz/blackbox).*
