<template>
  <v-app>
    <v-main>
      <v-container>
        <v-card>
          <v-card-title class="justify-center">
            <h1>Computer Builder</h1>
          </v-card-title>
          <v-row>
            <v-col cols="6" offset="3">
              <h2 class="text-center">step1 : Select your CPU</h2>
              <v-select
                label="Brand"
                :items="cpuBrands"
                v-model="selectedCpuBrand"
              >
              </v-select>
              <v-select
                label="Model"
                :items="cpuModelsByBrand"
                v-model="selectedCpuModel"
                :disabled="!selectedCpuBrand"
              >
              </v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="6" offset="3">
              <h2 class="text-center">step2 : Select your GPU</h2>
              <v-select
                label="Brand"
                :items="gpuBrands"
                v-model="selectedGpuBrand"
              >
              </v-select>
              <v-select
                label="Model"
                :items="gpuModelsByBrand"
                v-model="selectedGpuModel"
                :disabled="!selectedGpuBrand"
              >
              </v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="6" offset="3">
              <h2 class="text-center">step3 : Select your RAM</h2>
              <v-select
                label="How Many?"
                :items="ramNumbers"
                v-model="selectedRamNumber"
              >
              </v-select>
              <v-select
                label="Brand"
                :items="ramBrandsByNumber"
                v-model="selectedRamBrand"
                :disabled="!selectedRamNumber"
              >
              </v-select>
              <v-select
                label="Model"
                :items="ramModelsByNumberAndBrand"
                v-model="selectedRamModel"
                :disabled="!selectedRamBrand"
              >
              </v-select>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="6" offset="3">
              <h2 class="text-center">step4 : Select your storage HDD or SSD</h2>
              <v-select
                label="Storage"
                :items="storageItems"
                v-model="selectedStorageType"
              >
              </v-select>
              <template v-if="selectedStorageType === 'hdd'">
                <v-select
                  label="Brand"
                  :items="hddBrands"
                  v-model="selectedHddBrand"
                  :disabled="!selectedStorageType"
                >
                </v-select>
                <v-select
                  label="Model"
                  :items="hddModelsByBrand"
                  v-model="selectedHddModel"
                  :disabled="!selectedHddBrand"
                >
                </v-select>
              </template>
              <template v-else>
                <v-select
                  label="Brand"
                  :items="ssdBrands"
                  v-model="selectedSsdBrand"
                  :disabled="!selectedStorageType"
                >
                </v-select>
                <v-select
                  label="Model"
                  :items="ssdModelsByBrand"
                  v-model="selectedSsdModel"
                  :disabled="!selectedSsdBrand"
                >
                </v-select>
              </template>
            </v-col>
          </v-row>
        </v-card>
      </v-container>
      <v-container>
        <template v-if="isBuilt">
          <v-row>
            <v-col cols="6">
              <v-card>
                <v-card-title>
                  selected cpu
                </v-card-title>
                <v-card-text>
                  <p>Brand: {{selectedCpuItem.Brand}}</p>
                  <p>Model: {{selectedCpuItem.Model}}</p>
                  <p>Benchmark: {{selectedCpuItem.Benchmark}}</p>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col cols="6">
              <v-card>
                <v-card-title>
                  selected gpu
                </v-card-title>
                <v-card-text>
                  <p>Brand: {{selectedGpuItem.Brand}}</p>
                  <p>Model: {{selectedGpuItem.Model}}</p>
                  <p>Benchmark: {{selectedGpuItem.Benchmark}}</p>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col cols="6">
              <v-card>
                <v-card-title>
                  selected ram
                </v-card-title>
                <v-card-text>
                  <p>Brand: {{selectedRamItem.Brand}}</p>
                  <p>Model: {{selectedRamItem.Model}}</p>
                  <p>Benchmark: {{selectedRamItem.Benchmark}}</p>
                </v-card-text>
              </v-card>
            </v-col>
            <v-col cols="6">
              <v-card v-if="selectedStorageType === 'hdd'">
                <v-card-title>
                  selected hdd
                </v-card-title>
                <v-card-text>
                  <p>Brand: {{selectedHddItem.Brand}}</p>
                  <p>Model: {{selectedHddItem.Model}}</p>
                  <p>Benchmark: {{selectedHddItem.Benchmark}}</p>
                </v-card-text>
              </v-card>
              <v-card v-else-if="selectedStorageType === 'ssd'">
                <v-card-title>
                  selected ssd
                </v-card-title>
                <v-card-text>
                  <p>Brand: {{selectedSsdItem.Brand}}</p>
                  <p>Model: {{selectedSsdItem.Model}}</p>
                  <p>Benchmark: {{selectedSsdItem.Benchmark}}</p>
                </v-card-text>
              </v-card>
            </v-col>
          </v-row>
        </template>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
const url = 'https://api.recursionist.io/builder/computers'
const types = ['cpu', 'gpu', 'ram', 'hdd', 'ssd']

/**
 * @param {string} type
 * 
 * @return {Promise} Object[]
 */
async function fetchItemsBy(type){
  return fetch(`${url}?type=${type}`).then(response => response.json())
}

/**
 * @param {Object[]} items
 * @param {Function} callback
 * 
 * @return {Object} hash
 */
function itemHashGroups(items, callback){
  const hash = {}
  const keys = [...new Set(items.map( i => callback(i) ))]
  for(const key of keys){
    hash[key] = items.filter(i => key === callback(i))
  }
  return hash
}

export default {
  name: 'App',

  data: () => ({
    cpuItems: [],
    gpuItems: [],
    ramItems: [],
    hddItems: [],
    ssdItems: [],


    //cpu
    cpuHashGroupsByBrand: {},
    cpuHashGroupsByBrandAndModel: {},
    selectedCpuBrand: null,
    selectedCpuModel: null,
    //gpu
    gpuHashGroupsByBrand: {},
    gpuHashGroupsByBrandAndModel: {},
    selectedGpuBrand: null,
    selectedGpuModel: null,
    //ram
    ramHashGroupsByNumber: {},
    selectedRamNumber: null,
    ramBrandByNumber: [],
    selectedRamBrand: null,
    ramModelsByBrand: [],
    selectedRamModel: null,
    //storage
    storageItems: ['hdd', 'ssd'],
    selectedStorageType: null,
    //hdd
    hddHashGroupsByBrand: {},
    selectedHddBrand: null,
    selectedHddModel: null,
    //ssd
    ssdHashGroupsByBrand: {},
    selectedSsdBrand: null,
    selectedSsdModel: null,
  }),
  async created(){
    await this.fetchAllItems(types)
    this.setCpuHashGroups()
    this.setGpuHashGroups()
    this.setRamHashGroups()
    this.sethddHashGroups()
    this.setSsdHashGroups()
  },
  methods: {
    /**
     * @param {string[]} types
     */
    async fetchAllItems(types){
      for(const type of types){
        this[`${type}Items`] = await fetchItemsBy(type)
      }
    },
    setCpuHashGroups(){
      this.cpuHashGroupsByBrand = itemHashGroups(this.cpuItems, (i) => (i.Brand))
    },
    setGpuHashGroups(){
      this.gpuHashGroupsByBrand = itemHashGroups(this.gpuItems, (i) => (i.Brand))
    },
    setRamHashGroups(){
      const reg = /(\d+)x\d+[GB|TB]/
      const filterNumber = (i) => (i.Model.match(reg)[1])
      this.ramHashGroupsByNumber                 = itemHashGroups(this.ramItems, filterNumber)
    },
    sethddHashGroups(){
      this.hddHashGroupsByBrand = itemHashGroups(this.hddItems, (i) => (i.Brand))
    },
    setSsdHashGroups(){
      this.ssdHashGroupsByBrand = itemHashGroups(this.ssdItems, (i) => (i.Brand))
    },    
  },
  computed: {
    isBuilt(){
      if(this.selectedCpuItem && this.selectedGpuItem && this.selectedRamItem && (this.selectedHddItem || this.selectedSsdItem) ){
        return true
      } else {
        return false
      }
    },
    /*cpu*/
    /**
     * @return {String[]}
     */
    cpuBrands() {
      return Object.keys(this.cpuHashGroupsByBrand)
    },
    /**
     * cpu items selected by brand
     * @return {Object[]}
     */
    cpuItemsByBrand(){
      if (!this.cpuHashGroupsByBrand || !this.selectedCpuBrand){
        return []
      }
      return this.cpuHashGroupsByBrand[this.selectedCpuBrand]
    },
    /**
     * cpu item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    cpuHashGroupsByModel() {
      return itemHashGroups(this.cpuItemsByBrand, (i) => (i.Model))
    },
    /**
     * cpu models
     * @return {String[]}
     */
    cpuModelsByBrand() {
      return Object.keys(this.cpuHashGroupsByModel)
    },
    /**
     * selected cpu Item
     * @return {Object}
     */
    selectedCpuItem() {
      if (!this.cpuHashGroupsByModel || !this.selectedCpuModel ){
        return null
      }
      return this.cpuHashGroupsByModel[this.selectedCpuModel][0]
    },
    /*gpu*/
    /**
     * @return {String[]}
     */
    gpuBrands() {
      return Object.keys(this.gpuHashGroupsByBrand)
    },
    /**
     * gpu items selected by brand
     * @return {Object[]}
     */
    gpuItemsByBrand(){
      if (!this.gpuHashGroupsByBrand || !this.selectedGpuBrand){
        return []
      }
      return this.gpuHashGroupsByBrand[this.selectedGpuBrand]
    },
    /**
     * gpu item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    gpuHashGroupsByModel() {
      return itemHashGroups(this.gpuItemsByBrand, (i) => (i.Model))
    },
    /**
     * gpu models
     * @return {String[]}
     */
    gpuModelsByBrand() {
      return Object.keys(this.gpuHashGroupsByModel)
    },
    /**
     * selected gpu Item
     * @return {Object}
     */
    selectedGpuItem() {
      if (!this.gpuHashGroupsByModel || !this.selectedGpuModel ){
        return null
      }
      return this.gpuHashGroupsByModel[this.selectedGpuModel][0]
    },
    /*ram*/
    /**
     * @return {String[]}
     */
    ramNumbers() {
      return Object.keys(this.ramHashGroupsByNumber)
    },
    /**
     * ram items selected by number
     * @return {Object[]}
     */
    ramItemsByNumber() {
      if (!this.ramHashGroupsByNumber || !this.selectedRamNumber){
        return []
      }
      return this.ramHashGroupsByNumber[this.selectedRamNumber]
    },
    /**
     * gpu item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    ramHashGroupsByNumberAndBrand() {
      return itemHashGroups(this.ramItemsByNumber, (i) => (i.Brand))
    },
    /**
     * gpu item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    ramBrandsByNumber() {
      return Object.keys(this.ramHashGroupsByNumberAndBrand)
    },
    /**
     * ram items selected by number and brand
     * @return {Object[]}
     */
    ramItemsByNumberAndBrand() {
      if (!this.ramHashGroupsByNumberAndBrand || !this.selectedRamNumber || !this.selectedRamBrand){
        return []
      }
      return this.ramHashGroupsByNumberAndBrand[this.selectedRamBrand]
    },
    /**
     * gpu item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    ramHashGroupsByNumberAndBrandAndModel() {
      return itemHashGroups(this.ramItemsByNumberAndBrand, (i) => (i.Model))
    },
    ramModelsByNumberAndBrand() {
      return Object.keys(this.ramHashGroupsByNumberAndBrandAndModel)
    },
    /**
     * selected ram Item
     * @return {Object}
     */
    selectedRamItem() {
      if (!this.ramHashGroupsByNumberAndBrandAndModel || !this.selectedRamModel ){
        return null
      }
      return this.ramHashGroupsByNumberAndBrandAndModel[this.selectedRamModel][0]
    },
    /*hdd*/
    /**
     * @return {String[]}
     */
    hddBrands() {
      return Object.keys(this.hddHashGroupsByBrand)
    },
    /**
     * hdd items selected by brand
     * @return {Object[]}
     */
    hddItemsByBrand(){
      if (!this.hddHashGroupsByBrand || !this.selectedHddBrand){
        return []
      }
      return this.hddHashGroupsByBrand[this.selectedHddBrand]
    },
    /**
     * hdd item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    hddHashGroupsByModel() {
      return itemHashGroups(this.hddItemsByBrand, (i) => (i.Model))
    },
    /**
     * hdd models
     * @return {String[]}
     */
    hddModelsByBrand() {
      return Object.keys(this.hddHashGroupsByModel)
    },
    /**
     * selected hdd Item
     * @return {Object}
     */
    selectedHddItem() {
      if (!this.hddHashGroupsByModel || !this.selectedHddModel ){
        return null
      }
      return this.hddHashGroupsByModel[this.selectedHddModel][0]
    },
    /*ssd*/
    /**
     * @return {String[]}
     */
    ssdBrands() {
      return Object.keys(this.ssdHashGroupsByBrand)
    },
    /**
     * ssd items selected by brand
     * @return {Object[]}
     */
    ssdItemsByBrand(){
      if (!this.ssdHashGroupsByBrand || !this.selectedSsdBrand){
        return []
      }
      return this.ssdHashGroupsByBrand[this.selectedSsdBrand]
    },
    /**
     * ssd item hash tables grouped by model, after selected by brand 
     * @return {Object}
     */
    ssdHashGroupsByModel() {
      return itemHashGroups(this.ssdItemsByBrand, (i) => (i.Model))
    },
    /**
     * ssd models
     * @return {String[]}
     */
    ssdModelsByBrand() {
      return Object.keys(this.ssdHashGroupsByModel)
    },
    /**
     * selected ssd Item
     * @return {Object}
     */
    selectedSsdItem() {
      if (!this.ssdHashGroupsByModel || !this.selectedSsdModel ){
        return null
      }
      return this.ssdHashGroupsByModel[this.selectedSsdModel][0]
    },
  }
};
</script>
