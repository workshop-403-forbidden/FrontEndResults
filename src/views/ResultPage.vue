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
      <br/>
      <div class="paragraphe">Un mail à été envoyé à votre adress électronique: <span>{{adresseMail}}</span></div>
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
    <div class="paragraphe recommandation">
        <span>Recommandation:</span>
        <br/><br/>
        - Se laver régulièrement les mains ou utiliser une solution hydro-alcoolique
        <br/>
        - Tousser ou éternuer dans son coude ou dans son mouchoir
        <br />
        - Se moucher dans un mouchoir à usage unique puis le jeter
        <br />
        - Porter un masque quand la distance d’un mètre ne peut pas être respectée et partout où cela est obligatoire
        <br />
        - Respecter une distance d’au moins un mètre avec les autres
        <br />
        - Limiter au maximum ses contacts sociaux (6 maximum)
        <br />
        - Éviter de se toucher le visage
        <br />
        - Se moucher dans un mouchoir à usage unique puis le jeter
        <br />
        - Aérer les pièces 10 minutes, trois fois par jour
        <br />
        - Saluer sans serrer la main et arrêter les embrassades
        <br />
        - Utiliser les outils numériques (TousAntiCovid)
        <!--<br />
        <div>Se laver régulièrement les mains ou utiliser une solution hydro-alcoolique</div>
        <br />
        <div>Tousser ou éternuer dans son coude ou dans son mouchoir</div>
        <br />
        <div>Se moucher dans un mouchoir à usage unique puis le jeter</div>
        <br />
        <div>Porter un masque quand la distance d’un mètre ne peut pas être respectée et partout où cela est obligatoire</div>
        <br />
        <div>Respecter une distance d’au moins un mètre avec les autres</div>
        <br />
        <div>Limiter au maximum ses contacts sociaux (6 maximum)</div>
        <br />
        <div>Éviter de se toucher le visage</div>
        <br />
        <div>Se moucher dans un mouchoir à usage unique puis le jeter</div>
        <br />
        <div>Aérer les pièces 10 minutes, trois fois par jour</div>
        <br />
        <div>Saluer sans serrer la main et arrêter les embrassades</div>
        <br />
        <div>Utiliser les outils numériques (TousAntiCovid)</div>-->
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
          this.resultCovid = data.resultat;
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

  .recommandation {
    position: fixed;
    bottom: 0px;
    font-size: 18px;
    margin-bottom: 10px;
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