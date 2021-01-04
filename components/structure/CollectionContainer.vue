<template>
  <div
    class="collection-page d-flex flex-column align-center flex-md-row align-md-start"
  >
    <div class="filters d-flex flex-column align-center">
      <QualificationFilters
        @filter="
          (params) => {
            handleFilter(params)
          }
        "
      />
    </div>
    <v-divider
      :vertical="$vuetify.breakpoint.mdAndUp"
      style="width: 100%"
      class="my-4 my-md-0"
    />
    <div class="collection-container py-md-6 px-md-16">
      <slot />
    </div>
  </div>
</template>
<script>
export default {
  props: {
    handleFilter: { type: Function, default: () => {} },
  },
}
</script>
<style lang="scss">
.collection-page {
  min-height: 100%;

  .filters {
    padding: 32px;
    width: 350px;
    max-width: 100%;
  }

  .collection-container {
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
  }
}
</style>
