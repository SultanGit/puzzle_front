py code
<template>
  <v-app>
    <v-app-bar flat color="blue"></v-app-bar>
    <v-main>
      <v-container fill-height fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="6">
            <v-card>
              <v-card-title>
                Add Photo
              </v-card-title>
              <v-card-text>
                <v-file-input
                    v-model="selectedPhoto"
                    label="Select a photo"
                    accept="image/*"
                ></v-file-input>
                <v-img
                    v-if="selectedPhoto"
                    :src="selectedPhotoUrl"
                    height="200"
                    contain
                ></v-img>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      selectedPhoto: null,
    };
  },
  computed: {
    selectedPhotoUrl() {
      if (!this.selectedPhoto) {
        return null;
      }
      if (this.selectedPhoto.length === 0) {
        this.selectedPhoto = null;
        return null;
      }

      return URL.createObjectURL(this.selectedPhoto[0]);
    },
  },
};
</script>
