<template>
  <v-app style=
      "background-image: url('./bg_dark.png');
      height: 100%;
      background-position: center;
      background-repeat: no-repeat;
      background-size: cover;"
    >
    <v-app-bar
      :collapse=true
      dense
      app
      color="dark-grey"
      dark
    >
      <span>{{version_loaded}}</span>
      <v-menu
        bottom
        right
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
            icon
            color="light-green"
            v-bind="attrs"
            v-on="on"
          >
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>

        <v-list>
          <v-list-item @click="load_data('v0.11.7')">
            <v-list-item-title>v0.11.7</v-list-item-title>
          </v-list-item>

          <!-- <v-list-item @click="load_data('v.0.11.6')">
            <v-list-item-title>v.0.11.6</v-list-item-title>
          </v-list-item> -->

          <v-list-item @click="load_data('v0.11.5')">
            <v-list-item-title>v0.11.5</v-list-item-title>
          </v-list-item>

          <!-- <v-list-item @click="load_data('alpha')">
            <v-list-item-title>Alpha</v-list-item-title>
          </v-list-item> -->
        </v-list>
      </v-menu>

    </v-app-bar>

    <v-main>
      <v-container>
        <div class="d-flex justify-space-around">
          <v-img src="title_logo.png" max-height="150" max-width="250"></v-img>
          <!--<h1 class="text-h2 font-weight-bold">Aldarian Archive</h1>-->
        </div>
        <br />

        <v-card style="opacity: 0.90" >
          <v-progress-linear v-if="loading" indeterminate color="yellow darken-2"></v-progress-linear>
          <v-tabs v-model="selected_tab" background-color="blue-grey darken-4" center-active dark>
            <v-tab v-for="tab in tabs" :key="tab">
              {{ tab }}
            </v-tab>
          </v-tabs>

          <v-tabs-items theme--dark v-model="selected_tab">
            <v-tab-item v-for="tab in tabs" :key="tab">
              <div style="overflow-y: auto;">
                <!-- Artifacts -->
                <v-card v-if="tab == 'Artifacts'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="artifact_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                  <v-data-table dense :headers="artifact_headers" :items="artifacts" :search="artifact_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
                <!-- Bugs -->
                <v-card v-if="tab == 'Bugs'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="bug_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                  <v-data-table dense :headers="bug_headers" :items="bugs" :search="bug_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
                <!-- Cooking -->
                <v-card v-if="tab == 'Cooking'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="cooking_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                  <v-data-table dense class="text-pre-wrap" :headers="cooking_headers" :items="cooking" :search="cooking_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
                <!-- Fish -->
                <v-card v-if="tab == 'Fish'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="fish_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                <v-data-table dense :headers="fish_headers" :items="fish" :search="fish_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
                <!-- Forage -->
                <v-card v-if="tab == 'Forage'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="forage_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                <v-data-table dense :headers="forage_headers" :items="forage" :search="forage_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
                <!-- Harvest -->
                <v-card v-if="tab == 'Harvest'" dark style="opacity: 0.85; overflow-y: auto; max-height: 80vh">
                  <v-card-title>
                    <v-text-field v-model="crops_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
                  </v-card-title>
                  <v-data-table dense class="text-pre-wrap" :headers="crops_headers" :items="crops" :search="crops_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
                </v-card>
              </div>
            </v-tab-item>
          </v-tabs-items>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',

  data: () => ({
      lastLoadingPause: Date.now(),
      loading: true,
      version_loaded: "v0.11.7", // The version to load on startup
      selected_tab: null,
      tabs: [
        "Artifacts",
        //"Blacksmithing",
        "Bugs",
        "Cooking",
        //"Crafting",
        "Fish",
        "Forage",
        "Harvest",
        //"Locations",
        //"NPCs",
        //"Quests",
        // Schedules
      ],

      // fish_panels: [
      //   "Misc",
      //   "Ocean",
      //   "Pond",
      //   "River"
      // ],

      // Artifacts
      artifact_search: "",
      artifact_headers: [
        { text: "Name", value: "name" },
        { text: "Location", value: "location" },
        { text: "Sources", value: "sources" },
        { text: "Rarity", value: "rarity" },
        { text: "Sell Price", value: "value" },
        { text: "Renown", value: "renown" },
      ],
      artifacts: [],

      // Bugs
      bug_search: "",
      bug_headers: [
        { text: "Name", value: "name" },
        { text: "Type", value: "type" },
        { text: "Locations", value: "locations" },
        { text: "Seasons", value: "seasons" },
        { text: "Hours", value: "hours" },
        { text: "Weather", value: "weather" },
        { text: "Hiding Spots", value: "liked_object_categories" },
        { text: "Spawn", value: "spawn" },
        { text: "Attraction", value: "attraction" },
        { text: "Rarity", value: "rarity" },
        { text: "Sell Price", value: "value" },
      ],
      bugs: [],

      // Cooking
      cooking_search: "",
      cooking_headers: [
        { text: "Name", value: "name" },
        { text: "Stars", value: "stars" },
        { text: "Cooking Lv", value: "crafting_level_requirement" },
        { text: "Kitchen Lv", value: "kitchen_tier_requirement" },
        { text: "Obtained By", value: "obtained_by" },
        { text: "Obtained From", value: "obtained_from" },
        { text: "Price", value: "recipe_price" },
        { text: "Recipe", value: "recipe" },
        { text: "Time", value: "time" },
      ],
      cooking: [],

      // Crops
      crops_search: "",
      crops_headers: [
        { text: "Name", value: "name" },
        { text: "Season", value: "seasons" },
        { text: "Growth Time", value: "growth_time" },
        { text: "Regrowth Time", value: "growth_time" },
        { text: "Restoration", value: "restore" },
        { text: "Sell Price", value: "sell_price" },
        { text: "Purchase Price", value: "purchase_price" },
        { text: "Seed", value: "seed_name" },
        { text: "Seed Price", value: "seed_price" },
      ],
      crops: [],

      // Fish
      fish_search: "",
      fish_headers: [
        { text: "Name", value: "name" },
        { text: "Locations", value: "locations" },
        { text: "Seasons", value: "seasons" },
        { text: "Weather", value: "weather" },
        { text: "Diving", value: "diving" },
        { text: "Size", value: "size" },
        { text: "Rarity", value: "rarity" },
        { text: "Sell Price", value: "value" },
      ],
      fish: [],

      // Forage
      forage_search: "",
      forage_headers: [
        { text: "Name", value: "name" },
        { text: "Seasons", value: "seasons" },
        { text: "Rarity", value: "rarity" },
        { text: "Source (Bush/Tree)", value: "source" },
        { text: "Restoration", value: "restore" },
        { text: "Sell Price", value: "sell_price" },
        { text: "Purchase Price", value: "purchase_price" },
      ],
      forage: [],
    }),

    methods: {
      pauseLoading: function() {
        return new Promise(r => setTimeout(r));
      },

      loadingPauseCheck: async function() {
        if(Date.now() - 30 > this.lastLoadingPause) {
          this.lastLoadingPause = Date.now();
          await this.pauseLoading();
        }
      },

      clear_data: function() {
        this.artifacts = [];
        this.artifacts_dict = {};
        this.bugs = [];
        this.bugs_dict = {};
        this.cooking = [];
        this.cooking_dict = {};
        this.crops = [];
        this.crops_dict = {};
        this.fish = [];
        this.forage = [];
        this.forage_dict = {};
      },

      load_data: function(version) {
        this.clear_data();
        this.loading = true;
        this.version_loaded = version;
        console.log("Loaded Data: " + version);
        if(version === 'v0.11.7') {
          this.parse_localization(require('@/assets/data/v0.11.7/localization.json'));
          this.parse_fiddle(require('@/assets/data/v0.11.7/__fiddle__.json'));

        }
        if(version === 'v0.11.5') {
          this.parse_localization(require('@/assets/data/v0.11.5/localization.json'));
          this.parse_fiddle(require('@/assets/data/v0.11.5/__fiddle__.json'));
        }
      },

      /**
       * 
       * @param hours_array Array[int] of two numbers representing a time range (hours of the day) for something.
       */
      parse_hours_range: function(hours_array) {
        let start_time = "6:00AM";
        if(hours_array[0] > 6 && hours_array[0] < 12) {
          start_time = hours_array[0] + ":00AM"
        }
        else if(hours_array[0] == 12) {
          start_time = hours_array[0] + ":00PM"
        }
        else if(hours_array[0] > 12 && hours_array[0] < 24) {
          start_time = (hours_array[0] - 12) + ":00PM"
        }
        else if(hours_array[0] == 24) {
          start_time = hours_array[0] + ":00AM"
        }
        else if(hours_array[0] > 24) {
          start_time = (hours_array[0] - 24) + ":00AM"
        }

        let end_time = "6:00AM";
        if(hours_array[1] > 6 && hours_array[1] < 12) {
          end_time = hours_array[1] + ":00AM"
        }
        else if(hours_array[1] == 12) {
          end_time = hours_array[1] + ":00PM"
        }
        else if(hours_array[1] > 12 && hours_array[1] < 24) {
          end_time = (hours_array[1] - 12) + ":00PM"
        }
        else if(hours_array[1] == 24) {
          end_time = hours_array[1] + ":00AM"
        }
        else if(hours_array[1] > 24) {
          end_time = (hours_array[1] - 24) + ":00AM"
        }
        
        return start_time + " to " + end_time;
      },

      parse_localization: function(json) {
        this.localizations_eng = json["eng"];
      },

      find_localization_string: function(pattern) {
        for(let l in this.localizations_eng) {
          if(l.match(pattern)) {
            return l;
          }
        }
        return false;
      },

      parse_fiddle: async function(json) {
        // Artifact Data
        var data_artifacts = json["artifacts"];

        // Bugs Data
        var data_bugs = json["bugs"];

        // Dungeons Data
        var data_dungeons = json["dungeons"]

        // Fish Data
        var data_fish = json["fish"];
        var data_fishing = json["fishing"];

        // Items Data
        var items_other_artifacts = json["items"]["other"]["artifacts"];
        var items_other_bugs = json["items"]["other"]["bugs"];
        var items_other_crops_and_forage = json["items"]["other"]["crops_and_forage"];
        var items_fish = json["items"]["fish"];
        var items_mines = json["items"]["mines"];
        var items_other_cooked_dishes = json["items"]["other"]["cooked_dishes"];

        // Letters Data
        var data_letters = json["letters"];

        // Misc Data
        var data_misc = json["misc"];

        // Museum Archaeology Data
        var data_museum_wings = json["museum_wings"];
        
        // Object Prototypes Data
        var object_prototypes = json["object_prototypes"];

        // Quests Data
        var data_quests = json["quests"];

        // Stores Data
        var data_stores = json["stores"];

        // Wishing Well Data
        var data_wishing_well = json["wishing_well"];

        // Chicken Statue Data
        var data_chicken_statue = json["chicken_statue"];

        // Restoration Data
        var data_restoration = json["restoration"];

        // Forageables Data
        var data_forageables = json["forageables"];

        // PARSE FISH
        this.fish = [];

        // Extract all fish from the items/mines dictionary.
        for(let mines_location in items_mines) {
          await this.loadingPauseCheck();
          for(let m in items_mines[mines_location]) {
            if(items_mines[mines_location][m]["tags"] !== undefined) {
              if(items_mines[mines_location][m]["tags"].includes("fishable")) { 
                if(items_mines[mines_location][m]["value"] !== undefined) {
                  this.fish.push({
                    "key": m, // Game internal name, don't change and use for lookup
                    "name": this.localizations_eng[items_mines[mines_location][m]["name"]],
                    "value": items_mines[mines_location][m]["value"]["bin"],
                    "locations": mines_location
                  });
                }
                else {
                  this.fish.push({
                    "key": m, // Game internal name, don't change and use for lookup
                    "name": this.localizations_eng[items_mines[mines_location][m]["name"]],
                    "value": "None",
                    "locations": mines_location
                  });  
                }
              }

            }
          }
        }

        // Extract all fish from the items/fish dictionary.
        for(let fish_location in items_fish) {
          await this.loadingPauseCheck();
          for(let f in items_fish[fish_location]) {
            if(items_fish[fish_location][f]["tags"] !== undefined) {
              if(items_fish[fish_location][f]["tags"].includes("fishable")) {
                if(items_fish[fish_location][f]["value"] !== undefined) {
                  this.fish.push({
                    "key": f, // Game internal name, don't change and use for lookup
                    "name": this.localizations_eng[items_fish[fish_location][f]["name"]],
                    "value": items_fish[fish_location][f]["value"]["bin"]
                  });
                }
                else {
                  this.fish.push({
                    "key": f, // Game internal name, don't change and use for lookup
                    "name": this.localizations_eng[items_fish[fish_location][f]["name"]],
                    "value": "None"
                  });  
                }
              }
            }
          }
        }

        // Extract additional fish data from the fish dictionary.
        for(let i = 0; i < this.fish.length; i++) {
          await this.loadingPauseCheck();
          if(this.fish[i]["key"] in data_fish) {
            if(data_fish[this.fish[i]["key"]]["seasons"] !== undefined) {
              this.fish[i]["seasons"] = data_fish[this.fish[i]["key"]]["seasons"].join(", ");
            }
            else {
              this.fish[i]["seasons"] = "spring, summer, fall, winter";
            }

            if(data_fish[this.fish[i]["key"]]["water_type"] !== undefined) {
              if(Array.isArray(data_fish[this.fish[i]["key"]]["water_type"])) {
                this.fish[i]["locations"] = data_fish[this.fish[i]["key"]]["water_type"].join(", ");
              }
              else {
                this.fish[i]["locations"] = data_fish[this.fish[i]["key"]]["water_type"];
              }
            }

            if(data_fish[this.fish[i]["key"]]["size"] !== undefined) {
              this.fish[i]["size"] = data_fish[this.fish[i]["key"]]["size"];
            }

            if(data_fish[this.fish[i]["key"]]["rarity"] !== undefined) {
              this.fish[i]["rarity"] = data_fish[this.fish[i]["key"]]["rarity"];
            }
            else {
              this.fish[i]["rarity"] = "common";
            }

            if(data_fish[this.fish[i]["key"]]["weather"] !== undefined) {
              if(Array.isArray(data_fish[this.fish[i]["key"]]["weather"])) {
                this.fish[i]["weather"] = data_fish[this.fish[i]["key"]]["weather"].join(", ");
              }
              else {
                this.fish[i]["weather"] = data_fish[this.fish[i]["key"]]["weather"];
              }
            }
            else {
              this.fish[i]["weather"] = "calm, special, inclement, heavy_inclement";
            }

            if(data_fish[this.fish[i]["key"]]["retrieval"] !== undefined) {
              if(data_fish[this.fish[i]["key"]]["retrieval"].includes("divespot")) {
                this.fish[i]["diving"] = "yes";
              }
              else {
                this.fish[i]["diving"] = "no";
              }
            }
            else {
              this.fish[i]["diving"] = "no";
            }

          }
          // It's a from a dive spot or other forage.
          else {
            console.log("This should not execute!");
          }
        }

        // PARSE ARTIFACTS
        this.artifacts = [];
        this.artifacts_dict = {}

        // Create artifact location lookup.
        var artifact_location_lookup_dict = {
          // aldrian is in the /artifacts/location dictionary
          // caldosian is in the /artifacts/location dictionary
          // alda is in the /artifacts/location dictionary
          // ancient is in the /artifacts/location dictionary
          // prehistoric is in the /artifacts/location dictionary
          "oopart": "dig_spot (Well Placed Perk)",
          "aquatic": "fishing (Aquatic Antiquities Perk)",
          "sunken": "diving (Sunken Secrets Perk)",
          //"dungeon": "mines (Any Biome)", // Confirm this is correct
          "upper_mines_artifacts": "upper_mines",
          "tide_caverns": "tide_caverns",
          "deep_earth": "deep_earth"
        };
        for(let location in data_artifacts["locations"]) {
          artifact_location_lookup_dict[data_artifacts["locations"][location]] = location;
        }
        artifact_location_lookup_dict["mine"] = "mines (Any Biome)";
        console.log(artifact_location_lookup_dict); // TODO: Figure out how to look up perk requirements

        // Extract all artifacts from the items/mines dictionary.
        for(let mines_location in items_mines) {
          await this.loadingPauseCheck();
          for(let m in items_mines[mines_location]) {
            if(items_mines[mines_location][m]["tags"] !== undefined) {
              if(items_mines[mines_location][m]["tags"].includes("archaeology")) {
                this.artifacts_dict[m] = {
                  "key": m,
                  "name": this.localizations_eng[items_mines[mines_location][m]["name"]],
                  "location": mines_location,
                  "value": items_mines[mines_location][m]["value"]["bin"],
                  "renown": items_mines[mines_location][m]["value"]["renown"]
                }
              }
            }
          }
        }

        // Extract all artifacts from the items/other/artifacts dictionary.
        for(let artifact in items_other_artifacts) {
          await this.loadingPauseCheck();
          if(items_other_artifacts[artifact]["tags"] !== undefined) {
            if(items_other_artifacts[artifact]["tags"].includes("archaeology")) {
              this.artifacts_dict[artifact] = {
                "key": artifact,
                "name": this.localizations_eng[items_other_artifacts[artifact]["name"]],
                "value": items_other_artifacts[artifact]["value"]["bin"],
                "renown": items_other_artifacts[artifact]["value"]["renown"]
              }
            }
          }
        }

        // Extract additional data from the object_prototypes/rock dictionary.
        for(let node in object_prototypes["rock"]) {
          await this.loadingPauseCheck();
          if(object_prototypes["rock"][node]["drops"] !== undefined) {
            for(let drop in object_prototypes["rock"][node]["drops"]) {
              if(this.artifacts_dict[object_prototypes["rock"][node]["drops"][drop]["item"]] !== undefined) {
                if(this.artifacts_dict[object_prototypes["rock"][node]["drops"][drop]["item"]]["sources"] !== undefined) {
                  this.artifacts_dict[object_prototypes["rock"][node]["drops"][drop]["item"]]["sources"].push(node);
                }
                else {
                  this.artifacts_dict[object_prototypes["rock"][node]["drops"][drop]["item"]]["sources"] = [node];
                }
              }
            }
          }
        }

        // Extract additional data from the artifacts/loot dictionary.
        for(let artifact in data_artifacts["loot"]) {
          await this.loadingPauseCheck();
          if(this.artifacts_dict[artifact] !== undefined) {
            this.artifacts_dict[artifact]["rarity"] = data_artifacts["loot"][artifact];
          }
        }

        // Extract additional data from the museum_wings/archaeology dictionary.
        for(let set in data_museum_wings["archaeology"]["sets"]) {
          await this.loadingPauseCheck();
          for(let item in data_museum_wings["archaeology"]["sets"][set]["items"]) {
            let item_name = data_museum_wings["archaeology"]["sets"][set]["items"][item];
            if(this.artifacts_dict[item_name] !== undefined) {
              this.artifacts_dict[item_name]["location"] = artifact_location_lookup_dict[set];
              if(this.artifacts_dict[item_name]["sources"] !== undefined) {
                this.artifacts_dict[item_name]["sources"].push("dig_spot");
              }
              else {
                this.artifacts_dict[item_name]["sources"] = ["dig_spot"];
              }
            }
          }
        }

        // Extract additional data from the dungeons/dungeond/biomes dictionary.
        var object_to_biome_dict = {};
        for(let biome in data_dungeons["dungeons"]["biomes"]) {
          await this.loadingPauseCheck();
          let biome_name = data_dungeons["dungeons"]["biomes"][biome]["artifact_set"];
          if(biome_name === "upper_mines_artifacts") {
            biome_name = "upper_mines";
          }

          for(let i in data_dungeons["dungeons"]["biomes"][biome]["votes"]["ore_rock"]) {
            let ore = data_dungeons["dungeons"]["biomes"][biome]["votes"]["ore_rock"][i]["object"];
            object_to_biome_dict[ore] = biome_name;
          }          
        }

        // Note: Anything mapping to "mine" isn't obtaineable yet. Make that clear!
        for(let o in object_to_biome_dict) {
          await this.loadingPauseCheck();
          if(object_to_biome_dict[o] === "mine") {
            object_to_biome_dict[o] = "unobtainable";
          }
        }

        // Set location based on sources (for nodes and rocks).
        for(let a in this.artifacts_dict) {
          await this.loadingPauseCheck();
          for(let s in this.artifacts_dict[a]["sources"]) {
            let source = this.artifacts_dict[a]["sources"][s];
            if(object_to_biome_dict[source] !== undefined) {
              this.artifacts_dict[a]["location"] = object_to_biome_dict[source];
            }
          }
        }

        // Anything still without a source is unobtainable.
        for(let a in this.artifacts_dict) {
          await this.loadingPauseCheck();
          if(this.artifacts_dict[a]["sources"] === undefined) {
            this.artifacts_dict[a]["sources"] = ['none'];
          }
        }

        // Anything still without a location is unobtainable.
        for(let a in this.artifacts_dict) {
          await this.loadingPauseCheck();
          if(this.artifacts_dict[a]["location"] === undefined) {
            this.artifacts_dict[a]["location"] = "unobtainable";
          }
        }

        // Convert the artifacts dict to an array for the table.
        for(let a in this.artifacts_dict) {
          await this.loadingPauseCheck();
          this.artifacts_dict[a]["sources"] = this.artifacts_dict[a]["sources"].join(", ");
          this.artifacts.push(this.artifacts_dict[a]);
        }


        // PARSE BUGS
        this.bugs = [];
        this.bugs_dict = {};

        // Extract data from bugs dictionary
        for(let b in data_bugs) {
          await this.loadingPauseCheck();
          if(b !== "default") {
            this.bugs_dict[b] = {
              "key": b, // string
              "type": data_bugs[b]["type"], // string
              "seasons": data_bugs[b]["seasons"], // array[string]
              "hours": data_bugs[b]["hours"], // array[int]
              "weather": data_bugs[b]["weather"], // array[string]
              "liked_object_categories": data_bugs[b]["liked_object_categories"], // array[string]
              "rarity": data_bugs[b]["rarity"], // string
              "spawn": data_bugs[b]["spawn"], // array[string]
              "locations": data_bugs[b]["locations"], // array[string]
              "attraction": data_bugs[b]["attraction"], // string
              "has_light": data_bugs[b]["has_light"], // boolean
              "dungeon_biome": data_bugs[b]["dungeon_biome"], // string
            };
          }
        }

        // Extract data from items/other/bugs dictionary.
        for(let b in items_other_bugs) {
          await this.loadingPauseCheck();
          if(this.bugs_dict[b] === undefined) {
            this.bugs_dict[b] = {
              "name": this.localizations_eng[items_other_bugs[b]["name"]],
              "tags": items_other_bugs[b]["tags"],
              "value": items_other_bugs[b]["value"]["bin"]
            }
            // Cross-reference fish dictionary for more data
            if(data_fish[b] !== undefined) {
              this.bugs_dict[b]["seasons"] = data_fish[b]["seasons"];
              this.bugs_dict[b]["rarity"] = data_fish[b]["rarity"];
              this.bugs_dict[b]["liked_object_categories"] = data_fish[b]["retrieval"];
            }
          }
          else {
            this.bugs_dict[b]["name"] = this.localizations_eng[items_other_bugs[b]["name"]];
            this.bugs_dict[b]["tags"] = items_other_bugs[b]["tags"];
            this.bugs_dict[b]["value"] = items_other_bugs[b]["value"]["bin"];
          }
        }

        // Extract data from items/mines
        for(let l in items_mines) {
          await this.loadingPauseCheck();
          for(let x in items_mines[l]) {
            if(this.bugs_dict[x] !== undefined) {
              this.bugs_dict[x]["name"] = this.localizations_eng[items_mines[l][x]["name"]];
              this.bugs_dict[x]["value"] = items_mines[l][x]["value"]["bin"];
            }
          }
        }

        // Convert the bugs dict to an array for the table.
        for(let b in this.bugs_dict) {
          await this.loadingPauseCheck();
          if(this.bugs_dict[b]["locations"] === undefined) {
            this.bugs_dict[b]["locations"] = "overworld";
          }
          if(this.bugs_dict[b]["seasons"] !== undefined && Array.isArray(this.bugs_dict[b]["seasons"])) {
            this.bugs_dict[b]["seasons"] = this.bugs_dict[b]["seasons"].join(", ");
          }
          else {
            this.bugs_dict[b]["seasons"] = "spring, summer, fall, winter";
          }
          if(this.bugs_dict[b]["hours"] !== undefined && Array.isArray(this.bugs_dict[b]["hours"])) {
            this.bugs_dict[b]["hours"] = this.parse_hours_range(this.bugs_dict[b]["hours"]);
          }
          else {
            this.bugs_dict[b]["hours"] = "6:00AM to 2:00AM";
          }
          if(this.bugs_dict[b]["weather"] !== undefined && Array.isArray(this.bugs_dict[b]["weather"])) {
            this.bugs_dict[b]["weather"] = this.bugs_dict[b]["weather"].join(", ");
          }
          else {
            this.bugs_dict[b]["weather"] = "calm, special, inclement, heavy_inclement";
          }
          if(this.bugs_dict[b]["liked_object_categories"] !== undefined && Array.isArray(this.bugs_dict[b]["liked_object_categories"])) {
            this.bugs_dict[b]["liked_object_categories"] = this.bugs_dict[b]["liked_object_categories"].join(", ");
          }
          if(this.bugs_dict[b]["spawn"] !== undefined && Array.isArray(this.bugs_dict[b]["spawn"])) {
            this.bugs_dict[b]["spawn"] = this.bugs_dict[b]["spawn"].join(", ");
          }
          if(this.bugs_dict[b]["locations"] !== undefined && Array.isArray(this.bugs_dict[b]["locations"])) {
            this.bugs_dict[b]["locations"] = this.bugs_dict[b]["locations"].join(", ");
          }
          if(this.bugs_dict[b]["dungeon_biome"] !== undefined) {
            if(this.bugs_dict[b]["dungeon_biome"] == "upper") {
              this.bugs_dict[b]["locations"] = "upper_mines";
            }
            else {
              this.bugs_dict[b]["locations"] = this.bugs_dict[b]["dungeon_biome"];
            }
            
          }
          this.bugs.push(this.bugs_dict[b]);
        }

        // COOKED DISHES
        this.cooking = [];
        this.cooking_dict = {};

        // Extract data from the items/other/cooked_dishes dict.
        for(let c in items_other_cooked_dishes) {
          await this.loadingPauseCheck();
          this.cooking_dict[c] = {
            "key": c,
            "name": this.localizations_eng[items_other_cooked_dishes[c]["name"]],
            "stars": items_other_cooked_dishes[c]["stars"],
            "crafting_level_requirement": items_other_cooked_dishes[c]["crafting_level_requirement"],
            "kitchen_tier_requirement": items_other_cooked_dishes[c]["kitchen_tier_requirement"],
            "recipe": items_other_cooked_dishes[c]["recipe"]
            // NOTE: The value data here is the price of the actual item, NOT the recipe.
          }

          if(items_other_cooked_dishes[c]["recipe_is_default"] !== undefined && items_other_cooked_dishes[c]["recipe_is_default"] === true) { // You start with this recipe.
            this.cooking_dict[c]["obtained_by"] = "default";
            this.cooking_dict[c]["obtained_from"] = "game start";
          }
          else {
            // Extract recipe prices from misc/cooking_recipe_star_prices
            if(this.cooking_dict[c]["stars"] !== undefined) { // If the cooking dish has no stars it can't be purchased.
              this.cooking_dict[c]["recipe_price"] = data_misc["cooking_recipe_star_prices"][this.cooking_dict[c]["stars"]-1];
            }
          }
        }

        // Extract recipe purchase location from stores dict.
        for(let s in data_stores) {
          await this.loadingPauseCheck();
          let store_name = this.localizations_eng[data_stores[s]["name"]];
          for(let c in data_stores[s]["categories"]) {
            // Constant Stock (so far only Terithia's shop sells constant recipes)
            if(data_stores[s]["categories"][c]["constant_stock"] !== undefined) {
              for(let i in data_stores[s]["categories"][c]["constant_stock"]) {
                let stock_item = data_stores[s]["categories"][c]["constant_stock"][i];
                if(typeof stock_item === 'object' && stock_item["recipe_scroll"] !== undefined) {
                  let recipe_name = data_stores[s]["categories"][c]["constant_stock"][i]["recipe_scroll"]
                  if(this.cooking_dict[recipe_name] !== undefined) {
                    this.cooking_dict[recipe_name]["obtained_by"] = "purchase";
                    this.cooking_dict[recipe_name]["obtained_from"] = store_name;
                  }
                }
              }
            }
            // Random Stock (inn and darcey)
            if(data_stores[s]["categories"][c]["random_stock"] !== undefined) {
              for(let i in data_stores[s]["categories"][c]["random_stock"]) {
                let stock_item = data_stores[s]["categories"][c]["random_stock"][i];
                if(typeof stock_item === 'object' && stock_item["item"] !== undefined && stock_item["include_recipe"] === true) { // darcy's shop
                  let recipe_name = data_stores[s]["categories"][c]["random_stock"][i]["item"];
                  if(this.cooking_dict[recipe_name] !== undefined) {
                    this.cooking_dict[recipe_name]["obtained_by"] = "purchase";
                    this.cooking_dict[recipe_name]["obtained_from"] = store_name;
                  }
                }
                else if(typeof stock_item === 'object' && stock_item["recipe_scroll"] !== undefined) { // inn
                  let recipe_name = data_stores[s]["categories"][c]["random_stock"][i]["recipe_scroll"];
                  if(stock_item["building_fixed"] !== undefined) {
                    this.cooking_dict[recipe_name]["obtained_by"] = "purchase";
                    this.cooking_dict[recipe_name]["obtained_from"] = store_name;
                  }
                  else {
                    this.cooking_dict[recipe_name]["obtained_by"] = "purchase";
                    this.cooking_dict[recipe_name]["obtained_from"] = store_name + " (upgraded)";
                  }
                }
              }
            }
          }
        }

        // Extract recipe locations from fishing/chest_tables dict.
        for(let c in data_fishing["chest_tables"]) {
          await this.loadingPauseCheck();
          for(let i in data_fishing["chest_tables"][c]["items"]) {
            if(data_fishing["chest_tables"][c]["items"][i]["kind"] === "recipe") {
              let recipe_name = data_fishing["chest_tables"][c]["items"][i]["value"];
              if(this.cooking_dict[recipe_name] !== undefined) {
                this.cooking_dict[recipe_name]["obtained_by"] = "fishing";
                this.cooking_dict[recipe_name]["obtained_from"] = c + " treasure box";
                delete this.cooking_dict[recipe_name]["recipe_price"];
              }
            }
          }
        }
        
        // Extract recipe locations from wishing_well dict.
        for(let rarity in data_wishing_well) {
          await this.loadingPauseCheck();
          for(let i in data_wishing_well[rarity]["small_roll"]) {
            if(data_wishing_well[rarity]["small_roll"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_wishing_well[rarity]["small_roll"][i]["recipe_scroll"];
              this.cooking_dict[recipe_name]["obtained_by"] = "gacha (random)";
              this.cooking_dict[recipe_name]["obtained_from"] = "wishing_well";
              delete this.cooking_dict[recipe_name]["recipe_price"];
            }
          }
          for(let i in data_wishing_well[rarity]["large_roll"]) {
            if(data_wishing_well[rarity]["large_roll"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_wishing_well[rarity]["large_roll"][i]["recipe_scroll"];
              this.cooking_dict[recipe_name]["obtained_by"] = "gacha (random)";
              this.cooking_dict[recipe_name]["obtained_from"] = "wishing_well";
              delete this.cooking_dict[recipe_name]["recipe_price"];
            }
          }
        }

        // Extract recipe locations from chicken_statue dict.
        // TODO: Refactor this and wishing_well common code to a method
        for(let rarity in data_chicken_statue) {
          await this.loadingPauseCheck();
          for(let i in data_chicken_statue[rarity]["small_roll"]) {
            if(data_chicken_statue[rarity]["small_roll"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_chicken_statue[rarity]["small_roll"][i]["recipe_scroll"];
              this.cooking_dict[recipe_name]["obtained_by"] = "gacha (random)";
              this.cooking_dict[recipe_name]["obtained_from"] = "chicken_statue";
              delete this.cooking_dict[recipe_name]["recipe_price"];
            }
          }
          for(let i in data_chicken_statue[rarity]["large_roll"]) {
            if(data_chicken_statue[rarity]["large_roll"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_chicken_statue[rarity]["large_roll"][i]["recipe_scroll"];
              this.cooking_dict[recipe_name]["obtained_by"] = "gacha (random)";
              this.cooking_dict[recipe_name]["obtained_from"] = "chicken_statue";
              delete this.cooking_dict[recipe_name]["recipe_price"];
            }
          }
        }

        // Extract recipe locations from letters. 
        for(let l in data_letters) {
          await this.loadingPauseCheck();
          if(data_letters[l]["items"] !== undefined) {
            for(let i in data_letters[l]["items"]) {
              if(data_letters[l]["items"][i]["recipe_scroll"] !== undefined) {
                let recipe_name = data_letters[l]["items"][i]["recipe_scroll"];
                if(data_letters[l]["ari_has_sold"] !== undefined) {
                  let localization_string = "items/other/crops_and_forage/" + data_letters[l]["ari_has_sold"] + "/name";
                  let item_name = this.localizations_eng[localization_string];
                  this.cooking_dict[recipe_name]["obtained_by"] = "shipping (" + item_name + ")";
                  this.cooking_dict[recipe_name]["obtained_from"] = "letter/mail";
                  delete this.cooking_dict[recipe_name]["recipe_price"];
                }
                if(data_letters[l]["ari_has_donated"] !== undefined) {
                  let localization_string_regex = "^[a-z_/]+(" + data_letters[l]["ari_has_donated"] + ")[/](name)$";
                  let item_name = this.find_localization_string(localization_string_regex)
                  if(item_name !== false) {
                    item_name = this.localizations_eng[item_name];
                    this.cooking_dict[recipe_name]["obtained_by"] = "museum_donation (" + item_name + ")";
                    this.cooking_dict[recipe_name]["obtained_from"] = "letter/mail";
                    delete this.cooking_dict[recipe_name]["recipe_price"];
                  }
                }
              }
            }

          }
        }

        // Extract recipe locations from quests/story_quests dictionary.
        for(let q in data_quests["story_quests"]) {
          await this.loadingPauseCheck();
          for(let i in data_quests["story_quests"][q]["rewards"]) {
            if(data_quests["story_quests"][q]["rewards"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_quests["story_quests"][q]["rewards"][i]["recipe_scroll"];
              let quest_name = this.localizations_eng[data_quests["story_quests"][q]["name"]];
              if(this.cooking_dict[recipe_name] !== undefined) {
                this.cooking_dict[recipe_name]["obtained_by"] = "quest";
                this.cooking_dict[recipe_name]["obtained_from"] = quest_name;
              }
            }
          }
        }

        // Extract recipe locations from quests/fetch_quests dictionary.
        for(let q in data_quests["fetch_quests"]) {
          await this.loadingPauseCheck();
          for(let i in data_quests["fetch_quests"][q]["rewards"]) {
            if(data_quests["fetch_quests"][q]["rewards"][i]["recipe_scroll"] !== undefined) {
              let recipe_name = data_quests["fetch_quests"][q]["rewards"][i]["recipe_scroll"];
              let quest_name = this.localizations_eng[data_quests["fetch_quests"][q]["name"]];
              if(this.cooking_dict[recipe_name] !== undefined) {
                this.cooking_dict[recipe_name]["obtained_by"] = "quest";
                this.cooking_dict[recipe_name]["obtained_from"] = quest_name;
              }
            }
          }
        }

        // Extract recipe locations from mines treasure chests
        for(let b in data_dungeons["dungeons"]["biomes"]) {
          await this.loadingPauseCheck();
          let biome_name = data_dungeons["dungeons"]["biomes"][b]["artifact_set"];
          if(biome_name === "upper_mines_artifacts") {
            biome_name = "upper_mines";
          }
          for(let i in data_dungeons["dungeons"]["biomes"][b]["taste_maker"]) {
            let recipe_name = data_dungeons["dungeons"]["biomes"][b]["taste_maker"][i];
            if(this.cooking_dict[recipe_name] !== undefined) {
              this.cooking_dict[recipe_name]["obtained_by"] = "mines (treasure chest)";
              this.cooking_dict[recipe_name]["obtained_from"] = biome_name;
            }
          }
        }

        // Cleanup and build the list.
        for(let x in this.cooking_dict) {
          await this.loadingPauseCheck();
          // Remove the price for any recipe not marked purchaseable.
          if(this.cooking_dict[x]["obtained_by"] === undefined || this.cooking_dict[x]["obtained_by"] !== "purchase") {
            delete this.cooking_dict[x]["recipe_price"];
          }

          // Remove anything without a recipe.
          if(this.cooking_dict[x]["recipe"] === undefined) {
            console.log("Missing recipe for: " + x);
            delete this.cooking_dict[x];
          }
          else {
            // Mark unobtainable recipes.
            if(this.cooking_dict[x]["obtained_by"] === undefined) {
              this.cooking_dict[x]["obtained_by"] = "unobtainable";
              this.cooking_dict[x]["obtained_from"] = "unobtainable";
            }
            // Format the recipe string.
            let recipe = "";
            for(let i in this.cooking_dict[x]["recipe"]) {
              if(this.cooking_dict[x]["recipe"][i]["item"] !== undefined) {
                let localization_string_regex = "^(items)[/](?!furniture)[a-z_/]+(\\b" + this.cooking_dict[x]["recipe"][i]["item"] + ")[/](name)$";
                let item_name = this.find_localization_string(localization_string_regex)
                if(item_name !== false) {
                  item_name = this.localizations_eng[item_name];
                }
                else {
                  item_name = this.cooking_dict[x]["recipe"][i]["item"];
                }
                recipe += item_name + ": " + this.cooking_dict[x]["recipe"][i]["count"] + "\n";
              }
              // Extract the time.
              if(this.cooking_dict[x]["recipe"][i]["hours"] !== undefined || this.cooking_dict[x]["recipe"][i]["minutes"] !== undefined) {
                this.cooking_dict[x]["time"] = "Hours: " + this.cooking_dict[x]["recipe"][i]["hours"] + "\nMinutes: " + this.cooking_dict[x]["recipe"][i]["minutes"];
              }
            }
            this.cooking_dict[x]["recipe"] = recipe;
            this.cooking.push(this.cooking_dict[x]);
          }
        }

        // PARSE HARVEST (GROWABLES)
        this.crops = [];
        this.crops_dict = {};
        items_other_crops_and_forage;

        // Extract data from object_prototypes/crop dictionary.
        for(let c in object_prototypes["crop"]) {
          await this.loadingPauseCheck();
          if(object_prototypes["crop"][c]["day_to_stage"] !== undefined && c !== "default") {
            this.crops_dict[c] = {
              "key": c,
              "growth_time": object_prototypes["crop"][c]["day_to_stage"].length - 1
            }
            if(object_prototypes["crop"][c]["seasons"] !== undefined) {
              this.crops_dict[c]["seasons"] = object_prototypes["crop"][c]["seasons"];
            }
            if(object_prototypes["crop"][c]["regrow_days"] !== undefined) {
              this.crops_dict[c]["regrow_days"] = object_prototypes["crop"][c]["regrow_days"];
            }
            if(object_prototypes["crop"][c]["currency"] !== undefined) {
              this.crops_dict[c]["currency"] = object_prototypes["crop"][c]["currency"];
            }
          }
        }

        // Extract data from items/other/crops_and_forage dictionary.
        for(let c in items_other_crops_and_forage) {
          await this.loadingPauseCheck();
          if(this.crops_dict[c] !== undefined) {
            let name = items_other_crops_and_forage[c]["name"];
            this.crops_dict[c]["name"] = this.localizations_eng[name];
            this.crops_dict[c]["restore"] = items_other_crops_and_forage[c]["restore"];
            if(items_other_crops_and_forage[c]["value"] !== undefined && items_other_crops_and_forage[c]["value"]["bin"] !== undefined) {
              this.crops_dict[c]["sell_price"] = items_other_crops_and_forage[c]["value"]["bin"];
            }
            if(items_other_crops_and_forage[c]["value"] !== undefined && items_other_crops_and_forage[c]["value"]["store"] !== undefined) {
              this.crops_dict[c]["purchase_price"] = items_other_crops_and_forage[c]["value"]["store"];
            }          
          }
          else if(c === "rice_stalk") {
            let name = items_other_crops_and_forage[c]["name"];
            this.crops_dict["rice"]["name"] = this.localizations_eng[name];
            this.crops_dict["rice"]["restore"] = items_other_crops_and_forage[c]["restore"];
            if(items_other_crops_and_forage[c]["value"] !== undefined && items_other_crops_and_forage[c]["value"]["bin"] !== undefined) {
              this.crops_dict["rice"]["sell_price"] = items_other_crops_and_forage[c]["value"]["bin"];
            }
            if(items_other_crops_and_forage[c]["value"] !== undefined && items_other_crops_and_forage[c]["value"]["store"] !== undefined) {
              this.crops_dict["rice"]["purchase_price"] = items_other_crops_and_forage[c]["value"]["store"];
            }    
          }
        }

        // Extract data from items/other/crops_and_forage dictionary (again).
        for(let c in items_other_crops_and_forage) {
          await this.loadingPauseCheck();
          if(items_other_crops_and_forage[c]["tags"] !== undefined) {
            if(items_other_crops_and_forage[c]["tags"].includes("seed")) {
              let crop_name = items_other_crops_and_forage[c]["crop_object"];
              if(this.crops_dict[crop_name] !== undefined) {
                this.crops_dict[crop_name]["seed"] = c;
                this.crops_dict[crop_name]["seed_name"] = this.localizations_eng[items_other_crops_and_forage[c]["name"]];
                if(items_other_crops_and_forage[c]["value"] !== undefined && items_other_crops_and_forage[c]["value"]["store"] !== undefined) {
                  this.crops_dict[crop_name]["seed_price"] = items_other_crops_and_forage[c]["value"]["store"];
                }
              }
            }
          }
        }

        // Cleanup.
        for(let c in this.crops_dict) {
          await this.loadingPauseCheck();
          // Remove regrow duration from plantable forage
          if(this.crops_dict[c]["restore"] !== undefined && typeof this.crops_dict[c]["restore"] === "string") {
            if(this.crops_dict[c]["restore"].includes("forage")) {
              if(this.crops_dict[c]["regrow_days"] !== undefined) {
                delete this.crops_dict[c]["regrow_days"];
              }
            }
          }
          if(this.crops_dict[c]["seed_price"] === undefined) {
            this.crops_dict[c]["seed_price"] = "unpurchasable";
          }
          // Inedible
          if(this.crops_dict[c]["restore"] === undefined) {
            this.crops_dict[c]["restore"] = "inedible";
          }
          // Restoration lookup/conversion.
          else if(this.crops_dict[c]["restore"] !== undefined) {
            let restoration_name = this.crops_dict[c]["restore"];
            if(data_restoration["vars"][restoration_name] !== undefined) {
              this.crops_dict[c]["restore"] = data_restoration["vars"][restoration_name] + " HP/STA";
            }
          }
          this.crops.push(this.crops_dict[c]);
        }

        // PARSE FORAGE
        this.forage = [];
        this.forage_dict = {};
        // 1. Internal name, season, and rarity are specified in forageables dictionary.
        for(let t in data_forageables["tables"]) { // key (normal, beach)
          await this.loadingPauseCheck();
          let seasons = ["spring", "summer", "fall", "winter"];
          for(let s in seasons) {
            for(let r in data_forageables["tables"][t][seasons[s]]) { // key (rarity)
              for(let i in data_forageables["tables"][t][seasons[s]][r]) { // array index
                let name = data_forageables["tables"][t][seasons[s]][r][i];
                this.forage_dict[name] = {
                "key": name,
                "rarity": r,
                "seasons": seasons[s]
                }
              }
            }
          }
        }

        // 2. Additional data is specified in object_prototypes/bush dictionary.
        for(let b in object_prototypes["bush"]) {
          await this.loadingPauseCheck();
          if(b !== "default" && b !== "bush") {
            console.log("Bush: " + b);
            let name = object_prototypes["bush"][b]["harvest"];
            console.log("Name: " + name);
            this.forage_dict[name] = {
              "key": name,
              "source": b,
              "seasons": object_prototypes["bush"][b]["seasons"],
            }
          }
        }

        // 3. Even more data is specified in object_prototypes/tree dictionary.
        for(let t in object_prototypes["tree"]) {
          await this.loadingPauseCheck();
          if(object_prototypes["tree"][t]["fruit_data"] !== undefined) {
            console.log("Tree: " + t);
            let name = object_prototypes["tree"][t]["fruit_data"]["harvest"];
            console.log("Name: " + name);
            this.forage_dict[name] = {
              "key": name,
              "source": t,
              "seasons": object_prototypes["tree"][t]["fruit_data"]["seasons"]
            }
          }
        }

        // 4. Actual name and Value (bin/store) are specified in items/other/crops_and_forage dictionary.
        for(let f in items_other_crops_and_forage) {
          await this.loadingPauseCheck();
          if(this.forage_dict[f] !== undefined) {
            this.forage_dict[f]["name"] = this.localizations_eng[items_other_crops_and_forage[f]["name"]];
            if(items_other_crops_and_forage[f]["restore"] !== undefined) {
              this.forage_dict[f]["restore"] = items_other_crops_and_forage[f]["restore"];
            }
            if(items_other_crops_and_forage[f]["value"] !== undefined) {
              if(items_other_crops_and_forage[f]["value"]["bin"] !== undefined) {
                this.forage_dict[f]["sell_price"] = items_other_crops_and_forage[f]["value"]["bin"]
              }
              if(items_other_crops_and_forage[f]["value"]["store"] !== undefined) {
                this.forage_dict[f]["purchase_price"] = items_other_crops_and_forage[f]["value"]["store"]
              }
            }
          }
        }

        // 5. Extract data from items/fish/misc dictionary.
        for(let f in items_fish["misc"]) {
          await this.loadingPauseCheck();
          if(this.forage_dict[f] !== undefined) {
            this.forage_dict[f]["name"] = this.localizations_eng[items_fish["misc"][f]["name"]];
            if(items_fish["misc"][f]["value"] !== undefined) {
              if(items_fish["misc"][f]["value"]["bin"] !== undefined) {
                this.forage_dict[f]["sell_price"] = items_fish["misc"][f]["value"]["bin"];
              }
              if(items_fish["misc"][f]["value"]["store"] !== undefined) {
                this.forage_dict[f]["purchase_price"] = items_fish["misc"][f]["value"]["store"];
              }
            }
          }
        }

        // 6. Extract data from items/mines dictionary.
        for(let m in items_mines) {
          await this.loadingPauseCheck();
          for(let item in items_mines[m]) {
            if(this.forage_dict[item] !== undefined) {
              this.forage_dict[item]["name"] = this.localizations_eng[items_mines[m][item]["name"]];
              this.forage_dict[item]["restore"] = items_mines[m][item]["restore"];
              if(items_mines[m][item]["value"] !== undefined) {
                if(items_mines[m][item]["value"]["bin"] !== undefined) {
                  this.forage_dict[item]["sell_price"] = items_mines[m][item]["value"]["bin"];
                }
                if(items_mines[m][item]["value"]["store"] !== undefined) {
                  this.forage_dict[item]["purchase_price"] = items_mines[m][item]["value"]["store"];
                }
              }
            }
          }
        }

        // 7. Additional data is specified in object_prototypes/crop dictionary.
        for(let c in object_prototypes["crop"]) {
          await this.loadingPauseCheck();
          if(this.forage_dict[c] !== undefined) {
            this.forage_dict[c]["seasons"] = object_prototypes["crop"][c]["seasons"]
          }
        }

        // 8. Cleanup
        for(let f in this.forage_dict) {
          await this.loadingPauseCheck();
          // Seasons
          if(typeof this.forage_dict[f]["seasons"] === 'object') {
            this.forage_dict[f]["seasons"] = this.forage_dict[f]["seasons"].join(", ");
          }
          // Inedible
          if(this.forage_dict[f]["restore"] === undefined) {
            this.forage_dict[f]["restore"] = "inedible";
            console.log(this.forage_dict[f]);
          }
          // Restoration lookup/conversion.
          else if(this.forage_dict[f]["restore"] !== undefined) {
            let restoration_name = this.forage_dict[f]["restore"];
            if(data_restoration["vars"][restoration_name] !== undefined) {
              this.forage_dict[f]["restore"] = data_restoration["vars"][restoration_name] + " HP/STA";
            }
            else {
              this.forage_dict[f]["restore"] = this.forage_dict[f]["restore"] + " HP/STA";
            }
          }
          this.forage.push(this.forage_dict[f]);
        }

        
        // Done loading data.
        this.loading = false;
      }
    },

    mounted() {
      this.load_data(this.version_loaded);
    }
};
</script>
