<template>
  <CollectionContainer
    :handle-filter="
      (params) => {
        fetchJobs(params), (page = 1)
      }
    "
  >
    <v-pagination v-model="page" :length="totalPages" class="mt-2" />
    <div class="no-data" v-if="loading">
      <v-progress-circular indeterminate color="secondary" size="80" />
    </div>
    <template v-else>
      <template v-if="jobs.length > 0">
        <v-row no-gutters style="width: 100%" align-content="start">
          <v-col
            v-for="job in jobs"
            :key="job.id"
            cols="12"
            lg="6"
            class="pa-4 pa-md-8"
          >
            <v-card class="job-card">
              <v-row no-gutters>
                <v-col cols="12" sm="6">
                  <div class="job-location">
                    {{
                      job.location
                        ? `${job.location.city} - ${job.location.state}`
                        : 'Remote'
                    }}
                  </div>
                  <div class="job-experience">
                    {{
                      job.exp_min + (job.exp_max ? ` - ${job.exp_max}` : '+')
                    }}
                    anos de experiência
                  </div>
                </v-col>
                <v-col class="mt-8 mt-sm-0" cols="12" sm="6">
                  <div class="job-technologies d-flex justify-end">
                    <v-chip
                      v-for="tech in job.technologies"
                      :key="tech.id"
                      color="secondary"
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
              <v-row
                no-gutters
                class="mt-12"
                align="end"
                justify="space-between"
              >
                <div class="job-id">#{{ job.base_id || job.id }}</div>
                <v-btn
                  depressed
                  color="primary"
                  style="border-radius: 15px"
                  @click="viewCandidates(job)"
                  >Ver melhores candidatos</v-btn
                >
              </v-row>
            </v-card>
          </v-col>
        </v-row>
        <v-dialog v-model="candidatesDialog" width="fit-content">
          <v-card class="job-candidates" color="primary">
            <CandidatesList :params="candidateParams" />
          </v-card>
        </v-dialog>
      </template>
      <div class="no-data" v-else>Nenhuma vaga encontrada</div>
    </template>
  </CollectionContainer>
</template>
<script>
export default {
  data() {
    return {
      jobs: [],
      params: {},
      totalPages: null,
      page: 1,
      candidatesDialog: false,
      candidateParams: {},
      loading: true,
    }
  },
  mounted() {
    this.fetchJobs()
  },
  methods: {
    async fetchJobs(params = {}) {
      this.loading = true

      const response = await this.$axios.$get('/jobs', {
        params: { ...params, page: this.page },
      })

      this.jobs = response.jobs
      this.totalPages = response.pages
      this.params = params
      this.loading = false
    },
    viewCandidates(job) {
      this.candidateParams = {
        locations: (job.location || {}).id,
        experienceRange: [job.exp_min, job.exp_max],
        technologies: job.technologies.map((t) => t.id),
      }

      this.candidatesDialog = true
    },
  },
  watch: {
    page() {
      this.fetchJobs(this.params)
    },
  },
}
</script>
<style lang="scss">
.job-card {
  padding: 24px;
  box-shadow: 0 0 5px 1px rgba(#41a9b9, 0.25) !important;
  border-radius: 15px !important;

  .job-location {
    font-size: 24px;
    font-weight: bold;
    color: var(--v-secondary-base);
    margin-bottom: 6px;
  }

  .job-experience {
    font-weight: 500;
    color: var(--v-primary-base);
  }

  .job-id {
    font-size: 14px;
    color: grey;
  }

  .job-technologies {
    display: flex;
    flex-wrap: wrap;

    .v-chip {
      margin: 4px;
    }
  }
}

.job-candidates {
  display: flex;
  justify-content: center;
  border-radius: 15px !important;

  .candidate-card {
    padding: 16px;

    .candidate-technologies {
      .v-chip {
        font-size: 12px;
        height: 24px;
        margin: 4px;
        border: 3px solid transparent !important;
      }
    }
  }

  .no-data {
    color: white;
    font-size: 20px;
    text-align: center;
    padding: 20px;
  }

  @media screen and(min-width: 600px) {
    .no-data {
      padding: 60px;
    }
  }
}
</style>
