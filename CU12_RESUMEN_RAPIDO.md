# 📚 CU12 - Frontend Resumen Rápido

## 🎯 ¿Dónde está?

**Archivo:** `resources/js/Pages/Docentes/Index.vue`

## 🖼️ Vista Previa

```
╔═══════════════════════════════════════════════════════════╗
║              Gestionar Docentes                            ║
║  ┌─────────────────────────────────────────────────────┐  ║
║  │ ➕ Nuevo Docente                                      │  ║
║  └─────────────────────────────────────────────────────┘  ║
║                                                             ║
║  ┌─────────────────────────────────────────────────────┐  ║
║  │ Nombre │ Email │ Esp. │ Grupos │     Acciones     │  ║
║  ├─────────────────────────────────────────────────────┤  ║
║  │ Carlos │ car.. │ Mat  │  1     │ ✏️ 📚 📅 🗑️      │  ║
║  │ María  │ mar.. │ Fís  │  0     │ ✏️ 📚 📅 🗑️      │  ║
║  │ Pedro  │ ped.. │ Hist │  2     │ ✏️ 📚 📅 🗑️      │  ║
║  └─────────────────────────────────────────────────────┘  ║
║                 ↓ Click en "📚 Asignar Grupo"             ║
║                    (AQUÍ ESTÁ EL CU12)                    ║
╚═══════════════════════════════════════════════════════════╝

╔═══════════════════════════════════════════════════════════╗
║         📚 Asignar Grupo-Materia a Carlos García          ║
║  ┌─────────────────────────────────────────────────────┐  ║
║  │ Selecciona el Grupo-Materia:                        │  ║
║  │ ┌───────────────────────────────────────────────┐   │  ║
║  │ │ -- Selecciona --                              │   │  ║
║  │ │ ✓ Grupo A - Matemática (08:00 - 10:00)       │   │  ║
║  │ │   Grupo B - Física (10:00 - 12:00)           │   │  ║
║  │ │   Grupo C - Historia (14:00 - 16:00)         │   │  ║
║  │ └───────────────────────────────────────────────┘   │  ║
║  │                                                     │  ║
║  │           [✅ Asignar]  [❌ Cancelar]              │  ║
║  └─────────────────────────────────────────────────────┘  ║
╚═══════════════════════════════════════════════════════════╝

✅ ÉXITO: Asignación realizada exitosamente

Tabla ACTUALIZA:
│ Carlos │ Matemática │ 2 grupos │ ✏️ 📚 📅 🗑️ │
                         ↑ Aumentó de 1 a 2
```

## 🔗 Flujo Rápido

```
1. Ir a: http://localhost:8000/docentes
2. Tabla muestra todos los docentes
3. Click: 📚 Asignar Grupo (en columna "Acciones")
4. Modal abre con selector dropdown
5. Seleccionar: "Grupo A - Matemática"
6. Click: ✅ Asignar
7. Esperar respuesta ✅
8. Tabla se actualiza automáticamente
```

## 📝 Componentes Implicados

```vue
<!-- En Docentes/Index.vue -->
<button @click="abrirModalAsignar(docente)">
  📚 Asignar Grupo
</button>

<!-- Modal -->
<select v-model="formulario.grupo_materia_id">
  <option v-for="gm in gruposMaterias" :value="gm.id">
    {{ gm.grupo.nombre }} - {{ gm.materia.nombre }}
  </option>
</select>

<button @click="asignarGrupoMateria">
  ✅ Asignar
</button>
```

## ⚙️ Qué ocurre detrás

```
1. Frontend envía POST a: /docentes/1/asignar-grupo-materia
2. Backend (DocenteController):
   ✓ Valida permisos (authorize)
   ✓ Valida que grupo_materia_id exista
   ✓ Verifica no esté duplicado
   ✓ Ejecuta: $docente->grupoMaterias()->attach()
   ✓ Registra en Bitácora
3. BD: Inserta en tabla docente_grupo_materias
4. Frontend: Recibe éxito y actualiza
```

## 📊 BD - Tabla docente_grupo_materias

```
ANTES:
┌──────────────┬──────────────────┐
│ docente_id   │ grupo_materia_id │
├──────────────┼──────────────────┤
│ 1            │ 3                │
└──────────────┴──────────────────┘

DESPUÉS de CU12:
┌──────────────┬──────────────────┐
│ docente_id   │ grupo_materia_id │
├──────────────┼──────────────────┤
│ 1            │ 3                │
│ 1            │ 4                │ ← Nueva fila
└──────────────┴──────────────────┘
```

## 🔐 Permisos

```
✅ Admin: Sí
✅ Coordinador: Sí
❌ Docente: No
```

## ✨ Otras opciones en la tabla

```
✏️ Editar       → Editar datos del docente
📚 Asignar      → AQUÍ (CU12)
📅 Horarios     → Ver horarios (CU15)
🗑️ Eliminar     → Borrar docente
```

## 🧪 Test Rápido

```bash
# 1. Acceder
http://localhost:8000/docentes

# 2. Datos de prueba
Email: admin@sistema.com
Password: password123

# 3. Hacer clic en 📚 Asignar Grupo de cualquier docente
# 4. Seleccionar un grupo-materia
# 5. Hacer clic en ✅ Asignar

# Resultado esperado:
✅ "Asignación realizada exitosamente"
```

## 📂 Archivos Relacionados

```
resources/js/Pages/Docentes/
├── Index.vue          ← CU12 (Listar y asignar)
└── Horarios.vue       ← CU15 (Ver horarios)

app/Http/Controllers/
└── DocenteController.php
    ├── index()
    ├── asignarGrupoMateria()     ← Procesa CU12
    ├── desasignarGrupoMateria()
    └── horarios()

routes/web.php
└── POST /docentes/{docente}/asignar-grupo-materia
```

## ✅ Estado

- ✅ Frontend Vue: Completado
- ✅ Backend Laravel: Completado
- ✅ Rutas: Configuradas
- ✅ BD: Funcionando
- ✅ Validaciones: Activas
- ✅ Auditoría: Registrando

---

**Respuesta rápida:** El frontend del CU12 está en `resources/js/Pages/Docentes/Index.vue` en un modal que se abre al hacer clic en "📚 Asignar Grupo" en la tabla de docentes.

**Para más detalles:** Consulta `CU12_GUIA_COMPLETA.md`
