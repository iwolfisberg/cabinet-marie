<script setup>
import { ref, watchEffect, computed, onMounted, onUnmounted } from "vue";

const props = defineProps({
  type: {
    type: String,
    required: true,
    validator: (value) => ["physio", "acu"].includes(value),
  },
  initialTab: {
    type: Number,
    default: 0,
  },
});

const emit = defineEmits(["tab-changed"]);

const selectedTabIndex = ref(props.initialTab);
const currentTabs = ref([]);

const physioTabs = [
  {
    slug: "physio-troubles",
    title: "Troubles pris en charge",
    content: `<ul>
        <li>Domaine musculo-squelettique : troubles en rapport avec les muscles, tendons, ligaments, articulations, bourses et capsules,</li>
        <li>Hyperlaxité et syndrome d'Ehlers-Danlos,</li>
        <li>Pré et post-opératoires ou suite d'accident,</li>
        <li>Prévention des chutes et maintien de l'autonomie (notamment chez les personnes âgées).</li>
      </ul>
      <div class="note">
        <p>Les éléments indiqués ci-dessus le sont à titre indicatif.</p>
        <p>N'hésitez pas à me contacter pour toute demande ou problématique.</p>
      </div>`,
  },
  {
    slug: "physio-outils",
    title: "Outils et techniques",
    content: `<ul>
        <li>Exercices adaptés et personnalisés de renforcement, étirement, équilibre, proprioception, sauts,</li>
        <li>Prise de conscience corporelle,</li>
        <li>Travail de la respiration,</li>
        <li>Réapprentissage de schéma moteur (façon de bouger),</li>
        <li>Techniques manuelles : mobilisation passive, détonification musculaire, étirements,</li>
        <li>Accompagnement vers un retour à l'activité physique, réentrainement à l'effort,</li>
        <li>Utilisation de tape pour le traitement de certaines douleurs ou diminution des gros hématomes,</li>
        <li>Conseils : ergonomie, hygiène de vie, activité physique et douleur.</li>
      </ul>`,
  },
  {
    slug: "physio-precisions",
    title: "Précisions sur les séances",
    content: `<ul>
        <li>Je me déplace à domicile.</li>
        <li>Je ne reçois pas les enfants de moins de 10 ans.</li>
      </ul>`,
  },
  {
    slug: "physio-tarifs",
    title: "Tarifs et remboursement",
    content: `<ul>
        <li>Avec une ordonnance de votre médecin, la physiothérapie est remboursée par l'assurance maladie de base (LAMal), l'assurance accident (LAA), l'assurance militaire et l'assurance invalidité (AI).</li>
        <li>Vous avez le libre choix de votre physiothérapeute.</li>
        <li>La première séance de physiothérapie doit être faite maximum 35 jours après la date figurant sur votre ordonnance.</li>
        <li>Vous pouvez aussi venir en physiothérapie préventive sans ordonnance, paiement à votre charge directement à la fin de la séance (cash ou Twint).</li>
        <li>Toutes séances non excusée 24 heures à l'avance sera facturée.</li>
      </ul>`,
  },
];

const acuTabs = [
  {
    slug: "acu-troubles",
    title: "Troubles pris en charge",
    content: `<ul>
        <li>Douleurs, tensions musculaire et problèmes articulaires,</li>
        <li>Stress,</li>
        <li>Trop plein émotionnel,</li>
        <li>Troubles gynécologique tels que règles douloureuses ou difficultés à concevoir (infertilité),</li>
        <li>Inconforts lors de la grossesse,</li>
        <li>Problèmes de la sphère ORL,</li>
        <li>Allergies,</li>
        <li>Troubles respiratoires,</li>
        <li>Troubles digestifs,</li>
        <li>Difficultés de concentration ou de mémorisation,</li>
        <li>Et dans bien d'autres situations.</li>
      </ul>
      <div class="note">
        <p>Les éléments indiqués ci-dessus le sont à titre indicatif.</p>
        <p>N'hésitez pas à me contacter pour toute demande ou problématique.</p>
      </div>`,
  },
  {
    slug: "acu-precisions",
    title: "Précisions sur les séances",
    content: `<ul>
        <li>Une séance dure 1h15.</li>
        <li>Je me déplace à domicile.</li>
        <li>Il est possible de traiter les bébés et enfants avec un laser. Ceci est indolore et relativement court.</li>
      </ul>`,
  },
  {
    slug: "acu-tarifs",
    title: "Tarifs et remboursement",
    content: `<ul>
        <li>Je suis en général remboursée par les assurance faisant partie du RME mais je vous laisse le soin d'appeler votre assurance pour connaitre les conditions propres à votre assurance complémentaire.</li>
        <li>Coût d'une séance : 130 CHF, payable par cash ou Twint directement à la fin de la séance.</li>
        <li>Toutes séances non excusée 24 heures à l'avance sera facturée.</li>
      </ul>`,
  },
];

// Computed pour le tab actuel
const currentTab = computed(() => {
  return currentTabs.value[selectedTabIndex.value] || null;
});

// Navigation clavier
const handleKeydown = (event, index) => {
  const totalTabs = currentTabs.value.length;

  switch (event.key) {
    case "ArrowRight":
      event.preventDefault();
      selectedTabIndex.value = (index + 1) % totalTabs;
      break;
    case "ArrowLeft":
      event.preventDefault();
      selectedTabIndex.value = (index - 1 + totalTabs) % totalTabs;
      break;
    case "Home":
      event.preventDefault();
      selectedTabIndex.value = 0;
      break;
    case "End":
      event.preventDefault();
      selectedTabIndex.value = totalTabs - 1;
      break;
  }
};

watchEffect(() => {
  if (props.type) {
    const newTabs = props.type === "physio" ? physioTabs : acuTabs;
    currentTabs.value = newTabs;

    // Vérifier si l'index actuel est valide
    if (selectedTabIndex.value >= newTabs.length) {
      selectedTabIndex.value = 0;
    }
  }
});

const updateTab = (index) => {
  if (index !== selectedTabIndex.value) {
    selectedTabIndex.value = index;
    emit("tab-changed", {
      index,
      tab: currentTabs.value[index],
    });
  }
};

// Gestion du focus automatique
const focusTab = (index) => {
  if (typeof document !== "undefined") {
    const tabButton = document.getElementById(currentTabs.value[index]?.slug);
    tabButton?.focus();
  }
};

// Auto-focus après changement de tab via clavier
watchEffect(() => {
  if (selectedTabIndex.value !== undefined) {
    // Utiliser nextTick pour s'assurer que le DOM est mis à jour
    setTimeout(() => focusTab(selectedTabIndex.value), 0);
  }
});
</script>

<template>
  <div
    class="tabs"
    :class="type === 'physio' ? 'physio' : 'acu'"
    v-if="currentTabs.length > 0"
  >
    <!-- Navigation des onglets -->
    <div
      role="tablist"
      :aria-label="`Informations ${
        type === 'physio' ? 'physiothérapie' : 'acupuncture'
      }`"
      class="tab-list"
    >
      <button
        v-for="(tab, index) in currentTabs"
        :key="tab.slug"
        role="tab"
        :class="['tab-button text-md', { active: index === selectedTabIndex }]"
        :aria-selected="index === selectedTabIndex"
        :aria-controls="`tabpanel-${tab.slug}`"
        :id="`tab-${tab.slug}`"
        :tabindex="index === selectedTabIndex ? 0 : -1"
        @click="updateTab(index)"
        @keydown="handleKeydown($event, index)"
      >
        {{ tab.title }}
      </button>
    </div>

    <!-- Contenu des panels -->
    <div class="tab-content">
      <transition name="fade" mode="out-in">
        <div
          :key="`panel-${currentTab?.slug}`"
          :id="`tabpanel-${currentTab?.slug}`"
          role="tabpanel"
          tabindex="0"
          :aria-labelledby="`tab-${currentTab?.slug}`"
          class="tab-panel"
        >
          <div class="tab-title-wrapper">
            <span class="line" aria-hidden="true"></span>
            <h3 class="tab-title text-lg">{{ currentTab?.title }}</h3>
          </div>
          <div v-html="currentTab?.content" class="content text-md"></div>
        </div>
      </transition>
    </div>
  </div>
</template>

<style scoped>
.tabs {
  width: 100%;
  margin-top: 2rem;
  display: grid;
}

@media (min-width: 48rem) {
  .tabs {
    grid-template-columns: repeat(12, 1fr);
  }

  .tabs.physio {
    border-top: 2px solid var(--color-lila-500);
  }

  .tabs.acu {
    border-top: 2px solid var(--color-orange-500);
  }
}

.tab-list {
  display: flex;
  width: 100%;
  overflow-x: scroll;
}

.physio .tab-list {
  border-top: 2px solid var(--color-lila-500);
  border-bottom: 2px solid var(--color-lila-500);
}

.acu .tab-list {
  border-top: 2px solid var(--color-orange-500);
  border-bottom: 2px solid var(--color-orange-500);
}

@media (min-width: 48rem) {
  .tab-list {
    grid-column: 1 / 6;
    flex-direction: column;
    padding: 5rem 0;
  }

  .physio .tab-list {
    border-right: 2px solid var(--color-lila-500);
    border-top: 0;
    border-bottom: 0;
  }

  .acu .tab-list {
    border-right: 2px solid var(--color-orange-500);
    border-top: 0;
    border-bottom: 0;
  }
}

@media (min-width: 64rem) {
  .tab-list {
    grid-column: 1 / 5;
  }
}

.tab-button {
  padding: 1rem;
  border: none;
  background: transparent;
  cursor: pointer;
  border-radius: 0;
  transition: all 0.2s ease;
  font-weight: 700;
  text-transform: uppercase;
  white-space: nowrap;
  width: 100%;
  text-align: left;
}

.physio .tab-button {
  color: var(--color-lila-500);
}

.acu .tab-button {
  color: var(--color-orange-500);
}

.physio .tab-button:not(:last-child) {
  border-right: 2px solid var(--color-lila-500);
}

.acu .tab-button:not(:last-child) {
  border-right: 2px solid var(--color-orange-500);
}

@media (min-width: 48rem) {
  .tab-button {
    white-space: wrap;
    padding: 2.5rem 2rem;
  }

  .physio .tab-button {
    border-bottom: 2px solid var(--color-lila-500);
  }

  .acu .tab-button {
    border-bottom: 2px solid var(--color-orange-500);
  }

  .physio .tab-button:not(:last-child),
  .acu .tab-button:not(:last-child) {
    border-right: 0;
  }

  .physio .tab-button:first-child {
    border-top: 2px solid var(--color-lila-500);
  }

  .acu .tab-button:first-child {
    border-top: 2px solid var(--color-orange-500);
  }
}

.tab-button:hover {
  background-color: rgba(77, 68, 182, 0.1);
}

.physio .tab-button.active {
  background-color: var(--color-lila-500);
  color: var(--color-beige-100);
}

.acu .tab-button.active {
  background-color: var(--color-orange-500);
  color: var(--color-beige-100);
}

.tab-panel {
  display: flex;
  flex-direction: column;
  justify-content: start;
  gap: 1.5rem;
  padding: 2rem 0;
}

@media (min-width: 48rem) {
  .tab-panel {
    padding: 0;
    gap: 2rem;
  }
}

.tab-title {
  font-weight: 700;
}

@media (min-width: 48rem) {
  .tab-title-wrapper {
    display: flex;
    gap: 1.25rem;
    align-items: center;
  }

  .tab-title {
    text-transform: uppercase;
  }

  .physio .line {
    height: 2px;
    background-color: var(--color-lila-500);
    flex-grow: 1;
  }

  .acu .line {
    height: 2px;
    background-color: var(--color-orange-500);
    flex-grow: 1;
  }
}

.tab-content {
  position: relative;
  padding: 2rem 0.75rem;
}

@media (min-width: 48rem) {
  .tab-content {
    grid-column: 6 / 13;
    padding: 65px 1.25rem 5rem;
  }
}

@media (min-width: 64rem) {
  .tab-content {
    grid-column: 5 / 13;
  }
}

.content {
  display: flex;
  flex-direction: column;
  justify-content: start;
  gap: 2rem;
}

@media (min-width: 48rem) {
  .content {
    gap: 2.5rem;
  }
}

.content :deep(ul) {
  padding-left: 1.5rem;
}

/* Animations */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
