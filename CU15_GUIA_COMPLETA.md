# 🎓 CU15: Consultar Horarios Semanales - Guía Completa

## 📌 ¿Qué es el CU15?

**Caso de Uso 15:** Ver los horarios semanales de un docente (qué clases tiene cada día y hora).

### Ejemplo Real:
```
Docente: Carlos García
Semana de: 11/11/2025 al 15/11/2025

Lunes:     08:00-10:00 → Grupo A - Matemática (Aula 101)
Martes:    10:00-12:00 → Grupo B - Física (Aula 102)
Miércoles: 14:00-16:00 → Grupo A - Cálculo (Aula 103)
Jueves:    09:00-11:00 → Grupo C - Historia (Aula 104)
Viernes:   11:00-13:00 → Grupo B - Geografía (Aula 105)
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
    │   [Docentes]         │
    └──────────┬───────────┘
               │
               ▼ Click en "Docentes"
    ┌──────────────────────────────────┐
    │  PÁGINA: Docentes/Index.vue      │
    │                                  │
    │  Tabla de docentes:              │
    │  Carlos García | 📅 Horarios ← Click
    │  María López   | 📅 Horarios    │
    └──────────┬───────────────────────┘
               │
               ▼ Click en "📅 Horarios" (CU15)
    ┌──────────────────────────────────┐
    │  PÁGINA: Docentes/Horarios.vue   │
    │  (CU15 - Ver Horarios Semanales) │
    │                                  │
    │  Docente: Carlos García          │
    │  Especialidad: Matemática        │
    │                                  │
    │  ┌────────────────────────────┐  │
    │  │ 📅 Horarios Semanales:     │  │
    │  │                            │  │
    │  │ LUNES:                     │  │
    │  │ 08:00-10:00 Aula 101       │  │
    │  │ Grupo A - Matemática       │  │
    │  │                            │  │
    │  │ MARTES:                    │  │
    │  │ 10:00-12:00 Aula 102      │  │
    │  │ Grupo B - Física           │  │
    │  │                            │  │
    │  │ MIÉRCOLES:                 │  │
    │  │ 14:00-16:00 Aula 103      │  │
    │  │ Grupo A - Cálculo          │  │
    │  │ ... (más días)             │  │
    │  └────────────────────────────┘  │
    │                                  │
    │  📚 Grupos-Materias Asignados:   │
    │  ┌────────────────────────────┐  │
    │  │ Grupo A - Matemática       │  │
    │  │ Grupo B - Física           │  │
    │  │ Grupo A - Cálculo          │  │
    │  │ 🗑️ Desasignar             │  │
    │  └────────────────────────────┘  │
    │                                  │
    │  ← Volver a Docentes             │
    └──────────┬───────────────────────┘
               │
      ┌────────▼──────────────────┐
      │  BD: Tabla horarios       │
      │  (Agrupados por día)      │
      │  SELECT * FROM horarios   │
      │  WHERE docente_id = 1     │
      │  ORDER BY dia_semana      │
      └───────────────────────────┘
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Docentes/
└── Horarios.vue      ← AQUÍ ESTÁ EL CU15
    ├── Info del docente
    ├── Horarios agrupados por día
    ├── Tabla de horarios
    └── Lista de grupos-materias
```

### Backend
```
app/Http/Controllers/
└── DocenteController.php
    └── horarios()    ← Procesa CU15
        ├── Obtiene docente con relaciones
        ├── Obtiene horarios del docente
        ├── Agrupa por día de semana
        └── Retorna vista
```

### Rutas
```
routes/web.php
└── GET /docentes/{docente}/horarios  ← Mostrar página (CU15)
```

---

## 🎨 Vista Detallada de Horarios.vue

### Interfaz Visual

```
┌────────────────────────────────────────────────┐
│      📅 Horarios de Carlos García              │
├────────────────────────────────────────────────┤
│                                                │
│ Nombre: Carlos García                          │
│ Email: carlos.garcia@sistema.com               │
│ Especialidad: Matemática                       │
│                                                │
│ ┌──────────────────────────────────────────┐  │
│ │ 📅 Horarios Semanales                    │  │
│ ├──────────────────────────────────────────┤  │
│ │                                          │  │
│ │ LUNES:                                   │  │
│ │ • 08:00 - 10:00 │ Aula 101              │  │
│ │   Grupo A - Matemática                   │  │
│ │                                          │  │
│ │ MARTES:                                  │  │
│ │ • 10:00 - 12:00 │ Aula 102              │  │
│ │   Grupo B - Física                       │  │
│ │                                          │  │
│ │ MIÉRCOLES:                               │  │
│ │ • 14:00 - 16:00 │ Aula 103              │  │
│ │   Grupo A - Cálculo                      │  │
│ │                                          │  │
│ │ ... (Jueves, Viernes)                   │  │
│ └──────────────────────────────────────────┘  │
│                                                │
│ ┌──────────────────────────────────────────┐  │
│ │ 📚 Grupos-Materias Asignados             │  │
│ ├──────────────────────────────────────────┤  │
│ │ GRUPO      │ MATERIA    │ HORARIO       │  │
│ │ Grupo A    │ Matemática │ 08:00-10:00   │  │
│ │ Grupo B    │ Física     │ 10:00-12:00   │  │
│ │ Grupo A    │ Cálculo    │ 14:00-16:00   │  │
│ │ 🗑️ Desasignar                           │  │
│ └──────────────────────────────────────────┘  │
│                                                │
│ [← Volver a Docentes]                         │
└────────────────────────────────────────────────┘
```

### Estructura HTML

```vue
<template>
  <AuthenticatedLayout>
    <template #header>
      <h2>📅 Horarios de {{ docente.user.nombre }}</h2>
    </template>

    <!-- Info Docente -->
    <div class="grid grid-cols-3 gap-4 mb-6">
      <div>
        <p class="text-sm font-medium text-gray-600">Nombre:</p>
        <p class="font-semibold">{{ docente.user.nombre }} {{ docente.user.apellido }}</p>
      </div>
      <div>
        <p class="text-sm font-medium text-gray-600">Email:</p>
        <p>{{ docente.user.email }}</p>
      </div>
      <div>
        <p class="text-sm font-medium text-gray-600">Especialidad:</p>
        <p>{{ docente.especialidad }}</p>
      </div>
    </div>

    <!-- Horarios Semanales -->
    <div class="mb-6">
      <h3 class="text-lg font-semibold mb-4">📅 Horarios Semanales</h3>
      
      <div v-if="horariosAgrupados && Object.keys(horariosAgrupados).length > 0">
        <div v-for="(horarios, dia) in horariosAgrupados" :key="dia" class="mb-6 border-l-4 border-blue-500 pl-4">
          <h4 class="font-medium text-lg mb-2">{{ dia }}</h4>
          
          <div v-for="horario in horarios" :key="horario.id" class="mb-3 p-3 bg-gray-50 rounded">
            <div class="flex justify-between items-start">
              <div>
                <p class="font-semibold">{{ horario.hora_inicio }} - {{ horario.hora_fin }}</p>
                <p class="text-sm text-gray-600">Aula: {{ horario.aula?.nombre }}</p>
                <p class="text-sm text-blue-600">{{ horario.grupo_materias?.[0]?.grupo?.nombre }} - {{ horario.grupo_materias?.[0]?.materia?.nombre }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-else class="text-center py-8 text-gray-500">
        ℹ️ No hay horarios asignados
      </div>
    </div>

    <!-- Grupos Asignados -->
    <div class="mb-6">
      <h3 class="text-lg font-semibold mb-4">📚 Grupos-Materias Asignados</h3>
      
      <div v-if="docente.grupo_materias?.length > 0" class="space-y-2">
        <div v-for="gm in docente.grupo_materias" :key="gm.id" class="border rounded p-3 flex justify-between">
          <div>
            <p class="font-medium">{{ gm.grupo.nombre }} - {{ gm.materia.nombre }}</p>
            <p class="text-sm text-gray-600">{{ gm.horario.hora_inicio }} - {{ gm.horario.hora_fin }}</p>
          </div>
          <button @click="confirmarDesasignar(gm)" class="text-red-600 hover:text-red-900">
            🗑️ Desasignar
          </button>
        </div>
      </div>

      <div v-else class="text-center py-8 text-gray-500">
        ℹ️ No hay grupos asignados
      </div>
    </div>

    <Link href="/docentes" class="btn btn-secondary">
      ← Volver a Docentes
    </Link>
  </AuthenticatedLayout>
</template>
```

### Lógica JavaScript

```javascript
const horariosAgrupados = computed(() => {
  if (!props.horarios) return {};
  
  const agrupados = {};
  props.horarios.forEach(horario => {
    if (!agrupados[horario.dia_semana]) {
      agrupados[horario.dia_semana] = [];
    }
    agrupados[horario.dia_semana].push(horario);
  });
  
  return agrupados;
});

const confirmarDesasignar = (grupoMateria) => {
  if (confirm(`¿Desasignar ${grupoMateria.grupo.nombre}?`)) {
    router.delete(`/docentes/${props.docente.id}/desasignar-grupo-materia/${grupoMateria.id}`);
  }
};
```

---

## ⚙️ Backend - DocenteController.php

### Método: horarios()

```php
public function horarios(Docente $docente)
{
    // 1. AUTORIZACIÓN
    $this->authorize('view', 'horarios');
    
    // 2. OBTENER HORARIOS DEL DOCENTE
    $horariosDocente = Horario::whereHas('grupoMaterias', function ($query) use ($docente) {
        $query->whereHas('docentes', function ($q) use ($docente) {
            $q->where('docente_id', $docente->id);
        });
    })->with('grupoMaterias', 'aula')->get();
    
    // 3. RETORNAR VISTA CON DATOS
    return Inertia::render('Docentes/Horarios', [
        'docente' => $docente->load('user', 'grupoMaterias.grupo', 'grupoMaterias.materia', 'grupoMaterias.horario'),
        'horarios' => $horariosDocente,
    ]);
}
```

---

## 🧪 Prueba Paso a Paso

### 1. Acceder
```
URL: http://localhost:8000/docentes
Email: admin@sistema.com
```

### 2. Ver tabla de docentes
```
Carlos García | Matemática | 2 grupos | [✏️] [📚] [📅 Horarios] [🗑️]
```

### 3. Click en "📅 Horarios"
```
Ir a: /docentes/1/horarios (CU15)
```

### 4. Ver horarios semanales
```
LUNES:     08:00-10:00 Aula 101 | Grupo A - Matemática
MARTES:    10:00-12:00 Aula 102 | Grupo B - Física
MIÉRCOLES: 14:00-16:00 Aula 103 | Grupo A - Cálculo
```

### 5. Ver grupos asignados
```
Tabla con:
- Grupo A - Matemática | 08:00-10:00 | [🗑️ Desasignar]
- Grupo B - Física | 10:00-12:00 | [🗑️ Desasignar]
```

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **Caso de Uso** | CU15: Consultar Horarios Semanales |
| **Frontend** | Docentes/Horarios.vue |
| **Backend** | DocenteController::horarios() |
| **Método HTTP** | GET |
| **Ruta** | /docentes/{docente}/horarios |
| **Permisos** | Todos (view horarios) |
| **Validaciones** | Docente existe |
| **Auditoría** | Lectura (no registrada) |
| **Estado** | ✅ Completamente implementado |

---

**Versión:** 1.0 | **Última actualización:** 11 de Noviembre de 2025 | **Estado:** ✅ Listo
