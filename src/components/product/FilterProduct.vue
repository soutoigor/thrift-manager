<template>
  <v-row :no-gutters="$vuetify.breakpoint.smAndDown">
    <v-col
      cols="12"
      md="3"
    >
      <v-text-field
        v-model="filter.name"
        label="Descrição"
        clearable
        outlined
        @change="filterProducts"
      />
    </v-col>
    <v-col
      cols="12"
      md="3"
    >
      <v-autocomplete
        v-model="filter.category_id"
        :items="categories"
        label="Categoria"
        item-text="name"
        item-value="id"
        :loading="isLoadingCategories"
        outlined
        clearable
        @change="filterProducts"
      />
    </v-col>
    <v-col
      cols="12"
      md="3"
    >
      <v-autocomplete
        v-model="filter.provider_id"
        :items="providers"
        label="Fornecedor"
        item-text="name"
        item-value="id"
        :loading="isLoadingProviders"
        outlined
        clearable
        @change="filterProducts"
      />
    </v-col>

    <v-col
      cols="12"
      md="3"
    >
      <v-menu
        v-model="dateMenu"
        :close-on-content-click="false"
        transition="scale-transition"
        offset-y
        max-width="290px"
        min-width="auto"
      >
        <template #activator="{ on }">
          <v-text-field
            :value="dateFormatted"
            label="Data de inclusão"
            readonly
            outlined
            append-icon="mdi-calendar"
            v-on="on"
            clearable
            @click:clear="clearDates"
          />
        </template>
        <v-date-picker
          v-model="dates"
          no-title
          range
          color="secondary"
          @input="filterProducts"
        />
      </v-menu>
    </v-col>
  </v-row>
</template>

<script>
import { mapActions, mapGetters } from 'vuex'
import dates from '@/mixins/dates'

export default {
  mixins: [dates],
  data: () => ({
    filter: {},
    dateMenu: false,
    dates: [],
  }),
  computed: {
    ...mapGetters({
      categories: 'category/categories',
      providers: 'provider/providers',
      isLoadingCategories: 'category/isLoading',
      isLoadingProviders: 'provider/isLoading',
    }),
    dateFormatted() {
      return this.dateRange(this.dates)
    },
  },
  methods: {
    ...mapActions({
      setFilter: 'product/setFilter',
      listProducts: 'product/listProducts',
    }),
    clearDates() {
      this.dates = []
      this.filterProducts()
    },
    closeDateMenu() {
      this.dateMenu = false
    },
    filterProducts() {
      const [start_date, end_date] = this.sortDates(this.dates)
      const filter = {
        ...this.filter,
        start_date,
        end_date,
      }

      this.setFilter(filter)
      this.listProducts()
    },
  },
}
</script>
