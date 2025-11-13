<template>
  <AuthenticatedLayout>
    <Head title="Editar Rol" />
    
    <div class="py-12">
      <div class="max-w-2xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
          <div class="p-6 text-gray-900">
            <h1 class="text-2xl font-bold mb-6">Editar Rol</h1>

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
                <label class="block text-sm font-medium text-gray-700">Descripción</label>
                <textarea 
                  v-model="form.descripcion"
                  class="mt-1 block w-full border border-gray-300 rounded-md shadow-sm py-2 px-3"
                  rows="4"
                ></textarea>
                <p v-if="form.errors.descripcion" class="text-red-600 text-sm mt-1">{{ form.errors.descripcion }}</p>
              </div>

              <div>
                <label class="block text-sm font-medium text-gray-700">Permisos</label>
                <div class="mt-2 space-y-2 border border-gray-300 rounded-md p-3 max-h-64 overflow-y-auto">
                  <div v-for="permiso in permisos" :key="permiso.id" class="flex items-center">
                    <input 
                      :id="`permiso-${permiso.id}`"
                      type="checkbox"
                      :value="permiso.id"
                      v-model="form.permiso_ids"
                      class="rounded border-gray-300"
                    />
                    <label :for="`permiso-${permiso.id}`" class="ml-2 text-sm">
                      {{ permiso.nombre }}
                    </label>
                  </div>
                </div>
                <p v-if="form.errors.permiso_ids" class="text-red-600 text-sm mt-1">{{ form.errors.permiso_ids }}</p>
              </div>

              <div class="flex gap-4">
                <button 
                  type="submit"
                  class="bg-indigo-600 text-white px-4 py-2 rounded-md hover:bg-indigo-700"
                  :disabled="form.processing"
                >
                  Actualizar Rol
                </button>
                <Link href="/roles" class="bg-gray-300 text-gray-700 px-4 py-2 rounded-md">
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

const props = defineProps({
  rol: Object,
  permisos: Array,
});

const form = useForm({
  nombre: '',
  descripcion: '',
  permiso_ids: [],
});

form.defaults({
  nombre: props.rol.nombre,
  descripcion: props.rol.descripcion,
  permiso_ids: props.rol.permisos?.map(p => p.id) || [],
});

form.reset();

const submit = () => {
  form.put(`/roles/${props.rol.id}`, {
    data: {
      nombre: form.nombre,
      descripcion: form.descripcion,
      permisos: form.permiso_ids,
    }
  });
};
</script>
