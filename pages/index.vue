<template>
  <div id="app">
    <v-app>
      <v-main>
        <section id="hero">
          <v-row no-gutters>
            <v-img
              :height="'calc(100vh - ' + $vuetify.application.footer + 'px)'"
              src="universe.jpg"
            >
              <v-theme-provider dark>
                <v-container fill-height>
                  <v-row
                    align="center"
                    class="white--text mx-auto"
                    justify="center"
                  >
                    <v-col class="white--text text-center" cols="12" tag="h1">
                      <span
                        :class="[
                          $vuetify.breakpoint.smAndDown
                            ? 'display-3'
                            : 'display-4',
                        ]"
                        class="font-weight-black text-uppercase"
                      >
                        Estimate Your Data
                      </span>
                    </v-col>
                    <v-col
                      cols="12"
                      :class="{ isHovering: state.isHovering }"
                      @drop.prevent="addDropFile"
                      @dragover.prevent="handleStartHovering"
                      @dragleave.prevent="handleEndHovering"
                    >
                      <v-file-input
                        v-model="state.files"
                        show-size
                        counter
                        multiple
                        label="Drop your files here"
                        @change="resetPreviousEstimation"
                      ></v-file-input>
                    </v-col>
                    <v-col cols="12" align="center">
                      <v-btn
                        x-large
                        color="red"
                        :loading="state.isEstimating"
                        :disabled="!state.files.length > 0"
                        @click="estimateData"
                      >
                        <v-icon left>mdi-rocket</v-icon> ESTIMATE</v-btn
                      >
                      <v-spacer class="mb-5" />
                      <v-expand-transition>
                        <v-card v-show="state.showEstimation">
                          <v-card-text>
                            <h2
                              class="display-2 font-weight-black text-uppercase"
                            >
                              Our Estimation
                            </h2>
                            <div>
                              <p class="display-1">
                                According to our algorithm your data are worth
                                {{ state.estimation }}€
                              </p>
                            </div>
                          </v-card-text>
                          <v-card-actions>
                            <v-spacer />
                            <v-btn text @click="resetFields">
                              Close
                            </v-btn>
                          </v-card-actions>
                        </v-card>
                      </v-expand-transition>
                    </v-col>
                  </v-row>
                </v-container>
              </v-theme-provider>
            </v-img>
          </v-row>
        </section>
      </v-main>

      <v-footer class="justify-center" color="#292929" app>
        <div
          class="title font-weight-light grey--text text--lighten-1 text-center"
        >
          &copy; {{ new Date().getFullYear() }} — Made with 💜 by La Jeanch'
        </div>
      </v-footer>
    </v-app>
  </div>
</template>

<script lang="ts">
import { defineComponent, reactive } from '@vue/composition-api';

interface EstimatedFile {
  name: string;
  lastModified: number;
  size: number;
  type: string;
  estimation?: number;
}

export default defineComponent({
  setup() {
    const state = reactive({
      files: [] as File[],
      isHovering: false as boolean,
      isEstimating: false as boolean,
      showEstimation: false as boolean,
      estimation: 0 as number,
    });

    function resetFields(): void {
      state.isHovering = false;
      state.isEstimating = false;
      state.showEstimation = false;
      state.files = [];
      state.estimation = 0;
    }

    function resetPreviousEstimation(): void {
      state.isHovering = false;
      state.showEstimation = false;
      state.estimation = 0;
    }

    function addDropFile(e: DragEvent): void {
      state.isHovering = false;
      state.showEstimation = false;
      state.estimation = 0;
      state.files = Array.from(
        e.dataTransfer?.files as Iterable<File> | ArrayLike<File>
      );
    }

    async function estimateData(): Promise<void> {
      state.isEstimating = true;
      const importedFiles = saveFilesInfos(state.files);
      await setTimeout(function () {
        state.estimation = importedFiles.reduce(
          (prev, curr) => calculateFilePrice(curr) + prev,
          0
        );
        state.files = [];
        state.isHovering = false;
        state.isEstimating = false;
        state.showEstimation = true;
      }, Math.floor(Math.random() * 5000) + 3000);
    }

    function handleStartHovering() {
      state.isHovering = true;
    }

    function handleEndHovering() {
      state.isHovering = false;
    }

    function saveFilesInfos(files: File[]): EstimatedFile[] {
      return files.map((file) => {
        const normalizedFileName = normalizeFileName(file.name);
        if (!localStorage.getItem(normalizedFileName)) {
          const newFile = {
            name: file.name,
            lastModified: file.lastModified,
            size: file.size,
            type: file.type,
          };
          localStorage.setItem(normalizedFileName, JSON.stringify(newFile));
          return newFile;
        } else {
          return JSON.parse(localStorage.getItem(normalizedFileName) || '{}');
        }
      });
    }

    function updateFilePrice(file: EstimatedFile, price: number): void {
      const normalizedFileName = normalizeFileName(file.name);
      localStorage.setItem(
        normalizedFileName,
        JSON.stringify({ ...file, estimation: price })
      );
    }

    function calculateFilePrice(file: EstimatedFile): number {
      if (file.estimation) {
        return file.estimation;
      } else {
        const estimatedPrice = Math.floor(Math.random() * 500);
        updateFilePrice(file, estimatedPrice);
        return estimatedPrice;
      }
    }

    function normalizeFileName(rawFileName: string): string {
      return rawFileName.trim().toLowerCase().replace(/\s/g, '');
    }

    return {
      state,
      addDropFile,
      estimateData,
      handleStartHovering,
      handleEndHovering,
      resetFields,
      resetPreviousEstimation,
    };
  },
});
</script>

<style scoped>
.isHovering {
  opacity: 50%;
}
</style>
