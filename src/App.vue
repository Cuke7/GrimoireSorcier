<template>
    <v-app>
        <v-main>
            <div class="ma-12">
                <v-row justify="center">
                    <v-col cols="6">
                        <v-row>
                            <v-col cols="8">
                                <v-text-field v-model="searchQuery" label="Rechercher un sort" hide-details> </v-text-field>
                            </v-col>
                            <v-col cols="4">
                                <v-text-field v-model="spellLevel" label="Niveau du sort" type="number" min="1" max="10" hide-details></v-text-field>
                            </v-col>
                        </v-row>
                        <v-divider class="my-6"></v-divider>
                        <div v-for="(searchResult, index) in searchResults" :key="index">
                            {{ searchResult.item.translations.fr.name }}
                        </div>
                    </v-col>
                </v-row>
            </div>
        </v-main>
    </v-app>
</template>

<script setup>
import { ref, computed } from "vue";
import spells_srd from "./assets/spells-srd.json";
import { onMounted } from "vue";

let searchQuery = ref("");
let spellLevel = ref(1);

const spellList = computed(() => {
    let spells = spells_srd;
    spells = spells.filter((spell) => spell.type == "spell");
    spells = spells.filter((spell) => spell.traditions.includes("arcane"));
    spells = spells.filter((spell) => spell.translations.fr.status != "aucune");
    spells = spells.filter((spell) => spell.level == spellLevel.value);
    return spells;
});

const { Searcher } = require("fast-fuzzy");
const searcher = computed(() => {
    return new Searcher(spellList.value, {
        keySelector: (obj) => obj.translations.fr.name,
    });
});
const searchResults = computed(() => {
    return searcher.value.search(searchQuery.value, {
        returnMatchData: true,
    });
});

onMounted(() => {
    //
});
</script>
