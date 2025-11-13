# 🔐 Credenciales de Acceso - Sistema de Gestión de Docentes

**Fecha:** 12 de Noviembre de 2025  
**Status:** ✅ Sistema Funcional

---

## 📋 Usuarios Predefinidos (Cargados por Seeders)

### 1. Administrador
```
Email: admin@sistema.com
Contraseña: password123
Rol: Administrador
Permisos: Acceso completo a todas las funcionalidades
```

### 2. Coordinador
```
Email: coordinador@sistema.com
Contraseña: password123
Rol: Coordinador
Permisos: Gestión de docentes, horarios, asistencias y reportes
```

### 3. Docentes (5 usuarios)
```
1. Email: carlos.garcia@sistema.com
   Contraseña: password123
   Nombre: Carlos García
   
2. Email: maria.lopez@sistema.com
   Contraseña: password123
   Nombre: María López
   
3. Email: pedro.rodriguez@sistema.com
   Contraseña: password123
   Nombre: Pedro Rodríguez
   
4. Email: ana.martinez@sistema.com
   Contraseña: password123
   Nombre: Ana Martínez
   
5. Email: juan.sanchez@sistema.com
   Contraseña: password123
   Nombre: Juan Sánchez
```

---

## 🔑 Cómo Hacer Login

### Paso 1: Acceder a la Aplicación
```
URL: http://localhost:8000
```

### Paso 2: Serás Redirigido a Login
```
URL: http://localhost:8000/login
```

### Paso 3: Ingresar Credenciales
- Usa uno de los emails de arriba
- Contraseña: `password123`

### Paso 4: Hacer Clic en "Sign In" (Iniciar Sesión)

---

## 🎯 Recomendaciones para Probar

### Para Pruebas Completas: USA ADMIN
```
Email: admin@sistema.com
Contraseña: password123
```
**Razón:** Tiene acceso a TODO (usuarios, roles, docentes, horarios, asistencias, reportes, etc.)

### Para Pruebas de Docente: USA CARLOS
```
Email: carlos.garcia@sistema.com
Contraseña: password123
```
**Razón:** Verás los horarios y asistencias del docente

### Para Pruebas de Coordinador: USA COORDINADOR
```
Email: coordinador@sistema.com
Contraseña: password123
```
**Razón:** Permisos de coordinación

---

## ✅ Casos de Uso Disponibles (CU01-CU19)

### Para Administrador/Coordinador:
- **CU11:** Consultar Aulas
- **CU12:** Asignar Horarios a Docentes
- **CU13:** Generar Horarios Automáticamente
- **CU14:** Registrar Asistencia del Docente
- **CU15:** Consultar Horarios Semanales del Docente
- **CU16:** Consultar Aulas Disponibles
- **CU17:** Consultar Asistencia por Docente y Grupo
- **CU18:** Generar Reporte de Asistencias
- **CU19:** Generar Reporte de Horarios

### Para Docentes:
- **CU15:** Ver mis horarios
- Ver mis asistencias

---

## 🐛 Si Recibes un Error

### Error: "Usuario no encontrado" o "Credenciales inválidas"
**Solución:** Verifica que estés usando uno de los emails de la lista arriba.
- ✅ `admin@sistema.com`
- ✅ `coordinador@sistema.com`
- ✅ `carlos.garcia@sistema.com`
- ❌ `hans@gmail.com` (este no existe)

### Error: "Database Query Exception"
**Solución:** Reinicia el servidor:
```bash
php artisan serve
```

### Error: "Vite Manifest Not Found"
**Solución:** Recompila los assets:
```bash
npm run build
```

---

## 🔧 Crear Nuevos Usuarios (Como Admin)

1. Hacer login con `admin@sistema.com`
2. Ir a "Usuarios"
3. Click en "Nuevo Usuario"
4. Llenar el formulario
5. Guardar

---

## 📝 Datos de Prueba Cargados

### Aulas: 5
- Aula 101 (Salón, 40 estudiantes)
- Aula 102 (Salón, 35 estudiantes)
- Aula 103 (Laboratorio, 30 estudiantes)
- Aula 104 (Salón, 45 estudiantes)
- Aula 105 (Laboratorio, 25 estudiantes)

### Materias: 4
- Matemática
- Física
- Química
- Historia

### Grupos: 3
- Grupo A
- Grupo B
- Grupo C

### Docentes: 5 (Los 5 usuarios docentes arriba)

### Horarios: 12
(Generados automáticamente para los docentes)

---

## 🚀 Flujo Recomendado para Probar

1. **Login con Admin**
   ```
   admin@sistema.com / password123
   ```

2. **Ver Dashboard**
   - Debe mostrar información general del sistema

3. **Ir a Docentes**
   - Ver lista de docentes disponibles
   - Ver horarios de cada docente

4. **Ir a Aulas**
   - Consultar aulas disponibles
   - Consultar aulas por filtros

5. **Ir a Asistencias**
   - Registrar asistencia de un docente
   - Consultar asistencias por docente

6. **Ir a Reportes**
   - Generar reporte de asistencias
   - Generar reporte de horarios

---

## 📞 Contacto / Soporte

Si tienes problemas:

1. Verifica las credenciales
2. Reinicia el servidor (`php artisan serve`)
3. Revisa el archivo `storage/logs/laravel.log`
4. Consulta la documentación en `DOCUMENTACION_INDEX.md`

---

**Última actualización:** 12/11/2025  
**Versión:** 1.0  
**Sistema:** Completamente Funcional
