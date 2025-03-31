<template>
    <div id="browser">
      <div id="webHead">
        <h1>
          X-Scanning Data Corpus
        </h1>
      </div>
      <div id="selection">
        <div id="display" class="buttons">
          <p class="select-title">
            Display
          </p>
          <button v-for="value in uniqueValues.Display" :key="value" :class="{ selected: isSelected('Display', value) }"
            @click="toggleFilter('Display', value)">
            {{ value }}
          </button>
        </div>

        <div id="locomotion" class="buttons">
          <p class="select-title">
            Locomotion
          </p>
          <button v-for="value in uniqueValues.Locomotion" :key="value"
            :class="{ selected: isSelected('Locomotion', value) }" @click="toggleFilter('Locomotion', value)">
            {{ value }}
          </button>
        </div>

        <div id="bodyMovement" class="buttons">
          <p class="select-title">
            BodyMovement
          </p>
          <button v-for="value in uniqueValues.BodyMovement" :key="value"
            :class="{ selected: isSelected('BodyMovement', value) }" @click="toggleFilter('BodyMovement', value)">
            {{ value }}
          </button>
        </div>

        <div id="marktype" class="buttons">
          <p class="select-title">
            Mark Types
          </p>
          <button v-for="value in uniqueValues.MarkType" :key="value"
            :class="{ selected: isSelected('MarkType', value) }" @click="toggleFilter('MarkType', value)">
            {{ value }}
          </button>
        </div>

        <div id="encoding" class="buttons">
          <p class="select-title">
            Encoding Channel
          </p>
          <button v-for="value in uniqueValues.EncodingChannel" :key="value"
            :class="{ selected: isSelected('EncodingChannel', value) }" @click="toggleFilter('EncodingChannel', value)">
            {{ value }}
          </button>
        </div>

        <div id="movingSubjects" class="buttons">
          <p class="select-title">
            Moving Subjects
          </p>
          <button v-for="value in uniqueValues.MovingSubjects" :key="value"
            :class="{ selected: isSelected('MovingSubjects', value) }" @click="toggleFilter('MovingSubjects', value)">
            {{ value }}
          </button>
        </div>

        <div id="progressReview" class="buttons">
          <p class="select-title">
            Progress Review
          </p>
          <button v-for="value in uniqueValues.ProgressReview" :key="value"
            :class="{ selected: isSelected('ProgressReview', value) }" @click="toggleFilter('ProgressReview', value)">
            {{ value }}
          </button>
        </div>

        <div id="locusofControl" class="buttons">
          <p class="select-title">
            Locus of Control
          </p>
          <button v-for="value in uniqueValues.LocusofControl" :key="value"
            :class="{ selected: isSelected('LocusofControl', value) }" @click="toggleFilter('LocusofControl', value)">
            {{ value }}
          </button>
        </div>

        <div id="skippingTool" class="buttons">
          <p class="select-title">
            Skipping Tool
          </p>
          <button v-for="value in uniqueValues.SkippingTool" :key="value"
            :class="{ selected: isSelected('SkippingTool', value) }" @click="toggleFilter('SkippingTool', value)">
            {{ value }}
          </button>
        </div>

        <div id="anchor" class="buttons">
          <p class="select-title">
            Anchor
          </p>
          <button v-for="value in uniqueValues.Anchor" :key="value" :class="{ selected: isSelected('Anchor', value) }"
            @click="toggleFilter('Anchor', value)">
            {{ value }}
          </button>
        </div>

      </div>
      <div id="case-container">
        <div v-if="filteredCases.length > 0">
          <div v-for="(caseItem, index) in filteredCases" :key="index" class="case-item">
            <div v-if="caseItem.IsMarkScan">
              <h4>{{ caseItem.Title }}</h4>
              <p>
                <a v-if="caseItem.CaseURL" target="_blank" :href="caseItem.CaseURL">Case URL</a>
              </p>
              <p v-if="caseItem.ShortDes">
                {{ caseItem.ShortDes }}
              </p>
              <img v-if="caseItem.Image" style="width: 100%;" :src="`images/${caseItem.Image}.png`">
            </div>
          </div>
        </div>
        <p v-else>No cases match the selected filters.</p>
      </div>
    </div>
</template>

<script>
import { reactive, computed } from "vue";
import Papa from "papaparse";  // Import PapaParse

export default {
  name: "Browser",
  data() {
    return {
      cases: [], // Holds the processed data
      filters: reactive({}), // Use reactive for managing filters
    };
  },
  computed: {
    // Extract unique values for each column to create filter buttons
    uniqueValues() {
      const columns = [
        "Display",
        "Locomotion",
        "MarkType",
        "EncodingChannel",
        "BodyMovement",
        "Locomotion",
        "MovingSubjects",
        "ProgressReview",
        "LocusofControl",
        "SkippingTool",
        "Anchor"
      ];
      const uniqueValues = {};
      columns.forEach((column) => {
        const values = this.cases.flatMap((caseItem) =>
          caseItem[column] ? caseItem[column].split(",").map((v) => v.trim()) : []
        );
        uniqueValues[column] = [...new Set(values)]; // Ensure uniqueness
      });
      console.log(uniqueValues);
      return uniqueValues;
    },
    // Compute filtered cases based on selected filters
    filteredCases() {
      return this.cases.filter((caseItem) => {
        return Object.keys(this.filters).every((column) => {
          const selectedValues = this.filters[column] || [];
          if (selectedValues.length === 0) return true;
          const caseValues = caseItem[column]
            ? caseItem[column].split(",").map((v) => v.trim())
            : [];
          return caseValues.some((value) => selectedValues.includes(value));
        });
      });
    },
  },
  methods: {
    // Check if a value is selected for a column
    isSelected(column, value) {
      return this.filters[column]?.includes(value);
    },
    // Toggle a filter value
    toggleFilter(column, value) {
      if (!this.filters[column]) {
        this.filters[column] = []; // Initialize the column array if undefined
      }
      const index = this.filters[column].indexOf(value);
      if (index > -1) {
        this.filters[column].splice(index, 1); // Remove the value
      } else {
        this.filters[column].push(value); // Add the value
      }
    },
    // Function to load and parse the CSV file
    async loadCsv() {
      try {
        const response = await fetch("/data.csv"); // Ensure your CSV file is in the public folder
        const text = await response.text();

        Papa.parse(text, {
          header: true, // The first row contains headers
          skipEmptyLines: true, // Skip empty lines
          complete: (result) => {
            // Now we have the parsed data in `result.data`
            this.cases = result.data;
            console.log("Loaded and parsed CSV data:", this.cases);
          },
          error: (error) => {
            console.error("Error parsing CSV:", error);
          }
        });
      } catch (err) {
        console.error("Failed to load the CSV file:", err);
      }
    },
  },
  async created() {
    // Load and process the CSV file when the component is created
    await this.loadCsv();
  },
};
</script>

<style scoped>
/* position website head */
#webHead{
  position: fixed;
  top: 0;
  left: 0;
  z-index: 1030;
  width: 100%;
  height: 80px;
  background-color: cadetblue;
  padding: 20px;
  color: white;
}
/* position selection menu */
#selection{
  position: fixed;
  top: 80px;
  left: 0;
  z-index: 1030;
  width: 30%;
  padding-left: 20px;
  padding-top: 20px;
}
/* position case containers */
#browser{
  font-family: Arial, sans-serif;
  margin-top: 80px;
  margin-left: 30%;
  padding-top: 20px;
}
/* styles of selection menu and buttons */
.buttons {
  margin-bottom: 10px;
  text-align: left;
}

button {
  padding: 5px 5px;
  border: 1px solid #ccc;
  background-color: white;
  color: black;
  cursor: pointer;
  border-radius: 4px;
  font-size: 0.8rem;
}

button.selected {
  background-color: blue;
  color: white;
}

.case-item {
  padding: 10px;
  background: #f9f9f9;
  border: 1px solid #ddd;
  margin-bottom: 10px;
  border-radius: 4px;
  font-family: monospace;
  white-space: pre-wrap;
}
</style>
