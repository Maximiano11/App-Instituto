<template>
  <v-app>
    <v-app-bar flat color="blue">
      <v-app-bar-title>App Instituto</v-app-bar-title>
    </v-app-bar>

    <v-main>
      <v-container>
        <h1 class="text-h5 text-md-h3">Dashboard</h1>
        <div class="d-flex flex-column flex-md-row justify-space-between">
          <div
            class="w-100 pa-4 border rounded-lg elevation-2"
          >
            teste 
          </div>
          <div 
            class="w-100 pa-4 border rounded-lg elevation-2"
          >
            teste
          </div>
          <div 
            class="w-100 pa-4 border rounded-lg elevation-2"
          >
            teste
        </div>
        </div>
      </v-container>
      <router-view />
    </v-main>
  </v-app>
</template>

<script setup>
  //
</script>
