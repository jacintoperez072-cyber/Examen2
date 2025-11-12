# 🎓 CU14: Registrar Asistencia - Guía Completa

## 📌 ¿Qué es el CU14?

**Caso de Uso 14:** Registrar la asistencia del docente (si asistió o no a su clase/horario asignado).

### Ejemplo Real:
```
Docente: Carlos García
Clase: Grupo A - Matemática
Fecha: Lunes 08:00-10:00
Estado: ✅ Presente / ❌ Ausente
Resultado: Se registra la asistencia del docente
```

---

## 🔄 Flujo Completo

```
┌──────────────────────────────────┐
│      USUARIO (DOCENTE/ADMIN)     │
│      Inicia sesión               │
└──────────────┬────────────────────┘
               │
               ▼
    ┌──────────────────────┐
    │   Menú Principal     │
    │   [Asistencias]      │
    └──────────┬───────────┘
               │
               ▼ Click en "Asistencias"
    ┌──────────────────────────────────┐
    │  PÁGINA: Asistencias/Index.vue   │
    │  (Listar asistencias registradas)│
    │                                  │
    │  Tabla de asistencias            │
    │  [➕ Nueva Asistencia]            │
    └──────────┬───────────────────────┘
               │
               ▼ Click en "➕ Nueva Asistencia"
    ┌──────────────────────────────────┐
    │  PÁGINA: Asistencias/Create.vue  │
    │  (CU14 - Registrar Asistencia    │
    │   DEL DOCENTE)                   │
    │                                  │
    │  ┌────────────────────────────┐  │
    │  │ Formulario:                │  │
    │  │ Docente: [Carlos García]   │  │
    │  │ Grupo-Materia:            │  │
    │  │ [Grupo A - Matemática ▼]  │  │
    │  │ Fecha: [11/11/2025]        │  │
    │  │ Asistencia: [Presente ✅] │  │
    │  │             [Ausente ❌]  │  │
    │  │                            │  │
    │  │ [Guardar] [Cancelar]       │  │
    │  └────────────────────────────┘  │
    │          ↓ Click "Guardar"       │
    │     (Validando datos)            │
    │          ↓                       │
    │  ✅ "Asistencia registrada       │
    │      exitosamente"               │
    └──────────┬───────────────────────┘
               │
    ┌──────────▼──────────────────────┐
    │  BASE DE DATOS                  │
    │  Tabla: asistencias             │
    │  ┌──────────────────────────┐   │
    │  │ id, grupo_materia_id,    │   │
    │  │ docente_id, fecha,       │   │
    │  │ presente, created_at     │   │
    │  ├──────────────────────────┤   │
    │  │ Nueva fila por estudiante│   │
    │  │ (30 filas nuevas)        │   │
    │  └──────────────────────────┘   │
    │                                 │
    │  Tabla: bitacoras               │
    │  ┌──────────────────────────┐   │
    │  │ REGISTRAR, asistencias   │   │
    │  └──────────────────────────┘   │
    └────────────────────────────────┘
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Asistencias/
├── Index.vue        ← Listar asistencias
└── Create.vue       ← AQUÍ ESTÁ EL CU14
    ├── Formulario
    ├── Selectores
    ├── Lista de estudiantes
    └── Checkbox para cada uno
```

### Backend
```
app/Http/Controllers/
└── AsistenciaController.php
    ├── create()             ← Mostrar formulario
    └── store()              ← Procesar CU14
        ├── Validar datos
        ├── Crear registros
        ├── Registrar bitácora
        └── Retornar éxito
```

### Rutas
```
routes/web.php
├── GET  /asistencias/create  ← Mostrar formulario
└── POST /asistencias         ← Procesar registro
```

---

## 🎨 Vista Detallada de Create.vue

### Interfaz Visual

```
┌────────────────────────────────────────────────┐
│           Nueva Asistencia (CU14)              │
│       (Registrar asistencia del DOCENTE)       │
├────────────────────────────────────────────────┤
│                                                │
│  Docente: *                                    │
│  ┌────────────────────────────────────────┐   │
│  │ Carlos García                      ▼   │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  Grupo-Materia: *                             │
│  ┌────────────────────────────────────────┐   │
│  │ -- Selecciona --                   ▼   │   │
│  │ ✓ Grupo A - Matemática                 │   │
│  │   Grupo B - Física                     │   │
│  │   Grupo C - Historia                   │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  Fecha: * [11/11/2025]                       │
│                                                │
│  Asistencia: *                                │
│  ◉ ✅ Presente                               │
│  ○ ❌ Ausente                                │
│                                                │
│  Observaciones: (opcional)                    │
│  ┌────────────────────────────────────────┐   │
│  │ [Texto libre]                          │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  [💾 Guardar]  [❌ Cancelar]                 │
│                                                │
└────────────────────────────────────────────────┘
```

### Estructura HTML

```vue
<template>
  <AuthenticatedLayout>
    <template #header>
      <h2>Nueva Asistencia (Docente)</h2>
    </template>

    <form @submit.prevent="submit">
      <!-- Docente -->
      <div class="mb-4">
        <label>Docente: *</label>
        <select v-model="form.docente_id" required>
          <option value="">-- Selecciona --</option>
          <option v-for="docente in docentes" :value="docente.id">
            {{ docente.user.nombre }}
          </option>
        </select>
      </div>

      <!-- Grupo-Materia -->
      <div class="mb-4">
        <label>Grupo-Materia: *</label>
        <select v-model="form.grupo_materia_id" required>
          <option value="">-- Selecciona --</option>
          <option v-for="gm in gruposMaterias" :value="gm.id">
            {{ gm.grupo.nombre }} - {{ gm.materia.nombre }}
          </option>
        </select>
      </div>

      <!-- Fecha -->
      <div class="mb-4">
        <label>Fecha: *</label>
        <input v-model="form.fecha" type="date" required>
      </div>

      <!-- Asistencia (Radio) -->
      <div class="mb-4">
        <label>Asistencia: *</label>
        <div>
          <label>
            <input type="radio" v-model="form.presente" :value="true" required>
            ✅ Presente
          </label>
          <label>
            <input type="radio" v-model="form.presente" :value="false" required>
            ❌ Ausente
          </label>
        </div>
      </div>

      <!-- Observaciones -->
      <div class="mb-4">
        <label>Observaciones: (opcional)</label>
        <textarea v-model="form.observaciones" rows="3"></textarea>
      </div>

      <!-- Botones -->
      <div class="flex gap-2">
        <button type="submit" class="btn btn-primary">
          💾 Guardar
        </button>
        <Link href="/asistencias" class="btn btn-secondary">
          ❌ Cancelar
        </Link>
      </div>
    </form>
  </AuthenticatedLayout>
</template>
```

### Lógica JavaScript

```javascript
const form = reactive({
  docente_id: '',
  grupo_materia_id: '',
  fecha: new Date().toISOString().split('T')[0],
  presente: true,  // Por defecto presente
  observaciones: '',
});

const errors = ref({});
const cargando = ref(false);

const submit = async () => {
  cargando.value = true;
  try {
    // Enviar datos al backend
    const response = await router.post('/asistencias', form);
    // Redirige a índice si éxito
  } catch (error) {
    errors.value = error.response?.data?.errors || {};
  } finally {
    cargando.value = false;
  }
};
```

---

## ⚙️ Backend - AsistenciaController.php

### Método: store()

```php
public function store(Request $request)
{
    // 1. AUTORIZACIÓN
    $this->authorize('create', 'asistencias');
    
    // 2. VALIDACIÓN
    $validated = $request->validate([
        'docente_id' => 'required|exists:docentes,id',
        'grupo_materia_id' => 'required|exists:grupo_materias,id',
        'fecha' => 'required|date',
        'presente' => 'required|boolean',
        'observaciones' => 'nullable|string',
    ]);
    
    // 3. CREAR REGISTRO DE ASISTENCIA DEL DOCENTE
    Asistencia::create([
        'docente_id' => $validated['docente_id'],
        'grupo_materia_id' => $validated['grupo_materia_id'],
        'fecha' => $validated['fecha'],
        'presente' => $validated['presente'],
        'observaciones' => $validated['observaciones'],
    ]);
    
    // 4. REGISTRAR EN BITÁCORA
    BitacoraService::registrar(
        'REGISTRAR',
        'asistencias',
        $validated['grupo_materia_id'],
        'Asistencia del docente registrada: ' . ($validated['presente'] ? 'Presente' : 'Ausente')
    );
    
    // 5. RETORNAR
    return redirect('/asistencias')
        ->with('success', 'Asistencia registrada exitosamente');
}
```

---

## 🧪 Prueba Paso a Paso

### 1. Acceder
```
URL: http://localhost:8000/asistencias
Email: admin@sistema.com (o coordinador@sistema.com)
```

### 2. Click en "➕ Nueva Asistencia"
```
Ir a: /asistencias/create
```

### 3. Rellenar formulario
```
1. Seleccionar Docente: "Carlos García"
2. Seleccionar Grupo-Materia: "Grupo A - Matemática"
3. Fecha: 11/11/2025
4. Asistencia: Marcar "✅ Presente"
5. Observaciones: (opcional)
```

### 4. Click en "💾 Guardar"
```
Valida datos
Crea 1 registro de asistencia (del docente)
Registra en bitácora
Redirige a /asistencias
```

### 5. Ver resultado
```
✅ "Asistencia registrada exitosamente"

Nueva fila en tabla:
Carlos García | Grupo A - Matemática | ✅ Presente | Hoy
```

---

## 📊 Datos en BD

### Tabla: asistencias

```sql
-- Se crea UNA SOLA fila por cada registro de asistencia del docente
INSERT INTO asistencias 
(docente_id, grupo_materia_id, fecha, presente, observaciones)
VALUES
(1, 3, '2025-11-11', true, 'Asistencia normal'),   -- Presente
(1, 4, '2025-11-11', false, 'Enfermedad'),         -- Ausente
(2, 5, '2025-11-11', true, NULL),                  -- Presente
...
(3, 6, '2025-11-12', true, 'Llegó tarde');         -- Presente
```

**Total de filas creadas: 1 por asistencia registrada**  
(NO es una fila por estudiante)

### Tabla: horarios

```
Los horarios ya existen y NO se modifican
Solo se registra si el docente asistió o no
```

---

## ✨ Mejoras Futuras

### 1. Marcar por período (semana)
```javascript
// Permitir marcar asistencia para toda la semana del docente
const marcarSemana = async () => {
  // Obtener todos los horarios de la semana
  // Crear asistencia para cada uno
};
```

### 2. Cargar desde importación
```javascript
// Permitir importar lista de asistencias desde CSV
const importarCSV = (file) => {
  // Procesar CSV y cargar asistencias
};
```

### 3. Reportes de asistencia del docente
```javascript
// Ver estadísticas de asistencia por docente
// Porcentaje de asistencia en el mes
```

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **Caso de Uso** | CU14: Registrar Asistencia |
| **Frontend** | Asistencias/Create.vue |
| **Backend** | AsistenciaController::store() |
| **Método HTTP** | POST |
| **Ruta** | /asistencias |
| **Permisos** | Docente, Admin |
| **Validaciones** | Grupo existe, docente existe, fecha válida |
| **Auditoría** | Sí (Bitácora) |
| **Estado** | ✅ Completamente implementado |

---

**Versión:** 1.0  
**Última actualización:** 11 de Noviembre de 2025  
**Estado:** ✅ Listo para producción
