<script setup>
import {onMounted, reactive} from "vue";
import {doAjaxRequest} from "@/api";

const categorieVide = {
  libelle: "",
  description: ""
};

const page = 0;

let data = reactive({
  formulaireCategorie: {...categorieVide},
  listeCategories: [],
  listeLinks: []
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}
function chargeCategorieswithUrl(urlPage) {

  urlPage = urlPage.substring(urlPage.indexOf("/api"));
  doAjaxRequest(urlPage)
      .then((json) => {
        data.listeCategories = json._embedded.clients;
        data.listeLinks = json._links;
        console.log(data.listeLinks);
      })
      .catch(showError);
}

onMounted(() => {
  chargeCategorieswithUrl("/api/clients?&page=0&size=5");
})


</script>

<template>
<div>
<table>
  <caption>Liste des catégories</caption>
  <tr>
    <th>Code</th>
    <th>Société</th>
    <th>Contact</th>
    <th>Ville</th>
  </tr>
  <!-- Si le tableau des catégories est vide -->
  <tr v-if="data.listeCategories.length === 0">
    <td colspan="4">Veuillez patienter, chargement des catégories...</td>
  </tr>
  <!-- Si le tableau des catégories n'est pas vide -->
  <tr v-for="categorie in data.listeCategories" :key="categorie.code">
    <td>{{ categorie.code }}</td>
    <td>{{ categorie.societe }}</td>
    <td>{{ categorie.contact }}</td>
    <td>{{ categorie.adresse.ville }}</td>
  </tr>
  <tr>
    <td><button type="number" @click="chargeCategorieswithUrl(data.listeLinks.first.href)">first</button></td>
    <td><button v-if="typeof data.listeLinks.prev !== 'undefined'" type="number" @click="chargeCategorieswithUrl(data.listeLinks.prev.href)">prev</button></td>
    <td><button v-if="typeof data.listeLinks.next !== 'undefined'" type="number" @click="chargeCategorieswithUrl(data.listeLinks.next.href)">next</button></td>
    <td><button type="number" @click="chargeCategorieswithUrl(data.listeLinks.last.href)">last</button></td>
  </tr>
</table>
</div>

</template>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #04ff92;
  color: rgb(255, 255, 255);
}
</style>