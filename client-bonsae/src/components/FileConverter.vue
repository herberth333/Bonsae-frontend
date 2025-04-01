<script setup>
import { ref } from 'vue'
import * as XLSX from 'xlsx'

const imports = ref({
  users: { file: null, name: '' },
  academicPeriod: { file: null, name: '' },
  subjects: { file: null, name: '' },
  classes: { file: null, name: '' },
  teacherAssignments: { file: null, name: '' },
})

const handleFileSelect = (entity, event) => {
  const input = event.target
  if (input.files && input.files[0]) {
    imports.value[entity].file = input.files[0]
    imports.value[entity].name = input.files[0].name
  }
}

const importData = async (entity) => {
  const importFile = imports.value[entity]
  if (!importFile.file) {
    alert('Please select a file first')
    return
  }

  try {
    const buffer = await importFile.file.arrayBuffer()
    const workbook = XLSX.read(buffer)
    const worksheet = workbook.Sheets[workbook.SheetNames[0]]
    const csv = XLSX.utils.sheet_to_csv(worksheet)

    // Create and download the CSV file
    const blob = new Blob([csv], { type: 'text/csv' })
    const url = window.URL.createObjectURL(blob)
    const a = document.createElement('a')
    a.href = url
    a.download = `${entity}_import.csv`
    a.click()
    window.URL.revokeObjectURL(url)

    // Reset form
    imports.value[entity].file = null
    imports.value[entity].name = ''
  } catch (error) {
    alert('Error converting file. Please try again.')
    console.error(error)
  }
}
</script>

<template>
  <div class="min-h-screen bg-white">
    <v-container class="py-8">
      <v-card class="mx-auto max-w-4xl bg-white shadow-lg rounded-lg">
        <v-card-title class="text-center text-2xl font-bold text-blue-600 pt-8">
          Bonsae - Conversor de planilhas
        </v-card-title>

        <v-card-text class="p-6">
          <div class="mb-8 p-4 border border-blue-100 rounded-lg">
            <h3 class="text-lg font-semibold text-blue-800 mb-4">Usuários</h3>
            <div class="flex items-center gap-4">
              <v-btn color="primary" variant="outlined" @click="$refs.usersInput.click()">
                Selecione o arquivo .xlsx
              </v-btn>
              <input type="file" accept=".xlsx,.xls" class="hidden" ref="usersInput"
                @change="(e) => handleFileSelect('users', e)">
              <span v-if="imports.users.name" class="text-blue-600">
                {{ imports.users.name }}
              </span>
              <v-spacer></v-spacer>
              <v-btn color="primary" :disabled="!imports.users.file" @click="importData('users')">
                Import
              </v-btn>
            </div>
          </div>

          <div class="mb-8 p-4 border border-blue-100 rounded-lg">
            <h3 class="text-lg font-semibold text-blue-800 mb-4">Período Letivo</h3>
            <div class="flex items-center gap-4">
              <v-btn color="primary" variant="outlined" @click="$refs.periodInput.click()">
                Selecione o arquivo .xlsx
              </v-btn>
              <input type="file" accept=".xlsx,.xls" class="hidden" ref="periodInput"
                @change="(e) => handleFileSelect('academicPeriod', e)">
              <span v-if="imports.academicPeriod.name" class="text-blue-600">
                {{ imports.academicPeriod.name }}
              </span>
              <v-spacer></v-spacer>
              <v-btn color="primary" :disabled="!imports.academicPeriod.file" @click="importData('academicPeriod')">
                Import
              </v-btn>
            </div>
          </div>

          <div class="mb-8 p-4 border border-blue-100 rounded-lg">
            <h3 class="text-lg font-semibold text-blue-800 mb-4">Disciplinas</h3>
            <div class="flex items-center gap-4">
              <v-btn color="primary" variant="outlined" @click="$refs.subjectsInput.click()">
                Selecione o arquivo .xlsx
              </v-btn>
              <input type="file" accept=".xlsx,.xls" class="hidden" ref="subjectsInput"
                @change="(e) => handleFileSelect('subjects', e)">
              <span v-if="imports.subjects.name" class="text-blue-600">
                {{ imports.subjects.name }}
              </span>
              <v-spacer></v-spacer>
              <v-btn color="primary" :disabled="!imports.subjects.file" @click="importData('subjects')">
                Import
              </v-btn>
            </div>
          </div>

          <div class="mb-8 p-4 border border-blue-100 rounded-lg">
            <h3 class="text-lg font-semibold text-blue-800 mb-4">Alunos para Turma</h3>
            <div class="flex items-center gap-4">
              <v-btn color="primary" variant="outlined" @click="$refs.classesInput.click()">
                Selecione o arquivo .xlsx
              </v-btn>
              <input type="file" accept=".xlsx,.xls" class="hidden" ref="classesInput"
                @change="(e) => handleFileSelect('classes', e)">
              <span v-if="imports.classes.name" class="text-blue-600">
                {{ imports.classes.name }}
              </span>
              <v-spacer></v-spacer>
              <v-btn color="primary" :disabled="!imports.classes.file" @click="importData('classes')">
                Import
              </v-btn>
            </div>
          </div>

          <div class="mb-8 p-4 border border-blue-100 rounded-lg">
            <h3 class="text-lg font-semibold text-blue-800 mb-4">Professor para turma</h3>
            <div class="flex items-center gap-4">
              <v-btn color="primary" variant="outlined" @click="$refs.teacherInput.click()">
                Selecione o arquivo .xlsx
              </v-btn>
              <input type="file" accept=".xlsx,.xls" class="hidden" ref="teacherInput"
                @change="(e) => handleFileSelect('teacherAssignments', e)">
              <span v-if="imports.teacherAssignments.name" class="text-blue-600">
                {{ imports.teacherAssignments.name }}
              </span>
              <v-spacer></v-spacer>
              <v-btn color="primary" :disabled="!imports.teacherAssignments.file"
                @click="importData('teacherAssignments')">
                Import
              </v-btn>
            </div>
          </div>
        </v-card-text>
      </v-card>
    </v-container>
  </div>
</template>