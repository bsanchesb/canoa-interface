<template>
<div>
  <b-navbar toggleable="md" type="dark" variant="info">

    <b-navbar-toggle target="nav_collapse"></b-navbar-toggle>

    <b-navbar-brand href="#">Canoa Veiculos</b-navbar-brand>

    <b-collapse is-nav id="nav_collapse">
      <b-navbar-nav>
        <b-nav-item href="#">Adicionar Veiculos</b-nav-item>
      </b-navbar-nav>
      <!-- Right aligned nav items -->
      <b-navbar-nav class="ml-auto">

        <b-nav-form>
          <b-form-input size="sm" class="mr-sm-2" type="text" placeholder="Pesquisar"/>
          <b-button size="sm" class="my-2 my-sm-0" type="submit">Buscar</b-button>
        </b-nav-form>

      </b-navbar-nav>

    </b-collapse>
    </b-navbar>
      <b-card title="Veículos">
         <b-container fluid>
   

    <!-- Main table element -->
    <b-table show-empty
             stacked="md"
             :items="items"
             :fields="fields"
             :current-page="currentPage"
             :per-page="perPage"
             :filter="filter"
             :sort-by.sync="sortBy"
             :sort-desc.sync="sortDesc"
             @filtered="onFiltered"
    >
      <template slot="veheicle" slot-scope="row">{{row.value.veheicle}}</template>
      <template slot="sold" slot-scope="row">{{row.value?'Sim :)':'Não :('}}</template>
      <template slot="actions" slot-scope="row">
        <!-- We use @click.stop here to prevent a 'row-clicked' event from also happening -->
        <b-button size="sm" @click.stop="info(row.item, row.index, $event.target)" class="mr-1 btn-primary">
          Editar
        </b-button>
        <b-button size="sm" class="btn-danger">
          Excluir
        </b-button>
      </template>
      <template slot="row-details" slot-scope="row">
        <b-card>
          <ul>
            <li v-for="(value, key) in row.item" :key="key">{{ key }}: {{ value}}</li>
          </ul>
        </b-card>
      </template>
    </b-table>
    <b-col md="6" class="my-1">
        <b-pagination :total-rows="totalRows" :per-page="perPage" v-model="currentPage" class="my-0" />
      </b-col>

    <b-modal id="modalInfo" @hide="resetModal" title="Editar" ok-only>
      
    </b-modal>

  </b-container>
      </b-card>
    </div>
</template>

<script>
const items = [
  { isActive: true, yaer: 40, veheicle: { veheicle: 'Dickerson' } }
]
import axios from 'axios';
export default {
  data () {
    return {
      items: [],
      fields: [
        { key: 'vehicle', label: 'Veículo', sortable: true },
        { key: 'brand', label: 'Marca'},
        { key: 'description', label: 'Descrição'},
        { key: 'year', label: 'Ano', sortable: true, 'class': 'text-center' },
        { key: 'sold', label: 'Vendido' },
        { key: 'actions', label: '' }
      ],
      currentPage: 1,
      perPage: 5,
      totalRows: items.length,
      pageOptions: [ 5, 10, 15 ],
      sortBy: null,
      sortDesc: false,
      filter: null,
      modalInfo: { title: '', content: '' }
    }
  },
  computed: {
    sortOptions () {
      return this.fields
        .filter(f => f.sortable)
        .map(f => { return { text: f.label, value: f.key } })
    }
  },
   
  created() {
    axios.get(`http://canoa.api/api/vehicles`)
    .then(response => {
      this.items = response.data.result.vehicles
    })
    .catch(e => {
      this.errors.push(e)
    })
  },

  methods: {
    info (item, index, button) {
      this.modalInfo.title = `Row index: ${index}`
      this.modalInfo.content = JSON.stringify(item, null, 2)
      this.$root.$emit('bv::show::modal', 'modalInfo', button)
    },
    resetModal () {
      this.modalInfo.title = ''
      this.modalInfo.content = ''
    },
    onFiltered (filteredItems) {
      
      this.totalRows = filteredItems.length
      this.currentPage = 1
    }
  }
}
</script>
