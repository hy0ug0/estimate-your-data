<template>
  <v-expand-transition>
    <v-card v-show="show">
      <v-card-text>
        <h2 class="display-2 font-weight-black text-uppercase">
          OUR TEAM ANALYSING YOUR FILES
        </h2>
        <div>
          <v-fade-transition group>
            <div
              v-for="log in logsFinish"
              :key="log.id"
              style="text-align: left; padding: 5px 0;"
            >
              <div v-show="log.show" style="background-color: #b4b4b4;">
                {{ log.message }}
              </div>
            </div>
          </v-fade-transition>
        </div>
      </v-card-text>
    </v-card>
  </v-expand-transition>
</template>

<script>
export default {
  props: {
    show: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      showItem: false,
      logsFinish: [],
      logs: [
        {
          show: false,
          message: '➡️ Securing your data by encrypting it',
          id: 1,
        },
        {
          show: false,
          message: '✅️ Your data is now secure and encrypt',
          id: 2,
        },
        {
          show: false,
          message: '➡️ Sending file to server to annalysing your data...',
          id: 3,
        },
        {
          show: false,
          message: '✅ File successfully catch by our server',
          id: 4,
        },
        {
          show: false,
          message: '➡️ Format your data for our algorithm...',
          id: 5,
        },
        { show: false, message: '✅ Formating DONE', id: 6 },
        { show: false, message: '➡️ Analyse theme of your data...', id: 7 },
        {
          show: false,
          message: '✅ Theme found, good news its our favorite theme :)',
          id: 8,
        },
        { show: false, message: '➡️ Checking relevant data', id: 9 },
        { show: false, message: '✅ A lot of relevant data found !', id: 10 },
        {
          show: false,
          message:
            '➡️ Scoring this relevant data, by date, volume and farts... a lot of farts we need',
          id: 11,
        },
        {
          show: false,
          message: '✅ FARTS WAS GOOD, GOD THANK YOU BOY',
          id: 12,
        },
        { show: false, message: '➡️ Adding price to your data...', id: 13 },
        { show: false, message: '✅ Our estimation is ready !', id: 14 },
      ],
    };
  },
  mounted() {
    setTimeout(() => {
      this.waitRandom();
    }, 1500);
  },
  methods: {
    async waitRandom() {
      for (const log of this.logs) {
        await new Promise((resolve) => {
          setTimeout(
            () => {
              log.show = true;
              resolve('toto');
            },
            log.id % 2 === 0 ? Math.random() * 3 * 1000 : 0
          );
        });
        if (this.logsFinish.length === 2) {
          this.logsFinish = [];
        }
        this.logsFinish.push(log);
      }
    },
  },
};
</script>
