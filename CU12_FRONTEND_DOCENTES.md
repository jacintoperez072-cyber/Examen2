# 📋 Frontend del CU12: Asignar Horario Docente

## 🎯 Resumen

El **CU12 (Asignar horario docente)** está implementado en dos componentes Vue principales:

### 1️⃣ **Docentes/Index.vue** (Listado y Asignación)
**Ubicación:** `resources/js/Pages/Docentes/Index.vue`

**Funcionalidad:**
- Listar todos los docentes registrados
- Mostrar grupos-materias asignados a cada docente
- **Modal para asignar un grupo-materia a un docente** ← **CU12 AQUÍ**

**Características:**
- Tabla con docentes
- Columnas: Nombre, Email, Especialidad, Grupos Asignados, Acciones
- Botón "📚 Asignar Grupo" que abre modal
- Modal con selector de grupos-materias disponibles
- Validación de duplicados en backend
- Manejo de errores

**Botones en la tabla:**
```
✏️ Editar      → Editar datos del docente
📚 Asignar Grupo → ABRE MODAL (CU12)
📅 Horarios     → Ver horarios del docente (CU15)
🗑️ Eliminar     → Eliminar docente
```

### 2️⃣ **Docentes/Horarios.vue** (Ver Horarios - CU15)
**Ubicación:** `resources/js/Pages/Docentes/Horarios.vue`

**Funcionalidad:**
- Mostrar horarios semanales del docente
- Listar grupos-materias asignados con su información
- Opción de desasignar grupos-materias

## 🔄 Flujo Completo del CU12

### Frontend (Vue)
```
1. Usuario en Admin → Menú → Docentes
2. Se abre: resources/js/Pages/Docentes/Index.vue
3. Usuario ve tabla de docentes
4. Click en botón "📚 Asignar Grupo" de un docente
5. Modal aparece con selector de grupos-materias
6. Usuario selecciona un grupo-materia
7. Click en "✅ Asignar"
8. Se envía POST /docentes/{docente}/asignar-grupo-materia
```

### Backend (Laravel)
```
1. Ruta recibida: POST /docentes/{docente}/asignar-grupo-materia
2. Controlador: DocenteController::asignarGrupoMateria()
3. Validación:
   - Verifica permisos (authorize 'edit', 'usuarios')
   - Valida que grupo_materia_id exista
   - Verifica que no esté ya asignado (prevenir duplicados)
4. Acción:
   - $docente->grupoMaterias()->attach($validated['grupo_materia_id'])
5. Bitácora:
   - Registra: 'ASIGNAR', 'docente_grupo_materias', ...
6. Respuesta: Redirige con mensaje "Asignación realizada exitosamente"
```

## 📂 Estructura de Archivos

```
resources/
└── js/
    └── Pages/
        └── Docentes/
            ├── Index.vue          ← LISTA DOCENTES Y ASIGNA (CU12)
            └── Horarios.vue       ← VER HORARIOS (CU15)
```

## 🔗 Rutas Configuradas

**En `routes/web.php`:**

```php
// CU12: Asignar grupo-materia a docente
Route::post('/docentes/{docente}/asignar-grupo-materia', 
    [DocenteController::class, 'asignarGrupoMateria'])
    ->name('docentes.asignar-grupo-materia');

// Desasignar (para el modal)
Route::delete('/docentes/{docente}/desasignar-grupo-materia/{grupoMateria}', 
    [DocenteController::class, 'desasignarGrupoMateria'])
    ->name('docentes.desasignar-grupo-materia');

// CU15: Ver horarios del docente
Route::get('/docentes/{docente}/horarios', 
    [DocenteController::class, 'horarios'])
    ->name('docentes.horarios');
```

## 🎨 Interfaz del Modal (CU12)

```
┌─────────────────────────────────────────────────┐
│ 📚 Asignar Grupo-Materia a [Nombre del Docente] │
├─────────────────────────────────────────────────┤
│                                                 │
│ Selecciona el Grupo-Materia:                   │
│ [Dropdown con opciones]                         │
│                                                 │
│ -- Selecciona --                               │
│ ✓ Grupo A - Matemática (08:00 - 10:00)        │
│   Grupo B - Física (10:00 - 12:00)            │
│   ...                                          │
│                                                 │
├─────────────────────────────────────────────────┤
│ ✅ Asignar    ❌ Cancelar                       │
└─────────────────────────────────────────────────┘
```

## 📊 Validaciones Implementadas

### Frontend (Vue)
```javascript
✓ Campo grupo_materia_id requerido
✓ Mostrar errores del backend
✓ Deshabilitar botón mientras se procesa
✓ Limpiar errores al abrir modal
```

### Backend (Laravel - DocenteController)
```php
✓ Validar que grupo_materia_id exista en BD
✓ Verificar que no esté ya asignado
✓ Validar permisos (authorize)
✓ Validar que docente exista
✓ Registrar en bitácora automáticamente
```

## 🔐 Permisos Requeridos

Para acceder al CU12:
- **Rol Admin:** ✅ Acceso completo
- **Rol Coordinador:** ✅ Acceso permitido (tiene permiso 'edit' sobre 'usuarios')
- **Rol Docente:** ❌ No tiene acceso

```php
// En DocenteController::asignarGrupoMateria()
$this->authorize('edit', 'usuarios');
```

## 🧪 Pruebas Manuales

### Paso 1: Acceder
```
1. Login: admin@sistema.com / password123
2. Menú → Docentes
```

### Paso 2: Asignar Grupo
```
1. Buscar docente en tabla
2. Click en "📚 Asignar Grupo"
3. Seleccionar grupo-materia: "Grupo A - Matemática"
4. Click "✅ Asignar"
```

### Paso 3: Verificar
```
1. Debe aparecer: "Asignación realizada exitosamente"
2. La tabla debe actualizar y mostrar:
   - Grupos Asignados: 1 grupo
3. Ver en Bitácora el registro de la acción
```

## 📱 Componentes Reutilizables Usados

```vue
<AuthenticatedLayout>     ← Layout con navegación
<Link>                    ← Router links de Inertia
```

## 🚀 Cómo Extender

### Agregar más campos en la asignación
```vue
<!-- En Docentes/Index.vue, modal -->
<select v-model="formulario.aula_id">
  <!-- Agregar aula si es necesario -->
</select>
```

### Agregar validación de conflictos horarios
```php
// En DocenteController::asignarGrupoMateria()
$tieneCruce = $docente->grupoMaterias()
    ->whereHas('horario', function ($q) use ($grupoMateriaHorario) {
        $q->where('dia_semana', $grupoMateriaHorario->dia_semana)
          ->whereBetween('hora_inicio', [...]);
    })->exists();
```

## ✅ Estado Actual

- ✅ Frontend Index.vue: Listado con modal de asignación
- ✅ Frontend Horarios.vue: Ver horarios del docente
- ✅ Backend: DocenteController métodos completos
- ✅ Rutas: Configuradas en web.php
- ✅ Validaciones: Implementadas
- ✅ Bitácora: Registra asignaciones
- ✅ Permisos: Gates configurados

## 📞 Notas

- El modal usa formulario reactivo con Inertia
- Los errores se muestran debajo del campo
- Hay feedback visual (botón deshabilitado mientras procesa)
- La tabla se actualiza automáticamente tras la asignación
- El sistema previene duplicados automáticamente

---

**Última actualización:** 11 de Noviembre de 2025  
**Estado:** ✅ Completamente implementado
