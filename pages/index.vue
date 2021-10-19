<template>
  <div class="flex">
    <div class="mt-12"></div>
    <div class="w-1/5">
      <p
        class="w-1/2 px-4 py-2 mt-2 ml-12 font-semibold text-red-500 bg-transparent border border-red-500 rounded "
      >
        {{ label }}
      </p>

      <button
        @click="showAdd(item), showInfo(index - 1), showLabel(index)"
        v-for="(item, index) in buttonNames"
        :key="index"
        class="btn-blue"
      >
        {{ item }}
      </button>
    </div>

    <div class="w-1/2">
      <template v-if="showAddButton">
        <input
          v-model="inputInfo"
          placeholder="anahtar"
          class="w-full p-1 m-1 border-2 border-gray-600 border-solid"
          type="text"
        />
        <textarea
          v-model="areaInfo"
          placeholder="içerik"
          class="w-full p-1 m-1 border-2 border-gray-600 border-solid h-3/4"
        ></textarea>
        <div
          class="btn-blue"
          style="width: 120px; margin-left:2px"
          @click="storeInfo()"
        >
          Ekle
        </div>
      </template>
      <template v-else>
        <div
          class="flex flex-col items-center content-center w-full h-full p-1 m-1 border-2 border-gray-600 border-solid"
        >
          <div class="text-3xl">{{ showInformation }}</div>
          <div v-if="showAreaInformation" class="m-10 text-2xl ">
            {{ showArea }}
          </div>
        </div>
      </template>

      <div v-if="!showAddButton" class="flex w-1/2">
        <div class="btn-blue" @click="updateInfo(true)">
          Bildim
        </div>
        <div class="btn-blue" @click="updateInfo(false)">
          Bilemedim
        </div>
        <div class="btn-blue" @click="showAreaInfo()">
          Göster
        </div>
        <div class="btn-blue" @click="nextInfo()">
          Geç
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      label: "Öğren",
      showAreaInformation: false,
      showArea: null,
      showInformation: null,
      count: 0,
      nextInformation: [],
      inputInfo: null,
      areaInfo: null,
      showAddButton: false,
      buttonNames: [
        "Ekle",
        "Öğrenilecek",
        "1 saat",
        "3 saat",
        "8 saat",
        "24 saat",
        "1 hafta",
        "1 ay",
        "6 ay",
        "Öğrendim"
      ]
    };
  },
  methods: {
    showLabel(val) {
      this.label = this.buttonNames[val];
    },
    updateInfo(val) {
      let id = this.nextInformation[this.count - 1].id;
      let types = val
        ? this.nextInformation[this.count - 1].type + 1
        : this.nextInformation[this.count - 1].type - 1;
      types > 8 ? (types = 8) : types;
      types < 0 ? (types = 0) : types;

      axios
        .put("http://hardworking.test/api/table_information/" + id, {
          type: types
        })
        .then(res => {});
    },
    showAreaInfo() {
      this.showAreaInformation = true;
    },
    nextInfo() {
      this.showAreaInformation = false;

      if (this.nextInformation[this.count] == null) {
        this.showInformation = this.nextInformation[(this.count = 0)].key;
        this.showArea = this.nextInformation[this.count].information;
        this.count++;
      } else {
        this.showInformation = this.nextInformation[this.count].key;
        this.showArea = this.nextInformation[this.count].information;
        this.count++;
      }
    },
    showInfo(val) {
      this.count = 0;
      axios
        .post("http://hardworking.test/api/information-show", {
          type: val
        })
        .then(res => {
          this.nextInformation = res.data;
        });
    },
    indexInfo() {
      axios
        .get("http://hardworking.test/api/table_information")
        .then(res => {});
    },
    storeInfo() {
      axios
        .post("http://hardworking.test/api/table_information", {
          type: "0",
          key: this.inputInfo,
          information: this.areaInfo
        })
        .then(res => {});
    },
    showAdd(val) {
      if (val === "Ekle") {
        this.showAddButton = true;
      } else {
        this.showAddButton = false;
      }
    }
  }
};
</script>
<style scoped>
@layer components {
  .btn-blue {
    @apply w-1/2 px-4 py-2 mt-2 ml-12 font-semibold text-blue-700 bg-transparent border border-blue-500 rounded hover:bg-blue-500 hover:text-white hover:border-transparent;
  }
}
</style>
