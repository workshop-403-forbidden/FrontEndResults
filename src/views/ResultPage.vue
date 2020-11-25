<template>
  <div class="resultpage">
    <h1>Résultat du test antigénique</h1>
    <div class="paragraphe">Bonjour <span>{{nom}} {{prenom}}</span>,</div>
    <br/>
    <div v-if="resultCovid === null" >
      <div class="paragraphe" >
        Les résultats de votre test antigénique réalisé le <span>{{dateTest}}</span> à <span>{{heureTest}}</span> ne nous sont toujours pas parvenus.
      </div>
    </div>
    <div v-if="resultCovid === true" >
      <div class="paragraphe" >
        Suite à votre test antigénique réalisé le <span>{{dateTest}}</span> à <span>{{heureTest}}</span>.
      </div>
      <div class="paragraphe" >
        Nous vous informons que votre test c'est avéré <span class="negatif">positif</span>, votre médecin traitan {{ nomMedecinTraitant }} et l'assurance maladie ont été informé.
      </div>
      <button class="printButton" v-on:click="printPDF">Imprimer</button>
    </div>
    <div v-if="resultCovid === false" >
      <div class="paragraphe" >
        Suite à votre test antigénique réalisé le <span>{{dateTest}}</span> à <span>{{heureTest}}</span>.
      </div>
      <br/>
      <div class="paragraphe" >
        Nous vous informons que votre test c'est avéré <span class="positif">négatif</span>.
      </div>
      <br/>
      <div class="paragraphe">Un mail à été envoyé à votre adress électronique: <span>{{adresseMail}}</span></div>
      <button class="printButton" v-on:click="printPDF">Imprimer</button>
    </div>
  </div>
</template>

<script>
import moment from 'moment';

export default {
  name:"ResultPage",
  data() {
    return {
      adresseMail: '',
      adressePostale: '',
      nom: '',
      nomMedecinTraitant: '',
      prenom: '',
      choixPostale: '',
      tokenOfResult: this.$route.query.id,
      results: [],
      linkPDF: '',
      dateTest: '',
      heureTest: '',
      resultCovid: null,
    }
  },
  mounted() {
    this.getResultOfPatient()
  },
  watch: {
    results: function (newResults, oldResults) {
      if (newResults !== oldResults) {
        this.getResultData();
      }
    }
  },
  methods: {
    getResultOfPatient() {
      fetch(`https://workshop.mathiasughetto.fr/api/patients/${this.tokenOfResult}`)
      .then(result => {
        result.json().then(data => {
          this.adresseMail = data.adresseMail;
          this.adressePostale = data.adressePostale;
          this.nom = data.nom;
          this.nomMedecinTraitant = data.nomMedecinTraitant;
          this.prenom = data.prenom;
          this.results = data.resultats;
        });
      })
    },
    getResultData () {
      fetch(`https://workshop.mathiasughetto.fr${this.results[0]}`)
      .then(result => {
        result.json().then(data => {
          this. choixPostale = data.choixPostale;
          this.linkPDF = `https://workshop.mathiasughetto.fr${data.resultatPdf}`;
          // this.resultCovid = data.resultat;
          this.resultCovid = null;
          this.dateTest = moment(new Date(data.dateHeureTest)).format('DD/MM/YYYY');
          this.heureTest = moment(new Date(data.dateHeureTest)).format('HH:mm');
          });
      })
    },
    printPDF() {
      window.open(this.linkPDF);
    }
  },
}
</script>

<style>
  .printButton{
    position: fixed;
    right: 20px;
    top: 20px;
    background-color: rgb(84, 70, 84);
    color: beige;
    border-radius: 25px;
    margin: 10px;
    padding: 20px;
    font-size: 30px;
    text-align: center;
  }
  .paragraphe {
    text-align: justify !important;
    margin-right: 15%;
    margin-left: 15%;
    font-size: 30px;
  }

  span{
    font-size: 35px;
  }

  .positif{
    color: green;
  }
  
  .negatif{
    color:red;
  }
</style>