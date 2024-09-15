<template>
  <v-container>
    <div class="d-flex justify-space-around">
      <v-img src="title_logo.png" max-height="150" max-width="250"></v-img>
      <!--<h1 class="text-h2 font-weight-bold">Aldarian Archive</h1>-->
    </div>
    <br />

  <v-card style="opacity: 0.90" >
    <v-tabs v-model="selected_tab" background-color="blue-grey darken-4" center-active dark>
      <v-tab v-for="tab in tabs" :key="tab">
        {{ tab }}
      </v-tab>
    </v-tabs>

    <v-tabs-items v-model="selected_tab">
      <v-tab-item v-for="tab in tabs" :key="tab">
        <div style="overflow-y: visible;">
          <v-card v-if="tab == 'Artifacts'" dark style="opacity: 0.85; overflow-y: auto; max-height: 75vh">
            <v-card-title>
              <v-text-field v-model="artifact_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
            </v-card-title>
            <v-data-table dense :headers="artifact_headers" :items="artifacts" :search="artifact_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
          </v-card>
          <v-card v-if="tab == 'Bugs'" dark style="opacity: 0.85; overflow-y: auto; max-height: 75vh">
            <v-card-title>
              <v-text-field v-model="bug_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
            </v-card-title>
            <v-data-table dense :headers="bug_headers" :items="bugs" :search="bug_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
          </v-card>
          <v-card v-if="tab == 'Fish'" dark style="opacity: 0.85; overflow-y: auto; max-height: 75vh">
          <v-card-title>
            <v-text-field v-model="fish_search" append-icon="mdi-magnify" label="Search" single-line hide-details />
          </v-card-title>
          <v-data-table dense :headers="fish_headers" :items="fish" :search="fish_search" :sort-by="['name']" :items-per-page="25" :footer-props="{'items-per-page-options': [10, 15, 20, 25, -1]}"></v-data-table>
        </v-card>
        </div>
      </v-tab-item>
    </v-tabs-items>



  </v-card>






  </v-container>
</template>

<script>
  export default {
    name: 'HelloWorld',

    data: () => ({
      selected_tab: null,
      tabs: [
        "Artifacts",
        //"Blacksmithing",
        "Bugs",
        //"Cooking",
        //"Crafting",
        //"Crops",
        //"Forage",
        "Fish",
        //"NPCs",
        //"Quests"
      ],
      fish_panels: [
        "Misc",
        "Ocean",
        "Pond",
        "River"
      ],

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

    }),

    methods: {
      parse_localization: function(json) {
        this.localizations_eng = json["eng"];
      },

      parse_fiddle: function(json) {
        // Artifact Data
        var data_artifacts = json["artifacts"];

        // Bugs Data
        var data_bugs = json["bugs"];

        // Dungeons Data
        var data_dungeons = json["dungeons"]["dungeons"];

        // Fish Data
        var data_fish = json["fish"];

        // Items Data
        var items_other_artifacts = json["items"]["other"]["artifacts"];
        var items_other_bugs = json["items"]["other"]["bugs"];
        var items_fish = json["items"]["fish"];
        var items_mines = json["items"]["mines"];

        // Museum Archaeology Data
        var data_museum_wings = json["museum_wings"];
        
        // Object Prototypes Data
        var object_prototypes = json["object_prototypes"];

        // PARSE FISH
        this.fish = [];

        // Extract all fish from the items/mines dictionary.
        for(let mines_location in items_mines) {
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
              this.fish[i]["weather"] = "any";
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
          //"gems_of_mistria": "TODO", // Need a way to look up the biome
          //"metals_of_mistria": "TODO" // Need a way to look up the biome
        };
        for(let location in data_artifacts["locations"]) {
          artifact_location_lookup_dict[data_artifacts["locations"][location]] = location;
        }
        delete artifact_location_lookup_dict["mine"];
        artifact_location_lookup_dict["mine"] = "mines (Any Biome)";
        delete artifact_location_lookup_dict["dungeon"]; // this might not be used
        console.log(artifact_location_lookup_dict); // TODO: Figure out how to look up perk requirements

        // Extract all artifacts from the items/mines dictionary.
        for(let mines_location in items_mines) {
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
          if(this.artifacts_dict[artifact] !== undefined) {
            this.artifacts_dict[artifact]["rarity"] = data_artifacts["loot"][artifact];
          }
        }

        // Extract additional data from the museum_wings/archaeology dictionary.
        for(let set in data_museum_wings["archaeology"]["sets"]) {
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
        for(let biome in data_dungeons["biomes"]) {
          let biome_name = data_dungeons["biomes"][biome]["artifact_set"];
          if(biome_name === "upper_mines_artifacts") {
            biome_name = "upper_mines";
          }

          for(let i in data_dungeons["biomes"][biome]["votes"]["ore_rock"]) {
            let ore = data_dungeons["biomes"][biome]["votes"]["ore_rock"][i]["object"];
            object_to_biome_dict[ore] = biome_name;
          }          
        }

        // Note: Anything mapping to "mine" isn't obtaineable yet. Make that clear!
        for(let o in object_to_biome_dict) {
          if(object_to_biome_dict[o] === "mine") {
            object_to_biome_dict[o] = "unobtainable";
          }
        }

        // Set location based on sources (for nodes and rocks).
        for(let a in this.artifacts_dict) {
          for(let s in this.artifacts_dict[a]["sources"]) {
            let source = this.artifacts_dict[a]["sources"][s];
            if(object_to_biome_dict[source] !== undefined) {
              this.artifacts_dict[a]["location"] = object_to_biome_dict[source];
            }
          }
        }

        // Anything still without a source is unobtainable.
        for(let a in this.artifacts_dict) {
          if(this.artifacts_dict[a]["sources"] === undefined) {
            this.artifacts_dict[a]["sources"] = ['none'];
          }
        }

        // Anything still without a location is unobtainable.
        for(let a in this.artifacts_dict) {
          if(this.artifacts_dict[a]["location"] === undefined) {
            this.artifacts_dict[a]["location"] = "unobtainable";
          }
        }

        // Convert the artifacts dict to an array for the table.
        for(let a in this.artifacts_dict) {
          this.artifacts_dict[a]["sources"] = this.artifacts_dict[a]["sources"].join(", ");
          this.artifacts.push(this.artifacts_dict[a]);
        }


        // PARSE BUGS
        this.bugs = [];
        this.bugs_dict = {};

        // Extract data from bugs dictionary
        for(let b in data_bugs) {
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
          if(this.bugs_dict[b] === undefined) {
            console.log("Missing: " + b); // pond_snail, river_snail
            this.bugs_dict[b] = {
              // TODO: Probably need to look up info from fish data for these
              "name": this.localizations_eng[items_other_bugs[b]["name"]],
              "tags": items_other_bugs[b]["tags"],
              "value": items_other_bugs[b]["value"]["bin"]
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
          for(let x in items_mines[l]) {
            if(this.bugs_dict[x] !== undefined) {
              this.bugs_dict[x]["name"] = this.localizations_eng[items_mines[l][x]["name"]];
              this.bugs_dict[x]["value"] = items_mines[l][x]["value"]["bin"];
            }
          }
        }

        // Convert the bugs dict to an array for the table.
        for(let b in this.bugs_dict) {
          if(this.bugs_dict[b]["locations"] === undefined) {
            this.bugs_dict[b]["locations"] = "overworld";
          }
          if(this.bugs_dict[b]["seasons"] !== undefined && Array.isArray(this.bugs_dict[b]["seasons"])) {
            this.bugs_dict[b]["seasons"] = this.bugs_dict[b]["seasons"].join(", ");
          }
          if(this.bugs_dict[b]["weather"] !== undefined && Array.isArray(this.bugs_dict[b]["weather"])) {
            this.bugs_dict[b]["weather"] = this.bugs_dict[b]["weather"].join(", ");
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


        /**
        var artifacts_loot_keys = [];
        for(var temp in data_artifacts["loot"]) {
          artifacts_loot_keys.push(temp);
        }

        var items_other_artifacts_keys = [];
        for(var temp2 in items_other_artifacts) {
          items_other_artifacts_keys.push(temp2);
          if(!artifacts_loot_keys.includes(temp2)) {
            console.log(temp2 + " was missing from /artifacts/loot!");
          }
        }

        console.log("===================================");
        for(var temp3 in data_artifacts["loot"]) {
          if(!items_other_artifacts_keys.includes(temp3)) {
            console.log(temp3 + " was missing from /items/other/artifacts!");
          }
        }
        **/

      }
    },

    mounted() {
      // Load Images
      // Load Localization Data
      var v0115_localization = require('@/assets/data/v0.11.5/localization.json');
      this.parse_localization(v0115_localization);

      // Load Fiddle Data
      var v0115_fiddle = require('@/assets/data/v0.11.5/__fiddle__.json');
      this.parse_fiddle(v0115_fiddle);

      /*
      fetch(path)
        .then(response => response.json())
        .then(response => console.log(response))
        .then(data => this.desserts = data["desserts"]);
      */
    }
  }
</script>
