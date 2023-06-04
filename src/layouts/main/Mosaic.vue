<template>
  <v-app>
    <v-main>
      <v-container fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="6">
            <v-card>
              <v-card-title>
                Add image
              </v-card-title>
              <v-card-text>
                <v-file-input
                    ref="fileInput"
                    label="Select a photo"
                    accept="image/*"
                    @click:clear="clearTiles"
                    @change="processPhoto"
                ></v-file-input>
                <v-container v-if="tiles.length > 0" class="puzzle-container">
                  <v-row>
                    <v-col v-for="(tile, index) in tiles" :key="index" cols="4">
                      <div
                          v-if="tile"
                          class="puzzle-tile"
                          :style="{ backgroundImage: tile.src ? 'url(' + tile.src + ')' : '', order: tile.order }"
                          @click="moveTile(index)"
                      ></div>
                    </v-col>
                  </v-row>
                </v-container>
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
      tiles: [],
      emptyTileIndex: 8,
    };
  },
  methods: {
    clearTiles() {
      this.tiles = [];
      this.emptyTileIndex = 8;
    },
    processPhoto() {
      const files = this.$refs.fileInput.files;
      if (files.length === 0) return;

      const reader = new FileReader();
      reader.onload = (e) => {
        const img = new Image();
        img.onload = () => {
          const tileWidth = img.width / 3;
          const tileHeight = img.height / 3;

          const canvas = document.createElement('canvas');
          const context = canvas.getContext('2d');
          canvas.width = tileWidth;
          canvas.height = tileHeight;

          this.tiles = [];
          this.emptyTileIndex = 8;

          for (let i = 0; i < 3; i++) {
            for (let j = 0; j < 3; j++) {
              if (i === 2 && j === 2) {
                this.tiles.push(null);
                break;
              }
              context.clearRect(0, 0, tileWidth, tileHeight);
              context.drawImage(
                  img,
                  j * tileWidth,
                  i * tileHeight,
                  tileWidth,
                  tileHeight,
                  0,
                  0,
                  tileWidth,
                  tileHeight
              );
              const tileDataUrl = canvas.toDataURL('image/jpeg');
              const tileOrder = i * 3 + j;
              this.tiles.push({ src: tileDataUrl, order: tileOrder });
            }
          }

          this.shuffleTiles(); // Shuffle the tiles after generating them
        };

        img.src = e.target.result;
      };

      reader.readAsDataURL(files[0]);
    },
    moveTile(tileIndex) {
      const emptyIndex = this.emptyTileIndex;
      const diff = Math.abs(tileIndex - emptyIndex);
      if (diff === 1 || diff === 3) {
        this.swapTiles(tileIndex, emptyIndex);
        this.checkWin();
      }
    },
    swapTiles(tileIndex, emptyIndex) {
      const temp = this.tiles[tileIndex];
      this.tiles[tileIndex] = this.tiles[emptyIndex];
      this.tiles[emptyIndex] = temp;
      this.emptyTileIndex = tileIndex;
    },
    checkWin() {
      const orderedTiles = this.tiles.slice().sort((a, b) => a.order - b.order);
      if (JSON.stringify(this.tiles) === JSON.stringify(orderedTiles)) {
        alert('Congratulations! You won!');
      }
    },
    shuffleTiles() {
      const shuffledTiles = this.tiles.slice(0, this.tiles.length - 1);
      for (let i = shuffledTiles.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        const temp = shuffledTiles[i];
        shuffledTiles[i] = shuffledTiles[j];
        shuffledTiles[j] = temp;
      }
      this.tiles = shuffledTiles;
    },
  },
};
</script>

<style scoped>
.puzzle-container {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  height: 100%;
}

.puzzle-tile {
  width: 100%;
  padding-bottom: 100%;
  background-size: cover;
  cursor: pointer;
  position: relative;
}

.puzzle-tile img {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

@media (max-width: 600px) {
  .puzzle-tile {
    padding-bottom: calc(100% - 10px);
  }
}
</style>
