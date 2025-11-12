<template>
  <AuthenticatedLayout>
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">
        📅 Horarios de {{ docente.user.nombre }}
      </h2>
    </template>

    <div class="py-12">
      <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
        <!-- Tarjeta de información del docente -->
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg mb-6">
          <div class="p-6 text-gray-900">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-4">
              <div>
                <p class="text-sm font-medium text-gray-600">Nombre:</p>
                <p class="text-lg font-semibold">{{ docente.user.nombre }} {{ docente.user.apellido }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-600">Email:</p>
                <p class="text-lg">{{ docente.user.email }}</p>
              </div>
              <div>
                <p class="text-sm font-medium text-gray-600">Especialidad:</p>
                <p class="text-lg">{{ docente.especialidad || "Sin especialidad" }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Horarios semanales (CU15) -->
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg mb-6">
          <div class="p-6 text-gray-900">
            <h3 class="text-lg font-semibold mb-4">📅 Horarios Semanales</h3>

            <div v-if="horariosAgrupados && Object.keys(horariosAgrupados).length > 0" class="overflow-x-auto">
              <table class="min-w-full divide-y divide-gray-200">
                <thead class="bg-gray-50">
                  <tr>
                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Día</th>
                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Hora Inicio</th>
                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Hora Fin</th>
                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Aula</th>
                    <th class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Grupo - Materia</th>
                  </tr>
                </thead>
                <tbody class="bg-white divide-y divide-gray-200">
                  <tr v-for="(horarios, dia) in horariosAgrupados" :key="dia">
                    <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                      {{ dia }}
                    </td>
                    <td colspan="4" class="px-6 py-4">
                      <div class="space-y-2">
                        <div v-for="horario in horarios" :key="horario.id" class="flex flex-wrap gap-4 text-sm border-l-4 border-blue-500 pl-4 py-2">
                          <div>
                            <span class="font-medium">{{ horario.hora_inicio }} - {{ horario.hora_fin }}</span>
                          </div>
                          <div>
                            <span class="bg-yellow-100 text-yellow-800 px-2 py-1 rounded">
                              Aula: {{ horario.aula?.nombre || 'N/A' }}
                            </span>
                          </div>
                          <div>
                            <span class="bg-blue-100 text-blue-800 px-2 py-1 rounded">
                              {{ horario.grupo_materias?.[0]?.grupo?.nombre }} - {{ horario.grupo_materias?.[0]?.materia?.nombre }}
                            </span>
                          </div>
                        </div>
                      </div>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>

            <div v-else class="text-center py-8 text-gray-500">
              ℹ️ No hay horarios asignados para este docente
            </div>
          </div>
        </div>

        <!-- Grupos-Materias asignados -->
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg mb-6">
          <div class="p-6 text-gray-900">
            <h3 class="text-lg font-semibold mb-4">📚 Grupos-Materias Asignados</h3>

            <div v-if="docente.grupo_materias && docente.grupo_materias.length > 0" class="space-y-4">
              <div v-for="gm in docente.grupo_materias" :key="gm.id" class="border border-gray-200 rounded-lg p-4 hover:bg-gray-50">
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                  <div>
                    <p class="text-xs font-medium text-gray-600">GRUPO</p>
                    <p class="text-sm font-semibold">{{ gm.grupo.nombre }}</p>
                  </div>
                  <div>
                    <p class="text-xs font-medium text-gray-600">MATERIA</p>
                    <p class="text-sm font-semibold">{{ gm.materia.nombre }}</p>
                  </div>
                  <div>
                    <p class="text-xs font-medium text-gray-600">HORARIO</p>
                    <p class="text-sm">{{ gm.horario.hora_inicio }} - {{ gm.horario.hora_fin }}</p>
                  </div>
                  <div>
                    <button 
                      @click="confirmarDesasignar(gm)" 
                      class="text-red-600 hover:text-red-900 font-medium text-sm"
                    >
                      🗑️ Desasignar
                    </button>
                  </div>
                </div>
              </div>
            </div>

            <div v-else class="text-center py-8 text-gray-500">
              ℹ️ No hay grupos-materias asignados
            </div>
          </div>
        </div>

        <!-- Botón volver -->
        <div class="mt-6">
          <Link href="/docentes" class="inline-flex items-center px-4 py-2 bg-gray-600 border border-transparent rounded-md font-semibold text-xs text-white uppercase tracking-widest hover:bg-gray-500">
            ← Volver a Docentes
          </Link>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import { Link, router } from '@inertiajs/vue3';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';

const props = defineProps({
  docente: Object,
  horarios: Array,
});

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
  if (confirm(`¿Desasignar ${grupoMateria.grupo.nombre} - ${grupoMateria.materia.nombre}?`)) {
    router.delete(`/docentes/${props.docente.id}/desasignar-grupo-materia/${grupoMateria.id}`);
  }
};
</script>
