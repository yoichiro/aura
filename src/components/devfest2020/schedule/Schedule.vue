<template>
  <div>
    <div v-on:click="handleSessionClick">
      <ScheduleRow :sdata="sdata" />
    </div>
    <v-dialog v-model="dialog" @input="dialogClosed">
      <v-card>
        <v-card-title class="headline grey lighten-2" primary-title>{{sdata.title}}</v-card-title>
        <v-divider></v-divider>
        <v-layout wrap row fill-height justify-center class="pa-4">
          <v-flex xs12 sm12 md3 lg2 class="pa-2">
            <v-card-text>
              <h2>セッション概要</h2>
            </v-card-text>
          </v-flex>
          <v-flex xs12 sm12 md9 lg10 class="pa-2">
            <v-card-text>
              <p
                  style="white-space:pre-wrap; word-wrap:break-word;"
              >{{sdata.description}}</p>
            </v-card-text>
          </v-flex>
        </v-layout>
        <v-divider></v-divider>
        <v-layout wrap row fill-height justify-center class="pa-4">
          <v-flex xs12 sm10 md3 lg2 class="pa-2">
            <v-responsive :aspect-ratio="1/1">
              <v-avatar size="100%">
                <v-img
                    :src="getStorageUrl(sdata.profileImage)"
                    :lazy-src="getStorageUrl(sdata.profileImage)"
                    :aspect-ratio="1/1"
                >
                  <v-layout
                      slot="placeholder"
                      fill-height
                      align-center
                      justify-center
                      ma-0
                  >
                    <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                  </v-layout>
                </v-img>
              </v-avatar>
            </v-responsive>
          </v-flex>
          <v-flex xs12 sm12 md9 lg10 class="pa-2">
            <v-card-text>
              <p style="white-space:pre-wrap; word-wrap:break-word;">{{sdata.speakerName}}</p>
              <p style="white-space:pre-wrap; word-wrap:break-word;">{{sdata.profile}}</p>
            </v-card-text>
          </v-flex>
        </v-layout>
        <v-layout v-for="(coSpeaker, index) in getCoSpeakerData(sdata.coSpeakers)" :key="index" wrap row fill-height justify-center class="pa-4">
          <v-flex xs12 sm10 md3 lg2 class="pa-2">
            <v-responsive :aspect-ratio="1/1">
              <v-avatar size="100%">
                <v-img
                    :src="getStorageUrl(coSpeaker.profileImage)"
                    :lazy-src="getStorageUrl(coSpeaker.profileImage)"
                    :aspect-ratio="1/1"
                >
                  <v-layout
                      slot="placeholder"
                      fill-height
                      align-center
                      justify-center
                      ma-0
                  >
                    <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                  </v-layout>
                </v-img>
              </v-avatar>
            </v-responsive>
          </v-flex>
          <v-flex xs12 sm12 md9 lg10 class="pa-2">
            <v-card-text>
              <p style="white-space:pre-wrap; word-wrap:break-word;">{{coSpeaker.speakerName}}</p>
              <p style="white-space:pre-wrap; word-wrap:break-word;">{{coSpeaker.profile}}</p>
            </v-card-text>
          </v-flex>
        </v-layout>
      </v-card>
    </v-dialog>
  </div>
</template>

<style>
</style>

<script>
import ScheduleRow from "@/components/devfest2020/schedule/ScheduleRow";
import SessionData from "@/assets/data/devfest2020session.json";
import Mixin from "@/mixin.js";

export default {
  data() {
    return {
      dialog: false,
      sessionsData: SessionData
    };
  },
  props: {
    sdata: {
      type: Object,
      default: null
    },
    isDay1: {
      type: Boolean
    }
  },
  components: {
    ScheduleRow,
  },
  watch: {
    $route(to, from) {
      const fromSessionId = from.params.session_id;
      const toSessionId = to.params.session_id;

      if (fromSessionId && toSessionId === undefined) {
        this.dialog = false;
      }
    },
  },
  mounted() {
    if (this.$route.params.session_id) {
      if (this.sdata.id === this.$route.params.session_id) {
        this.dialog = true;
      }
    }
  },
  methods: {
    handleSessionClick: function() {
      this.dialog = true;
      this.$router.push(`/devfest2020/schedule/${this.isDay1 ? 1 : 2}/${this.sdata.id}`);
    },
    dialogClosed() {
      this.$router.back();
    },
    getCoSpeakerData(coSpeakers) {
      if (coSpeakers == null) {
        return []
      }
      return this.sessionsData.filter(data => {
        return coSpeakers.includes(data.id);
      })
    }
  },
  mixins: [Mixin]
};
</script>
