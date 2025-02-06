<template>
    <Page>
      <ActionBar title="Próxima Página" />
      <StackLayout>
        <Label text="Você está na próxima página!" class="message" />
        <Button text="Voltar" @tap="goBack" />
      </StackLayout>
    </Page>
  </template>
  
  <script>
  export default {
    methods: {
      goBack() {
        this.$navigateBack();
      },
    },
  };
  </script>
  
  <style scoped>
  .message {
    font-size: 20;
    text-align: center;
    margin: 20;
  }
  </style>
  