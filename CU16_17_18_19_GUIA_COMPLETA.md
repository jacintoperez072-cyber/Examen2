# 🎓 CU16: Consultar Aulas Disponibles - Guía Completa

## 📌 ¿Qué es el CU16?

**Caso de Uso 16:** Filtrar y consultar qué aulas están disponibles en un día, hora y con capacidad mínima específicos.

### Ejemplo Real:
```
Búsqueda: Lunes, 10:00, capacidad mínima 30
Resultado:
- Aula 101: Disponible, 40 estudiantes
- Aula 103: Disponible, 35 estudiantes
- Aula 105: NO disponible (ocupada 10:00-12:00)
```

---

## 🔄 Flujo Completo

```
Menú → Aulas → [Disponibles]
         ↓
   Formulario Filtros:
   - Día: Lunes
   - Hora: 10:00
   - Capacidad: 30
         ↓
   [🔍 Buscar]
         ↓
   API: GET /aulas/disponibles?dia_semana=Lunes&hora_inicio=10:00&capacidad_minima=30
         ↓
   BD: SELECT * FROM aulas WHERE disponible Y capacidad >= 30
         ↓
   Tabla: 2 aulas disponibles
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Aulas/
└── Disponibles.vue    ← AQUÍ ESTÁ EL CU16
    ├── Filtros (día, hora, capacidad)
    ├── Botón buscar
    └── Tabla de resultados
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
├── GET /aulas-disponibles          ← Mostrar página
└── GET /aulas/disponibles          ← API (JSON)
```

---

## 🎨 Vista: Aulas/Disponibles.vue

```
┌──────────────────────────────────────────────┐
│      Aulas Disponibles (CU16)                │
├──────────────────────────────────────────────┤
│                                              │
│ Filtros:                                     │
│ Día: [Lunes ▼]                              │
│ Hora Inicio: [10:00]                        │
│ Capacidad Mínima: [30]                      │
│                                              │
│ [🔍 Buscar]                                 │
│                                              │
│ ─────────────────────────────────────────    │
│                                              │
│ Resultados (2 aulas disponibles):            │
│ ┌──────────────────────────────────────┐    │
│ │ AULA    │ CAPACIDAD │ ESTADO        │    │
│ ├──────────────────────────────────────┤    │
│ │ Aula 101│    40     │ ✅ Disponible │    │
│ │ Aula 103│    35     │ ✅ Disponible │    │
│ └──────────────────────────────────────┘    │
│                                              │
└──────────────────────────────────────────────┘
```

### Lógica
```javascript
const consultar = async () => {
  const params = new URLSearchParams({
    dia_semana: filtros.dia_semana,
    hora_inicio: filtros.hora_inicio,
    capacidad_minima: filtros.capacidad_minima,
  });
  
  const response = await fetch(`/aulas/disponibles?${params}`);
  aulasDisponibles.value = await response.json();
};
```

---

## ⚙️ Backend - AulaController.php

```php
public function disponibles(Request $request)
{
    $query = Aula::query();
    
    if ($request->dia_semana) {
        $query->whereDoesntHave('horarios', function ($q) use ($request) {
            $q->where('dia_semana', $request->dia_semana)
              ->where('hora_inicio', $request->hora_inicio);
        });
    }
    
    if ($request->capacidad_minima) {
        $query->where('capacidad', '>=', $request->capacidad_minima);
    }
    
    return response()->json($query->get());
}
```

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU16: Consultar Aulas Disponibles |
| **Frontend** | Aulas/Disponibles.vue |
| **Backend** | AulaController::disponibles() |
| **Ruta** | GET /aulas/disponibles |
| **Permisos** | Todos |
| **Validaciones** | Día válido, hora válida |
| **Estado** | ✅ Completamente implementado |

---

# 🎓 CU17: Consultar Asistencia por Docente y Grupo - Guía Completa

## 📌 ¿Qué es el CU17?

**Caso de Uso 17:** Filtrar asistencias de un docente específico en un grupo específico.

### Ejemplo Real:
```
Búsqueda: Docente "Carlos García", Grupo "Grupo A"
Resultado: Todos los registros de asistencia de
Carlos García enseñando al Grupo A
- 11/11: 29/30 presentes
- 12/11: 28/30 presentes
- 13/11: 30/30 presentes
```

---

## 🔄 Flujo Completo

```
Menú → Asistencias → [Consultar]
         ↓
   Selectors:
   - Docente: [Carlos García ▼]
   - Grupo: [Grupo A ▼]
         ↓
   [🔍 Buscar]
         ↓
   API: GET /asistencias/por-docente-grupo?docente_id=1&grupo_id=2
         ↓
   Tabla: Registros de asistencia filtrados
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Asistencias/
└── Consultar.vue      ← AQUÍ ESTÁ EL CU17
    ├── Selector docente
    ├── Selector grupo
    ├── Botón buscar
    └── Tabla resultados
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
├── GET /asistencias-consultar           ← Mostrar página
└── GET /asistencias/por-docente-grupo   ← API (JSON)
```

---

## 🎨 Vista: Asistencias/Consultar.vue

```
┌─────────────────────────────────────────────┐
│   Consultar Asistencia por Doc/Grupo (CU17) │
├─────────────────────────────────────────────┤
│                                             │
│ Filtros:                                    │
│ Docente: [Carlos García ▼]                 │
│ Grupo: [Grupo A ▼]                         │
│                                             │
│ [🔍 Buscar]                                │
│                                             │
│ Resultados:                                 │
│ ┌──────────────────────────────────────┐   │
│ │ ESTUDIANTE │ FECHA      │ PRESENTE  │   │
│ ├──────────────────────────────────────┤   │
│ │ Juan Pérez │ 11/11/2025 │ ✅ Sí     │   │
│ │ María López│ 11/11/2025 │ ✅ Sí     │   │
│ │ Pedro Grp. │ 11/11/2025 │ ❌ No     │   │
│ │ Ana Martín │ 12/11/2025 │ ✅ Sí     │   │
│ └──────────────────────────────────────┘   │
│                                             │
└─────────────────────────────────────────────┘
```

### Lógica
```javascript
const consultar = async () => {
  const response = await fetch(
    `/asistencias/por-docente-grupo?docente_id=${filtros.docente_id}&grupo_id=${filtros.grupo_id}`
  );
  asistencias.value = await response.json();
};
```

---

## ⚙️ Backend - AsistenciaController.php

```php
public function porDocenteGrupo(Request $request)
{
    $asistencias = Asistencia::query()
        ->where('docente_id', $request->docente_id)
        ->whereHas('grupoMateria', function ($q) use ($request) {
            $q->where('grupo_id', $request->grupo_id);
        })
        ->with('grupoMateria.grupo', 'grupoMateria.materia')
        ->get();
    
    return response()->json($asistencias);
}
```

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **CU** | CU17: Consultar Asistencia Docente/Grupo |
| **Frontend** | Asistencias/Consultar.vue |
| **Backend** | AsistenciaController::porDocenteGrupo() |
| **Ruta** | GET /asistencias/por-docente-grupo |
| **Permisos** | Docente, Admin |
| **Validaciones** | Docente existe, grupo existe |
| **Estado** | ✅ Completamente implementado |

---

# 🎓 CU18 & CU19: Reportes (PDF y Excel) - Guía Completa

## 📌 ¿Qué son CU18 y CU19?

**CU18:** Generar reporte de asistencias en PDF  
**CU19:** Generar reporte de asistencias en Excel

### Ejemplo Real:
```
PDF: Documento con logo, título, tabla de asistencias
Excel: Archivo .xlsx con datos filtrados
```

---

## 🔄 Flujo Completo

```
Menú → Reportes
         ↓
   [📄 PDF - Asistencias]    [📊 Excel - Asistencias]
   [📄 PDF - Bitácora]       [📊 Excel - Bitácora]
         ↓
   Click botón
         ↓
   API: GET /reportes/asistencia-pdf
              (o /asistencia-excel)
         ↓
   Backend genera archivo
         ↓
   Descarga archivo en navegador
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Reportes/
└── Index.vue    ← AQUÍ ESTÁN CU18 y CU19
    ├── 4 botones
    └── Manejo de blobs (descarga)
```

### Backend
```
app/Http/Controllers/
└── ReporteController.php
    ├── asistenciaPdf()     ← CU18
    ├── asistenciaExcel()   ← CU19
    ├── bitacoraPdf()       ← CU18 (bitácora)
    └── bitacoraExcel()     ← CU19 (bitácora)
```

### Rutas
```
routes/web.php
├── GET /reportes                    ← Mostrar página
├── GET /reportes/asistencia-pdf    ← CU18 API
├── GET /reportes/asistencia-excel  ← CU19 API
├── GET /reportes/bitacora-pdf      ← CU18 API
└── GET /reportes/bitacora-excel    ← CU19 API
```

---

## 🎨 Vista: Reportes/Index.vue

```
┌────────────────────────────────────────────────┐
│           Reportes (CU18, CU19)                │
├────────────────────────────────────────────────┤
│                                                │
│ ┌──────────────────────────────────────────┐  │
│ │ 📄 Reporte de Asistencia (PDF)          │  │
│ │ Exportar en formato PDF                 │  │
│ │ [📥 Descargar PDF]  [Bitácora PDF]     │  │
│ └──────────────────────────────────────────┘  │
│                                                │
│ ┌──────────────────────────────────────────┐  │
│ │ 📊 Reporte de Asistencia (Excel)        │  │
│ │ Exportar en formato Excel                │  │
│ │ [📥 Descargar Excel] [Bitácora Excel]  │  │
│ └──────────────────────────────────────────┘  │
│                                                │
│ ✅ Descarga completada                       │
│                                                │
└────────────────────────────────────────────────┘
```

### Lógica
```javascript
const descargarAsistenciaPDF = async () => {
  const response = await fetch('/reportes/asistencia-pdf');
  const blob = await response.blob();
  const url = window.URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'asistencias.pdf';
  a.click();
};
```

---

## ⚙️ Backend - ReporteController.php

### PDF
```php
public function asistenciaPdf()
{
    $this->authorize('view', 'reportes');
    
    $asistencias = Asistencia::with('grupoMateria', 'docente')->get();
    
    // Generar PDF con DomPDF
    $pdf = Pdf::loadView('reportes.asistencia-pdf', [
        'asistencias' => $asistencias,
    ]);
    
    return $pdf->download('asistencias.pdf');
}
```

### Excel
```php
public function asistenciaExcel()
{
    $this->authorize('view', 'reportes');
    
    return Excel::download(
        new AsistenciasExport,
        'asistencias.xlsx'
    );
}
```

---

## 🎨 Vista: Reportes/Index.vue Completo

```vue
<template>
  <AuthenticatedLayout>
    <template #header>
      <h2>Reportes</h2>
    </template>

    <div class="py-12">
      <div class="max-w-4xl mx-auto">
        <div class="bg-white rounded shadow-sm p-6">
          <h3 class="text-lg font-semibold mb-6">Generar Reportes</h3>

          <!-- CU18: PDF -->
          <div class="mb-6 p-4 border rounded">
            <h4 class="font-medium mb-3">📄 Reporte de Asistencia (PDF)</h4>
            <div class="flex gap-2">
              <button 
                @click="descargarAsistenciaPDF" 
                :disabled="cargando"
                class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 disabled:bg-gray-400"
              >
                {{ cargando ? "Generando..." : "📥 Descargar PDF" }}
              </button>
              <button 
                @click="descargarBitacoraPDF" 
                :disabled="cargando"
                class="px-4 py-2 bg-red-600 text-white rounded hover:bg-red-700 disabled:bg-gray-400"
              >
                Bitácora PDF
              </button>
            </div>
          </div>

          <!-- CU19: Excel -->
          <div class="mb-6 p-4 border rounded">
            <h4 class="font-medium mb-3">📊 Reporte de Asistencia (Excel)</h4>
            <div class="flex gap-2">
              <button 
                @click="descargarAsistenciaExcel" 
                :disabled="cargando"
                class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700 disabled:bg-gray-400"
              >
                {{ cargando ? "Generando..." : "📥 Descargar Excel" }}
              </button>
              <button 
                @click="descargarBitacoraExcel" 
                :disabled="cargando"
                class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700 disabled:bg-gray-400"
              >
                Bitácora Excel
              </button>
            </div>
          </div>

          <div v-if="mensaje" class="mt-4 p-4 rounded" :class="mensaje.tipo === 'success' ? 'bg-green-100 text-green-700' : 'bg-red-100 text-red-700'">
            {{ mensaje.texto }}
          </div>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import { ref, reactive } from 'vue';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';

const cargando = ref(false);
const mensaje = reactive({ tipo: '', texto: '' });

// Funciones de descarga...
const descargarAsistenciaPDF = async () => {
  await descargar('/reportes/asistencia-pdf', 'asistencias.pdf');
};

const descargarAsistenciaExcel = async () => {
  await descargar('/reportes/asistencia-excel', 'asistencias.xlsx');
};

const descargar = async (url, filename) => {
  cargando.value = true;
  try {
    const response = await fetch(url);
    const blob = await response.blob();
    const link = document.createElement('a');
    link.href = window.URL.createObjectURL(blob);
    link.download = filename;
    link.click();
    mensaje.tipo = 'success';
    mensaje.texto = '✅ Descargado: ' + filename;
  } catch (error) {
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};
</script>
```

---

## 📝 Resumen CU18 & CU19

| Aspecto | CU18 | CU19 |
|--------|------|------|
| **Caso de Uso** | Reporte PDF | Reporte Excel |
| **Frontend** | Reportes/Index.vue | Reportes/Index.vue |
| **Backend** | ReporteController::asistenciaPdf() | ReporteController::asistenciaExcel() |
| **Ruta** | GET /reportes/asistencia-pdf | GET /reportes/asistencia-excel |
| **Formato** | PDF | XLSX |
| **Permisos** | Admin | Admin |
| **Estado** | ✅ Implementado | ✅ Implementado |

---

**Versión:** 1.0 | **Última actualización:** 11 de Noviembre de 2025 | **Estado:** ✅ Listo
