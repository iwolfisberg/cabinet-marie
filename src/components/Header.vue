<script setup>
import { ref } from "vue";

import BurgerIcon from "../icons/BurgerIcon.vue";
import CloseIcon from "../icons/CloseIcon.vue";
import AppointmentModal from "../components/AppointmentModal.vue";

const isExpanded = ref(false);

const menuItems = [
  {
    title: "À propos",
    link: "#",
  },
  {
    title: "Approche thérapeutique",
    link: "#",
  },
  {
    title: "Physiothérapie",
    link: "#",
  },
  {
    title: "Acupuncture",
    link: "#",
  },
  {
    title: "Contact",
    link: "#",
  },
  {
    title: "Horaires",
    link: "#",
  },
  {
    title: "Accès",
    link: "#",
  },
];

const toggleMenu = () => {
  isExpanded.value = !isExpanded.value;
  document.querySelector("body").style.overflow = isExpanded.value
    ? "hidden"
    : "auto";
};
</script>

<template>
  <div class="header-wrapper">
    <header class="header">
      <div class="header-inside">
        <div class="header-title">
          <h1 class="heading-md">Marie Santschi</h1>
          <p class="text-md">
            Cabinet de physiothérapie et d'acupuncture à Fribourg
          </p>
        </div>

        <div class="header-cta">
          <AppointmentModal />
        </div>

        <button
          type="button"
          aria-controls="menu"
          :aria-expanded="isExpanded"
          aria-label="Ouvrir et fermer le menu"
          @click="toggleMenu"
          class="btn-toggle-menu"
        >
          <CloseIcon v-if="isExpanded" />
          <BurgerIcon v-else />
        </button>
      </div>
    </header>
    <div id="menu" class="menu" :class="{ 'menu-hidden': !isExpanded }">
      <ul class="menu-list">
        <li v-for="item in menuItems" :key="item.title" class="menu-list-item">
          <a :href="item.link" class="text-lg">
            {{ item.title }}
          </a>
        </li>
      </ul>
      <AppointmentModal />
    </div>
  </div>
</template>

<style scoped>
.header-wrapper {
  position: relative;
}

.header {
  padding: 0 0.75rem;
}

@media (min-width: 64rem) {
  .header {
    padding: 0 2.5rem;
  }
}

@media (min-width: 48rem) {
  .header {
    padding: 0 1.25rem;
  }
}

.header-inside {
  display: flex;
  justify-content: space-between;
  align-items: end;
  gap: 1rem;
  padding: 1.25rem 0;
  border-bottom: 2px solid var(--color-green-500);
}

.header-title {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.header-cta {
  display: none;
}

@media (min-width: 48rem) {
  .header-cta {
    display: inline-flex;
  }
}

.btn-toggle-menu {
  background-color: transparent;
  border: none;
  margin: 0;
  padding: 0;
  width: 2rem;
  height: 2rem;
}

@media (min-width: 48rem) {
  .btn-toggle-menu {
    display: none;
  }
}

.menu {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background-color: var(--color-beige-100);
  box-shadow: 0 4px 4px 0 rgba(9, 36, 13, 0.25);
  border-radius: 0 0 4px 4px;
  padding: 2.5rem 0.75rem;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;

  transition: opacity 0.25s ease, visibility 0.3s ease;
}

.menu-hidden {
  opacity: 0;
  visibility: hidden;
}

.menu-list {
  list-style: none;
  margin-block: 0;
  padding-inline-start: 0;
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}
</style>
