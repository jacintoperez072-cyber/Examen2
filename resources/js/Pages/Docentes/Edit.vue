<template>
  <AuthenticatedLayout>
    <Head title="Editar Docente" />
    
    <div class="py-12">
      <div class="max-w-2xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
          <div class="p-6 text-gray-900">
            <h1 class="text-2xl font-bold mb-6">Editar Docente</h1>

            <form @submit.prevent="submit" class="space-y-6">
              <div>
                <label class="block text-sm font-medium text-gray-700">Nombre</label>
                <input 
                  v-model="form.nombre" 
                  type="text"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                />
                <p v-if="form.errors.nombre" class="text-red-600 text-sm mt-1">{{ form.errors.nombre }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Apellido</label>
                <input 
                  v-model="form.apellido" 
                  type="text"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                />
                <p v-if="form.errors.apellido" class="text-red-600 text-sm mt-1">{{ form.errors.apellido }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Email</label>
                <input 
                  v-model="form.email" 
                  type="email"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                />
                <p v-if="form.errors.email" class="text-red-600 text-sm mt-1">{{ form.errors.email }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Especialidad</label>
                <input 
                  v-model="form.especialidad" 
                  type="text"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                />
                <p v-if="form.errors.especialidad" class="text-red-600 text-sm mt-1">{{ form.errors.especialidad }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Fecha de Contrato</label>
                <input 
                  v-model="form.fecha_contrato" 
                  type="date"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                />
                <p v-if="form.errors.fecha_contrato" class="text-red-600 text-sm mt-1">{{ form.errors.fecha_contrato }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Estado</label>
                <select 
                  v-model="form.estado"
                  required
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                >
                  <option value="activo">Activo</option>
                  <option value="inactivo">Inactivo</option>
                </select>
              </div>

              <div class="flex gap-4">
                <button 
                  type="submit"
                  class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
                  :disabled="form.processing"
                >
                  Actualizar Docente
                </button>
                <Link href="/docentes" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md">
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
  docente: Object,
});

const form = useForm({
  nombre: '',
  apellido: '',
  email: '',
  especialidad: '',
  fecha_contrato: '',
  estado: 'activo',
});

form.defaults({
  nombre: docente.user?.nombre || '',
  apellido: docente.user?.apellido || '',
  email: docente.user?.email || '',
  especialidad: docente.especialidad,
  fecha_contrato: docente.fecha_contrato,
  estado: docente.estado,
});

form.reset();

const submit = () => {
  form.put(`/docentes/${docente.id}`);
};
</script>
