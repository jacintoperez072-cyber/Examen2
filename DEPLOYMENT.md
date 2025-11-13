# Guía de Despliegue y Configuración

## Instrucciones para Clonar e Instalar el Proyecto

Este proyecto ha sido desplegado en GitHub siguiendo buenas prácticas. Las dependencias (`vendor`, `node_modules`) y archivos sensibles (`.env`) **NO están versionados** y deben instalarse localmente.

### 1. Clonar el repositorio

```bash
git clone https://github.com/jacintoperez072-cyber/Examen2.git
cd Examen2
```

### 2. Copiar el archivo de entorno

```bash
cp .env.example .env
```

**Importante:** Editar `.env` con las credenciales de tu base de datos local.

### 3. Instalar dependencias PHP (Composer)

```bash
composer install
```

**Nota:** `composer.json` y `composer.lock` están versionados y son coherentes, garantizando que todos los desarrolladores tengan exactamente las mismas versiones de dependencias.

### 4. Instalar dependencias JavaScript

```bash
npm install
```

### 5. Generar la clave de la aplicación

```bash
php artisan key:generate
```

### 6. Ejecutar migraciones de base de datos (si es necesario)

```bash
php artisan migrate
```

### 7. Compilar assets (si es necesario)

```bash
npm run dev
```

---

## Estructura de Archivos Versionados

- ✅ `composer.json` - Definición de dependencias PHP
- ✅ `composer.lock` - Versiones exactas de dependencias PHP
- ✅ `package.json` - Definición de dependencias JavaScript
- ✅ `package-lock.json` - Versiones exactas de dependencias JavaScript
- ✅ `.env.example` - Plantilla de variables de entorno
- ✅ `.gitignore` - Configurado para excluir archivos sensibles

## Archivos NO Versionados

- ❌ `.env` - Variables de entorno (sensibles)
- ❌ `vendor/` - Dependencias PHP
- ❌ `node_modules/` - Dependencias JavaScript
- ❌ `.git/` - Historial de git (ignorado en remotes)

---

## Verificación de Coherencia

Antes de hacer commit, asegúrate de que:

1. `composer.json` y `composer.lock` son coherentes:
   ```bash
   composer update --lock --no-install
   ```

2. No hay archivos sensibles en staging:
   ```bash
   git status
   ```

3. `.env`, `vendor/`, `node_modules/` están en `.gitignore`:
   ```bash
   git check-ignore .env vendor node_modules
   ```

---

## Notas de Seguridad

⚠️ **Nunca commits:**
- Archivos `.env` con credenciales reales
- Carpeta `vendor/`
- Carpeta `node_modules/`
- Archivos de sesión o cache

✅ **Siempre commits:**
- `composer.lock` - para reproducibilidad
- `.env.example` - como referencia
- `.gitignore` - para definir reglas

---

## Despliegue en Producción

Para despliegue en servidores como AWS EC2, consultar `deploy.sh` y la documentación en `removed_docs_backup/`.

