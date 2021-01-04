<template>
  <div class="qualification-filters">
    <v-radio-group v-model="remote" row>
      <v-radio :value="true" label="Remoto" />
      <v-radio :value="false" label="Local" />
    </v-radio-group>
    <v-autocomplete
      v-model="selectedState"
      :items="stateOptions"
      label="Estado"
      clearable
      :disabled="remote"
    />
    <v-autocomplete
      v-model="selectedCity"
      :items="cityOptions"
      label="Cidade"
      clearable
      :disabled="remote || !selectedState"
    />
    <v-autocomplete
      v-model="filters.technologies"
      :items="technologyOptions"
      multiple
      chips
      label="Tecnologias"
      clearable
      deletable-chips
    />
    <div class="mt-4 grey--text">Tempo de experiência</div>
    <v-range-slider
      v-model="filters.experienceRange"
      min="0"
      max="12"
      style="width: 100%"
      class="mt-6"
      thumb-size="25"
      thumb-label="always"
    />
    <v-btn color="accent" @click="filter" depressed>Filtrar</v-btn>
    <div class="mt-16 pt-md-16 d-flex flex-column">
      <v-btn
        @click="updateBase"
        color="secondary"
        small
        :loading="updating"
        depressed
      >
        Atualizar base
      </v-btn>
      <v-btn class="mt-4" text small color="grey" to="/">Voltar</v-btn>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      remote: false,
      locationOptions: [],
      stateOptions: [],
      technologyOptions: [],
      selectedState: null,
      selectedCity: null,
      filters: {
        experienceRange: [0, 12],
        technologies: [],
      },
      updating: false,
    }
  },
  mounted() {
    this.fetchLocationOptions()
    this.fetchTechnologyOptions()
  },
  computed: {
    cityOptions() {
      const stateLocations = this.locationOptions.filter((location) => {
        return location.state === this.selectedState
      })

      return stateLocations.map((l) => ({
        text: l.city,
        value: l.id,
      }))
    },
    remoteLocation() {
      const remoteLocation = this.locationOptions.find(
        (l) => l.city === 'Remote'
      )

      return remoteLocation ? remoteLocation.id : null
    },
    locations() {
      if (this.remote || !this.selectedState) return null

      if (this.selectedCity) {
        return this.selectedCity
      }

      return this.cityOptions.map((c) => c.value)
    },
  },
  methods: {
    async fetchLocationOptions() {
      const locations = await this.$axios.$get('/locations')

      locations.sort((a, b) => {
        if (a.state === b.state) {
          return a.city > b.city ? 1 : -1
        } else {
          return a.state > b.state ? 1 : -1
        }
      })

      locations.forEach((location) => {
        if (location.state && !this.stateOptions.includes(location.state)) {
          this.stateOptions.push(location.state)
        }
      })

      this.locationOptions = locations
    },
    async fetchTechnologyOptions() {
      const technologies = await this.$axios.$get('/technologies')

      this.technologyOptions = technologies.map((t) => ({
        text: t.name,
        value: t.id,
      }))
    },
    filter() {
      this.$emit('filter', {
        ...this.filters,
        locations: this.locations,
        remote: this.remote && this.remoteLocation,
      })
    },
    async updateBase() {
      this.updating = true
      await this.$axios.post('/update_base')

      this.fetchLocationOptions()
      this.fetchTechnologyOptions()
      this.filter()
      this.updating = false
    },
  },
}
</script>
<style lang="scss">
.qualification-filters {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
}
</style>
