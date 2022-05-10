<template>
  <div>
    <v-snackbar
      v-model="snackbar"
      absolute
      top
      right
      color="success"
    >
      <span>Registration successful!</span>
      <v-icon dark>
        mdi-checkbox-marked-circle
      </v-icon>
    </v-snackbar>
    <div class="title deep-orange--text text--accent-2 pt-5">
      <div class="font-weight-black text-center">
        簡単に会議が開けるやつです
      </div>
      <div class="mt-3 font-weight-black text-center text-h4">
        会議開くくん
      </div>
    </div>
    <div class="d-flex justify-space-around mt-10">
      <v-card color="deep-orange lighten-1" dark width="200" height="200" class="d-flex justify-center align-center font-weight-black">
        定例会議を開く
      </v-card>
      <v-card color="deep-orange lighten-3" dark width="200" height="200" class="d-flex justify-center align-center font-weight-black">
        単体会議を開く
      </v-card>
    </div>

    <!-- 定例会議 -->
    <div class="title mt-10">
      <!-- project名 -->
      <div class="deep-orange--text text--accent-2 pt-5 font-weight-black text-center">
        プロジェクト名
      </div>
      <div class="d-flex justify-center mx-2">
        <v-select
          :items="projects"
          item-text="name"
          item-value="id"
          v-model="selectedProjectId"
          label="Outlined style"
          outlined
          @change="onChangeProject"
        ></v-select>
      </div>

      <div class="until mt-10">
        <div class="deep-orange--text text--accent-2 font-weight-black text-center">
          概要
        </div>
        <div class="mt-3 my-2 d-flex justify-center">
          <v-textarea
            v-model="memo"
            label="会議の概要"
            counter
            rows=3
            maxlength="60"
            single-line
          ></v-textarea>
        </div>
      </div>

      <!-- 曜日指定 -->
      <div class="deep-orange--text text--accent-2 pt-5 font-weight-black text-center">
        何曜日？
      </div>
      <div class="mt-5 d-flex justify-space-around">
        <v-btn
          v-for="(day, i) in days" :key="i"
          elevation="2"
          icon
          outlined
          class="font-weight-black"
          @click="onSelectDays(day)"
        >
          {{day}}
        </v-btn>
      </div>

      <div v-if="selectedDay" class="mt-5 text-center font-weight-black">
        <span class="orange--text">
          {{selectedDay}}
        </span>
         曜日
      </div>

      <!-- 日時 -->
      <div class="nichizi mt-10">
        <div class="deep-orange--text text--accent-2 pt-5 font-weight-black text-center">
          何時から？
        </div>
        <div class="mt-3 d-flex justify-center">
          <vue-timepicker :minute-interval="10" :hour-range="[5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22]" v-model="startTime"  format="HH:mm"></vue-timepicker>
        </div>
      </div>

      <div class="until mt-10">
        <div class="deep-orange--text text--accent-2 font-weight-black text-center">
          いつまで？
        </div>
        <div class="mt-3 d-flex justify-center">
          <v-date-picker
            v-model="until"
            color="deep-orange lighten-2"
          ></v-date-picker>
        </div>
      </div>

      <!-- result -->
      <div class="text-center">
        <span class="deep-orange--text text--accent-2 font-weight-black">
          {{until}}
        </span>
        まで<br>
        毎週
        <span class="deep-orange--text text--accent-2 font-weight-black">
          {{selectedDay}}
        </span>
        曜日,
         <span class="deep-orange--text text--accent-2 font-weight-black">
           {{startTime}}
        </span>
        時から,
        <span class="deep-orange--text text--accent-2 font-weight-black">
          {{duration}}
        </span>
        分
      </div>

      <!-- dialog -->
      <div class="text-center">
        <v-dialog
          v-model="dialog"
          width="500"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="red lighten-2"
              dark
              v-bind="attrs"
              v-on="on"
            >
              確認
            </v-btn>
          </template>

          <v-card>
            <div class="title pt-5 text-center">
              コスト約:
                <span class=" deep-orange--text text--accent-2 font-weight-black text-center">{{ expectedCost }}</span>
              円
              <div class="mt-3 font-weight-black text-center">
                参加者:
                <span v-for="attendee in attendees" :key="attendee">{{attendee}}</span>
              </div>
            </div>
            

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="primary"
                text
                @click="onSubmit"
              >
                送信!
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>


      <div v-if='error != null'>
        {{ error }}
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  async asyncData({ $axios }) {
    const params = {
      crossDomain: true
    }
    const url = "https://script.googleusercontent.com/macros/echo?user_content_key=mO69dNe9eLZzLvoz6_3StwERo6EX4LLkYNq121VkT_gsDhZW-qCxCKuCQ3_8CW3UOUCz1xllvq4977Huy9JZDd5Fqx7UgKI-m5_BxDlH2jW0nuo2oDemN9CCS2h10ox_1xSncGQajx_ryfhECjZEnAsGPfO2IXCSvq6dMjSRPlbQ-inGNbUO07pQS2GYJhGbReFeysLx4ONEPOfzsUXstPiQtEYFC1KHPBzMziUrOOg0Sad3A2AZAw&lib=MM8YFrhtThaXWeNMz7TBMyLaEnKguDlaw";
    const response = await $axios.$get(url, { params });

    return {
      projects: response
    };
  },
  data() {
    return {
      snackbar: false,
      memo: '',
      selectedProjectId: '',
      days: [
        "月",
        "火",
        "水",
        "木",
        "金"
      ],
      selectedDay: '',
      startTime: '10:00',
      duration: 30,
      until: '',
      expectedCost: 10000,
      attendees: ['nguchi', 'daisuke', 'プロジェクト太郎'],

      dialog: false,
      error: null,
    }
  },
  computed: {
    dateRangeText () {
      return this.dates.join(' ~ ')
    },
  },
  methods: {
    getDateDiff() {
    },
    onChangeProject() {
    },
    onSelectDays(day) {
      this.selectedDay = day
    },
    onSubmit() {
      this.dialog = false

      // run api
      // const url = "https://script.googleusercontent.com/macros/echo?user_content_key=mO69dNe9eLZzLvoz6_3StwERo6EX4LLkYNq121VkT_gsDhZW-qCxCKuCQ3_8CW3UOUCz1xllvq4977Huy9JZDd5Fqx7UgKI-m5_BxDlH2jW0nuo2oDemN9CCS2h10ox_1xSncGQajx_ryfhECjZEnAsGPfO2IXCSvq6dMjSRPlbQ-inGNbUO07pQS2GYJhGbReFeysLx4ONEPOfzsUXstPiQtEYFC1KHPBzMziUrOOg0Sad3A2AZAw&lib=MM8YFrhtThaXWeNMz7TBMyLaEnKguDlaw";
      // const params = {
      //   crossDomain: true,
      //   projectId: this.selectedProjectId,
      // }

      // try {
      //   const response = await this.$axios.$post(url, { params });
      // } catch (error) {
      //   this.error = error
      // }
    }
  }
}
</script>
