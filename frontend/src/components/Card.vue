<template>
  <div class="card-flipper" :class="[cardClasses, { 'is-flipped': isFlipped }]" @click="isFlipped = !isFlipped">
    <div class="card" :style="cardFrontStyle">
      <!-- Front of the Card -->
      <div class="card-front">
        <main class="card-body-front" :class="{ 'is-item': card.type === 'item' && card.category !== 'Soldier' }">
          <template v-if="card.type === 'player' || card.category === 'Soldier'">
            <header class="card-header-front">
              <div class="logo-text">
                <span class="zelda-logo-jp" v-if="language === 'jp'">ゼルダの伝説</span>
                <span class="zelda-logo-en" v-else>The Legend of Zelda</span>
                <span class="triforce-logo-jp" v-if="language === 'jp'">神々のトライフォース</span>
                <span class="triforce-logo-en" v-else>A Link to the Past</span>
              </div>
            </header>
            <div class="name-jp">{{ language === 'jp' ? characterName : '' }}</div>
            <div class="name-en">{{ characterName.toUpperCase() }}</div>
          </template>
          <template v-else-if="card.type === 'item' && card.category !== 'Soldier'">
            <div class="item-name-box">
              <span class="item-name-jp">{{ language === 'jp' ? characterName : '' }}</span>
            </div>
            <span class="item-name-en">{{ language === 'en' ? characterName.toUpperCase() : '' }}</span>
          </template>
          <div class="character-art-placeholder"></div>
          <div class="details-right">
            <div class="card-in-box">
              <span class="card-in-arrow">▶</span>
              <span>{{ frontInstructions }}</span>
            </div>
            <div class="dice-mode-box">
              <p class="dice-mode-title">{{ frontMode }}</p>
              <p class="player-card-text">{{ cardType }}</p>
            </div>
          </div>
        </main>
        <footer class="card-footer-front">
          <span class="brand">Barcode Battler II</span>
          <span class="stat">{{ card.stat_type }}{{ card.stat_value }}</span>
        </footer>
      </div>

      <!-- Back of the Card -->
      <div class="card-back">
        <header class="card-header-back">
          <div class="header-left">
            <span class="conveni-wars">CONVENI WARS</span>
            <span class="brand-back">Barcode Battler II</span>
          </div>
          <div class="header-right">
            <span class="c2-mode">{{ language === 'en' ? 'C2 MODE' : 'C2モード対応' }}</span>
            <span class="barcode-battler-logo">BARCODE BATTLER</span>
          </div>
        </header>
        <main class="card-body-back">
           <div class="character-art-placeholder-back"></div>
           <div class="back-content">
            <div class="back-title">
              <p class="character-name-back">{{ characterName }}</p>
              <p class="card-type-back">{{ backCardText }}</p>
            </div>
            <p class="description">{{ backDescription }}</p>
            <div class="dice-mode-box-back">
                <p class="dice-mode-title-back">{{ backMode }}</p>
                <p class="player-card-text-back">{{ cardType }}</p>
            </div>
            <span class="nintendo-logo">© Nintendo</span>
           </div>
        </main>
        <footer class="card-footer-back">
          <div class="barcode-container">
            <canvas ref="barcodeRef"></canvas>
          </div>
        </footer>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, onMounted } from 'vue';
import JsBarcode from 'jsbarcode';

interface CardData {
  id: string;
  character: {
    name_english: string;
    name_japanese: string;
  };
  stat_type: string;
  stat_value: number;
  c2_compatible: boolean;
  category: string;
  type: string;
  item_type?: string;
  item_color?: {
    primary: string;
    secondary: string;
  };
  sides: {
    front: {
      instructions_english: string;
      instructions_japanese: string;
      mode: {
        english: string;
        japanese: string;
      };
      dice_value?: string;
      magic_attack?: {
        value: string;
      };
      psycho_icons?: string[];
    };
    back: {
      header_english: string;
      header_japanese: string;
      card_text: {
        english: string;
        japanese: string;
      };
      description: {
        english: string;
        japanese: string;
      };
      mode: {
        english: string;
        japanese: string;
      };
    };
  };
}

const props = defineProps<{
  card: CardData;
  language: 'en' | 'jp';
}>();

const isFlipped = ref(false);
const barcodeRef = ref<HTMLCanvasElement | null>(null);

function formatBarcodeId(id: string): string {
  return id.padStart(13, '0');
}

const characterName = computed(() => {
  return props.language === 'en' ? props.card.character.name_english : props.card.character.name_japanese;
});

const frontInstructions = computed(() => {
  return (props.language === 'en' ? props.card.sides.front.instructions_english : props.card.sides.front.instructions_japanese).toUpperCase();
});

const frontMode = computed(() => {
  return props.language === 'en' ? props.card.sides.front.mode.english : props.card.sides.front.mode.japanese;
});

const backCardText = computed(() => {
  return props.language === 'en' ? props.card.sides.back.card_text.english : props.card.sides.back.card_text.japanese;
});

const backDescription = computed(() => {
  return props.language === 'en' ? props.card.sides.back.description.english : props.card.sides.back.description.japanese;
});

const backMode = computed(() => {
  return props.language === 'en' ? props.card.sides.back.mode.english : props.card.sides.back.mode.japanese;
});

const cardType = computed(() => {
    if (props.card.type === 'player') return props.language === 'en' ? 'Player Card' : 'プレイヤーカード';
    if (props.card.type === 'item') return props.language === 'en' ? 'Item Card' : 'アイテムカード';
    if (props.card.type === 'enemy') return props.language === 'en' ? 'Enemy Card' : 'エネミーカード';
    return '';
})

const cardFrontStyle = computed(() => {
  if (props.card.type === 'item' && props.card.item_color) {
    return {
      '--card-primary-color': props.card.item_color.primary,
      '--card-secondary-color': props.card.item_color.secondary
    };
  }
  return {};
});

const cardClasses = computed(() => ({
  'is-item': props.card.type === 'item' && props.card.category !== 'Soldier',
  [`item-type-${props.card.item_type}`]: props.card.type === 'item' && props.card.item_type && props.card.category !== 'Soldier'
}));

onMounted(() => {
  if (barcodeRef.value) {
    JsBarcode(barcodeRef.value, formatBarcodeId(props.card.id), {
      format: "EAN13",
      width: 2,
      height: 60,
      displayValue: false,
      background: "#ffffff",
      margin: 0,
      marginTop: 0,
      marginBottom: 0,
      marginLeft: 10,
      marginRight: 10,
    });
  }
});
</script>

<style scoped>
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  font-family: "MS Gothic", "Osaka", "ヒラギノ角ゴ Pro W3", "Hiragino Kaku Gothic Pro", "メイリオ", Meiryo, sans-serif;
}

.card-flipper {
  perspective: 1000px;
  width: 350px;
  height: 220px;
  cursor: pointer;
}

.card {
  width: 100%;
  height: 100%;
  position: relative;
  transform-style: preserve-3d;
  transition: transform 0.8s;
}

.card-flipper.is-flipped .card {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  color: #000;
  overflow: hidden;
  border: 1px solid #888;
}

/* Front of Card */
.card-front {
  background-color: #fff;
}

.card-header-front {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  background-color: #f9e800;
  padding: 4px 10px;
  border-bottom: 3px solid #00a9e0;
}

.logo-text {
  display: flex;
  flex-direction: column;
  font-weight: bold;
  font-size: 0.7rem;
  width: fit-content;
}

.zelda-logo-jp,
.zelda-logo-en,
.triforce-logo-jp,
.triforce-logo-en {
  display: block;
}

.card-body-front {
  flex-grow: 1;
  display: flex;
  position: relative;
  background-size: cover;
  padding-top: 45px;
}

.name-jp {
  position: absolute;
  left: 15px;
  top: 55px;
  font-size: 1.8rem;
  font-weight: bold;
  color: #0075c2;
  text-shadow: 
    1px 1px 0 #fff,
    -1px -1px 0 #fff,
    1px -1px 0 #fff,
    -1px 1px 0 #fff;
}

.name-en {
  position: absolute;
  top: 3px;
  right: 15px;
  font-size: 2.2rem;
  font-weight: bold;
  color: #e60012;
  font-style: italic;
  transform: skew(-10deg);
  line-height: 1;
  text-shadow: 
    1px 1px 0 #fff,
    -1px -1px 0 #fff,
    1px -1px 0 #fff,
    -1px 1px 0 #fff;
}

.character-art-placeholder {
  flex-grow: 1;
}

.details-right {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 10px;
}

.card-in-box {
  background-color: rgba(0, 0, 0, 0.7);
  color: #fff;
  padding: 5px;
  text-align: center;
  font-size: 0.8rem;
}

.dice-mode-box {
  background-color: #fff;
  border: 2px solid #000;
  padding: 5px;
  text-align: center;
}

.dice-mode-title {
  background-color: #000;
  color: #fff;
  font-size: 0.7rem;
  padding: 2px;
}

.player-card-text {
  font-size: 0.9rem;
  font-weight: bold;
  padding-top: 5px;
}

.card-footer-front {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #c0c0c0;
  padding: 5px 10px;
  border-top: 1px solid #888;
}

.brand {
  font-weight: bold;
  font-style: italic;
}

.stat {
  font-size: 1.5rem;
  font-weight: bold;
}

.card-back {
  transform: rotateY(180deg);
  background-color: #e0f0e0;
}

.card-header-back {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #009e73;
  color: #fff;
  padding: 2px 10px;
  font-weight: bold;
}

.header-left, .header-right {
    display: flex;
    align-items: center;
    gap: 8px;
}

.brand-back {
    font-style: italic;
}

.c2-mode {
    background-color: #fff;
    color: #009e73;
    padding: 1px 4px;
    border-radius: 3px;
    font-size: 0.7rem;
}

.barcode-battler-logo {
    border: 1px solid #fff;
    padding: 1px 4px;
    font-size: 0.7rem;
}

.card-body-back {
  height: 140px;
  position: relative;
  display: flex;
  flex-direction: column;
  background-color: #fff;
}

.character-art-placeholder-back {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background-size: cover;
    opacity: 0.2;
    z-index: 0;
}

.back-content {
  position: relative;
  z-index: 1;
  padding: 10px;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.back-title {
    display: flex;
    align-items: baseline;
    gap: 10px;
    border-bottom: 2px solid #000;
    padding-bottom: 3px;
    margin-bottom: 8px;
}

.character-name-back {
    font-size: 1.2rem;
    font-weight: bold;
}

.card-type-back {
    font-size: 0.9rem;
    font-weight: bold;
}

.description {
    margin-top: 10px;
    font-size: 0.65rem;
    line-height: 1.3;
    flex-grow: 1;
    text-align: left;
    width: calc(100% - 120px);
    white-space: pre-line;
}

.dice-mode-box-back {
    position: absolute;
    top: 25px;
    right: 10px;
    width: 100px;
    background-color: #fff;
    border: 2px solid #000;
    padding: 5px;
    text-align: center;
}

.dice-mode-title-back {
    background-color: #000;
    color: #fff;
    font-size: 0.7rem;
    padding: 2px;
}

.player-card-text-back {
    font-size: 0.9rem;
    font-weight: bold;
    padding-top: 5px;
}

.nintendo-logo {
    font-size: 0.8rem;
    font-weight: bold;
    align-self: flex-end;
}

.card-footer-back {
  background-color: #fff;
  padding: 0;
  border-top: 1px solid #888;
  height: 80px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.barcode-container {
  width: 95%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  background: white;
}

.barcode-container canvas {
  height: 60px !important;
  width: 100% !important;
}

/* Item Card Specific Styles */
.card-body-front.is-item {
  background: var(--card-primary-color, #9ddb89);
  background: radial-gradient(
    circle at 50% 50%,
    var(--card-secondary-color, #c5e0b4) 25%,
    var(--card-primary-color, #9ddb89) 25%
  );
  background-size: 8px 8px;
  position: relative;
  padding-top: 0;
}

.item-name-box {
  position: absolute;
  top: 20px;
  left: 20px;
  background: white;
  padding: 4px 8px;
  border-radius: 4px;
  border: 1px solid #000;
}

.item-name-jp {
  font-size: 1rem;
  font-weight: bold;
}

.item-name-en {
  position: absolute;
  top: 20px;
  right: 20px;
  font-size: 2rem;
  font-weight: bold;
  color: #0000FF;
  font-style: italic;
  text-shadow: 2px 2px 0 white;
}

/* Specific item type styles */
.card-flipper.item-type-healing .dice-mode-box,
.card-flipper.item-type-healing .dice-mode-box-back {
  border-color: #e60012;
}

.card-flipper.item-type-healing .dice-mode-title,
.card-flipper.item-type-healing .dice-mode-title-back {
  background-color: #e60012;
}

.card-flipper.item-type-weapon .dice-mode-box,
.card-flipper.item-type-weapon .dice-mode-box-back {
  border-color: #0075c2;
}

.card-flipper.item-type-weapon .dice-mode-title,
.card-flipper.item-type-weapon .dice-mode-title-back {
  background-color: #0075c2;
}
</style>