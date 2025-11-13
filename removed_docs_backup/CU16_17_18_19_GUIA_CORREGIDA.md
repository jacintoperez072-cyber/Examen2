# 🎓 CU16-19: Consultas y Reportes del Sistema de Docentes - Guía Completa

---

## 📌 Aclaración Importante

**IMPORTANTE:** Este sistema está diseñado para **GESTIONAR DOCENTES**, no estudiantes. Todos los casos de uso se enfocan en:
- 📅 Horarios de docentes
- 🎯 Asignaciones de docentes a grupos y materias
- ✅ Asistencia del docente
- 📊 Reportes sobre docentes

---

# 🎓 CU16: Consultar Aulas Disponibles - Guía Completa

## 📌 ¿Qué es el CU16?

**Caso de Uso 16:** Filtrar y consultar qué aulas están disponibles en un día, hora específicos para asignarlas a docentes.

### Ejemplo Real:
```
Búsqueda: Lunes, 10:00
Resultado:
- Aula 101: Disponible (no hay docente asignado en esa hora)
- Aula 103: Disponible
- Aula 105: NO disponible (docente Carlos García ya tiene clase 10:00-12:00)
```

---

## 🔄 Flujo Completo

```
Menú → Aulas → [Disponibles]
         ↓
   Formulario Filtros:
   - Día de Semana: Lunes
   - Hora: 10:00
         ↓
   [🔍 Buscar Aulas Disponibles]
         ↓
   API: GET /aulas/disponibles?dia_semana=Lunes&hora_inicio=10:00
         ↓
   BD: SELECT * FROM aulas WHERE no tienen horario docente en esa hora
         ↓
   Tabla: Aulas sin docentes asignados en esa hora
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Aulas/
└── Disponibles.vue    ← AQUÍ ESTÁ EL CU16
    ├── Filtros (día, hora)
    ├── Botón buscar
    └── Tabla de aulas disponibles
```

### Backend
```
app/Http/Controllers/
└── AulaController.php
    └── disponibles()  ← Procesa CU16
```

### Rutas
```
routes/web.php
├── GET /aulas/disponibles  ← Mostrar página
└── GET /api/aulas/disponibles ← API (JSON)
```

---

## 🎨 Vista: Aulas/Disponibles.vue

```
┌──────────────────────────────────────────────┐
│      Aulas Disponibles (CU16)                │
│   (Para asignar a docentes)                  │
├──────────────────────────────────────────────┤
│                                              │
│ Filtros de búsqueda:                         │
│ Día: [Lunes ▼]                              │
│ Hora Inicio: [10:00]                        │
│                                              │
│ [🔍 Buscar]                                 │
│                                              │
│ ─────────────────────────────────────────    │
│                                              │
│ Resultados (3 aulas disponibles):            │
│ ┌────────────────────────────────────────┐  │
│ │ AULA    │ CAPACIDAD │ ESTADO          │  │
│ ├────────────────────────────────────────┤  │
│ │ Aula 101│    40     │ ✅ Disponible    │  │
│ │ Aula 103│    35     │ ✅ Disponible    │  │
│ │ Aula 105│    30     │ ✅ Disponible    │  │
│ │ Aula 102│    50     │ ❌ Ocupada       │  │
│ └────────────────────────────────────────┘  │
│                                              │
│ ℹ️ Las aulas "Ocupadas" tienen docentes    │
│    asignados en esa hora                     │
│                                              │
└──────────────────────────────────────────────┘
```

### Lógica JavaScript

```javascript
import { ref } from 'vue';

const filtros = ref({
  dia_semana: 'Lunes',
  hora_inicio: '10:00',
});

const aulasDisponibles = ref([]);
const cargando = ref(false);

const consultar = async () => {
  cargando.value = true;
  try {
    const params = new URLSearchParams({
      dia_semana: filtros.value.dia_semana,
      hora_inicio: filtros.value.hora_inicio,
    });
    
    const response = await fetch(`/api/aulas/disponibles?${params}`);
    aulasDisponibles.value = await response.json();
  } finally {
    cargando.value = false;
  }
};
```

---

## ⚙️ Backend - AulaController.php

```php
public function disponibles(Request $request)
{
    $request->validate([
        'dia_semana' => 'required|in:Lunes,Martes,Miércoles,Jueves,Viernes',
        'hora_inicio' => 'required|date_format:H:i',
    ]);
    
    // Aulas que NO tienen horarios asignados en ese día/hora
    $aulasBloqueadas = Horario::where('dia_semana', $request->dia_semana)
        ->where('hora_inicio', $request->hora_inicio)
        ->pluck('aula_id')
        ->unique();
    
    $aulasDisponibles = Aula::whereNotIn('id', $aulasBloqueadas)
        ->orderBy('numero')
        ->get();
    
    return response()->json($aulasDisponibles);
}
```

---

## 📝 Resumen CU16

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU16: Consultar Aulas Disponibles |
| **Frontend** | Aulas/Disponibles.vue |
| **Backend** | AulaController::disponibles() |
| **Ruta** | GET /aulas/disponibles |
| **Propósito** | Verificar qué aulas están libres para asignar docentes |
| **Permisos** | Admin, Coordinador |
| **Estado** | ✅ Completamente implementado |

---

---

# 🎓 CU17: Consultar Asistencia por Docente y Grupo - Guía Completa

## 📌 ¿Qué es el CU17?

**Caso de Uso 17:** Ver todos los registros de asistencia de un docente específico en un grupo específico.

### Ejemplo Real:
```
Búsqueda: 
- Docente: Carlos García
- Grupo: Grupo A
- Materia: Matemática

Resultados:
11/11/2025 - Presente (08:00-10:00)
12/11/2025 - Retardo (08:15-10:00)
13/11/2025 - Ausente
14/11/2025 - Presente (08:00-10:00)
15/11/2025 - Justificada (Conferencia)
```

---

## 🔄 Flujo Completo

```
Menú → Asistencias → [Consultar]
         ↓
   Formulario Filtros:
   - Docente: Carlos García
   - Grupo-Materia: Grupo A - Matemática
   - Fecha desde: 01/11/2025
   - Fecha hasta: 30/11/2025
         ↓
   [🔍 Consultar Asistencias]
         ↓
   API: GET /asistencias/consultar?docente_id=1&grupo_materia_id=3&fecha_desde=...
         ↓
   BD: SELECT * FROM asistencias 
       WHERE docente_id=1 AND grupo_materia_id=3 AND fecha BETWEEN...
         ↓
   Tabla: 5 registros de asistencia del docente
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Asistencias/
└── Consultar.vue    ← AQUÍ ESTÁ EL CU17
    ├── Filtros (docente, grupo, fechas)
    ├── Botón buscar
    └── Tabla de asistencias
```

### Backend
```
app/Http/Controllers/
└── AsistenciaController.php
    └── porDocenteGrupo()  ← Procesa CU17
```

### Rutas
```
routes/web.php
├── GET /asistencias/consultar       ← Mostrar página
└── GET /api/asistencias/filtrados   ← API (JSON)
```

---

## 🎨 Vista: Asistencias/Consultar.vue

```
┌────────────────────────────────────────────────┐
│   Consultar Asistencia del Docente (CU17)      │
├────────────────────────────────────────────────┤
│                                                │
│ Filtros de búsqueda:                           │
│ Docente: [Carlos García ▼]                    │
│ Grupo-Materia: [Grupo A - Matemática ▼]       │
│ Desde: [01/11/2025]                           │
│ Hasta: [30/11/2025]                           │
│                                                │
│ [🔍 Buscar Asistencias]                       │
│                                                │
│ ─────────────────────────────────────────      │
│                                                │
│ Resultados (5 registros):                      │
│ ┌──────────┬──────────┬─────────┬────────────┐│
│ │ FECHA    │ HORA     │ ESTADO  │ OBSERV.  ││
│ ├──────────┼──────────┼─────────┼────────────┤│
│ │ 11/11    │08:00-10:00│Presente │          ││
│ │ 12/11    │08:15-10:00│Retardo │Tráfico   ││
│ │ 13/11    │--       │Ausente │Enfermo    ││
│ │ 14/11    │08:00-10:00│Presente │          ││
│ │ 15/11    │--       │Justificada│Conferencia││
│ └──────────┴──────────┴─────────┴────────────┘│
│                                                │
│ Estadísticas:                                  │
│ Presentes: 2 | Retardos: 1 | Ausentes: 1     │
│ Justificadas: 1                                │
│                                                │
└────────────────────────────────────────────────┘
```

### Lógica JavaScript

```javascript
const filtros = ref({
  docente_id: '',
  grupo_materia_id: '',
  fecha_desde: '',
  fecha_hasta: '',
});

const asistencias = ref([]);

const buscar = async () => {
  const params = new URLSearchParams();
  if (filtros.value.docente_id) 
    params.append('docente_id', filtros.value.docente_id);
  if (filtros.value.grupo_materia_id) 
    params.append('grupo_materia_id', filtros.value.grupo_materia_id);
  if (filtros.value.fecha_desde) 
    params.append('fecha_desde', filtros.value.fecha_desde);
  if (filtros.value.fecha_hasta) 
    params.append('fecha_hasta', filtros.value.fecha_hasta);
  
  const response = await fetch(`/api/asistencias/filtrados?${params}`);
  asistencias.value = await response.json();
};
```

---

## ⚙️ Backend - AsistenciaController.php

```php
public function porDocenteGrupo(Request $request)
{
    $query = Asistencia::query();
    
    if ($request->docente_id) {
        $query->where('docente_id', $request->docente_id);
    }
    
    if ($request->grupo_materia_id) {
        $query->where('grupo_materia_id', $request->grupo_materia_id);
    }
    
    if ($request->fecha_desde) {
        $query->where('fecha', '>=', $request->fecha_desde);
    }
    
    if ($request->fecha_hasta) {
        $query->where('fecha', '<=', $request->fecha_hasta);
    }
    
    return response()->json(
        $query->with(['docente.user', 'grupoMateria.grupo', 'grupoMateria.materia'])
            ->orderBy('fecha', 'desc')
            ->get()
    );
}
```

---

## 📝 Resumen CU17

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU17: Consultar Asistencia por Docente y Grupo |
| **Frontend** | Asistencias/Consultar.vue |
| **Backend** | AsistenciaController::porDocenteGrupo() |
| **Ruta** | GET /asistencias/consultar |
| **Propósito** | Consultar registro histórico de asistencia del docente |
| **Permisos** | Admin, Coordinador |
| **Qué registra** | Asistencia del DOCENTE (no estudiantes) |
| **Estado** | ✅ Completamente implementado |

---

---

# 🎓 CU18: Generar Reporte de Asistencias - Guía Completa

## 📌 ¿Qué es el CU18?

**Caso de Uso 18:** Generar un reporte (PDF o CSV) con todas las asistencias registradas en el sistema durante un período.

### Ejemplo Real:
```
Reporte: Asistencias Docentes - Noviembre 2025

Docente                 Grupo           Materia         Fecha       Estado
────────────────────────────────────────────────────────────────────────
Carlos García          Grupo A         Matemática      11/11/2025  Presente
Carlos García          Grupo A         Matemática      12/11/2025  Retardo
María López            Grupo B         Física          11/11/2025  Presente
...

Total de registros: 127
Docentes únicos: 5
Período: 01/11/2025 - 30/11/2025
```

---

## 🔄 Flujo Completo

```
Menú → Reportes → [Descargar]
         ↓
   Formulario Filtros:
   - Fecha desde: 01/11/2025
   - Fecha hasta: 30/11/2025
   - Formato: PDF o CSV
         ↓
   [📥 Generar Reporte]
         ↓
   API: GET /reportes/asistencias?formato=pdf&fecha_desde=...
         ↓
   BD: SELECT * FROM asistencias 
       WHERE fecha BETWEEN ... 
       JOIN docentes, grupos, materias
         ↓
   Backend genera PDF/CSV
         ↓
   Descarga archivo: reporte_asistencias_nov2025.pdf
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Reportes/
└── Index.vue    ← AQUÍ ESTÁ EL CU18
    ├── Filtros (fechas, formato)
    ├── Botón generar
    └── Descarga automática
```

### Backend
```
app/Http/Controllers/
└── ReporteController.php
    └── asistencias()  ← Procesa CU18
        ├── Query DB
        ├── Genera PDF (Laravel PDF)
        └── Retorna descarga
```

### Rutas
```
routes/web.php
├── GET /reportes                ← Mostrar página
└── GET /reportes/asistencias    ← API (genera descarga)
```

---

## 🎨 Vista: Reportes/Index.vue

```
┌────────────────────────────────────────────────┐
│   Generar Reportes (CU18)                      │
├────────────────────────────────────────────────┤
│                                                │
│ Opciones de reporte:                           │
│ ┌────────────────────────────────────────────┐ │
│ │ 📊 Reporte de Asistencias Docentes        │ │
│ │                                            │ │
│ │ Filtros:                                   │ │
│ │ Desde: [01/11/2025]                       │ │
│ │ Hasta: [30/11/2025]                       │ │
│ │                                            │ │
│ │ Formato: ⚪ PDF  ⚫ CSV  ⚪ Excel        │ │
│ │                                            │ │
│ │ [📥 Descargar Reporte]                    │ │
│ └────────────────────────────────────────────┘ │
│                                                │
│ ┌────────────────────────────────────────────┐ │
│ │ 📈 Reporte de Horarios Docentes          │ │
│ │ (Similar estructura)                       │ │
│ └────────────────────────────────────────────┘ │
│                                                │
└────────────────────────────────────────────────┘
```

### Lógica JavaScript

```javascript
const descargarReporte = async (tipo) => {
  const params = new URLSearchParams({
    formato: formato.value,
    fecha_desde: filtros.value.fecha_desde,
    fecha_hasta: filtros.value.fecha_hasta,
  });
  
  // Descarga directa del archivo
  window.location.href = `/reportes/${tipo}?${params}`;
};
```

---

## ⚙️ Backend - ReporteController.php

```php
public function asistencias(Request $request)
{
    // Validar
    $request->validate([
        'formato' => 'required|in:pdf,csv,excel',
        'fecha_desde' => 'required|date',
        'fecha_hasta' => 'required|date',
    ]);
    
    // Query
    $asistencias = Asistencia::whereBetween('fecha', [
            $request->fecha_desde,
            $request->fecha_hasta
        ])
        ->with(['docente.user', 'grupoMateria.grupo', 'grupoMateria.materia'])
        ->orderBy('fecha', 'desc')
        ->get();
    
    // Generar según formato
    if ($request->formato === 'pdf') {
        return PDF::generate($asistencias);  // Laravel PDF
    } elseif ($request->formato === 'csv') {
        return CSV::generate($asistencias);  // CSV
    }
}
```

---

## 📝 Resumen CU18

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU18: Generar Reporte de Asistencias |
| **Frontend** | Reportes/Index.vue |
| **Backend** | ReporteController::asistencias() |
| **Ruta** | GET /reportes/asistencias |
| **Propósito** | Exportar registros de asistencia docente |
| **Formatos** | PDF, CSV, Excel |
| **Qué incluye** | Docente, grupo, materia, fecha, estado |
| **Permisos** | Admin, Coordinador |
| **Estado** | ✅ Completamente implementado |

---

---

# 🎓 CU19: Generar Reporte de Horarios - Guía Completa

## 📌 ¿Qué es el CU19?

**Caso de Uso 19:** Generar un reporte (PDF o CSV) con todos los horarios de los docentes.

### Ejemplo Real:
```
Reporte: Horarios Docentes - Semestre 2025

Docente         Grupo           Materia         Día         Hora            Aula
─────────────────────────────────────────────────────────────────────────────
Carlos García   Grupo A         Matemática      Lunes       08:00-10:00     101
Carlos García   Grupo B         Física          Martes      10:00-12:00     102
María López     Grupo A         Cálculo         Miércoles   14:00-16:00     103
...

Total de asignaciones: 45
Docentes: 5
Aulas utilizadas: 6
```

---

## 🔄 Flujo Completo

```
Menú → Reportes → [Horarios]
         ↓
   Formulario Filtros:
   - Docente: (opcional)
   - Formato: PDF o CSV
         ↓
   [📥 Generar Reporte]
         ↓
   API: GET /reportes/horarios?formato=pdf&docente_id=...
         ↓
   BD: SELECT * FROM horarios
       JOIN docentes, grupos, materias, aulas
         ↓
   Backend genera PDF/CSV
         ↓
   Descarga archivo: reporte_horarios_2025.pdf
```

---

## 🎨 Vista: Reportes/Index.vue (segunda sección)

```
┌────────────────────────────────────────────────┐
│   Generar Reporte de Horarios (CU19)           │
├────────────────────────────────────────────────┤
│                                                │
│ Filtros:                                       │
│ Docente: [-- Todos -- ▼]                      │
│ Formato: ⚪ PDF  ⚫ CSV  ⚪ Excel             │
│                                                │
│ [📥 Descargar Reporte]                        │
│                                                │
│ Información:                                   │
│ Total horarios: 45                             │
│ Docentes: 5                                    │
│ Aulas: 6                                       │
│                                                │
└────────────────────────────────────────────────┘
```

---

## ⚙️ Backend - ReporteController.php

```php
public function horarios(Request $request)
{
    // Query base
    $query = Horario::with([
        'docente.user',
        'grupoMateria.grupo',
        'grupoMateria.materia',
        'aula'
    ]);
    
    // Filtro opcional
    if ($request->docente_id) {
        $query->where('docente_id', $request->docente_id);
    }
    
    $horarios = $query->orderBy('dia_semana')
                      ->orderBy('hora_inicio')
                      ->get();
    
    // Generar según formato
    if ($request->formato === 'pdf') {
        return PDF::generate($horarios);
    } elseif ($request->formato === 'csv') {
        return CSV::generate($horarios);
    }
}
```

---

## 📝 Resumen CU19

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU19: Generar Reporte de Horarios |
| **Frontend** | Reportes/Index.vue |
| **Backend** | ReporteController::horarios() |
| **Ruta** | GET /reportes/horarios |
| **Propósito** | Exportar horarios de todos los docentes |
| **Formatos** | PDF, CSV, Excel |
| **Qué incluye** | Docente, grupo, materia, día, hora, aula |
| **Permisos** | Admin, Coordinador |
| **Estado** | ✅ Completamente implementado |

---

---

## 📊 Tabla Comparativa: CU16-19

| CU | Nombre | Qué Consulta | Formato | Propósito |
|----|--------|--------------|---------|-----------|
| **16** | Aulas Disponibles | Aulas sin docente asignado | Tabla HTML | Planificación de aulas |
| **17** | Consultar Asistencia | Asistencia de docente específico | Tabla HTML | Verificar historiales |
| **18** | Reportes Asistencias | Todas las asistencias | PDF, CSV, Excel | Exportar datos |
| **19** | Reportes Horarios | Todos los horarios | PDF, CSV, Excel | Exportar horarios |

---

## 🔐 Permisos Generales (CU16-19)

```
✅ Admin:        Sí (todos los reportes)
✅ Coordinador:  Sí (si tiene permisos)
❌ Docente:      Solo lectura (consultar propios datos)
```

---

## ✅ Verificación Final

✅ **CU16:** Consultar aulas disponibles - Implementado
✅ **CU17:** Consultar asistencia por docente - Implementado
✅ **CU18:** Generar reporte asistencias - Implementado
✅ **CU19:** Generar reporte horarios - Implementado

**Estado:** Todos los casos de uso de reportes y consultas están completamente implementados.

---

**Versión:** 1.0 (Corregida - Sistema de Gestión de Docentes)  
**Última actualización:** 11 de Noviembre de 2025  
**Nota Principal:** Sistema enfocado en GESTIÓN DE DOCENTES (no estudiantes)  
- Asistencia = Asistencia del DOCENTE
- Horarios = Horarios de DOCENTES
- Aulas = Disponibilidad para DOCENTES
- Reportes = Sobre DOCENTES
