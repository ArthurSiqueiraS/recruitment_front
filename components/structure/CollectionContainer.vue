<template>
  <div
    class="collection-page d-flex flex-column align-center flex-md-row align-md-start"
  >
    <div
      class="filters d-flex flex-column align-center"
      :style="{
        width: mobile ? '100%' : '400px',
      }"
    >
      <QualificationFilters
        @filter="
          (params) => {
            handleFilter(params)
          }
        "
      />
    </div>
    <v-divider :vertical="!mobile" style="width: 100%" />
    <div
      class="collection-container py-md-6 px-md-16"
      :style="{
        width: mobile ? '100%' : 'min-content',
      }"
    >
      <slot />
    </div>
  </div>
</template>
<script>
export default {
  props: {
    handleFilter: { type: Function, default: () => {} },
  },
  computed: {
    mobile() {
      return this.$vuetify.breakpoint.smAndDown
    },
  },
}
</script>
<style lang="scss">
.collection-page {
  min-height: 100%;
  height: 100%;

  .filters {
    padding: 32px;
    background: white;
    position: sticky;
  }

  .collection-container {
    background: var(--v-background-base);
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: min-content;
    height: 100%;
    min-height: 300px;

    .no-data {
      margin: auto;
      font-size: 24px;
      color: grey;
      text-align: center;
    }
  }
}
</style>
