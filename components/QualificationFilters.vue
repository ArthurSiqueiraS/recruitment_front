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
    />
    <v-range-slider v-model="filters.experienceRange" min="0" max="12" />
    <v-btn color="accent" @click="filter">Filtrar</v-btn>
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
      const locations = await this.$axios.$get(
        'http://localhost:3000/locations'
      )

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
      const technologies = await this.$axios.$get(
        'http://localhost:3000/technologies'
      )

      this.technologyOptions = technologies.map((t) => ({
        text: t.name,
        value: t.id,
      }))
    },
    filter() {
      this.$emit('filter', { ...this.filters, locations: this.locations })
    },
  },
}
</script>
<style lang="scss">
.qualification-filters {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
