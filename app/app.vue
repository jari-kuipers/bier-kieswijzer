<template>
  <div class="container">
    <div class="beer-quiz">
      <h1 class="title">🍺 De Ultieme Bierkieswijzer 🍺</h1>
      <p class="subtitle">Grolsch of Peroni? Wij helpen je kiezen!</p>

      <div v-if="!showResult" class="quiz-section">
        <div class="question-card">
          <h2>{{ currentQuestion.question }}</h2>
          <div class="options">
            <button
              v-for="option in currentQuestion.options"
              :key="option.value"
              @click="selectAnswer(option.value)"
              class="option-btn"
            >
              {{ option.text }}
            </button>
          </div>
          <div class="progress">
            Vraag {{ currentQuestionIndex + 1 }} van {{ questions.length }}
          </div>
        </div>
      </div>

      <div v-else class="result-section">
        <div class="result-card">
          <h2 class="result-title">{{ result.title }}</h2>
          <div class="beer-emoji">{{ result.emoji }}</div>
          <p class="result-text">{{ result.message }}</p>
          
          <div v-if="result.beer === 'peroni'" class="warning-box">
            <span class="warning-icon">⚠️</span>
            <p class="warning-text">
              <strong>Let op!</strong> Peroni kan op gaan! Er zijn nog maar 
              <span class="highlight">{{ peroniStock }}</span> flesjes over. 
              Wees er snel bij! 🏃‍♂️💨
            </p>
          </div>

          <button @click="resetQuiz" class="reset-btn">
            Nog een keer! 🔄
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const currentQuestionIndex = ref(0)
const scores = ref({ grolsch: 0, peroni: 0 })
const showResult = ref(false)
const result = ref(null)
const peroniStock = ref(Math.floor(Math.random() * 8) + 3) // 3-10 flesjes

const questions = [
  {
    question: "Hoe belangrijk is het voor jou dat je bier altijd beschikbaar is?",
    options: [
      { text: "Ik wil graag een betrouwbare voorraad! 📦", value: "grolsch" },
      { text: "Exclusiviteit > beschikbaarheid 💎", value: "peroni" }
    ]
  },
  {
    question: "Wat is jouw ideale avond?",
    options: [
      { text: "Gezellig op de bank met bitterballen 🛋️", value: "grolsch" },
      { text: "Aperitivo op een Italiaans terras ☀️", value: "peroni" }
    ]
  },
  {
    question: "Hoe voel je je over beperkte edities en schaarste?",
    options: [
      { text: "Ik wil gewoon altijd kunnen pakken wat ik wil! ✅", value: "grolsch" },
      { text: "Ik leef graag op het randje! 🎲", value: "peroni" }
    ]
  },
  {
    question: "Wat is jouw mening over het einde van een feest?",
    options: [
      { text: "Ik wil dat het bier op gaat en het feest eindigt! 🎉🚫", value: "peroni" },
      { text: "Het feest mag nog uren doorgaan! 🍻", value: "grolsch" }
    ]
  },
  {
    question: "Laatste vraag: Welke beugel spreekt je meer aan?",
    options: [
      { text: "De iconische Grolsch beugel! Classic! 💪", value: "grolsch" },
      { text: "Gewoon een flesje, geen gedoe 😎", value: "peroni" }
    ]
  }
]

const currentQuestion = computed(() => questions[currentQuestionIndex.value])

function selectAnswer(answer) {
  scores.value[answer]++
  
  if (currentQuestionIndex.value < questions.length - 1) {
    currentQuestionIndex.value++
  } else {
    calculateResult()
  }
}

function calculateResult() {
  const grolschScore = scores.value.grolsch
  const peroniScore = scores.value.peroni
  
  if (grolschScore > peroniScore) {
    result.value = {
      beer: 'grolsch',
      title: 'Je bent een Grolsch persoon! 🇳🇱',
      emoji: '🍺',
      message: 'Jij houdt van de vertrouwde, Nederlandse kwaliteit. Die beugel, dat geluid bij het openen, die smaak... Puur Nederlands vakmanschap! Proost! 🍻'
    }
  } else if (peroniScore > grolschScore) {
    result.value = {
      beer: 'peroni',
      title: 'Peroni is jouw perfecte match! 🇮🇹',
      emoji: '🍋',
      message: 'Jij hebt een verfijnde smaak en houdt van een vleugje Italiaanse flair. Licht, fris, en perfect voor een zonnige dag (of een beetje vakantiegevoel in Nederland). Salute! 🥂'
    }
  } else {
    // Bij gelijkspel, random kiezen maar wel leuk maken
    const random = Math.random() > 0.5
    if (random) {
      result.value = {
        beer: 'grolsch',
        title: 'Het lot heeft gesproken: Grolsch! 🎲',
        emoji: '🍺',
        message: 'Je zat er tussenin, dus hebben we voor je gekozen. Grolsch het wordt! Betrouwbaar, lekker, en altijd goed. Geniet ervan! 🎉'
      }
    } else {
      result.value = {
        beer: 'peroni',
        title: 'Het lot heeft gesproken: Peroni! 🎲',
        emoji: '🍋',
        message: 'Je was in dubio, dus nu krijg je een Italiaans avontuur! Peroni staat voor je klaar (als ze niet op zijn...). Salute! 🎉'
      }
    }
  }
  
  showResult.value = true
}

function resetQuiz() {
  currentQuestionIndex.value = 0
  scores.value = { grolsch: 0, peroni: 0 }
  showResult.value = false
  result.value = null
  peroniStock.value = Math.floor(Math.random() * 8) + 3 // Nieuwe voorraad
}
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {
  min-height: 100vh;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.beer-quiz {
  width: 100%;
  max-width: 600px;
}

.title {
  color: white;
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  animation: bounce 1s ease-in-out;
}

.subtitle {
  color: rgba(255, 255, 255, 0.9);
  text-align: center;
  font-size: 1.2rem;
  margin-bottom: 30px;
}

.question-card,
.result-card {
  background: white;
  border-radius: 20px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
  animation: slideUp 0.5s ease-out;
}

.question-card h2 {
  color: #333;
  font-size: 1.8rem;
  margin-bottom: 30px;
  text-align: center;
}

.options {
  display: flex;
  flex-direction: column;
  gap: 15px;
  margin-bottom: 30px;
}

.option-btn {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border: none;
  padding: 20px;
  font-size: 1.1rem;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 600;
}

.option-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 25px rgba(102, 126, 234, 0.4);
}

.option-btn:active {
  transform: translateY(-1px);
}

.progress {
  text-align: center;
  color: #666;
  font-size: 0.9rem;
}

.result-title {
  color: #333;
  font-size: 2rem;
  text-align: center;
  margin-bottom: 20px;
}

.beer-emoji {
  font-size: 5rem;
  text-align: center;
  margin: 20px 0;
  animation: spin 1s ease-in-out;
}

.result-text {
  color: #555;
  font-size: 1.2rem;
  text-align: center;
  line-height: 1.6;
  margin-bottom: 30px;
}

.warning-box {
  background: linear-gradient(135deg, #ff6b6b 0%, #ff8e53 100%);
  border-radius: 12px;
  padding: 20px;
  margin: 20px 0;
  display: flex;
  align-items: flex-start;
  gap: 15px;
  animation: pulse 2s ease-in-out infinite;
}

.warning-icon {
  font-size: 2rem;
  flex-shrink: 0;
}

.warning-text {
  color: white;
  font-size: 1rem;
  line-height: 1.5;
}

.warning-text strong {
  display: block;
  margin-bottom: 5px;
  font-size: 1.1rem;
}

.highlight {
  font-weight: bold;
  font-size: 1.3rem;
  text-decoration: underline;
}

.reset-btn {
  background: linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
  color: white;
  border: none;
  padding: 15px 40px;
  font-size: 1.1rem;
  border-radius: 12px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 600;
  display: block;
  margin: 0 auto;
}

.reset-btn:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 25px rgba(17, 153, 142, 0.4);
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-10px); }
}

@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes spin {
  from { transform: rotate(0deg) scale(0.5); }
  to { transform: rotate(360deg) scale(1); }
}

@keyframes pulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.02); }
}

@media (max-width: 640px) {
  .title {
    font-size: 1.8rem;
  }
  
  .subtitle {
    font-size: 1rem;
  }
  
  .question-card,
  .result-card {
    padding: 25px;
  }
  
  .question-card h2 {
    font-size: 1.4rem;
  }
  
  .option-btn {
    padding: 15px;
    font-size: 1rem;
  }
}
</style>
