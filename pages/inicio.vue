<script lang="ts" setup>
import { ref } from 'vue';
import { useFetch } from '#app';

export interface Character {
  info: Info;
  results: Result[];
}

export interface Info {
  count: number;
  pages: number;
  next: string;
  prev: string | null;
}

export interface Location {
  name: string;
  url: string;
}

export interface Result {
  id: number;
  name: string;
  status: string;
  species: string;
  type: string;
  gender: string;
  origin: Location;
  location: Location;
  image: string;
  episode: string[];
  url: string;
  created: Date;
}

const { data } = await useFetch<Character>('https://rickandmortyapi.com/api/character');
const characters = ref<Result[]>(data.value?.results || []);
const editedCharacter = ref<Result | null>(null);
const newCharacter = ref<Result>({
  id: Date.now(),
  name: '',
  status: '',
  species: '',
  type: '',
  gender: '',
  origin: { name: '', url: '' },
  location: { name: '', url: '' },
  image: '',
  episode: [],
  url: '',
  created: new Date()
});
const dialog = ref(false);
const addDialog = ref(false);

const editCharacter = (character: Result) => {
  editedCharacter.value = JSON.parse(JSON.stringify(character));
  dialog.value = true;
};

const saveCharacter = () => {
  if (editedCharacter.value) {
    const index = characters.value.findIndex(c => c.id === editedCharacter.value?.id);
    if (index !== -1) {
      characters.value[index] = { ...editedCharacter.value };
    }
    dialog.value = false;
  }
};

const deleteCharacter = (id: number) => {
  characters.value = characters.value.filter(character => character.id !== id);
};

const addCharacter = () => {
  characters.value.push({ ...newCharacter.value, id: Date.now() });
  newCharacter.value = {
    id: Date.now(),
    name: '',
    status: '',
    species: '',
    type: '',
    gender: '',
    origin: { name: '', url: '' },
    location: { name: '', url: '' },
    image: '',
    episode: [],
    url: '',
    created: new Date()
  };
  addDialog.value = false;
};
</script>

<template>
  <v-container fluid>
    <v-btn color="green" @click="addDialog = true">Agregar Personaje</v-btn>
    <v-row>
      <v-col v-for="item in characters" :key="item.id" cols="12" md="4">
        <v-card class="mx-auto" max-width="344">
          <v-img height="200px" :src="item.image" cover></v-img>

          <v-card-title>
            {{ item.name }}
          </v-card-title>

          <v-card-subtitle>
            {{ item.species }} - {{ item.status }}
          </v-card-subtitle>

          <v-card-actions>
            <v-btn color="blue-lighten-2" @click="editCharacter(item)">Editar</v-btn>
            <v-btn color="red-lighten-2" @click="deleteCharacter(item.id)">Eliminar</v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>

  <v-dialog v-model="dialog" max-width="500px">
    <v-card>
      <v-card-title>Editar Personaje</v-card-title>
      <v-card-text>
        <v-text-field v-model="editedCharacter!.name" label="Nombre"></v-text-field>
        <v-text-field v-model="editedCharacter!.species" label="Especie"></v-text-field>
        <v-text-field v-model="editedCharacter!.status" label="Estado"></v-text-field>
        <v-text-field v-model="editedCharacter!.image" label="URL de la Imagen"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="green" @click="saveCharacter">Guardar</v-btn>
        <v-btn color="red" @click="dialog = false">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>

  <v-dialog v-model="addDialog" max-width="500px">
    <v-card>
      <v-card-title>Agregar Personaje</v-card-title>
      <v-card-text>
        <v-text-field v-model="newCharacter.name" label="Nombre"></v-text-field>
        <v-text-field v-model="newCharacter.species" label="Especie"></v-text-field>
        <v-text-field v-model="newCharacter.status" label="Estado"></v-text-field>
        <v-text-field v-model="newCharacter.image" label="URL de la Imagen"></v-text-field>
      </v-card-text>
      <v-card-actions>
        <v-btn color="green" @click="addCharacter">Agregar</v-btn>
        <v-btn color="red" @click="addDialog = false">Cancelar</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
