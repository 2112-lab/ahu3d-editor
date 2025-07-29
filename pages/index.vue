<template>
  <v-app id="appContainer">
    <div id="pageWrapper">
      <!-- Title and Subtitle -->
      <div id="titleContainer">
        <h1>Ahu3D Module</h1>
      </div>

      <!-- Description -->
      <div id="descriptionContainer">
        <p>This app showcases the deployment of multiple Ahu3d instances in a single page.</p>
      </div>      

      <v-row no-gutters justify="end">
        <!-- Scene Containers -->
        <div>
          <div id="sceneContainer1"></div>
          <div id="sceneContainer2"></div>
        </div>

        <div class="ml-6">
          <div id="buttonContainer" class="mb-2">
            <span style="margin-right:6px">Display Xeto Samples:</span>
            <button id="button" type="button" @click="displayMockXeto(0)">1</button>
            <button id="button" type="button" @click="displayMockXeto(1)">2</button>
            <button id="button" type="button" @click="displayMockXeto(2)">3</button>
            <button id="button" type="button" @click="displayMockXeto(3)">4</button>
            <button id="button" type="button" @click="displayMockXeto(4)">5</button>
          </div>
          
          <textarea 
            class="jsonDisplay" 
            style="border:2px solid #ccc; overflow:auto; white-space:nowrap; position:relative; z-index:10;" 
            v-model="jsonDisplayData">
          </textarea>

          <v-row no-gutters class="mb-1">
            <v-checkbox
            v-model="selectedAhu3dInstances"
            label="Primary"
            value="ahu3d_primary"
            :disabled="jsonDisplayData === ''"
            dense
            style="width:100px"
          ></v-checkbox>
          <v-checkbox
            v-model="selectedAhu3dInstances"
            label="Secondary"
            value="ahu3d_secondary"
            :disabled="jsonDisplayData === ''"
            dense
            style="width:100px"
          ></v-checkbox>
          </v-row>

          <v-btn @click="loadMockXeto" :disabled="jsonDisplayData === ''">
            Load Xeto
          </v-btn> 

        </div>
      </v-row>
  
    </div>

    <v-row no-gutters justify="center" style="position:relative; bottom:0px">
      <footer style="font-size: 13px;">
        PROPERTY OF COGNITIVE DYNAMICS LTD. - ALL RIGHTS RESERVED - 2024.
      </footer>
    </v-row>

  </v-app>
  </template>
  
  <script>
  import { Ahu3D } from '@2112-lab/ahu3d';
  
  export default {
    data() {
      return {
        menu: false, // controls the visibility of the dropdown menu
        selectedOptions: [], // stores selected checkboxes
        options: [],
        sliderValue: 0, // Default value for the slider
        renderedAssembly: [],
        jsonDisplayData: '',
        selectedAhu3dInstances: [],
      };
    },
    async mounted() {
      this.instantiatePrimaryScene();
      this.instantiateSecondaryScene();
    },
    methods: {
      async instantiatePrimaryScene() {
        // Initialize Ahu3D instance
        this.ahu3d_primary = new Ahu3D({
          scene: {
            renderer: {
              size: {
                width: 725,
                height: 340
              }
            }
          },
          ui: {
            showTooltip: false
          }
        });
    
        // Attach 3D scene to the page
        this.ahu3d_primary.attachScene("#sceneContainer1");
    
        // Define component assets configuration
        let assetConfigs = {
          assetsPath: "/assets/", // Path to component assets (models and metadata)
          componentList: [        // List of all available component types
            "AirFilter", "AirFlowSensor", 
            "CoolingCoil", "Damper", "Fan", "GenericSensor", "HeatingCoil", 
            "TemperatureSensor", "DwyerOutside", "FanPropeller", "FanHorizontal", 
            "HumiditySensor", "SmokeDetector"
          ]
        };
    
        await this.ahu3d_primary.loadLibrary(assetConfigs);

      },
      async instantiateSecondaryScene() {
        // Initialize Ahu3D instance
        this.ahu3d_secondary = new Ahu3D({
          scene: {
            renderer: {
              size: {
                width: 725,
                height: 340
              }
            }
          },
          ui: {
            showTooltip: false
          }
        });
    
        // Attach 3D scene to the page
        this.ahu3d_secondary.attachScene("#sceneContainer2");
    
        // Define component assets configuration
        let assetConfigs = {
          assetsPath: "/assets/", // Path to component assets (models and metadata)
          componentList: [        // List of all available component types
            "AirFilter", "AirFlowSensor", 
            "CoolingCoil", "Damper", "Fan", "GenericSensor", "HeatingCoil", 
            "TemperatureSensor", "DwyerOutside", "FanPropeller", "FanHorizontal", 
            "HumiditySensor", "SmokeDetector"
          ]
        };
    
        await this.ahu3d_secondary.loadLibrary(assetConfigs);
      },
      displayMockXeto(index = 0) {
        const mockXeto = this.getMockXeto(index);
        this.jsonDisplayData = JSON.stringify(mockXeto, null, 2);
      },
      async loadMockXeto() {
        console.log("loadMockXeto started:", this.selectedAhu3dInstances);
        const jsonDisplayData = JSON.parse(this.jsonDisplayData);

        this.renderedAssembly = [];
        for (const instanceKey of this.selectedAhu3dInstances) {
          console.log("loadMockXeto instanceKey:", instanceKey);
          await this[instanceKey].runAhu3D(jsonDisplayData, "full3d");
        }

        console.log("this.renderedAssembly:", this.renderedAssembly);

      },
      getMockXeto(index = 0) {
        const mockXetoArray = [
          require("~/static/mock-data/xeto/xeto-sample-1.json"),
          require("~/static/mock-data/xeto/xeto-sample-2.json"),
          require("~/static/mock-data/xeto/xeto-sample-3.json"),
          require("~/static/mock-data/xeto/xeto-sample-4.json"),
          require("~/static/mock-data/xeto/xeto-sample-5.json"),
          require("~/static/mock-data/xeto/xeto-sample-6.json"),
        ];
        return mockXetoArray[index];
      },
    }
  }
  </script>
  
  <style>
    #pageWrapper {
      display: flex;
      flex-direction: column;
      justify-content: space-between; /* Adjust this to distribute space */
      align-items: center;
      padding-bottom: 40px;
      box-sizing: border-box; /* Ensure padding is included in height */
      overflow-y: auto; /* Enable scrolling if content overflows */
    }
  
    #titleContainer {
      text-align: center;
      margin-top:40px;
      margin-bottom:40px;
      line-height: 1;
    }
  
    #descriptionContainer {
      text-align: center;
      margin-top: 10px;
      margin-bottom: 20px;
      line-height: 0.3;
    }
  
    #sceneContainer1 {
      width: 725px;
      height: 340px;
      margin-top: 20px;
      margin-bottom: 10px;
      background: linear-gradient(to bottom, #3e9dd4, #FFFFFF);
      border: 1px solid #5555; /* Border around the scene */
      position: relative; /* Ensure proper positioning for child elements */
    }

    #sceneContainer2 {
      width: 725px;
      height: 340px;
      background: linear-gradient(to bottom, #3e9dd4, #FFFFFF);
      border: 1px solid #5555; /* Border around the scene */
      position: relative; /* Ensure proper positioning for child elements */
    }
  
    #buttonContainer {
      margin-top: 20px;
    }
  
    #button {
      border:1px solid #5555;
      width:30px;
      height:30px;
    }
  
    .v-input__control{
      height:30px;
      width:500px;
    }

    .jsonDisplay {
      width: 370px;
      height: 525px;
      font-family: monospace;
      font-size: 12px;
      resize: none;
    }
    textarea:focus {
        outline: none;
        border: none;
        box-shadow: none;
    }

    /* Ensure AHU3D renderers stay within their containers */
    .ahu3d-renderer {
      display: block !important;
      position: relative !important;
    }
  </style>
  