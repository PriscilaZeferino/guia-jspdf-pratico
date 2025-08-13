<template>
  <div id="app">
    <button class="btn" @click="gerarPDFSemTabela()">
      Gerar PDF sem tabela
    </button>
    <button class="btn" @click="gerarPDFComTabela()">
      Gerar PDF com tabela
    </button>
  </div>
</template>

<script>
import jsPDF from "jspdf";
import "jspdf-autotable";
import Imagem from "./assets/imagem.png";

export default {
  name: "App",
  components: {},
  methods: {
    gerarPDFSemTabela() {
      let doc = new jsPDF("p", "mm", "a4");

      const larguraX = doc.internal.pageSize.getWidth();
      const alturaY = doc.internal.pageSize.getHeight();

      //margem
      let margemDireita = 10;
      let margemEsquerda = 10;

      //posicionamento
      let x = 0.9;
      let y = 5;
      doc.text("Olá, mundo!", x, y);

      //imagem
      let imagem = Imagem;
      let tipoDeImagem = "PNG";
      let posX = x;
      let posY = y + 5;
      let larguraDaImagem = 50;
      let alturaDaImagem = 50;
      let compressaoDeImagem = "FAST";

      doc.addImage(
        imagem,
        tipoDeImagem,
        posX,
        posY,
        larguraDaImagem,
        alturaDaImagem,
        compressaoDeImagem
      );

      y += alturaDaImagem;

      //texto
      let alturaDaLinha = 10; //vc descobre a altura que uma linha ocupa por tentativa e erro
      y += alturaDaLinha;
      
      doc.text("TEXTO", margemEsquerda, y);
      doc.setTextColor(100);
      y += alturaDaLinha;

      doc.text("TEXTO", margemEsquerda, y);
      y += alturaDaLinha;

      doc.setTextColor("black");
      doc.text("TEXTO", margemEsquerda, y);
      y += alturaDaLinha;

      //quebra de texto
      let larguraDisponivel = larguraX - margemDireita;
      let texto =
        "Estou escrevendo um texto muito grande para que ele ultrapasse o tamanho máximo da página de propósito";
      y = y + alturaDaLinha;

      //sem quebra de texto
      doc.text("SEM QUEBRA DE TEXTO", margemEsquerda, y);
      y += alturaDaLinha;

      doc.text(texto, margemEsquerda, y);
      y += alturaDaLinha;

      //com quebra de texto
      doc.text("COM QUEBRA DE TEXTO", margemEsquerda, y);
      y += alturaDaLinha;

      if (doc.getTextWidth(texto) <= larguraDisponivel) {
        doc.text(texto, margemEsquerda, y);
        y += alturaDaLinha;
      } else {
        const textoQuebrado = doc.splitTextToSize(texto, larguraDisponivel);
        textoQuebrado.forEach((linha) => {
          doc.text(linha, margemEsquerda, y);
          y += alturaDaLinha;
        });
      }

      //desenhar linhas como o separador - sem margem
      doc.setDrawColor("#000");
      doc.setLineWidth(0.01);
      doc.setLineDash([], 0);
      doc.line(0, y, larguraX, y, "S");
      y += alturaDaLinha;

      //desenhar linhas como o separador - com margem
      doc.setDrawColor("#000");
      doc.setLineWidth(0.01);
      doc.setLineDash([], 0);
      doc.line(0 + margemEsquerda, y, larguraX - margemDireita, y, "S");

      //quebra de pagina
      //com a quebra de pagina precisamos resetar as variaveis x e y. podemos manter
      //a x se vamos usar a mesma margem e apenas modificar a y.

      //independente se o conteudo ultrapassa ou nao a pagina
      doc.addPage();
      doc.text("NOVA PAGINA", margemEsquerda, y);

      //resetando a variavel y
      y = 5;
      doc.text("NOVA PAGINA 2", margemEsquerda, y);

      //como saber se preciso gerar uma nova pagina?
      //vc pode verificar se y eh maior que a alturaY
      if (y >= alturaY) {
        doc.addPage();
        y = 5;
        doc.text("NOVA PAGINA 3", margemEsquerda, y);
      }

      //vamos supor que sim definindo y como mairo q alturaY
      y = alturaY + 10;

      if (y >= alturaY) {
        doc.addPage();
        y = 5;
        doc.text("NOVA PAGINA 4 - passei", margemEsquerda, y);
      }

      var blob = doc.output("blob");
      window.open(URL.createObjectURL(blob));
    },
    gerarPDFComTabela() {
      const dados = [
        {
          id: 1,
          nome: "Ana Silva",
          email: "ana.silva@example.com",
          dataCadastro: "2025-08-10",
          status: "Ativo",
        },
        {
          id: 2,
          nome: "Bruno Costa",
          email: "bruno.costa@example.com",
          dataCadastro: "2025-07-25",
          status: "Inativo",
        },
        {
          id: 3,
          nome: "Carla Souza",
          email: "carla.souza@example.com",
          dataCadastro: "2025-08-01",
          status: "Pendente",
        },
      ];

      let doc = new jsPDF("p", "mm", "a4");
      let y = 10;

      //com dados fixos
      doc.autoTable({
        head: [["Nome", "Idade", "Email"]],
        body: [
          ["João", 25, "joao@email.com"],
          ["Maria", 30, "maria@email.com"],
        ],
      });

      //como ver onde a ultima tabela termina?
      y = doc.autoTable.previous.finalY + 4;

      doc.autoTable({
        margin: [6, 5, 6, 5],
        startY: y,
        body: [
          ["João", 25, "joao@email.com"],
          ["Maria", 30, "maria@email.com"],
        ],
      });

      y = doc.autoTable.previous.finalY + 4;

      //com dados dinamicos
      doc.autoTable({
        head: [Object.keys(dados[0])],
        body: dados.map(obj => Object.values(obj))
      });

      y = doc.autoTable.previous.finalY + 4;

      //personalizando linhas
      //quando personalizo as linhas e preciso de um head personalizado,
      //eu uso a primeira linha 
      let headerStyle = {
        halign: "left",
        fillColor: [251, 98, 85],
        textColor: "#fff",
        fontStyle: "bold"
      }

      const bodyStyle = {
        halign: "left",
        textColor: "#666",
        fontStyle: "normal"
      }

      let dadosFormatados = []
      dadosFormatados.push([
        { content: 'Nome', colSpan: 1, styles: headerStyle },
        { content: 'Email', colSpan: 1, styles: headerStyle },
        { content: 'Status', colSpan: 1, styles: headerStyle }
      ])

      dados.map((item) => {
        console.log(item)
        dadosFormatados.push([
        { content: item.nome, colSpan: 1, styles: bodyStyle },
        { content: item.email, colSpan: 1, styles: bodyStyle },
        { content: item.status, colSpan: 1, styles: bodyStyle },
        ])
      })

      doc.autoTable({
        head: "",
        body: dadosFormatados,
        startY: y,
        showHead: "firstPage",
        rowPageBreak: "avoid",
        pageBreak: "auto",
        tableWidth: "auto",
        margin: [5, 5, 5, 5],
      });


      var blob = doc.output("blob");
      window.open(URL.createObjectURL(blob));
    },
  },
};
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
  margin: 10px;
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
