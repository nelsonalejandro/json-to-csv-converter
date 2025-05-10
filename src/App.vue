<template>
  <div class="min-h-screen bg-primary text-white p-6 flex items-center justify-center">
    <div class="w-full max-w-5xl bg-secondary rounded-2xl shadow-2xl p-6">
      <h1 class="text-3xl font-bold text-center mb-6">JSON ↔ CSV Converter</h1>

      <div class="grid md:grid-cols-2 gap-4 mb-6">
        <div>
          <label class="block mb-2 text-lg font-medium">JSON</label>
          <textarea v-model="jsonInput" class="w-full h-60 p-3 rounded-xl bg-primary text-white resize-none"></textarea>
        </div>
        <div>
          <label class="block mb-2 text-lg font-medium">CSV</label>
          <textarea v-model="csvOutput" class="w-full h-60 p-3 rounded-xl bg-primary text-white resize-none"></textarea>
        </div>
      </div>

      <div class="flex flex-wrap justify-center gap-4">
        <button @click="convertToCSV" class="bg-accent text-black font-bold px-6 py-2 rounded-xl hover:scale-105 transition">JSON → CSV</button>
        <button @click="convertToJSON" class="bg-accent text-black font-bold px-6 py-2 rounded-xl hover:scale-105 transition">CSV → JSON</button>
        <button @click="clearAll" class="bg-red-500 text-white font-bold px-6 py-2 rounded-xl hover:scale-105 transition">Limpiar</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const jsonInput = ref('')
const csvOutput = ref('')

function convertToCSV() {
  try {
    const data = JSON.parse(jsonInput.value)
    if (!Array.isArray(data)) throw new Error('El JSON debe ser un array de objetos.')
    const headers = Object.keys(data[0])
    const csv = [headers.join(',')]
    for (const row of data) {
      const values = headers.map(key => JSON.stringify(row[key] ?? ''))
      csv.push(values.join(','))
    }
    csvOutput.value = csv.join('\\n')
  } catch (e) {
    alert('Error al convertir JSON a CSV: ' + e.message)
  }
}

function convertToJSON() {
  try {
    const [headerLine, ...lines] = csvOutput.value.split('\n')
    const headers = headerLine.split(',')
    const result = lines.map(line => {
      const values = line.split(',')
      const obj = {}
      headers.forEach((h, i) => {
        // Gestiona las comas dentro de los valores con comillas
        const value = values[i].replace(/^"|"$/g, '')
        obj[h] = value
      })
      return obj
    })
    jsonInput.value = JSON.stringify(result, null, 2)
  } catch (e) {
    alert('Error al convertir CSV a JSON: ' + e.message)
  }
}

function clearAll() {
  jsonInput.value = ''
  csvOutput.value = ''
}
</script>
