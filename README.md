# 🐳 Akaunting Docker - Software de Contabilidad Autohospedado

[![GitHub Stars](https://img.shields.io/github/stars/akaunting/akaunting?style=social)](https://github.com/akaunting/akaunting)
[![Docker Pulls](https://img.shields.io/docker/pulls/akaunting/akaunting)](https://hub.docker.com/r/akaunting/akaunting)
[![License](https://img.shields.io/badge/License-BSL-blue.svg)](https://github.com/akaunting/akaunting/blob/master/LICENSE.md)

## 📋 Descripción general

**Akaunting** es un software de contabilidad online **autohospedado** diseñado para pequeñas empresas y freelancers. Construido con **Laravel + VueJS**, ofrece facturación profesional, seguimiento de gastos, presupuestos, reportes financieros (P&L, Balance, Cash Flow), integraciones de pago (Stripe/PayPal), sistema modular de extensiones (App Store), API REST, gestión multi-usuario con roles granulares y soporte multi-empresa. Todo bajo tu control, sin dependencias externas ni lock-in de datos. Licencia **BSL (Business Source License)**, 10.1k+ estrellas en GitHub, listo para producción.

## ✨ Características principales

- 📄 **Facturas profesionales**: Crear, enviar, rastrear con plantillas personalizables y recordatorios automáticos
- 💰 **Gastos e ingresos**: Registro categorizado, seguimiento de ingresos por cliente
- 📊 **Presupuestos**: Crear presupuestos, comparar vs gastos reales, alertas de excedente
- 📈 **Reportes financieros**: P&L, Balance Sheet, Cash Flow, exportables a PDF/Excel por período
- 💳 **Integraciones de pago**: Stripe, PayPal para pagos online en facturas con reconciliación automática
- 👥 **Multi-usuario y roles**: Permisos granulares (Admin, Accountant, Customer), roles personalizados
- 🏢 **Multi-empresa**: Gestionar múltiples empresas con datos separados y control de acceso
- 🧩 **App Store modular**: Extensiones y plugins comunitarios (impuestos, email templates, Slack, Zapier, reportes avanzados)
- 🔌 **API REST**: Integración con ERP/CRM y sistemas externos
- 🐳 **Docker listo para producción**: Imagen oficial optimizada con PHP 8.x

## 📋 Requisitos del sistema

- **Docker y Docker Compose**: Versión reciente
- **PHP 8.1+**: Incluido en la imagen Docker
- **Base de datos**: PostgreSQL 10+, MySQL 5.7+, MariaDB 10.3+, SQLite
- **RAM**: Mínimo 1 GB; recomendado 2-4 GB
- **Almacenamiento**: 5-20 GB (facturas, reportes, datos)
- **Puertos**: 8080 (app web), 5432 o 3306 (BD opcional)

> 📌 **Tip**: Akaunting es muy flexible con BDs. Elige **PostgreSQL** para mayor estabilidad en producción.

## 🐳 Instalación

### Paso 1: Crear `docker-compose.yml`

```yaml
version: '3.8'

services:
  app:
    image: akaunting/akaunting:latest
    ports:
      - "8080:80"
    environment:
      APP_ENV: production
      APP_DEBUG: "false"
      APP_KEY: base64:clave_base64_aqui
      DB_CONNECTION: pgsql
      DB_HOST: postgres
      DB_PORT: 5432
      DB_DATABASE: akaunting
      DB_USERNAME: akaunting
      DB_PASSWORD: contraseña_db_segura
      TZ: Europe/Madrid
    depends_on:
      - postgres
    volumes:
      - ./storage:/var/www/html/storage
      - ./public:/var/www/html/public
    networks:
      - akaunting-net

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: akaunting
      POSTGRES_USER: akaunting
      POSTGRES_PASSWORD: contraseña_db_segura
      TZ: Europe/Madrid
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - akaunting-net

volumes:
  postgres_data:

networks:
  akaunting-net:
```

### Paso 2: Lanzar Docker Compose

```bash
docker compose up -d
```

### Paso 3: Acceder a la interfaz

La aplicación estará disponible en **http://localhost:8080** en ~30 segundos. La base de datos se crea automáticamente.

## ⚙️ Configuración

1. **Generar APP_KEY segura**: Ejecuta `docker run --rm akaunting/akaunting:latest php artisan key:generate --show` y usa el valor base64 en `docker-compose.yml`
2. **Cambiar contraseñas por defecto**: Modifica `DB_PASSWORD` y `POSTGRES_PASSWORD` por valores fuertes (16+ caracteres)
3. **Ajustar zona horaria**: Cambia `TZ: Europe/Madrid` según tu ubicación
4. **Configurar volúmenes persistentes**: Los directorios `./storage` y `./public` persisten facturas, documentos y assets
5. **Red aislada**: La red `akaunting-net` aísla la comunicación app-BD

## 🚀 Primeros pasos

1. Abre **http://localhost:8080** en tu navegador
2. Selecciona **idioma** (Español disponible)
3. Crea tu **empresa**: nombre, moneda, zona horaria
4. Crea **usuario admin**: email y contraseña fuerte (16+ caracteres)
5. Configura **impuestos y categorías** (opcional, según tu país)
6. ¡Listo! Accede al **dashboard principal**

> ⚠️ **Importante**: Usa contraseña admin fuerte (16+ caracteres). Cambia en producción.
> 💡 **Consejo**: Familiarízate primero creando una factura de prueba. Luego configura integraciones de pago (Stripe/PayPal).

## 💡 Casos de uso

- **Freelancers**: Facturas a clientes, gastos deducibles, impuestos anuales
- **Pequeñas empresas**: Invoicing, expense tracking, budgeting, multi-usuario
- **Autónomos**: Control ingresos/gastos, reportes para hacienda
- **Agencias**: Multi-empresa, multi-usuario, proyectos (con extensiones)
- **Startups**: Contabilidad básica escalable con plugins
- **Cumplimiento fiscal**: Generación reportes para declaraciones
- **Integración ERP/CRM**: API REST conecta otros sistemas

## 🔒 Acceso remoto seguro

Para exponer Akaunting de forma segura en internet:

- **Reverse Proxy**: Usa Nginx Proxy Manager, Traefik o Caddy con SSL automático (Let's Encrypt)
- **Autenticación adicional**: Añade Authelia, Authentik o Cloudflare Access delante del proxy
- **VPN**: Accede vía WireGuard, Tailscale o OpenVPN sin exponer puertos públicos
- **Firewall**: Limita acceso al puerto 8080 solo a IPs de confianza si no usas proxy

## 🛠️ Gestión y mantenimiento

### Ver estado y logs
```bash
docker compose ps
docker compose logs -f app
```

### Detener contenedor
```bash
docker compose down
```

### Actualizar Akaunting
```bash
docker compose pull
docker compose up -d
```
> Las migraciones de BD corren automáticamente al iniciar.

### Backup de datos
**Base de datos PostgreSQL:**
```bash
docker exec akaunting_postgres_1 pg_dump -U akaunting akaunting > backup.sql
```

**Volumen storage (facturas, documentos):**
```bash
tar -czf storage_backup.tar.gz ./storage/
```

### Monitoreo
- **Facturas pendientes**: Dashboard muestra resumen
- **Pagos recibidos**: Seguimiento automático con Stripe/PayPal
- **Usuarios activos**: Settings → Users
- **Uso almacenamiento**: Crece con histórico de facturas/reportes

## 📝 Licencia

**BSL (Business Source License)** - Código fuente disponible, uso gratuito para autohospedado. Consulta [LICENSE.md](https://github.com/akaunting/akaunting/blob/master/LICENSE.md) en el repo oficial.

---

> 📖 **Guía completa**: [Cómo instalar y configurar Akaunting: Software de Contabilidad Autohospedado en Docker](https://genbyte.blogspot.com/2026/10/como-instalar-y-configurar-akaunting.html)