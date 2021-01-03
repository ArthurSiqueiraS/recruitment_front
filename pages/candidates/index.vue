<template>
  <div>
    <QualificationFilters
      @filter="
        (params) => {
          fetchCandidates(params)
        }
      "
    />
    <div v-for="candidate in candidates" :key="candidate.id">
      {{ candidate.id }}
    </div>
  </div>
</template>
<script>
export default {
  async asyncData({ $axios }) {
    const candidates = await $axios.$get('http://localhost:3000/candidates')
    console.log(candidates)
    return { candidates }
  },
  methods: {
    async fetchCandidates(params) {
      this.candidates = await this.$axios.$get(
        'http://localhost:3000/candidates',
        {
          params,
        }
      )
    },
  },
}
</script>
