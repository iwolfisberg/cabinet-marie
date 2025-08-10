<script setup>
import { ref, watchEffect, onMounted } from "vue";
import CloseIcon from "../icons/CloseIcon.vue";

const {
  service,
  classes = "btn-primary",
  onBeforeOpen,
} = defineProps(["service", "classes", "onBeforeOpen"]);

const isExpanded = ref(false);
const isMounted = ref(false);

// S'assurer que le composant est monté côté client
onMounted(() => {
  isMounted.value = true;
});

const baseUrl =
  "https://reservation.marie-santschi.ch/index.php?only=1&provider=4";
const appointmentUrl = ref(baseUrl);

watchEffect(() => {
  if (service) {
    appointmentUrl.value = baseUrl + `&service=${service}`;
  } else {
    appointmentUrl.value = baseUrl;
  }
});

const toggleModal = () => {
  if (!isExpanded.value && onBeforeOpen) {
    onBeforeOpen();
  }
  isExpanded.value = !isExpanded.value;
  if (typeof document !== "undefined") {
    document.querySelector("body").style.overflow = isExpanded.value
      ? "hidden"
      : "auto";
  }
};
</script>

<template>
  <button
    type="button"
    aria-controls="appointment-modal"
    :aria-expanded="isExpanded"
    @click="toggleModal"
    class="btn-open-modal text-md"
    :class="classes"
  >
    Prendre rendez-vous
  </button>

  <Teleport to="body" v-if="isMounted">
    <div class="modal-wrapper" id="appointment-modal" v-show="isExpanded">
      <div class="modal" @click.stop>
        <button
          type="button"
          aria-label="Fermer la modal de réservation"
          class="close-modal-btn"
          @click="toggleModal"
        >
          <CloseIcon />
        </button>

        <iframe
          :src="appointmentUrl"
          frameborder="0"
          width="100%"
          height="100%"
          loading="lazy"
        ></iframe>
      </div>
    </div>
  </Teleport>
</template>

<style scoped>
.modal-wrapper {
  position: fixed;
  inset: 0;
  background: rgb(9 36 13 / 25%);
  justify-content: center;
  align-items: center;
  z-index: 1000;
  display: flex;
}

@media (min-width: 48rem) {
  .modal-wrapper {
    padding: 7.5rem 5rem;
  }
}

.modal {
  background: var(--color-beige-100);
  padding: 4rem 0.75rem 1rem;
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 85%;
  height: calc(100svh - 15rem);
  border-radius: var(--rounded-sm);
}

@media (min-width: 48rem) {
  .modal {
    padding: 4rem 2.5rem;
    height: 100%;
    width: 60%;
    max-width: calc(100rem - 5rem);
  }
}

@media (min-width: 80rem) {
  .modal {
    width: 35%;
  }
}

.close-modal-btn {
  position: absolute;
  top: 0.75rem;
  right: 0.75rem;
  background: transparent;
  border: none;
  padding: 0;
  width: 2rem;
  height: 2rem;
  z-index: 1500;
  cursor: pointer;
}

@media (min-width: 48rem) {
  .close-modal-btn {
    top: 1rem;
    right: 1rem;
    width: 2.5rem;
    height: 2.5rem;
  }
}

.btn-open-modal {
  cursor: pointer;
}

iframe {
  border: none;
  width: 100%;
  height: 90%;
}
</style>
