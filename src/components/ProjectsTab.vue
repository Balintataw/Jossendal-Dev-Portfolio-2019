<template>
  <div class="project-container">
    <v-layout wrap style="height: 100%;">
      <!-- Projects section -->
      <v-flex xs12 md6 style="height: 100%;">
        <v-layout justify-center align-center fill-height>
          <v-list class="professional-project-list list-scroll">
            <v-list-tile
              v-for="project in getProjects"
              :key="project.title"
              class="project-list-item-left"
            >
              <v-list-tile-content>
                <a
                  class="project-titles"
                  @mouseenter="setPopupText(project.id)"
                  @mouseleave="clearPopupText()"
                  :href="project.url ? project.url : null"
                  v-on="
                    project && project.ref
                      ? { click: () => openModal(project.ref) }
                      : null
                  "
                  _target="blank"
                  >{{ project.title }}</a
                >
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
        </v-layout>
      </v-flex>
      <!-- Project blurb section -->
      <v-flex xs12 md6>
        <v-layout class="text-popup">
          <p>{{ popupText }}</p>
        </v-layout>
      </v-flex>
    </v-layout>

    <!-- Begin onerous modals section -->

    <joss-dialog
      v-for="project in getProjectsWithModal"
      maxWidth="80%"
      :ref="project.ref"
      :key="project.title + project.id"
    >
      <div slot="modaltitle">{{ project.title }}</div>
      <div slot="modalcontent">
        <v-layout wrap>
          <v-flex xs12 sm3>
            <h4 class="modal-tech-title">Tech Used:</h4>
            <ul>
              <li
                class="modal-tech-list-item"
                v-for="item in project.tech"
                :key="project.title + item"
              >
                {{ item }}
              </li>
            </ul>
            <p class="pt-2 modal-tech-description">{{ project.description }}</p>
          </v-flex>
          <v-flex xs12 sm9>
            <v-layout wrap justify-space-around>
              <!-- this is the zoom on hover variation, WIP -->
              <!-- <div class="project-images">
                                <zoom-on-hover img-normal="/assets/cms_admin.png" img-zoom="/assets/cms_admin.png" :scale="2" imgAlt="CMS Dashboard"></zoom-on-hover>
                            </div>
              <img @mousemove="zoomIn($event)" @mouseout="zoomOut($event)" src="../assets/cms_admin.png" class="project-images" alt="CMS Dashboard"/>-->
              <picture v-for="(img, i) in project.images" :key="img.alt + i">
                <source :srcset="img.url" type="image/webp" />
                <img
                  :src="img.url_fallback"
                  class="project-images"
                  :alt="img.alt"
                />
              </picture>
            </v-layout>
          </v-flex>
        </v-layout>
      </div>
      <div slot="detailsButton" v-if="project.button">
        <v-btn
          :name="`${project.name} button link`"
          color="accent"
          :disabled="!project.button.url"
          right
          @click="projectLink(project.button.url)"
          >{{ project.button.text }}</v-btn
        >
      </div>
      <div slot="detailsButton" v-if="project.badgeButton">
        <!-- <v-btn 
                    :name="`${project.name} button link`"
                    right 
        class="pr-0 pl-0">-->
        <v-img
          :height="50"
          :width="100"
          style="margin-right:20px; cursor:pointer;"
          alt="Google Play store button"
          @click="projectLink(project.badgeButton.url)"
          :src="project.badgeButton.android"
        />
        <!-- </v-btn> -->
      </div>
    </joss-dialog>
  </div>
</template>
<script>
import Dialog from "@/components/Dialog.vue";
// import ZoomOnHover from '@/components/ZoomOnHover.vue';

import projectsJson from "../projects.json";

export default {
  components: {
    "joss-dialog": Dialog
    // 'zoom-on-hover': ZoomOnHover,
  },
  data() {
    return {
      projects: projectsJson,
      popupText:
        "Check out what we're currently working on at Jossendal Development"
    };
  },
  methods: {
    setPopupText(id) {
      // fetch description by id from json file by the data attr id
      this.popupText = this.projects.data[id].description;
      let el = document.querySelector(".text-popup");
      el.classList.toggle("animate-grow");
    },
    clearPopupText() {
      // this.popupText = "Check out what we're currently working on at Jossendal Development";
      let el = document.querySelector(".text-popup");
      el.classList.toggle("animate-grow");
    },
    projectLink(url) {
      if (url) window.open(url);
    },

    openModal(ref) {
      // ref is an array of component props
      this.$refs[ref][0].open();
    }
  },
  computed: {
    getProjects() {
      // TODO: remove p.type and just grab all projects from json
      return this.projects.data.filter(p => {
        return p.type === "professional";
      });
    },
    getProjectsWithModal() {
      return this.projects.data.filter(p => {
        return p.modal;
      });
    }
  }
};
</script>
<style lang="scss" scoped>
.project-container {
  background-color: var(--v-secondary-base);
  padding: 0px;
  height: 100%;
  background: radial-gradient(
    circle farthest-side at 80%,
    #fd775248,
    transparent,
    transparent
  );
}
.project-images {
  max-height: 200px;
  max-width: 300px;
  margin-bottom: 10px;
  margin-right: 10px;
  margin-left: 10px;
  border-radius: 5px;
  box-shadow: 4px 5px 8px 2px grey;
}
.project-titles {
  font-family: AvenirNext, Avenir, Helvetica, Arial, sans-serif;
  font-display: auto;
  font-size: 2.4rem;
  cursor: pointer;
  text-decoration: none;
  color: var(--v-primary-base);
}
.project-titles:hover {
  color: var(--v-primaryDark-base);
}
.professional-project-list {
  // margin-right: auto;
}
.list-scroll {
  overflow-y: auto;
  overflow-x: hidden;
  // height: 35rem;
  // height: 100%;
  // padding-top: 2.7rem;
  padding-bottom: 0;
  &::-webkit-scrollbar {
    // width: 0.3em;  /* scrollbar space */
    width: 0em; /* scrollbar space */
    background: transparent; /* Optional: just make scrollbar invisible */
  }
  // &::-webkit-scrollbar-thumb {
  //     background-color: var(--v-accent-base);
  //     border-radius: 8px;
  // }
  // &::-webkit-scrollbar-track {
  //     -webkit-box-shadow: inset 0 0 6px rgba(111,111,111,0.7);
  // }
}
.project-list-item-left > div > div {
  align-items: center;
}
@for $i from 1 to 10 {
  // scss loop syntax to give cascading effect of animation 'drop<direction>'
  .project-list-item-left:nth-child(#{$i}) {
    animation: dropleft 0.5s;
    -webkit-animation: dropleft 0.5s;
    animation-delay: $i * 0.05s;
    -webkit-animation-delay: $i * 0.05s;
    animation-fill-mode: both;
    -webkit-animation-fill-mode: both;
  }
}
@keyframes dropleft {
  from {
    opacity: 0;
    margin-bottom: 0;
    transform: translateY(-50px) rotateY(0deg);
    -webkit-transform: translateY(-50px) rotateY(0deg);
  }
  to {
    opacity: 1;
    margin-bottom: 30px;
    transform: translateY(0px) rotateY(35deg);
    -webkit-transform: translateY(0px) rotateY(35deg);
  }
}
@keyframes recenter {
  from {
    transform: rotateY(0deg);
    -webkit-transform: rotateY(0deg);
  }
  to {
    transform: rotateY(0deg);
    -webkit-transform: rotateY(0deg);
  }
}
// center text styles
.text-popup {
  // position: absolute;
  // top: 0;
  // right: 120px;
  // bottom: 0;
  // left: 0;
  // width: 25%;
  height: 100%;
  margin: 0 auto;
  z-index: 20;
  justify-content: center;
  align-items: center;
}
.animate-grow {
  animation: grow 0.25s;
  -webkit-animation: grow 0.25s;
}
.text-popup > p {
  text-align: center;
  color: var(--v-primary-base);
  font-size: 2rem;
  width: 90%;
}
@keyframes grow {
  from {
    transform: scale(0);
    -webkit-transform: scale(0);
  }
  to {
    transform: scale(1);
    -webkit-transform: scale(1);
  }
}
.modal-tech-title {
  font-size: 1.3rem;
  margin-bottom: 0.2rem;
}
.modal-tech-list-item {
  font-size: 1.3rem;
  margin-bottom: 0.2rem;
}
.modal-tech-description {
  font-size: 1.3rem;
}

/* *********  Queries section  *********** */

@media screen and (max-width: 960px) {
  .project-container {
    background: radial-gradient(
      circle farthest-side,
      #fd775248,
      transparent,
      transparent
    );
  }
  .professional-project-list {
    margin: 0 auto;
  }
  @for $i from 1 to 10 {
    .project-list-item-left:nth-child(#{$i}) {
      animation: recenter 0.5s;
      -webkit-animation: recenter 0.5s;
      animation-delay: $i * 0.05s;
      -webkit-animation-delay: $i * 0.05s;
      animation-fill-mode: both;
      -webkit-animation-fill-mode: both;
    }
  }
  .list-scroll {
    // height: auto;
    // padding-top: 0;
    padding-bottom: 1rem;
  }
  .text-popup {
    visibility: hidden;
  }
  .project-titles {
    font-size: 2.2rem;
  }
  .project-list-item-left > div > div {
    align-items: center;
  }
}
@media screen and (max-width: 480px) {
  .project-titles {
    font-size: 2rem;
  }
}
</style>
