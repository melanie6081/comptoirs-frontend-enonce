<template>
  <main>
    <table>
      <caption>
        Liste des produits - page
        {{
          data.infoPage.number + 1
        }}/{{
          data.infoPage.totalPages
        }}
      </caption>
      <thead>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
      </thead>
      <!-- Si le tableau des produits est vide -->
      <tbody>
        <tr v-if="!data.listeProduits">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.reference">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
        <tr>
          <td class="tableau">
            <button @click="chargeProduits(data.liens.first.href)">
              <img
                src="../assets/fleche_premierePage.png"
                alt="Première page"
              />
            </button>
          </td>
          <td class="tableau">
            <button @click="chargeProduits(data.liens.prev.href)">
              <img
                src="../assets/fleche_pagePrecedente.png"
                alt="Page pécédente"
              />
            </button>
          </td>
          <td class="tableau">
            <button @click="chargeProduits(data.liens.next.href)">
              <img
                src="../assets/flèche_pageSuivante.png"
                alt="Page suivante"
              />
            </button>
          </td>
          <td class="tableau">
            <button @click="chargeProduits(data.liens.last.href)">
              <img
                src="../assets/fleche_dernierePage.png"
                alt="Dernière page"
              />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { BACKEND, doAjaxRequest } from "../api";

let data = reactive({
  // La liste des produits affichée sous forme de table
  listeProduits: [],
  infoPage: {},
  liens: {},
});

function chargeProduits(url) {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(url)
    .then((json) => {
      data.listeProduits = json._embedded.produits;
      console.log(data.listeProduits);
      data.infoPage = json.page;
      data.liens = json._links;
    })
    .catch((error) => alert(error.message));
}

// A l'affichage du composant, on affiche la liste
onMounted(() =>
  chargeProduits(BACKEND + "/api/produits?size=5&sort=reference,asc&page=0")
);
</script>

<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
  width: 100%;
  text-align: justify;
}
.tableau {
  width: 25%;
  text-align: center;
}

button {
  background-color: black;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
