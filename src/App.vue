<template>
  <div id="app">
    <h1>{{ message }}</h1>
    <div v-for="medication in medications">
      <input v-model="medication.name" placeholder="Medication Name" />
      <div>
        {{ medication.name }}
      </div>
      <div>
        <label>Start Date:</label>
        <input
          v-model="medication.start_date"
          type="date"
          max="medication.end_date"
        />
      </div>
      <div>
        <label>Starting Dose:</label>
        <input v-model="medication.starting_dose" type="number" />
      </div>
      <div v-for="doseChange in medication.doseChanges">
        <div>
          <label>Dose changed to:</label>
          <input v-model="doseChange.dose" type="number" />
        </div>
        <div>
          <label>on:</label>
          <input v-model="doseChange.date" type="date" />
        </div>
      </div>

      <button type="button" @click="addDoseChange(medication)">
        Add Dose Change
      </button>
      <div>
        <label>Currently Taking:</label>
        <input v-model="medication.currently_taking" type="checkbox" />
      </div>
      <div v-if="!medication.currently_taking">
        <label>End Date:</label>
        <input
          v-model="medication.end_date"
          type="date"
          value="2018-07-22"
          min="2018-01-01"
        />
      </div>
    </div>
    <button type="button" @click="addMedication()">Add Medication</button>
    <button type="button" @click="update_chart()">update</button>
  </div>
  <div>
    <canvas id="chart"></canvas>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      medications: [],
    };
  },
  methods: {
    addMedication() {
      this.medications.push({
        name: '',
        start_date: new Date(),
        starting_dose: 0,
        currently_taking: true,
        end_date: new Date(),
        doseChanges: [],
      });
    },
    addDoseChange(medication) {
      console.log(medication);
      medication.doseChanges.push({ date: new Date(), dose: 0 });
    },
    update_chart() {
      var dose = this.medications[0].starting_dose;
      let data = [
        { x: this.medications[0].start_date, y: 0 },
        {
          x: this.medications[0].start_date,
          y: dose,
        },
      ];
      for (var doseChange of this.medications[0].doseChanges) {
        console.log(doseChange);
        data.push({ x: doseChange.date, y: dose });
        dose = doseChange.dose;
        data.push({ x: doseChange.date, y: doseChange.dose });
      }
      if (this.medications[0].currently_taking) {
        data.push({ x: new Date(), y: dose });
      } else {
        data.push({
          x: this.medications[0].end_date,
          y: dose,
        });
        data.push({ x: this.medications[0].end_date, y: 0 });
      }

      this.chart.data.datasets = [
        {
          data: data,
        },
      ];
      this.chart.update();
    },
  },
  mounted() {
    const ctx = document.getElementById('chart');
    this.chart = new Chart(ctx, {
      type: 'scatter',
      data: {
        datasets: [
          {
            data: [
              { x: '2022-1-9 00:00', y: 20 },
              { x: '2022-1-1', y: 10 },
              { x: '2022-1-11', y: 40 },
            ],
          },
        ],
      },
      options: {
        showLine: true,

        scales: {
          y: {
            beginAtZero: true,
          },
          x: {
            type: 'time',
          },
        },
      },
    });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
