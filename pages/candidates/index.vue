<template>
  <CollectionContainer :handle-filter="fetchCandidates">
    <v-card
      v-for="candidate in candidates"
      :key="candidate.id"
      class="candidate-card"
    >
      <v-row no-gutters>
        <v-col cols="12" sm="6">
          <div class="candidate-id">ID do candidato: #{{ candidate.id }}</div>
          <div class="candidate-location">
            {{ candidate.location.city }} - {{ candidate.location.state }}
          </div>
          <div class="candidate-experience">
            {{
              candidate.exp_min +
              (candidate.exp_max ? ` - ${candidate.exp_max}` : '+')
            }}
            anos de experiência
          </div>
        </v-col>
        <v-col class="candidate-technologies mt-8 mt-sm-0" cols="12" sm="6">
          <div v-for="tech in candidate.technologies" :key="tech.id">
            <v-chip
              :color="tech.main ? 'secondary' : 'accent'"
              :class="
                (params.technologies &&
                  params.technologies.includes(tech.id)) == true
                  ? 'target-tech'
                  : ''
              "
            >
              {{ tech.name }}
            </v-chip>
          </div>
        </v-col>
      </v-row>
    </v-card>
  </CollectionContainer>
</template>
<script>
export default {
  async asyncData({ $axios }) {
    const candidates = await $axios.$get('http://localhost:3000/candidates')
    console.log(candidates)
    return { candidates }
  },
  data() {
    return { params: {} }
  },
  methods: {
    async fetchCandidates(params) {
      this.candidates = await this.$axios.$get(
        'http://localhost:3000/candidates',
        {
          params,
        }
      )

      this.params = params
    },
  },
}
</script>
<style lang="scss">
.candidate-card {
  padding: 24px;
  margin: 20px;
  width: -webkit-fill-available;
  max-width: 1000px;
  box-shadow: 0 0 5px 1px rgba(#6e2b77, 0.25) !important;
  border-radius: 15px !important;

  .candidate-id {
    font-size: 24px;
    font-weight: bold;
    color: var(--v-secondary-base);
    margin-bottom: 12px;
  }

  .candidate-location,
  .candidate-experience {
    font-weight: 500;
    color: var(--v-primary-base);
  }

  .candidate-technologies {
    display: flex;
    flex-wrap: wrap;

    .v-chip {
      margin: 4px;
      border: 3px solid transparent !important;

      &.target-tech {
        border-color: var(--v-primary-base) !important;
      }
    }
  }
}
</style>
