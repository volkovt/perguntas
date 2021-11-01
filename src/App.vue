<template>
  <div>
    <PlacarJogo :vitoria="this.vitorias" :derrota="this.derrotas"/>
    <template v-if="this.pergunta">
      <h1 v-html="this.pergunta"></h1>
      <template v-for="(resposta, index) in this.respostas" :key="index">
        <input
          :disabled="this.respostaEnviada"
          type="radio"
          name="options"
          :value="resposta"
          v-model="this.respostaEscolhida"
        />
        <label v-html="resposta"> </label><br />
      </template>
      <button v-if="!respostaEnviada" class="send" type="button" @click="enviarResposta">Send</button>

      <section v-if="respostaEnviada" class="result">
        <h4 
          v-if="this.respostaEscolhida != this.respostaCorreta"
          v-html="'&#10060; Me desculpe, você escolheu a resposta errada. A opção correta era ' + this.respostaCorreta">
        </h4>
        <h4 v-else
          v-html="'&#9989; Parabéns, a resposta ' + this.respostaCorreta + ' está correta.'">
          
        </h4>
        <button class="send" type="button" @click="getNovaPergunta()">Próxima</button>
      </section>

    </template>
  </div>
  <router-view />
</template>

<script>
import PlacarJogo from '@/components/Placar.vue'

export default {
  name: "App",
  components: { PlacarJogo },
  data() {
    return {
      pergunta: null,
      respostasIncorretas: null,
      respostaCorreta: null,
      respostaEscolhida: null,
      respostaEnviada: false,
      vitorias: 0,
      derrotas: 0
    };
  },
  computed: {
    respostas() {
      var respostas = JSON.parse(JSON.stringify(this.respostasIncorretas));
      respostas.splice(
        Math.round(Math.random() * respostas.length + 1),
        0,
        this.respostaCorreta
      );
      return respostas;
    },
  },
  created() {
    var placar = localStorage.getItem('placar')
    if(placar) {
      placar = JSON.parse(placar)
      this.vitorias = placar.vitoriaContador
      this.derrotas = placar.derrotaContador
    }
    this.getNovaPergunta()
  },
  methods: {
    getNovaPergunta() {
      this.respostaEnviada = false
      this.respostaEscolhida = null
      this.pergunta = null
      
      this.axios
      .get("https://opentdb.com/api.php?amount=10&category=30")
      .then((response) => {
        /* response.data.results.forEach(pergunta => {
         this.perguntas.push(pergunta.question)
         this.perguntasIncorretas.push(pergunta.incorrect_answers)
         this.perguntaCorreta.push(pergunta.correct_answer)
       }) */
        this.pergunta = response.data.results[0].question;
        this.respostasIncorretas = response.data.results[0].incorrect_answers;
        this.respostaCorreta = response.data.results[0].correct_answer;
      });
    },
    enviarResposta: function () {
      if (!this.respostaEscolhida) {
        alert("Pick you option");
      } else {
        this.respostaEnviada = true
        if (this.respostaEscolhida == this.respostaCorreta) {
          this.vitorias++
        } else {
          this.derrotas++
        }

        var vitoriaContador = this.vitorias
        var derrotaContador = this.derrotas
        localStorage.setItem('placar', JSON.stringify({vitoriaContador, derrotaContador}))
      }
    },
  }
};
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  max-width: 960px;
  margin: 60px auto;
}

#app input[type="radio"] {
  margin: 12px 4px;
}

#app button {
  font-size: 14px;
  font-weight: 500;
  line-height: 1.71em;
  text-transform: uppercase;
  text-align: center;
  color: #fff;
  width: 8em;
  border-radius: 8px;
  background-color: #1976d2;
  cursor: pointer;
  box-shadow: 1px 1px 1px 1px rgba(0, 0, 0, 0.2);
}

#app button:hover {
  box-shadow: 2px 2px 2px 1px rgba(0, 0, 0, 0.4);
}
</style>
