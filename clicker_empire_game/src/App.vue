<template>
  <v-app>
    <v-main>
      <v-container>
        <v-row>
          <v-col cols="4">
            <v-card height="700">
              <v-app-bar
                dark
              >
                <v-toolbar-title>Click Hamburger</v-toolbar-title>
              </v-app-bar>
              <v-vard-text>
                <v-container>
                  <v-card>
                    <v-card-title class="justify-center">
                      {{ burgers | filterNumberByComma }} Burgers
                    </v-card-title>
                    <v-card-text class="text-center">
                      {{ profitPerSecond | filterNumberByComma }}$ per second / {{ profitPerClick | filterNumberByComma }}$ per click
                    </v-card-text> 
                  </v-card> 
                </v-container>              
              </v-vard-text>
              <v-scroll-y-transition>
                <v-img 
                  src="hamburger.png"
                  @click="clickBurger"
                  :key="burgers"
                >
                </v-img>
              </v-scroll-y-transition>
            </v-card>
          </v-col>
          <v-col cols="8">
            <v-card
              class="mx-auto"
              height="700"
            >
              <v-app-bar
                dark
              >
                <v-toolbar-title>User Status</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <v-icon v-bind="attrs" v-on="on" large>mdi-folder-download-outline</v-icon>
                  </template>
                  <span>現在データのダウンロード</span>
                </v-tooltip>
                <div class="mx-2"></div>
                <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <v-icon v-bind="attrs" v-on="on" large>mdi-reload</v-icon>
                  </template>
                  <span>続きから始める</span>
                </v-tooltip>
                <div class="mx-2"></div>
                <v-tooltip bottom>
                  <template v-slot:activator="{ on, attrs }">
                    <v-icon v-bind="attrs" v-on="on" large @click="profileDialogOpen()">mdi-account-cog</v-icon>
                  </template>
                  <span>ユーザーデータの設定</span>
                </v-tooltip>
              </v-app-bar>
              <v-container>
                <v-row dense>
                  <v-col cols="6">
                    <v-card
                    >
                      <v-card-text
                        class="text-center"
                      >
                        Name: {{name}}
                      </v-card-text>
                    </v-card>
                  </v-col>
                  <v-col cols="6">
                    <v-card>
                      <v-card-text
                        class="text-center"
                      >
                        {{ realYears | filterNumberByComma }} yrs old
                      </v-card-text>
                    </v-card>
                  </v-col>
                  <v-col cols="6">
                    <v-card
                    >

                      <v-card-text
                        class="text-center"
                      >
                        {{ days | filterNumberByComma }} days
                      </v-card-text>
                    </v-card>
                  </v-col>
                  <v-col cols="6">
                    <v-card
                    >
                      <v-card-text
                        class="text-center"
                      >
                        ${{ money | filterNumberByComma }}
                      </v-card-text>
                    </v-card>
                  </v-col>
                </v-row>
              </v-container>

              <v-container>
                <v-list max-height="450" class="overflow-y-auto">
                <v-row dense>
                  <v-list-item
                    v-for="(item, i) in items"
                    :key="i"
                    cols="12"
                  >
                  <v-col>
                    <v-card
                      dark
                    >
                      <div class="d-flex flex-no-wrap justify-space-between">
                        <div class="d-flex">
                        <v-avatar
                          class="ma-3"
                          size="125"
                          tile
                        >
                          <v-img :src="item.src"></v-img>
                        </v-avatar>
                        <div>
                          <v-card-title
                            class="text-h5"
                            v-text="item.title"
                          ></v-card-title>

                          <v-card-subtitle v-text="item.description"></v-card-subtitle>

                          <v-card-actions>
                            <v-btn
                              class="ml-2 mt-5"
                              outlined
                              rounded
                              small
                            >
                              {{ item.price | filterNumberByComma }}$ Buy
                            </v-btn>
                          </v-card-actions>
                        </div>
                        </div>
                        <div class="d-flex align-center mr-10">
                          <v-chip color="primary">
                            count: {{ item.count | filterNumberByComma }} / {{ item.maxCount | filterNumberByComma | filterInfinite }}
                          </v-chip>
                        </div>
                      </div>
                    </v-card>
                  </v-col>
                  </v-list-item>
                </v-row>
                </v-list>
              </v-container>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
      <v-dialog
        v-model="profileDialog"
        persistent
        max-width="600px"
      >
        <v-card>
          <v-card-title>
            <span class="headline">User Profile</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12">
                  <v-text-field
                    label="User name"
                    v-model="profileForm.name"
                    required
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              color="blue darken-1"
              text
              @click="profileDialog = false"
            >
              Close
            </v-btn>
            <v-btn
              color="blue darken-1"
              text
              @click="profileDataSave()"
            >
              Save
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </v-main>
  </v-app>
</template>

<script>

const initialYears = 25

export default {
  name: 'App',

  data: () => ({
    items: [
        {
          src: 'hanburger-machine.png',
          title: 'Flip machine',
          description: 'グリルをクリックごとに 25 円を取得します。',
          price: 15000,
          maxCount: 500,
          count: 0,
        },
        {
          src: 'stock.png',
          title: 'ETF Stock',
          description: 'ETF 銘柄の購入分をまとめて加算し、毎秒 0.1% を取得します。',
          price: 300000,
          maxCount: Infinity,
          count: 0,
        },
        {
          src: 'stock.png',
          title: 'ETF Bonds',
          description: '債券 ETF の購入分をまとめて加算し、毎秒 0.07% を取得します。',
          price: 300000,
          maxCount: Infinity,
          count: 0,
        },
        {
          src: 'lemonade-stand.png',
          title: 'Lemonade Stand',
          description: '毎秒 30 円を取得します。',
          price: 30000,
          maxCount: 1000,
          count: 0,
        },
        {
          src: 'icecream-trailer.png',
          title: 'Ice Cream Truck',
          description: '毎秒 120 円を取得します。',
          price: 100000,
          maxCount: 500,
          count: 0,
        },
        {
          src: 'house.png',
          title: 'House',
          description: '毎秒 32,000 円を取得します。',
          price: 20000000,
          maxCount: 100,
          count: 0,
        },
        {
          src: 'town-house.png',
          title: 'TownHouse',
          description: '毎秒 64,000 円を取得します。',
          price: 40000000,
          maxCount: 100,
          count: 0,
        },
        {
          src: 'mansion.png',
          title: 'Mansion',
          description: '毎秒 500,000 円を取得します。',
          price: 250000000,
          maxCount: 20,
          count: 0,
        },
        {
          src: 'industrial-space.png',
          title: 'Industrial Space',
          description: '毎秒 2,200,000 円を取得します。',
          price: 1000000000,
          maxCount: 10,
          count: 0,
        },
        {
          src: 'hotel.png',
          title: 'Hotel Skyscraper',
          description: '毎秒 25,000,000 円を取得します。',
          price: 10000000000,
          maxCount: 5,
          count: 0,
        },
        {
          src: 'shinkansen.png',
          title: 'Bullet-Speed Sky Railway',
          description: '毎秒 30,000,000,000 円を取得します。',
          price: 10000000000000,
          maxCount: 1,
          count: 0,
        },
      ],
      name: 'Name',
      profileForm: {
        name: '',
      },
      days: 0,
      money: 50000,
      burgers: 0,
      profitPerClick: 25,
      profitPerSecond: 25,
      profileDialog: false,
  }),
  computed: {
    passedYears(){
      return Math.floor(this.days / 365)
    },
    realYears(){
      return initialYears + this.passedYears 
    },
  },
  methods: {
    clickBurger(){
      this.money += this.profitPerClick
      this.burgers += 1
    },
    profileDialogOpen(){
      this.profileForm.name = this.name
      this.profileDialog = true
    },
    profileDataSave(){
      this.name = this.profileForm.name
      this.profileDialog = false
    }
  },
  mounted(){
    setInterval(function(){
      this.days++
    }.bind(this), 1000)
  },
  filters: {
    filterInfinite: function (value) {
      if (value === Infinity ) return '∞'
      return value
    },
    filterNumberByComma: function (value) {
      if (!value) return '0'
      return value.toLocaleString();
    },
  }
};
</script>
