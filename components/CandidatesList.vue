<template>
  <div class="flex-grow-1 d-flex flex-column">
    <div class="no-data" v-if="loading">
      <v-progress-circular indeterminate color="secondary" size="80" />
    </div>
    <template v-else>
      <template v-if="candidates.length > 0">
        <v-card
          v-for="candidate in candidates"
          :key="candidate.id"
          class="candidate-card ma-3 ma-sm-5"
        >
          <v-row no-gutters>
            <v-col cols="12" sm="6">
              <div class="candidate-id">
                ID do candidato: #{{ candidate.base_id || candidate.id }}
              </div>
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
                  :color="!tech.main ? 'primary' : 'accent'"
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
      </template>
      <div class="no-data" v-else>
        Não foram encontrados candidatos com estes requisitos
      </div>
    </template>
  </div>
</template>
<script>
export default {
  props: { params: { type: Object, default: () => ({}) } },
  data() {
    return { candidates: [], loading: true }
  },
  watch: {
    params: {
      immediate: true,
      deep: true,
      handler() {
        this.fetchCandidates()
      },
    },
  },
  methods: {
    async fetchCandidates() {
      this.loading = true
      this.candidates = await this.$axios.$get('/candidates', {
        params: this.params,
      })
      this.loading = false
    },
  },
}
</script>
<style lang="scss">
.candidate-card {
  padding: 24px;
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
      max-width: 100%;

      &.target-tech {
        border-color: var(--v-success-base) !important;
      }
    }
  }
}
</style>
