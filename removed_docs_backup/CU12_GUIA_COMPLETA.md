# 🎓 CU12: Asignar Horario Docente - Guía Completa

## 📌 ¿Qué es el CU12?

**Caso de Uso 12:** Asignar un grupo-materia con su respectivo horario a un docente para que pueda impartir esa clase.

### Ejemplo Real:
```
Docente: Carlos García (ID: 1)
Asignar → Grupo A - Matemática (Horario: Lunes 08:00-10:00, Aula 101)

Resultado: Carlos García enseñará Matemática al Grupo A
           los lunes de 08:00 a 10:00 en el aula 101
```

---

## 🔄 Flujo Completo

```
┌──────────────────────────────────────────────────────────────────┐
│                         USUARIO (ADMIN)                          │
│                          Inicia sesión                            │
└──────────────────────┬───────────────────────────────────────────┘
                       │
                       ▼
            ┌──────────────────────┐
            │  Menú Principal      │
            │  [Docentes]          │
            │  [Usuarios]          │
            │  [Reportes]          │
            └──────────┬───────────┘
                       │
                       ▼ Click en "Docentes"
        ┌─────────────────────────────────────────┐
        │    PÁGINA: Docentes/Index.vue            │
        │                                         │
        │  Tabla de Docentes:                     │
        │  ┌─────────────────────────────────┐   │
        │  │ Carlos García │ Matemática │ 1  │   │
        │  │ María López   │ Física     │ 0  │   │
        │  │ Pedro Ruiz    │ Historia   │ 2  │   │
        │  └─────────────────────────────────┘   │
        │          ↓ Click en botón               │
        │      "📚 Asignar Grupo"                 │
        └─────────────┬──────────────────────────┘
                      │
                      ▼
        ┌─────────────────────────────────────┐
        │  MODAL: Asignar Grupo-Materia       │
        │  ┌──────────────────────────────┐   │
        │  │ Docente: Carlos García       │   │
        │  │                              │   │
        │  │ Selecciona grupo-materia:    │   │
        │  │ [Dropdown ▼]                 │   │
        │  │  • Grupo A - Matemática      │   │
        │  │  • Grupo B - Física          │   │
        │  │  • Grupo C - Historia        │   │
        │  │                              │   │
        │  │ [✅ Asignar] [❌ Cancelar]   │   │
        │  └──────────────────────────────┘   │
        │          ↓ Seleccionar               │
        │      "Grupo A - Matemática"          │
        │          ↓ Click "✅ Asignar"        │
        └─────────────┬──────────────────────┘
                      │
        ┌─────────────▼──────────────────────────┐
        │   BACKEND - Laravel                    │
        │                                        │
        │  POST /docentes/1/asignar-grupo-materia│
        │  {                                     │
        │    "grupo_materia_id": 3              │
        │  }                                     │
        │                                        │
        │  DocenteController::asignarGrupoMateria
        │  ├─ Verificar permisos ✓              │
        │  ├─ Validar grupo_materia_id ✓        │
        │  ├─ Verificar no duplicado ✓          │
        │  ├─ Ejecutar: attach() ✓              │
        │  ├─ Registrar en Bitácora ✓           │
        │  └─ Retornar success ✓                │
        └─────────────┬──────────────────────────┘
                      │
                      ▼
        ┌─────────────────────────────────────┐
        │  BASE DE DATOS                      │
        │  Tabla: docente_grupo_materias      │
        │  ┌──────────────────────────────┐   │
        │  │ docente_id │ grupo_materia_id│   │
        │  │     1      │        3        │   │
        │  └──────────────────────────────┘   │
        │                                     │
        │  Tabla: bitacoras                   │
        │  ┌──────────────────────────────┐   │
        │  │ ASIGNAR, docente_grupo...   │   │
        │  └──────────────────────────────┘   │
        └─────────────┬──────────────────────┘
                      │
                      ▼
        ┌─────────────────────────────────────┐
        │  RESPUESTA AL USUARIO               │
        │  ✅ "Asignación realizada           │
        │      exitosamente"                  │
        │                                     │
        │  Tabla ACTUALIZA:                   │
        │  Carlos García │ Matemática │ 2    │
        │  ↑ Ahora tiene 2 grupos            │
        └─────────────────────────────────────┘
```

---

## 📂 Archivos Involucrados

### Frontend
```
resources/js/Pages/Docentes/
├── Index.vue          ← AQUÍ ESTÁ EL CU12
│   ├── Tabla de docentes
│   ├── Modal de asignación
│   └── Botones de acciones
│
└── Horarios.vue       ← Ver horarios del docente (CU15)
    ├── Horarios semanales
    ├── Grupos asignados
    └── Botón desasignar
```

### Backend
```
app/Http/Controllers/
└── DocenteController.php
    ├── index()                      ← Lista docentes
    ├── asignarGrupoMateria()        ← CU12 AQUÍ
    ├── desasignarGrupoMateria()     ← Acción inversa
    ├── horarios()                   ← CU15
    └── generarHorarios()            ← Extensión futura
```

### Modelos
```
app/Models/
├── Docente.php
│   └── grupoMaterias()              ← belongsToMany
│
└── GrupoMateria.php
    └── docentes()                   ← belongsToMany
```

### Rutas
```
routes/web.php
├── GET  /docentes                   ← Lista
├── POST /docentes/{id}/asignar-grupo-materia    ← CU12
├── DELETE /docentes/{id}/desasignar-grupo-materia/{gm}
└── GET /docentes/{id}/horarios      ← Ver horarios
```

### Tablas
```
database/migrations
└── docente_grupo_materias (pivot table)
    ├── docente_id (FK)
    ├── grupo_materia_id (FK)
    ├── created_at
    └── updated_at
```

---

## 🎯 Vista Detallada de Index.vue

### Estructura HTML
```html
<template>
  <!-- Encabezado -->
  <h2>Gestionar Docentes</h2>
  
  <!-- Botón crear -->
  <Link href="/docentes/create">
    ➕ Nuevo Docente
  </Link>
  
  <!-- Tabla de docentes -->
  <table>
    <thead>
      <tr>
        <th>Nombre</th>
        <th>Email</th>
        <th>Especialidad</th>
        <th>Grupos Asignados</th>
        <th>Acciones</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="docente in docentes.data">
        <!-- Datos del docente -->
        <td>{{ docente.user.nombre }}</td>
        <td>{{ docente.user.email }}</td>
        <td>{{ docente.especialidad }}</td>
        
        <!-- Contador de grupos -->
        <td>
          <span>{{ docente.grupo_materias.length }} grupos</span>
        </td>
        
        <!-- Acciones -->
        <td>
          <button @click="abrirModalAsignar(docente)">
            📚 Asignar Grupo          ← AQUÍ ABRE MODAL CU12
          </button>
        </td>
      </tr>
    </tbody>
  </table>
  
  <!-- MODAL DE ASIGNACIÓN (CU12) -->
  <div v-if="mostrarModal" class="modal">
    <h3>Asignar Grupo-Materia a {{ docenteSeleccionado.user.nombre }}</h3>
    
    <!-- Selector -->
    <label>Selecciona el Grupo-Materia:</label>
    <select v-model="formulario.grupo_materia_id">
      <option value="">-- Selecciona --</option>
      <option v-for="gm in gruposMaterias" :value="gm.id">
        {{ gm.grupo.nombre }} - {{ gm.materia.nombre }}
        ({{ gm.horario.hora_inicio }} - {{ gm.horario.hora_fin }})
      </option>
    </select>
    
    <!-- Botones -->
    <button @click="asignarGrupoMateria">
      ✅ Asignar
    </button>
    <button @click="cerrarModal">
      ❌ Cancelar
    </button>
  </div>
</template>
```

### Lógica JavaScript
```javascript
// Datos reactivos
const formulario = reactive({
  grupo_materia_id: '', // ID del grupo-materia a asignar
});

const docenteSeleccionado = ref(null);
const mostrarModal = ref(false);

// Abrir modal para asignar
const abrirModalAsignar = (docente) => {
  docenteSeleccionado.value = docente;
  formulario.grupo_materia_id = '';
  mostrarModal.value = true;
};

// Asignar grupo-materia
const asignarGrupoMateria = async () => {
  try {
    // Enviar POST al backend
    await router.post(
      `/docentes/${docenteSeleccionado.value.id}/asignar-grupo-materia`,
      {
        grupo_materia_id: formulario.grupo_materia_id,
      }
    );
    // Si éxito, cierra modal
    cerrarModal();
  } catch (error) {
    // Mostrar errores
    console.error(error);
  }
};

// Cerrar modal
const cerrarModal = () => {
  mostrarModal.value = false;
  docenteSeleccionado.value = null;
};
```

---

## ⚙️ Backend - DocenteController.php

### Método: asignarGrupoMateria()
```php
public function asignarGrupoMateria(Request $request, Docente $docente)
{
    // 1. AUTORIZACIÓN
    $this->authorize('edit', 'usuarios');
    // Solo admin y coordinadores pueden hacer esto
    
    // 2. VALIDACIÓN
    $validated = $request->validate([
        'grupo_materia_id' => 'required|exists:grupo_materias,id',
        // Campo requerido y debe existir en BD
    ]);
    
    // 3. VERIFICAR NO ESTÉ DUPLICADO
    $existe = $docente->grupoMaterias()
        ->where('grupo_materia_id', $validated['grupo_materia_id'])
        ->exists();
    
    if ($existe) {
        return back()->withErrors([
            'error' => 'Este grupo-materia ya está asignado al docente'
        ]);
    }
    
    // 4. REALIZAR ASIGNACIÓN
    $docente->grupoMaterias()
        ->attach($validated['grupo_materia_id']);
    
    // 5. REGISTRAR EN BITÁCORA (Auditoría)
    BitacoraService::registrar(
        'ASIGNAR',                          // Acción
        'docente_grupo_materias',           // Tabla
        $docente->id,                       // ID del registro
        'Grupo-Materia asignado a docente'  // Descripción
    );
    
    // 6. RETORNAR RESPUESTA
    return redirect()->back()
        ->with('success', 'Asignación realizada exitosamente');
}
```

---

## 📊 Ejemplo de Datos en BD

### Tabla: docentes
```
| id | user_id | especialidad    |
|----|---------|-----------------|
| 1  | 5       | Matemática      |
| 2  | 6       | Física          |
```

### Tabla: grupo_materias
```
| id | grupo_id | materia_id | horario_id |
|----|----------|-----------|-----------|
| 3  | 1        | 1         | 5         |
| 4  | 2        | 2         | 6         |
| 5  | 1        | 3         | 7         |
```

### Tabla: docente_grupo_materias (ANTES)
```
| docente_id | grupo_materia_id |
|------------|------------------|
| 1          | 3                |
```

### Después de ejecutar CU12 (asignar grupo 4)
```
| docente_id | grupo_materia_id |
|------------|------------------|
| 1          | 3                |
| 1          | 4                | ← Nueva asignación
```

---

## 🔐 Sistema de Permisos

### Requisitos para ejecutar CU12:

```php
// En AppServiceProvider:
Gate::define('edit', function (User $user) {
    return $user->rol->permisos()
        ->where('nombre_permiso', 'edit')
        ->exists();
});

// En DocenteController::asignarGrupoMateria():
$this->authorize('edit', 'usuarios');
```

### Quién puede ejecutar CU12:
- ✅ **Admin** - Tiene permiso 'edit' sobre 'usuarios'
- ✅ **Coordinador** - También tiene permiso 'edit' sobre 'usuarios'
- ❌ **Docente** - NO tiene este permiso

---

## 🧪 Prueba Paso a Paso

### 1. Acceder al sistema
```
URL: http://localhost:8000/docentes
Email: admin@sistema.com
Password: password123
```

### 2. Ver tabla de docentes
```
Debería ver:
┌─────────────────────────────────────────┐
│ Nombre     │ Email      │ Esp. │ Grupos │
├─────────────────────────────────────────┤
│ Carlos     │ carlos...  │ Mat  │ 1      │
│ María      │ maria...   │ Fís  │ 0      │
└─────────────────────────────────────────┘
```

### 3. Hacer clic en "📚 Asignar Grupo" para Carlos
```
Se abre modal:
┌──────────────────────────────────────┐
│ Asignar Grupo a Carlos García        │
├──────────────────────────────────────┤
│ Selecciona:                          │
│ [Dropdown ▼]                         │
│ Grupo A - Matemática (08:00-10:00)   │
│ Grupo B - Física (10:00-12:00)       │
│                                      │
│ [✅ Asignar] [❌ Cancelar]            │
└──────────────────────────────────────┘
```

### 4. Seleccionar "Grupo A - Matemática"
```
Dropdown muestra opciones con:
- Nombre del grupo
- Nombre de la materia
- Horario
```

### 5. Clic en "✅ Asignar"
```
Backend procesa:
✓ Valida permisos
✓ Valida grupo_materia_id
✓ Verifica no duplicado
✓ Ejecuta attach()
✓ Registra en bitácora
```

### 6. Respuesta
```
✅ "Asignación realizada exitosamente"

Tabla actualiza:
Carlos │ Matemática │ 2 grupos ← Aumentó de 1 a 2
```

### 7. Verificar en Bitácora
```
Menú → Bitácora
Mostrar: "ASIGNAR docente_grupo_materias ..."
```

---

## 🐛 Troubleshooting

### Error: "Modal no aparece"
```
Verificar en consola (F12):
- mostrarModal debe cambiar a true
- docenteSeleccionado debe tener datos
```

### Error: "No se puede asignar"
```
Verificar:
1. Usuario logeado ¿es Admin o Coordinador?
2. El grupo-materia ¿existe en BD?
3. ¿Está duplicado? (Verificar tabla docente_grupo_materias)
```

### Error: "No veo el botón 📚 Asignar Grupo"
```
Verificar:
1. La página está en: /docentes
2. Hay docentes en la BD
3. El componente se renderizó correctamente
```

---

## ✨ Mejoras Futuras

### 1. Agregar validación de conflictos horarios
```php
// Verificar que el docente no tenga otro grupo
// a la misma hora en el mismo día
$tieneCruce = $docente->grupoMaterias()
    ->whereHas('horario', function ($q) use ($grupoMateria) {
        $q->where('dia_semana', $grupoMateria->horario->dia_semana)
          ->where('hora_inicio', '=', $grupoMateria->horario->hora_inicio);
    })->exists();
```

### 2. Agregar filtro por especialidad
```vue
<select v-model="filtroEspecialidad">
  <option>Todas</option>
  <option>Matemática</option>
  <option>Física</option>
</select>
```

### 3. Agregar búsqueda por nombre
```vue
<input v-model="busqueda" placeholder="Buscar docente...">
```

### 4. Mostrar vista previa del horario
```javascript
// Al seleccionar grupo-materia, mostrar horario
const grupoMateriaSeleccionado = computed(() => {
  return gruposMaterias.find(
    gm => gm.id === formulario.grupo_materia_id
  );
});
```

---

## 📝 Resumen

| Aspecto | Detalle |
|--------|---------|
| **Caso de Uso** | CU12: Asignar Horario Docente |
| **Frontend** | Docentes/Index.vue (Modal) |
| **Backend** | DocenteController::asignarGrupoMateria() |
| **Método HTTP** | POST |
| **Ruta** | /docentes/{id}/asignar-grupo-materia |
| **Tabla Principal** | docente_grupo_materias |
| **Permisos** | Admin, Coordinador |
| **Validaciones** | Existe grupo-materia, No duplicado |
| **Auditoría** | Sí (Bitácora) |
| **Estado** | ✅ Completamente implementado |

---

**Versión:** 1.0  
**Última actualización:** 11 de Noviembre de 2025  
**Estado:** ✅ Listo para producción
