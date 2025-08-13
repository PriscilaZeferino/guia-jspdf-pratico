<template>
  <div id="app">
    <button class="btn" @click="gerarPDFSemTabela()">Gerar PDF sem tabela</button>
    <button class="btn" @click="gerarPDFComTabela()">Gerar PDF com tabela</button>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {

  },
  methods: {
    gerarPDFSemTabela() {},
    gerarPDFComTabela() {},
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.btn {
  background-color: #4f46e5;
  color: #fff;
  border: none;
  padding: 0.75rem 1.5rem;
  font-size: 1rem;
  font-weight: 500;
  border-radius: 8px;
  cursor: pointer;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
  margin: 10px
}

.btn:hover {
  background-color: #4338ca;
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
}

.btn:active {
  transform: translateY(0);
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1);
}
</style>
