<template>
  <AuthenticatedLayout>
    <Head title="Editar Asignación Grupo-Materia" />
    
    <div class="py-12">
      <div class="max-w-2xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
          <div class="p-6 text-gray-900">
            <h1 class="text-2xl font-bold mb-6">Editar Asignación Grupo-Materia</h1>

            <form @submit.prevent="submit" class="space-y-6">
              <div>
                <label class="block text-sm font-medium text-gray-700">Grupo</label>
                <input 
                  :value="grupoMateria.grupo?.nombre"
                  type="text"
                  disabled
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 bg-gray-100"
                />
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Materia</label>
                <input 
                  :value="grupoMateria.materia?.nombre"
                  type="text"
                  disabled
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3 bg-gray-100"
                />
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Horario</label>
                <select 
                  v-model="form.horario_id"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                >
                  <option value="">Seleccionar Horario</option>
                  <option v-for="horario in horarios" :key="horario.id" :value="horario.id">
                    {{ horario.dia_semana }} {{ horario.hora_inicio }} - {{ horario.hora_fin }} (Aula: {{ horario.aula?.nombre_aula }})
                  </option>
                </select>
                <p v-if="form.errors.horario_id" class="text-red-600 text-sm mt-1">{{ form.errors.horario_id }}</p>
              </div>

              <div class="flex gap-4">
                <button 
                  type="submit"
                  class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
                  :disabled="form.processing"
                >
                  Actualizar Asignación
                </button>
                <Link href="/grupo-materias" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md">
                  Cancelar
                </Link>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';

defineProps({
  grupoMateria: Object,
  horarios: Array,
});

const form = useForm({
  horario_id: '',
});

form.defaults({
  horario_id: grupoMateria.horario_id,
});

form.reset();

const submit = () => {
  form.put(`/grupo-materias/${grupoMateria.id}`);
};
</script>
