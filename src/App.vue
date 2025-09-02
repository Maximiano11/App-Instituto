<template>
  <v-app>
    <!-- Toolbar -->
    <v-app-bar app color="blue" dark elevation="4">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      
      <v-toolbar-title>App Instituto</v-toolbar-title>

      <v-spacer />

      <!-- Botão de busca -->
      <v-btn icon @click="showSearchDialog = true">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>

      <!-- Menu da toolbar -->
      <v-menu offset-y>
        <template v-slot:activator="{ on, attrs }">
          <v-btn icon v-bind="attrs" v-on="on">
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item @click="showProfile">
            <v-list-item-icon><v-icon>mdi-account</v-icon></v-list-item-icon>
            <v-list-item-title>Perfil</v-list-item-title>
          </v-list-item>
          <v-list-item @click="showSettings">
            <v-list-item-icon><v-icon>mdi-cog</v-icon></v-list-item-icon>
            <v-list-item-title>Configurações</v-list-item-title>
          </v-list-item>
          <v-divider />
          <v-list-item @click="logout">
            <v-list-item-icon><v-icon>mdi-logout</v-icon></v-list-item-icon>
            <v-list-item-title>Sair</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>

    <!-- Menu lateral -->
    <v-navigation-drawer v-model="drawer" app temporary>
      <v-list-item>
        <v-list-item-title class="text-h6">Menu</v-list-item-title>
      </v-list-item>
      <v-divider />
      <v-list dense nav>
        <v-list-item
          v-for="item in menuItems"
          :key="item.title"
          @click="selectMenuItem(item)"
        >
          <v-list-item-icon><v-icon>{{ item.icon }}</v-icon></v-list-item-icon>
          <v-list-item-title>{{ item.title }}</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <!-- Dialog de busca -->
    <v-dialog v-model="showSearchDialog" max-width="500px">
      <v-card>
        <v-card-title><span class="text-h5">Buscar</span></v-card-title>
        <v-card-text>
          <v-text-field
            v-model="searchQuery"
            label="Digite sua busca..."
            outlined
            prepend-inner-icon="mdi-magnify"
            clearable
            @keyup.enter="performSearch"
          />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="grey" @click="showSearchDialog = false">Cancelar</v-btn>
          <v-btn text color="blue darken-1" @click="performSearch">Buscar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Snackbar -->
    <v-snackbar v-model="showSnackbar" :timeout="3000" :color="snackbarColor">
      {{ snackbarMessage }}
      <template v-slot:action="{ attrs }">
        <v-btn text v-bind="attrs" @click="showSnackbar = false">Fechar</v-btn>
      </template>
    </v-snackbar>

    <!-- Conteúdo principal -->
    <v-main>
      <v-container>
        <v-row justify="center">
          <v-col cols="12" md="10">
            <v-card class="pa-6">
              <v-card-title float-xs-start float-lg-end class="text-h4 text-center mb-4">
                Bem-vindo ao Instituto Uma Forma de Amar
              </v-card-title>
              <v-card-text>
                <p class="text-h6 text-center mb-4">
                  Escolha uma das opções abaixo para começar:
                </p>

                <v-divider class="my-6" />

                <v-row>
                  <v-col cols="12" sm="6" md="4" v-for="item in homeCards" :key="item.title">
                    <v-card outlined hover @click="selectMenuItem(item)">
                      <v-card-text class="text-center">
                        <v-icon large :color="item.color" class="mb-2">{{ item.icon }}</v-icon>
                        <div class="text-h6">{{ item.title }}</div>
                        <p class="text-body-2">{{ item.desc }}</p>
                      </v-card-text>
                    </v-card>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <!-- Rodapé -->
    <v-footer color="blue" padless>
      <div class="d-flex align-center justify-center pa-2 w-100">
        <v-img
          v-if="logoUrl"
          :src="logoUrl"
          max-height="60"
          max-width="60"
          class="mr-4"
        />
        <span class="text-body-2 white--text">
          {{ new Date().getFullYear() }} — <strong>Instituto Uma Forma de Amar</strong>
        </span>
      </div>
    </v-footer>
    
  </v-app>
</template>

<script>

export default {
  name: "App",
  data() {
    return {
      logoUrl:
        "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTQ8i5CQNh9Kt14yLwzKEKOQEI5HOEF_gmXvw&s",
      drawer: false,
      showSearchDialog: false,
      searchQuery: "",
      showSnackbar: false,
      snackbarMessage: "",
      snackbarColor: "success",

      // Menu lateral
      menuItems: [
        { title: "Cadastrar Famílias", icon: "mdi-account-group" },
        { title: "Controle de Estoque", icon: "mdi-warehouse" },
        { title: "Controle de Doadores", icon: "mdi-hand-heart" },
        { title: "Dashboard de Doações", icon: "mdi-view-dashboard" },
        { title: "Estoque (Alimentos e Dinheiro)", icon: "mdi-food" },
      ],

      // Cards da tela inicial
      homeCards: [
        { title: "Cadastrar Famílias", desc: "Registre famílias necessitadas", icon: "mdi-account-group", color: "primary" },
        { title: "Controle de Estoque", desc: "Gerencie o estoque disponível", icon: "mdi-warehouse", color: "deep-purple" },
        { title: "Controle de Doadores", desc: "Acompanhe e cadastre doadores", icon: "mdi-hand-heart", color: "pink" },
        { title: "Dashboard de Doações", desc: "Visualize dados e estatísticas", icon: "mdi-view-dashboard", color: "green" },
        { title: "Estoque Financeiro", desc: "Gerencie alimentos e dinheiro", icon: "mdi-food", color: "orange" },
      ],
    };
  },
  methods: {
    showProfile() {
      this.notify("Abrindo perfil...", "info");
    },
    showSettings() {
      this.notify("Abrindo configurações...", "info");
    },
    logout() {
      this.notify("Você saiu da aplicação.", "error");
    },
    selectMenuItem(item) {
      this.notify(`Navegando para ${item.title}`, "primary");
    },
    performSearch() {
      if (!this.searchQuery) {
        this.notify("Digite algo para buscar!", "warning");
        return;
      }
      this.notify(`Buscando por: ${this.searchQuery}`, "success");
      this.showSearchDialog = false;
      this.searchQuery = "";
    },
    notify(message, color = "success") {
      this.snackbarMessage = message;
      this.snackbarColor = color;
      this.showSnackbar = true;
    },
  },
};
</script>
