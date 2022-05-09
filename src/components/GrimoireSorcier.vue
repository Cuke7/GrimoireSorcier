<template>
    <div class="ma-12">
        <div class="text-h2 my-6 text-center">Grimoire du sorcier</div>
        <v-row>
            <v-col cols="6">
                <v-row justify="center">
                    <v-col cols="8">
                        <v-text-field v-model="searchQuery" label="Rechercher un sort" hide-details> </v-text-field>
                    </v-col>
                </v-row>
                <v-row justify="center">
                    <v-col cols="auto">
                        <v-switch v-model="sortLevel" label="Trier par niveau" color="blue" hide-details> </v-switch>
                    </v-col>
                    <v-col cols="auto">
                        <v-text-field v-model="spellLevel" label="Niveau du sort" type="number" min="1" max="10" hide-details :disabled="!sortLevel"></v-text-field>
                    </v-col>
                </v-row>

                <v-divider class="my-6"></v-divider>
                <div v-for="(searchResult, index) in searchResults" :key="index">
                    <div @click="selectedSpell = searchResult.item">
                        <v-row align="center">
                            <v-col cols="auto">
                                <v-avatar color="light-blue"> {{ searchResult.item.level }} </v-avatar>
                            </v-col>
                            <v-col cols="auto">
                                {{ searchResult.item.translations.fr.name }}
                            </v-col>
                        </v-row>
                    </div>
                    <v-divider class="my-4"></v-divider>
                </div>
            </v-col>
            <v-col cols="6">
                <SpellPreview :selectedSpell="selectedSpell" />
            </v-col>
        </v-row>
    </div>
</template>

<script setup>
import SpellPreview from "./SpellPreview.vue";
import { ref, computed } from "vue";
import spells_srd from "../assets/spells-srd.json";

let searchQuery = ref("");
let spellLevel = ref(1);
let sortLevel = ref(false);

// Liste de sorts de la tradition arcaniste, rituels et sorts focalisés exclus
const spellList = computed(() => {
    let spells = spells_srd;
    spells = spells.filter((spell) => spell.type == "spell");
    spells = spells.filter((spell) => spell.traditions.includes("arcane"));
    spells = spells.filter((spell) => spell.translations.fr.status != "aucune");
    if (sortLevel.value) {
        spells = spells.filter((spell) => spell.level == spellLevel.value);
    }

    return spells;
});

const { Searcher } = require("fast-fuzzy");
// Objet nécessaire pour faire la recherche
const searcher = computed(() => {
    return new Searcher(spellList.value, {
        keySelector: (obj) => obj.translations.fr.name, // On fait la recherche sur le nom du sort
    });
});

// Tableau avec les résultats de la recherche, on en affiche que 5
const searchResults = computed(() => {
    return searcher.value
        .search(searchQuery.value, {
            returnMatchData: true,
        })
        .slice(0, 5);
});

// Contient le dernier sort cliqué pour l'afficher dans le cadre à droite
let selectedSpell = ref(null);
</script>

<style scoped>
#card {
    overflow: auto;
}

/* Permet de customiser un peu la scrollbar/*
/* width */
::-webkit-scrollbar {
    width: 10px;
}
/* Track */
::-webkit-scrollbar-track {
    box-shadow: inset 0 0 5px grey;
    border-radius: 20px;
}
/* Handle */
::-webkit-scrollbar-thumb {
    background: grey;
    border-radius: 10px;
}
/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
    background: #361268;
}
</style>
