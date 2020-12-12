<template>
  <v-app>
    <v-app-bar app flat color="rgba(0, 0, 0, 0)">
      <v-app-bar-nav-icon @click="drawer = !drawer" color="white"></v-app-bar-nav-icon>

      <v-toolbar-title class="title white--text pl-0">
        Menu
      </v-toolbar-title>

      <v-spacer></v-spacer>

      <v-btn color="white" icon>
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
    </v-app-bar>
    <v-navigation-drawer v-model="drawer" absolute temporary>
      <v-list nav dense>
        <v-list-item-group v-model="model" active-class="deep-purple--text text--accent-4">
          <v-list-item v-for="city in cities" :key="city">
            <v-list-item-title>{{ city }}</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
    <v-main>
      <v-carousel v-model="model" :show-arrows="false" height="100%">
        <v-carousel-item v-for="city in cities" :key="city">
          <v-sheet :color="getRandomColor()" height="100%" tile>
            <v-row class="fill-height" align="center" justify="center">
              <div class="d-flex flex-column">
                <h1 class="text-h3" align="center">{{ name }}</h1>
                <p class="text-h6" align="center">{{ title }}</p>
                <div align="center">
                  <img :src="icon" alt="Weather icon" />
                </div>
                <p class="text-h1" align="center">{{ temp }}</p>
              </div>
            </v-row>
          </v-sheet>
        </v-carousel-item>
      </v-carousel>
    </v-main>
  </v-app>
</template>

<script>
import gql from "graphql-tag";
export default {
  name: "App",

  components: {},

  mounted() {
    this.fetchCities();
  },

  data: () => ({
    model: 0,
    drawer: false,
    group: null,
    weather: {},
    cities: [],
  }),

  computed: {
    name() {
      return this?.weather?.name || "";
    },
    title() {
      return this?.weather?.weather?.summary?.title || "";
    },
    icon() {
      return this?.weather?.weather?.summary?.icon
        ? `https://openweathermap.org/img/wn/${this?.weather?.weather?.summary?.icon}@4x.png`
        : "data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNjZweCIgIGhlaWdodD0iNjZweCIgIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgdmlld0JveD0iMCAwIDEwMCAxMDAiIHByZXNlcnZlQXNwZWN0UmF0aW89InhNaWRZTWlkIiBjbGFzcz0ibGRzLWdvb2dsZSI+CiAgICA8ZyB0cmFuc2Zvcm09InRyYW5zbGF0ZSg1MCA1MCkiPgogICAgICA8ZyB0cmFuc2Zvcm09InJvdGF0ZSg5MCkiPgogICAgICAgIDxhbmltYXRlVHJhbnNmb3JtIGF0dHJpYnV0ZU5hbWU9InRyYW5zZm9ybSIgdHlwZT0icm90YXRlIiBjYWxjTW9kZT0iZGlzY3JldGUiIHZhbHVlcz0iMDs5MDsxODA7MjcwIiBrZXlUaW1lcz0iMDswLjI1OzAuNTswLjc1IiBkdXI9IjEuMXMiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIj48L2FuaW1hdGVUcmFuc2Zvcm0+CiAgICAgICAgPHBhdGggbmctYXR0ci1kPSJ7e2NvbmZpZy5kMX19IiBmaWxsPSIjZmZmZmNiIiBkPSJNLTQwIDBBNDAgNDAgMCAxIDAgNDAgMCI+CiAgICAgICAgICA8YW5pbWF0ZSBhdHRyaWJ1dGVOYW1lPSJmaWxsIiBjYWxjTW9kZT0iZGlzY3JldGUiIHZhbHVlcz0iI2ZmZmZjYjsjZmFjMDkwOyNmZjdjODE7I2MwZjZkMjsjZmZmZmNiIiBrZXlUaW1lcz0iMDswLjI0OzAuNDk7MC43NDswLjk5IiBkdXI9IjEuMXMiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIj48L2FuaW1hdGU+CiAgICAgICAgPC9wYXRoPgogICAgICAgIDxwYXRoIG5nLWF0dHItZD0ie3tjb25maWcuZDJ9fSIgZmlsbD0iI2ZhYzA5MCIgZD0iTS00MCAwQTQwIDQwIDAgMCAxIDQwIDAiPgogICAgICAgICAgPGFuaW1hdGUgYXR0cmlidXRlTmFtZT0iZmlsbCIgY2FsY01vZGU9ImRpc2NyZXRlIiB2YWx1ZXM9IiNmYWMwOTA7I2ZmN2M4MTsjYzBmNmQyOyNmZmZmY2IiIGtleVRpbWVzPSIwOzAuMjU7MC41OzAuNzUiIGR1cj0iMS4xcyIgcmVwZWF0Q291bnQ9ImluZGVmaW5pdGUiPjwvYW5pbWF0ZT4KICAgICAgICA8L3BhdGg+CiAgICAgICAgPHBhdGggbmctYXR0ci1kPSJ7e2NvbmZpZy5kNH19IiBzdHJva2U9InJnYigxNzksIDE3OSwgMTQyKSIgc3Ryb2tlLXdpZHRoPSIyIiBkPSJNLTM5IDBMMzkgMCI+CiAgICAgICAgICA8YW5pbWF0ZSBhdHRyaWJ1dGVOYW1lPSJzdHJva2UiIHZhbHVlcz0iI2ZmZmZjYjtyZ2IoMTc5LCAxNzksIDE0Mik7cmdiKDE3NSwgMTM0LCAxMDEpOyNmYWMwOTA7cmdiKDE3NSwgMTM0LCAxMDEpO3JnYigxNzksIDg3LCA5MCk7I2ZmN2M4MTtyZ2IoMTc5LCA4NywgOTApO3JnYigxMzQsIDE3MiwgMTQ3KTsjYzBmNmQyO3JnYigxMzQsIDE3MiwgMTQ3KTtyZ2IoMTc5LCAxNzksIDE0Mik7I2ZmZmZjYiIga2V5VGltZXM9IjA7MC4xMjQ7MC4xMjU7MC4yNTswLjM3NDswLjM3NTswLjU7MC42MjQ7MC42MjU7MC43NTswLjg3NDswLjg3NTsxIiBkdXI9IjEuMXMiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIj48L2FuaW1hdGU+CiAgICAgICAgPC9wYXRoPgogICAgICAgIDxnIHRyYW5zZm9ybT0ic2NhbGUoMSAwLjA5MDkwOTEpIj4KICAgICAgICAgIDxwYXRoIG5nLWF0dHItZD0ie3tjb25maWcuZDN9fSIgZmlsbD0icmdiKDE3OSwgMTc5LCAxNDIpIiBkPSJNLTQwIDBBNDAgNDAgMCAwIDEgNDAgMFoiPgogICAgICAgICAgICA8YW5pbWF0ZSBhdHRyaWJ1dGVOYW1lPSJmaWxsIiB2YWx1ZXM9IiNmZmZmY2I7cmdiKDE3OSwgMTc5LCAxNDIpO3JnYigxNzUsIDEzNCwgMTAxKTsjZmFjMDkwO3JnYigxNzUsIDEzNCwgMTAxKTtyZ2IoMTc5LCA4NywgOTApOyNmZjdjODE7cmdiKDE3OSwgODcsIDkwKTtyZ2IoMTM0LCAxNzIsIDE0Nyk7I2MwZjZkMjtyZ2IoMTM0LCAxNzIsIDE0Nyk7cmdiKDE3OSwgMTc5LCAxNDIpOyNmZmZmY2IiIGtleVRpbWVzPSIwOzAuMTI0OzAuMTI1OzAuMjU7MC4zNzQ7MC4zNzU7MC41OzAuNjI0OzAuNjI1OzAuNzU7MC44NzQ7MC44NzU7MSIgZHVyPSIxLjFzIiByZXBlYXRDb3VudD0iaW5kZWZpbml0ZSI+PC9hbmltYXRlPgogICAgICAgICAgPC9wYXRoPgogICAgICAgICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJzY2FsZSIgdmFsdWVzPSIxIDE7MSAwOzEgLTE7MSAxIiBrZXlUaW1lcz0iMDswLjU7MC45OTk7MSIgZHVyPSIwLjI3NXMiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIj48L2FuaW1hdGVUcmFuc2Zvcm0+CiAgICAgICAgPC9nPgogICAgICA8L2c+CiAgICA8L2c+CiAgPC9zdmc+";
    },
    temp() {
      return this?.weather?.weather?.temperature?.actual
        ? Math.round(this?.weather?.weather?.temperature?.actual)
        : "";
    },
  },

  methods: {
    fetchCities() {
      this.cities = ["Barrie", "Honolulu", "San Martino", "Nuuk", "Jakarta"];
    },
    getRandomRGB() {
      return Math.floor(Math.random() * 128);
    },
    getRandomColor() {
      return `rgb(${this.getRandomRGB()},${this.getRandomRGB()},${this.getRandomRGB()})`;
    },
  },

  apollo: {
    weather: {
      query: gql(`
      query getWeather($city: String!){
        weather: getCityByName(name: $city, config: { units: metric }) {
          name
          weather {
            summary {
              title
              icon
            }
            temperature {
              actual
            }
          }
        }
      }
    `),
      variables() {
        return {
          city: this.cities[this.model],
        };
      },
    },
  },
};
</script>

<style scoped>
main {
  margin-top: -57px;
}
</style>
