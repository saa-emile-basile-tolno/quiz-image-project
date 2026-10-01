<script setup>
import { ref, computed } from 'vue'


const questions = [
  {
    texte: 'Combien de côtés a cette forme ?',
    image: '/images/triangle.svg',
    choix: ['4 côtés', '3 côtés', '5 côtés'],
    bonneReponse: 1,
    descriptionImage: 'Un triangle rouge'
  },
  {
    texte: 'Comment s\'appelle cette forme ?',
    image: '/images/carre.svg',
    choix: ['Un carré', 'Un rectangle', 'Un losange'],
    bonneReponse: 0,
    descriptionImage: 'Un carré bleu'
  },
  {
    texte: 'Quelle forme voyez-vous ?',
    image: '/images/cercle.svg',
    choix: ['Un ovale', 'Un hexagone', 'Un cercle'],
    bonneReponse: 2,
    descriptionImage: 'Un cercle vert'
  },
  {
    texte: 'Combien de branches a cette étoile ?',
    image: '/images/etoile.svg',
    choix: ['6 branches', '5 branches', '4 branches'],
    bonneReponse: 1,
    descriptionImage: 'Une étoile jaune à cinq branches'
  },
  {
    texte: 'Quelle forme voyez-vous ?',
    image: '/images/rectangle.svg',
    choix: ['Un rectangle', 'Un carré', 'Un triangle'],
    bonneReponse: 0,
    descriptionImage: 'Un rectangle violet'
  }
]


const indexQuestion = ref(0)      
const score = ref(0)              
const reponseChoisie = ref(null)  
const termine = ref(false)        


const questionCourante = computed(() => questions[indexQuestion.value])


const reponsesDonnees = computed(() => {
  if (reponseChoisie.value !== null) return indexQuestion.value + 1
  return indexQuestion.value
})

const aRepondu = computed(() => reponseChoisie.value !== null)


const derniereQuestion = computed(() => indexQuestion.value === questions.length - 1)


const reponseJuste = computed(
  () => reponseChoisie.value === questionCourante.value.bonneReponse
)


function repondre(indice) {
  if (reponseChoisie.value !== null) return   // garde : empêche le double comptage
  reponseChoisie.value = indice
  if (indice === questionCourante.value.bonneReponse) {
    score.value++
  }
}


function avancer() {
  if (!aRepondu.value) return  
  if (derniereQuestion.value) {
    termine.value = true
  } else {
    indexQuestion.value++
    reponseChoisie.value = null
  }
}

// Remise à Zéro
function rejouer() {
  indexQuestion.value = 0
  score.value = 0
  reponseChoisie.value = null
  termine.value = false
}
</script>

<template>
  <div class="quiz">
    <header class="entete">
      <h1>Quiz en images</h1>
      <p class="score">Score : {{ score }} / {{ questions.length }}</p>
    </header>

    <progress :value="reponsesDonnees" :max="questions.length"></progress>
    <p class="progression">{{ reponsesDonnees }} réponse(s) donnée(s) sur {{ questions.length }}</p>

    <!-- Écran du quiz -->
    <section v-if="!termine">
      <h2>Question {{ indexQuestion + 1 }} sur {{ questions.length }}</h2>

      <img :src="questionCourante.image" :alt="questionCourante.descriptionImage" />

      <p class="question">{{ questionCourante.texte }}</p>

      <div class="choix">
        <button
          v-for="(choix, i) in questionCourante.choix"
          :key="i"
          :disabled="aRepondu"
          :class="{
            juste: aRepondu && i === questionCourante.bonneReponse,
            fausse: aRepondu && i === reponseChoisie && i !== questionCourante.bonneReponse
          }"
          @click="repondre(i)"
        >
          {{ choix }}
        </button>
      </div>

      <p v-if="aRepondu" class="retour" :class="reponseJuste ? 'ok' : 'ko'">
        <span v-if="reponseJuste">✔ Bonne réponse !</span>
        <span v-else>✘ Mauvaise réponse. La bonne réponse était : {{ questionCourante.choix[questionCourante.bonneReponse] }}</span>
      </p>

      <button v-if="aRepondu" class="navigation" @click="avancer">
        {{ derniereQuestion ? 'Voir le résultat' : 'Question suivante' }}
      </button>
    </section>

    <!-- Écran final -->
    <section v-else class="resultat">
      <h2>Résultat final</h2>
      <p class="score-final">Vous avez obtenu {{ score }} / {{ questions.length }}</p>
      <button class="navigation" @click="rejouer">Rejouer</button>
    </section>
  </div>
</template>

<style>
* { box-sizing: border-box; }

body {
  margin: 0;
  font-family: Arial, sans-serif;
  background: #f4f6f8;
}

.quiz {
  max-width: 600px;
  margin: 20px auto;
  padding: 16px;
  background: white;
  border-radius: 12px;
  text-align: center;
}

.entete {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.score { font-weight: bold; }

progress { width: 100%; height: 16px; }

img {
  width: 100%;
  max-width: 250px;
  height: 200px;
  object-fit: contain;
}

.choix {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin: 16px 0;
}

button {
  padding: 12px;
  font-size: 1rem;
  border: 2px solid #ccc;
  border-radius: 8px;
  background: white;
  cursor: pointer;
}

button:disabled { cursor: not-allowed; }

button.juste { border-color: #2ecc71; background: #eafaf1; }
button.fausse { border-color: #e74c3c; background: #fdedec; }

.ok { color: #1e8449; }
.ko { color: #c0392b; }

.navigation {
  background: #42b883;
  color: white;
  border: none;
}

@media (min-width: 600px) {
  .choix { flex-direction: row; }
  .choix button { flex: 1; }
}
</style>