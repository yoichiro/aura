<template>
  <v-container>
    <v-layout wrap row>
      <v-flex xs12 align-center justify-center v-if="showLoader">
        <v-progress-circular :size="50" color="blue" indeterminate></v-progress-circular>
      </v-flex>
      <v-flex v-if="showData">
        <v-card>
          <v-card-title class="headline grey lighten-2" primary-title>{{detail.name}}</v-card-title>
          <v-card-text v-html="detail.position"></v-card-text>
          <v-divider></v-divider>
          <v-layout
            wrap
            row
            fill-height
            align-center
            class="pa-4"
            xs12
            v-if="(detail.profileImage).length>0"
          >
            <v-flex xs12 sm4 md3 lg2 class="pa-2">
              <v-responsive :aspect-ratio="1/1">
                <v-avatar size="100%">
                  <v-img
                    :src="getStorageUrl(detail.profileImage)"
                    :lazy-src="getStorageUrl(detail.profileImage)"
                    v-on="on"
                  >
                    <v-layout slot="placeholder" fill-height align-center justify-center ma-0>
                      <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
                    </v-layout>
                  </v-img>
                </v-avatar>
              </v-responsive>
            </v-flex>
            <v-flex xs12 sm8 md9 lg10 class="pa-2">
              <v-card-text>
                <h2>Profile</h2>
                <p style="white-space:pre-wrap; word-wrap:break-word;" v-html="speakerProfile"></p>
              </v-card-text>
            </v-flex>
          </v-layout>
          <v-layout
            wrap
            row
            fill-height
            align-center
            xs12
            class="pa-4"
            v-if="(detail.sessionTitle).length>0"
          >
            <v-card-text>
              <h2>講演概要</h2>
              <h3>{{detail.sessionTitle}}</h3>
              <p style="white-space:pre-wrap; word-wrap:break-word;" v-html="detail.sessionDetail"></p>
            </v-card-text>
          </v-layout>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import Mixin from "@/mixin.js";

export default {
  created() {
    this.getDetail();
    this.setMeta();
  },
  data() {
    return {
      detail: "",
      speakerName: "",
      speakerProfile: "",
      showLoader: true,
      showData: false
    };
  },
  mixins: [Mixin],
  methods: {
    getDetail() {
      const speakerID = this.$route.params.id;
      const baseUrl = "/api/speaker/";
      fetch(baseUrl + speakerID)
        .then(data => data.json())
        .then(response => {
          this.showLoader = false;
          this.showData = true;
          this.detail = response;
          this.speakerName = response.name;
          this.speakerProfile = response.profile;
          this.setMeta();
        });
    },
    setMeta() {
      var title = this.speakerName + " | GDG DevFest Tokyo 2019";
      var description = this.sessionTitle;
      document.title = title;
      document
        .querySelector("meta[property='og:title']")
        .setAttribute("content", title);
      document
        .querySelector("meta[name='description']")
        .setAttribute("content", description);
      document
        .querySelector("meta[property='og:description']")
        .setAttribute("content", description);
    }
  }
};
</script>