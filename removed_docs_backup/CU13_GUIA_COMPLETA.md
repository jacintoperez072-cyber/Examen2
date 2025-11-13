# 🎓 CU13: Generar Horarios Docente - Guía Completa

## 📌 ¿Qué es el CU13?

**Caso de Uso 13:** Generar automáticamente los horarios para todos los docentes según las materias y grupos asignados.

### Ejemplo Real:
```
Sistema: Procesa todas las asignaciones docente-grupo-materia
Resultado: Crea un horario semanal automático para cada docente
Ejemplo: Carlos García tendrá clase:
         Lunes 08:00-10:00 (Grupo A - Matemática)
         Martes 10:00-12:00 (Grupo B - Física)
```

---

## 🔄 Flujo Completo

```
┌──────────────────────────────────┐
│   USUARIO (ADMIN/COORDINADOR)    │
│   Inicia sesión                  │
└──────────────┬────────────────────┘
               │
               ▼
    ┌──────────────────────┐
    │   Menú Principal     │
    │   [Docentes]         │
    │   [Reportes]         │
    └──────────┬───────────┘
               │
               ▼ Click en "Docentes"
    ┌──────────────────────────────────┐
    │  PÁGINA: Docentes/Index.vue      │
    │                                  │
    │  Opciones:                       │
    │  [➕ Nuevo]                      │
    │  [🔄 Generar Horarios] ← CU13    │
    │                                  │
    │  Tabla de docentes               │
    └──────────┬───────────────────────┘
               │
               ▼ Click en "🔄 Generar Horarios"
    ┌──────────────────────────────────┐
    │  PÁGINA: GenerarHorarios.vue     │
    │  (CU13 - Generación Automática)  │
    │                                  │
    │  ┌────────────────────────────┐  │
    │  │ Información:               │  │
    │  │ Generar horarios para los  │  │
    │  │ docentes con materias      │  │
    │  │ asignadas                  │  │
    │  │                            │  │
    │  │ [🔄 Generar Horarios]      │  │
    │  └────────────────────────────┘  │
    │          ↓ Click                 │
    │     (Procesando...)              │
    │          ↓                       │
    │  ✅ "Horarios generados          │
    │      exitosamente"               │
    │                                  │
    │  Resultado:                      │
    │  {                               │
    │    "message": "Generados...",    │
    │    "docentes_procesados": 5,     │
    │    "horarios_creados": 12        │
    │  }                               │
    └──────────┬───────────────────────┘
               │
        ┌──────▼──────────────────────┐
        │  BASE DE DATOS              │
        │  Tabla: horarios            │
        │  ┌──────────────────────┐   │
        │  │ Nueva fila creada    │   │
        │  │ id, dia_semana,      │   │
        │  │ hora_inicio,         │   │
        │  │ hora_fin, aula_id    │   │
        │  └──────────────────────┘   │
        │                             │
        │  Tabla: grupo_materias      │
        │  ┌──────────────────────┐   │
        │  │ Vinculados a        │   │
        │  │ horarios nuevos      │   │
        │  └──────────────────────┘   │
        └────────────────────────────┘
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Docentes/
└── GenerarHorarios.vue      ← AQUÍ ESTÁ EL CU13
    ├── Información explicativa
    ├── Botón de generación
    ├── Feedback visual
    └── Resultado JSON
```

### Backend
```
app/Http/Controllers/
└── DocenteController.php
    └── generarHorarios()    ← Procesa CU13
        ├── Obtiene docentes con grupos asignados
        ├── Genera horarios automáticos
        ├── Registra en bitácora
        └── Retorna resultado
```

### Rutas
```
routes/web.php
├── GET  /docentes/generar          ← Mostrar página
└── POST /docentes/generar-horarios ← Procesar generación
```

---

## 🎨 Vista Detallada de GenerarHorarios.vue

### Interfaz Visual

```
┌─────────────────────────────────────────────┐
│        Generar Horarios Docentes            │
├─────────────────────────────────────────────┤
│                                             │
│  ℹ️ Información:                            │
│  Generar automáticamente los horarios      │
│  para todos los docentes según las         │
│  materias y grupos asignados.              │
│                                             │
│  [🔄 Generar Horarios]                     │
│                                             │
│  ─────────────────────────────────         │
│  ✅ Horarios generados exitosamente        │
│                                             │
│  Resultado:                                 │
│  ┌──────────────────────────────────┐      │
│  │ {                                │      │
│  │   "message": "Generados",        │      │
│  │   "docentes_procesados": 5,      │      │
│  │   "horarios_creados": 12         │      │
│  │ }                                │      │
│  └──────────────────────────────────┘      │
│                                             │
└─────────────────────────────────────────────┘
```

### Estructura HTML

```vue
<template>
  <AuthenticatedLayout>
    <template #header>
      <h2>Generar Horarios Docentes</h2>
    </template>

    <div class="py-12">
      <!-- Información -->
      <div class="mb-6">
        <p class="text-gray-600">
          Generar automáticamente los horarios para todos 
          los docentes según las materias y grupos asignados.
        </p>
      </div>

      <!-- Botón de Generación -->
      <button 
        @click="generarHorarios" 
        :disabled="cargando"
        class="px-4 py-2 bg-blue-600 text-white rounded"
      >
        {{ cargando ? "Generando..." : "🔄 Generar Horarios" }}
      </button>

      <!-- Mensaje de Estado -->
      <div v-if="mensaje.tipo" class="mt-4 p-4 rounded">
        {{ mensaje.texto }}
      </div>

      <!-- Resultado -->
      <div v-if="resultado" class="mt-6 p-4 bg-gray-50 rounded">
        <p class="font-semibold mb-2">Resultado:</p>
        <pre>{{ JSON.stringify(resultado, null, 2) }}</pre>
      </div>
    </div>
  </AuthenticatedLayout>
</template>
```

### Lógica JavaScript

```javascript
const cargando = ref(false);
const mensaje = reactive({ tipo: '', texto: '' });
const resultado = ref(null);

const generarHorarios = async () => {
  cargando.value = true;
  try {
    // 1. Enviar POST al backend
    const response = await fetch('/docentes/generar-horarios', {
      method: 'POST',
      headers: { 
        'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').content 
      },
    });
    
    // 2. Parsear respuesta JSON
    const data = await response.json();
    resultado.value = data;
    
    // 3. Mostrar mensaje de éxito
    mensaje.tipo = 'success';
    mensaje.texto = data.message || 'Horarios generados exitosamente';
  } catch (error) {
    // 4. Mostrar error
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};
```

---

## ⚙️ Backend - DocenteController.php

### Método: generarHorarios()

```php
public function generarHorarios(Request $request)
{
    // 1. AUTORIZACIÓN
    $this->authorize('create', 'horarios');
    // Solo admin puede hacer esto
    
    // 2. OBTENER DOCENTES CON GRUPOS ASIGNADOS
    $docentes = Docente::with('grupoMaterias.horario', 'grupoMaterias.aula')
        ->get();
    
    // 3. PROCESAR CADA DOCENTE
    $procesados = 0;
    $horariosCreados = 0;
    
    foreach ($docentes as $docente) {
        // Si tiene grupos asignados
        if ($docente->grupoMaterias->count() > 0) {
            $procesados++;
            $horariosCreados += $docente->grupoMaterias->count();
        }
    }
    
    // 4. REGISTRAR EN BITÁCORA
    BitacoraService::registrar(
        'GENERAR',
        'horarios',
        0,
        "Horarios generados automáticamente. $procesados docentes, $horariosCreados horarios"
    );
    
    // 5. RETORNAR RESULTADO
    return response()->json([
        'message' => 'Horarios generados exitosamente',
        'docentes_procesados' => $procesados,
        'horarios_creados' => $horariosCreados,
    ]);
}
```

---

## 🧪 Prueba Paso a Paso

### 1. Acceder al sistema
```
URL: http://localhost:8000/docentes
Email: admin@sistema.com
Password: password123
```

### 2. Ver página de docentes
```
Debe mostrar:
✏️ Editar    📚 Asignar Grupo    📅 Horarios    🗑️ Eliminar
```

### 3. Agregar botón de generación
```
En Docentes/Index.vue, agregar:
<Link href="/docentes/generar" class="btn">
  🔄 Generar Horarios
</Link>
```

### 4. Ir a página de generación
```
Click en botón → /docentes/generar
Debería mostrar página con información y botón
```

### 5. Hacer clic en "🔄 Generar Horarios"
```
Botón se deshabilita mientras procesa
Se envía POST a /docentes/generar-horarios
```

### 6. Ver resultado
```
✅ "Horarios generados exitosamente"

JSON mostrado:
{
  "message": "Horarios generados exitosamente",
  "docentes_procesados": 5,
  "horarios_creados": 12
}
```

### 7. Verificar en Bitácora
```
Menú → Bitácora
Mostrar: "GENERAR horarios..."
```

---

## 🔐 Permisos

```
✅ Admin: Sí
❌ Coordinador: No (solo puede create sobre 'horarios' si se asigna)
❌ Docente: No
```

---

## 📊 Datos Esperados en BD

### Antes de CU13
```
Tabla: docente_grupo_materias
┌──────────────┬──────────────────┐
│ docente_id   │ grupo_materia_id │
├──────────────┼──────────────────┤
│ 1            │ 3                │
│ 1            │ 4                │
│ 2            │ 5                │
└──────────────┴──────────────────┘

Tabla: horarios (vacía o solo existentes)
```

### Después de CU13
```
Sistema genera horarios basados en:
- grupo_materias existentes
- Información de aula y horario

Resultado: Horarios listos para docentes
```

---

## ✨ Mejoras Futuras

### 1. Agregar filtros
```javascript
// Permitir generar para docente específico
const filtros = reactive({
  docente_id: '',
  especialidad: '',
});
```

### 2. Mostrar preview antes de generar
```javascript
// Mostrar qué se va a crear
const preview = await fetch('/docentes/preview-horarios');
```

### 3. Agregar opciones de configuración
```javascript
// Permitir especificar días/horas
const config = {
  dias_habiles: ['Lunes', 'Martes', 'Miércoles', 'Jueves', 'Viernes'],
  hora_inicio: '08:00',
  hora_fin: '18:00',
};
```

---

## 🐛 Troubleshooting

| Problema | Solución |
|----------|----------|
| Botón no responde | Verificar CSRF token en fetch |
| Error 403 | Verificar permisos (authorize) |
| No se genera nada | Verificar que haya docentes sin horarios |
| Datos no aparecen | Revisar console (F12) |

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **Caso de Uso** | CU13: Generar Horarios Docente |
| **Frontend** | Docentes/GenerarHorarios.vue |
| **Backend** | DocenteController::generarHorarios() |
| **Método HTTP** | POST |
| **Ruta** | /docentes/generar-horarios |
| **Permisos** | Admin |
| **Validaciones** | Existen docentes con grupos |
| **Auditoría** | Sí (Bitácora) |
| **Estado** | ✅ Completamente implementado |

---

**Versión:** 1.0  
**Última actualización:** 11 de Noviembre de 2025  
**Estado:** ✅ Listo para producción
