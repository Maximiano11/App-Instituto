<template>
  <v-app>
    <!-- Toolbar -->
    <v-app-bar app color="blue" dark elevation="4">
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title>App Instituto</v-toolbar-title>
      <v-spacer />
      
      <!-- Ícone de perfil do usuário -->
      <v-btn icon @click="profileDrawer = true">
        <v-avatar size="36">
          <template v-if="userPhoto">
            <img :src="userPhoto" alt="Perfil" />
          </template>
          <template v-else>
            <v-icon large color="white">mdi-account</v-icon>
          </template>
        </v-avatar>
      </v-btn>
    </v-app-bar>

    <!-- Barra lateral de perfil do usuário -->
    <v-navigation-drawer
      v-model="profileDrawer"
      right
      temporary
      width="300"
    >
      <v-list>
        <v-list-item>
          <v-avatar size="48" class="mr-3">
            <template v-if="userPhoto">
              <img :src="userPhoto" alt="Perfil" />
            </template>
            <template v-else>
              <v-icon large color="grey lighten-1">mdi-account</v-icon>
            </template>
          </v-avatar>
          <span class="text-h6">{{ userName }}</span>
        </v-list-item>
        <v-divider />
        <v-list-item @click="showProfile">
          <v-list-item-icon><v-icon>mdi-account</v-icon></v-list-item-icon>
          <v-list-item-title>Visualizar Perfil</v-list-item-title>
        </v-list-item>
        <v-list-item @click="logout">
          <v-list-item-icon><v-icon>mdi-logout</v-icon></v-list-item-icon>
          <v-list-item-title>Sair</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <!-- Modal de opções do usuário -->
    <v-dialog v-model="showUserMenu" max-width="300px">
      <v-card>
        <v-card-title>
          <v-avatar size="48" class="mr-3">
            <template v-if="userPhoto">
              <img :src="userPhoto" alt="Perfil" />
            </template>
            <template v-else>
              <v-icon large color="grey lighten-1">mdi-account</v-icon>
            </template>
          </v-avatar>
          <span class="text-h6">{{ userName }}</span>
        </v-card-title>
        <v-divider />
        <v-list>
          <v-list-item @click="showProfile">
            <v-list-item-icon><v-icon>mdi-account</v-icon></v-list-item-icon>
            <v-list-item-title>Visualizar Perfil</v-list-item-title>
          </v-list-item>
          <v-list-item @click="logout">
            <v-list-item-icon><v-icon>mdi-logout</v-icon></v-list-item-icon>
            <v-list-item-title>Sair</v-list-item-title>
          </v-list-item>
        </v-list>
      </v-card>
    </v-dialog>

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
    <!--
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
    -->

    <!-- Dialog de cadastro de famílias -->
    <v-dialog v-model="showFamilyDialog" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Cadastrar Família</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="familyData.nomeTitular" label="Nome do titular" outlined />
          <v-text-field v-model="familyData.dataNascimento" label="Data de nascimento" outlined type="date" />
          <v-text-field v-model="familyData.endereco" label="Endereço completo" outlined />
          <v-text-field v-model="familyData.qtdPessoas" label="Quantidade de pessoas na casa" outlined type="number" />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="grey" @click="showFamilyDialog = false">Cancelar</v-btn>
          <v-btn text color="primary" @click="submitFamily">Cadastrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Dialog de estoque -->
    <v-dialog v-model="showStockDialog" max-width="400px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Quantidade de Itens no Estoque</span>
        </v-card-title>
        <v-card-text>
          <div v-for="item in estoqueItens" :key="item.nome" class="mb-2">
            <strong>{{ item.nome }}:</strong> {{ item.quantidade }}
          </div>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="primary" @click="showStockDialog = false">Fechar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Dialog de doadores -->
    <v-dialog v-model="showDonorDialog" max-width="500px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Cadastrar Doador</span>
        </v-card-title>
        <v-card-text>
          <v-text-field v-model="donorData.nome" label="Nome do doador" outlined />
          <v-text-field v-model="donorData.item" label="Item doado" outlined />
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="grey" @click="showDonorDialog = false">Cancelar</v-btn>
          <v-btn text color="primary" @click="submitDonor">Salvar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Dialog de dashboard -->
    <v-dialog v-model="showDashboardDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Dashboard de Doações</span>
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col cols="12">
              <canvas id="donationChart"></canvas>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="primary" @click="showDashboardDialog = false">Fechar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Dialog de estoque financeiro -->
    <v-dialog v-model="showFinanceDialog" max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5">Estoque Financeiro</span>
        </v-card-title>
        <v-card-text>
          <v-simple-table>
            <thead>
              <tr>
                <th>Item</th>
                <th>Quantidade</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in estoqueFinanceiro" :key="item.nome">
                <td>{{ item.nome }}</td>
                <td>{{ item.quantidade }}</td>
              </tr>
            </tbody>
          </v-simple-table>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn text color="primary" @click="showFinanceDialog = false">Fechar</v-btn>
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
                    <v-card outlined hover @click="handleCardClick(item)">
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
import Chart from 'chart.js/auto';

export default {
  name: "App",
  data() {
    return {
      logoUrl:
        "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTQ8i5CQNh9Kt14yLwzKEKOQEI5HOEF_gmXvw&s",
      drawer: false,
      profileDrawer: false, // Novo estado para a barra lateral de perfil
      userPhoto: "https://randomuser.me/api/portraits/men/32.jpg", // Foto de perfil
      userName: "João Vitor", // Nome do usuário
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

      // Novo estado para o diálogo de cadastro de famílias
      showFamilyDialog: false,
      familyData: {
        nomeTitular: "",
        dataNascimento: "",
        endereco: "",
        qtdPessoas: "",
      },
      showStockDialog: false,
      estoqueItens: [
        { nome: "Arroz", quantidade: 120 },
        { nome: "Feijão", quantidade: 80 },
        { nome: "Macarrão", quantidade: 50 },
      ],
      showDonorDialog: false,
      donorData: {
        nome: "",
        item: "",
      },
      showDashboardDialog: false,
      showFinanceDialog: false,
      estoqueFinanceiro: [
        { nome: "Arroz", quantidade: 120 },
        { nome: "Dinheiro", quantidade: 500 },
        { nome: "Feijão", quantidade: 80 },
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
      switch (item.title) {
        case "Cadastrar Famílias":
          this.showFamilyDialog = true;
          break;
        case "Controle de Estoque":
          this.showStockDialog = true;
          break;
        case "Controle de Doadores":
          this.showDonorDialog = true;
          break;
        case "Dashboard de Doações":
          this.showDashboardDialog = true;
          this.$nextTick(this.renderChart);
          break;
        case "Estoque Financeiro":
        case "Estoque (Alimentos e Dinheiro)":
          this.showFinanceDialog = true;
          break;
        default:
          this.notify(`Navegando para ${item.title}`, "primary");
      }
    },
    notify(message, color = "success") {
      this.snackbarMessage = message;
      this.snackbarColor = color;
      this.showSnackbar = true;
    },
    // Novo método para lidar com o clique no cartão
    handleCardClick(item) {
      switch (item.title) {
        case "Cadastrar Famílias":
          this.showFamilyDialog = true;
          break;
        case "Controle de Estoque":
          this.showStockDialog = true;
          break;
        case "Controle de Doadores":
          this.showDonorDialog = true;
          break;
        case "Dashboard de Doações":
          this.showDashboardDialog = true;
          this.$nextTick(this.renderChart);
          break;
        case "Estoque Financeiro":
        case "Estoque (Alimentos e Dinheiro)":
          this.showFinanceDialog = true;
          break;
        default:
          this.notify(`Navegando para ${item.title}`, "primary");
      }
    },
    // Novo método para enviar o formulário de cadastro de famílias
    submitFamily() {
      const { nomeTitular, dataNascimento, endereco, qtdPessoas } = this.familyData;
      if (!nomeTitular || !dataNascimento || !endereco || !qtdPessoas) {
        this.notify("Preencha todos os campos!", "warning");
        return;
      }
      this.notify(`Família "${nomeTitular}" cadastrada com sucesso!`, "success");
      this.showFamilyDialog = false;
      this.familyData = { nomeTitular: "", dataNascimento: "", endereco: "", qtdPessoas: "" };
    },
    submitDonor() {
      const { nome, item } = this.donorData;
      if (!nome || !item) {
        this.notify("Preencha todos os campos!", "warning");
        return;
      }
      this.notify(`Doador "${nome}" cadastrou o item "${item}"!`, "success");
      this.showDonorDialog = false;
      this.donorData = { nome: "", item: "" };
    },
    submitStock() {
      this.showStockDialog = false;
    },
    submitFinance() {
      this.showFinanceDialog = false;
    },
    renderChart() {
      const ctx = document.getElementById('donationChart');
      if (!ctx) return;
      if (this._chart) {
        this._chart.destroy();
      }
      this._chart = new Chart(ctx, {
        type: 'bar',
        data: {
          labels: ['Dia', 'Mês', 'Ano'],
          datasets: [{
            label: 'Doações',
            data: [12, 50, 320],
            backgroundColor: ['#1976D2', '#43A047', '#FF9800'],
          }]
        },
        options: {
          responsive: true,
          plugins: {
            legend: { display: false }
          }
        }
      });
    },
  },
  beforeDestroy() {
    if (this._chart) {
      this._chart.destroy();
    }
  }
};
</script>
