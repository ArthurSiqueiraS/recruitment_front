<template>
  <CollectionContainer
    :handle-filter="
      (filters) => {
        params = filters
      }
    "
  >
    <CandidatesList :params="params" />
  </CollectionContainer>
</template>
<script>
export default {
  data() {
    return {
      params: {},
    }
  },
}
</script>
