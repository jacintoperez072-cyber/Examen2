<template>
  <AuthenticatedLayout>
    <template #header>
      <h2 class="font-semibold text-xl text-gray-800 leading-tight">
        Reportes
      </h2>
    </template>

    <div class="py-12">
      <div class="max-w-4xl mx-auto sm:px-6 lg:px-8">
        <div class="bg-white overflow-hidden shadow-sm sm:rounded-lg">
          <div class="p-6 text-gray-900">
            <h3 class="text-lg font-semibold mb-6">Generar Reportes</h3>

            <!-- CU18: Reporte PDF -->
            <div class="mb-6 p-4 border rounded">
              <h4 class="font-medium mb-3">📄 Reporte de Asistencia (PDF)</h4>
              <p class="text-sm text-gray-600 mb-3">Exportar registro de asistencias en formato PDF</p>
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

            <!-- CU19: Reporte Excel -->
            <div class="mb-6 p-4 border rounded">
              <h4 class="font-medium mb-3">📊 Reporte de Asistencia (Excel)</h4>
              <p class="text-sm text-gray-600 mb-3">Exportar registro de asistencias en formato Excel</p>
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
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import { ref, reactive } from 'vue';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';

const cargando = ref(false);
const mensaje = reactive({ tipo: '', texto: '' });

const descargarAsistenciaPDF = async () => {
  cargando.value = true;
  try {
    const response = await fetch('/reportes/asistencia-pdf');
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'asistencias.pdf';
    a.click();
    mensaje.tipo = 'success';
    mensaje.texto = '✅ PDF descargado';
  } catch (error) {
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};

const descargarAsistenciaExcel = async () => {
  cargando.value = true;
  try {
    const response = await fetch('/reportes/asistencia-excel');
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'asistencias.xlsx';
    a.click();
    mensaje.tipo = 'success';
    mensaje.texto = '✅ Excel descargado';
  } catch (error) {
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};

const descargarBitacoraPDF = async () => {
  cargando.value = true;
  try {
    const response = await fetch('/reportes/bitacora-pdf');
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'bitacora.pdf';
    a.click();
  } catch (error) {
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};

const descargarBitacoraExcel = async () => {
  cargando.value = true;
  try {
    const response = await fetch('/reportes/bitacora-excel');
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = 'bitacora.xlsx';
    a.click();
  } catch (error) {
    mensaje.tipo = 'error';
    mensaje.texto = 'Error: ' + error.message;
  } finally {
    cargando.value = false;
  }
};
</script>
