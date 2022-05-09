<template>
    <v-card class="pa-2" height="600" id="card">
        <div v-if="props.selectedSpell">
            <v-card-title>
                {{ spell.name }}
            </v-card-title>
            <div class="font-weight-bold ma-4">Niveau : {{ level }}</div>
            <div v-for="(key, index) in Object.keys(spell)" :key="index" class="ml-4">
                <v-chip v-if="key != 'status' && key != 'name' && key != 'description'" class="my-2"> {{ key }} : {{ spell[key] }} </v-chip>
            </div>
            <v-card-text v-html="spell.description"></v-card-text>
        </div>
    </v-card>
</template>

<script setup>
import { defineProps, computed } from "vue";
const props = defineProps(["selectedSpell"]);

// Décompose la props, par soucis de simplicité
let spell = computed(() => {
    return props.selectedSpell.translations.fr;
});
let level = computed(() => {
    return props.selectedSpell.level;
});
</script>
