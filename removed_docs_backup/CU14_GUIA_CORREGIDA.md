# 🎓 CU14: Registrar Asistencia del Docente - Guía Completa

## 📌 ¿Qué es el CU14?

**Caso de Uso 14:** Registrar la asistencia del docente (si asistió o no a su clase).

### Ejemplo Real:
```
Docente: Carlos García 
Clase: Grupo A - Matemática
Fecha: Lunes 11/11/2025
Hora entrada: 08:00
Hora salida: 10:00
Estado: Presente
Resultado: Registro de asistencia del docente completado
```

---

## 🔄 Flujo Completo

```
┌──────────────────────────────────┐
│      USUARIO (COORDINADOR/ADMIN) │
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
    │  Tabla de asistencias de docentes│
    │  [➕ Nueva Asistencia]            │
    └──────────┬───────────────────────┘
               │
               ▼ Click en "➕ Nueva Asistencia"
    ┌──────────────────────────────────┐
    │  PÁGINA: Asistencias/Create.vue  │
    │  (CU14 - Registrar Asistencia)   │
    │                                  │
    │  ┌────────────────────────────┐  │
    │  │ Formulario:                │  │
    │  │ Grupo-Materia: [Dropdown ▼]│  │
    │  │  • Grupo A - Matemática    │  │
    │  │  • Grupo B - Física        │  │
    │  │                            │  │
    │  │ Docente: [Dropdown ▼]      │  │
    │  │  • Carlos García           │  │
    │  │  • María López             │  │
    │  │                            │  │
    │  │ Fecha: [Date Picker]       │  │
    │  │ [11/11/2025]               │  │
    │  │                            │  │
    │  │ Hora Entrada: [08:00]      │  │
    │  │ Hora Salida: [10:00]       │  │
    │  │                            │  │
    │  │ Estado: [Dropdown ▼]       │  │
    │  │ ✓ Presente                 │  │
    │  │   Ausente                  │  │
    │  │   Retardo                  │  │
    │  │   Justificada              │  │
    │  │                            │  │
    │  │ Observaciones: [Texto...]  │  │
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
    │  │ hora_entrada, hora_salida│   │
    │  │ estado, observaciones    │   │
    │  ├──────────────────────────┤   │
    │  │ Nueva fila:              │   │
    │  │ 1, 3, 1, 11/11/2025,    │   │
    │  │ 08:00, 10:00,           │   │
    │  │ presente, null           │   │
    │  └──────────────────────────┘   │
    │                                 │
    │  Tabla: bitacoras               │
    │  ┌──────────────────────────┐   │
    │  │ CREAR, asistencias       │   │
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
    ├── Formulario con campos
    ├── Selectores (grupo, docente)
    ├── Entrada de fecha y horas
    ├── Campo estado
    └── Observaciones
```

### Backend
```
app/Http/Controllers/
└── AsistenciaController.php
    ├── create()     ← Mostrar formulario
    └── store()      ← Procesar CU14
        ├── Validar datos
        ├── Crear registro
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
│       Registrar Asistencia del Docente (CU14)  │
├────────────────────────────────────────────────┤
│                                                │
│  Grupo-Materia: *                             │
│  ┌────────────────────────────────────────┐   │
│  │ -- Selecciona --                   ▼   │   │
│  │ ✓ Grupo A - Matemática                 │   │
│  │   Grupo B - Física                     │   │
│  │   Grupo C - Historia                   │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  Docente: *                                   │
│  ┌────────────────────────────────────────┐   │
│  │ -- Selecciona --                   ▼   │   │
│  │ ✓ Carlos García                        │   │
│  │   María López                          │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  Fecha: * [11/11/2025]                       │
│                                                │
│  Hora Entrada: [08:00]                       │
│  Hora Salida: [10:00]                        │
│                                                │
│  Estado: *                                    │
│  ┌────────────────────────────────────────┐   │
│  │ ✓ Presente                         ▼   │   │
│  │   Ausente                              │   │
│  │   Retardo                              │   │
│  │   Justificada                          │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  Observaciones:                               │
│  ┌────────────────────────────────────────┐   │
│  │ Retraso por tráfico vehicular        │   │
│  │                                    │   │
│  └────────────────────────────────────────┘   │
│                                                │
│  [💾 Registrar] [❌ Cancelar]                │
│                                                │
└────────────────────────────────────────────────┘
```

### Estructura HTML

```vue
<template>
  <AuthenticatedLayout>
    <template #header>
      <h2>Registrar Asistencia del Docente</h2>
    </template>

    <form @submit.prevent="submit">
      <!-- Grupo-Materia -->
      <div class="mb-4">
        <label>Grupo-Materia: *</label>
        <select v-model="form.grupo_materia_id" required>
          <option value="">-- Selecciona --</option>
          <option v-for="gm in grupoMaterias" :value="gm.id">
            {{ gm.grupo.nombre }} - {{ gm.materia.nombre }}
          </option>
        </select>
        <span v-if="form.errors.grupo_materia_id" class="text-red-600">
          {{ form.errors.grupo_materia_id }}
        </span>
      </div>

      <!-- Docente -->
      <div class="mb-4">
        <label>Docente: *</label>
        <select v-model="form.docente_id" required>
          <option value="">-- Selecciona --</option>
          <option v-for="docente in docentes" :value="docente.id">
            {{ docente.user.nombre }} {{ docente.user.apellido }}
          </option>
        </select>
        <span v-if="form.errors.docente_id" class="text-red-600">
          {{ form.errors.docente_id }}
        </span>
      </div>

      <!-- Fecha -->
      <div class="mb-4">
        <label>Fecha: *</label>
        <input v-model="form.fecha" type="date" required>
        <span v-if="form.errors.fecha" class="text-red-600">
          {{ form.errors.fecha }}
        </span>
      </div>

      <!-- Horas -->
      <div class="grid grid-cols-2 gap-4 mb-4">
        <div>
          <label>Hora Entrada:</label>
          <input v-model="form.hora_entrada" type="time">
        </div>
        <div>
          <label>Hora Salida:</label>
          <input v-model="form.hora_salida" type="time">
        </div>
      </div>

      <!-- Estado -->
      <div class="mb-4">
        <label>Estado: *</label>
        <select v-model="form.estado" required>
          <option value="presente">Presente</option>
          <option value="ausente">Ausente</option>
          <option value="retardo">Retardo</option>
          <option value="justificada">Justificada</option>
        </select>
        <span v-if="form.errors.estado" class="text-red-600">
          {{ form.errors.estado }}
        </span>
      </div>

      <!-- Observaciones -->
      <div class="mb-4">
        <label>Observaciones:</label>
        <textarea v-model="form.observaciones" rows="3"></textarea>
      </div>

      <!-- Botones -->
      <div class="flex gap-2">
        <button type="submit" class="btn btn-primary" :disabled="form.processing">
          💾 Registrar
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
const form = useForm({
  grupo_materia_id: '',
  docente_id: '',
  fecha: new Date().toISOString().split('T')[0],  // Hoy
  hora_entrada: '',
  hora_salida: '',
  estado: 'presente',
  observaciones: '',
});

const submit = () => {
  form.post('/asistencias');  // POST a backend
};
```

---

## ⚙️ Backend - AsistenciaController.php

### Método: store()

```php
public function store(Request $request)
{
    // 1. AUTORIZACIÓN
    $this->authorize('crear', 'asistencia');
    
    // 2. VALIDACIÓN
    $validated = $request->validate([
        'grupo_materia_id' => 'required|exists:grupo_materias,id',
        'docente_id' => 'required|exists:docentes,id',
        'fecha' => 'required|date',
        'hora_entrada' => 'nullable|date_format:H:i',
        'hora_salida' => 'nullable|date_format:H:i',
        'estado' => 'required|in:presente,ausente,retardo,justificada',
        'observaciones' => 'nullable|string',
    ]);
    
    // 3. CREAR REGISTRO
    $asistencia = Asistencia::create($validated);
    
    // 4. REGISTRAR EN BITÁCORA
    BitacoraService::registrar(
        'CREAR',
        'asistencias',
        $asistencia->id,
        'Asistencia del docente registrada'
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
Email: admin@sistema.com
```

### 2. Click en "➕ Nueva Asistencia"
```
Ir a: /asistencias/create (CU14)
```

### 3. Rellenar formulario
```
1. Seleccionar: "Grupo A - Matemática"
2. Seleccionar: "Carlos García"
3. Fecha: 11/11/2025
4. Hora Entrada: 08:00
5. Hora Salida: 10:00
6. Estado: Presente
7. Observaciones: (opcional)
```

### 4. Click en "💾 Registrar"
```
Valida datos
Crea 1 registro en asistencias
Registra en bitácora
Redirige a /asistencias
```

### 5. Ver resultado
```
✅ "Asistencia registrada exitosamente"

Nueva fila en tabla:
Grupo A - Matemática | Carlos García | 11/11/2025 | 08:00-10:00 | Presente
```

---

## 📊 Tabla: asistencias

```sql
-- Estructura
CREATE TABLE asistencias (
  id INT PRIMARY KEY,
  grupo_materia_id INT FOREIGN KEY,
  docente_id INT FOREIGN KEY,
  fecha DATE,
  hora_entrada TIME,
  hora_salida TIME,
  estado ENUM('presente', 'ausente', 'retardo', 'justificada'),
  observaciones TEXT,
  created_at TIMESTAMP,
  updated_at TIMESTAMP
);

-- Inserción por CU14
INSERT INTO asistencias 
(grupo_materia_id, docente_id, fecha, hora_entrada, hora_salida, estado, observaciones)
VALUES
(3, 1, '2025-11-11', '08:00', '10:00', 'presente', NULL);
```

---

## 🔐 Permisos

```
✅ Admin: Sí
✅ Coordinador: Sí (si tiene permiso 'crear' sobre 'asistencia')
❌ Docente: No (solo puede verlas)
```

---

## 🐛 Troubleshooting

| Problema | Solución |
|----------|----------|
| Error "grupo_materia_id" | Verificar que existan grupos asignados |
| Error "docente_id" | Verificar que existan docentes |
| Fecha rechazada | Usar formato YYYY-MM-DD |
| Hora no guarda | Verificar formato HH:mm |
| Estado inválido | Solo: presente, ausente, retardo, justificada |

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **Caso de Uso** | CU14: Registrar Asistencia del Docente |
| **Frontend** | Asistencias/Create.vue |
| **Backend** | AsistenciaController::store() |
| **Método HTTP** | POST |
| **Ruta** | /asistencias |
| **Qué se registra** | Asistencia DEL DOCENTE (no estudiantes) |
| **Campos** | Grupo-materia, docente, fecha, horas, estado |
| **Permisos** | Admin, Coordinador |
| **Validaciones** | Grupo existe, docente existe, fecha válida |
| **Auditoría** | Sí (Bitácora) |
| **Estado** | ✅ Completamente implementado |

---

**Versión:** 1.0 (Corregida)  
**Última actualización:** 11 de Noviembre de 2025  
**Estado:** ✅ Listo para producción  
**Nota:** Registra asistencia del DOCENTE, no de estudiantes
