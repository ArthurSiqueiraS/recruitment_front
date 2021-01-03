<template>
  <div id="index" class="d-flex flex-column flex-sm-row">
    <v-btn depressed tile color="primary" to="/candidates">
      Quero buscar por candidatos para uma vaga
    </v-btn>
    <v-btn depressed tile color="secondary" to="/jobs">
      Quero verificar as vagas abertas
    </v-btn>
  </div>
</template>
<style lang="scss">
#index {
  height: 100vh;
  width: 100vw;

  .v-btn {
    flex-grow: 1;
    height: auto;
    font-size: 24px;

    .v-btn__content {
      max-width: 100%;
      white-space: normal;
      text-align: center;
    }
  }
}
</style>
