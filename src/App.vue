<script setup>
import { ref, shallowRef, computed, watch, nextTick } from 'vue'
import Chart from 'chart.js/auto'

const weight = ref([])
const weightChartEl = ref(null)
const weightChart = shallowRef(null)
const weightInput = ref(60.0)
const currentWeight = computed(() => {
  // return weight.value.sort((a, b) => b.date - a.date)[0]?.weight
  return weight.value.sort((a, b) => b.date - a.date)[0] || { weight: 0 }
})

const addWeight = () => {
  weight.value.push({
    date: new Date().getTime(),
    weight: weightInput.value
  })

  console.log(weight.value[0].weight);
  console.log(weightChart.value);

}
watch(weight, newWeight => {
  const ws = [...newWeight]
if(weightChart.value){
  weightChart.value.data.labels = ws.sort((a, b) => a.date - b.date).slice(-7)
  .map(w => new Date(w.date).toLocaleDateString())

  weightChart.value.data.datasets[0].data = ws.sort((a, b) => a.date - b.date).slice(-7)
  .map(w => w.weight)

  weightChart.value.update()
  return
}

  nextTick(() => {
    weightChart.value = new Chart(weightChartEl.value, {
      type: 'line',
      data: {
        labels: ws.sort((a, b) => a.date - b.date)
        .map(w => new Date(w.date).toLocaleDateString()),
        datasets: [{
          label: 'Weight',
          data: ws.sort((a, b) => a.date - b.date)
          .map(w => w.weight),
          borderColor: 'rgb(255, 99, 132)',
          backgroundColor: 'rgba(255, 99, 132, 0.5)',
          fill: true
        }]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          y: {
            beginAtZero: true
          }
        }
      }
    })
  })
}, { deep: true })


</script>

<template>
  <main>
    <h1>Weight Tracker</h1>
    <div class="current">
      <span>{{ currentWeight.weight }}</span>
      <small>Current Weight (kg)</small>
    </div>
    <form @submit.prevent="addWeight">
      <input type="number" step="0.1" v-model="weightInput" />

      <input type="submit" value="Add weight" />
    </form>

    <div v-if="weight && weight.length > 0">
      <h2>Last 7 days</h2>
      <div class="canvas-box">
        <canvas ref="weightChartEl" ></canvas>
      </div>
      <div class="weight-history">
        <h2>Weight History</h2>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Weight</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="w in weight">
              <td>{{ new Date(w.date).toLocaleDateString() }}</td>
              <td>{{ w.weight }}kg</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </main>
</template>

<style scoped>
.current {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 2rem;

  width: 200px;
  height: 200px;

  text-align: center;
  background-color: whitesmoke;
  border-radius: 999px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: 5px solid hwb(330 41% 0%);

  margin: 0 auto 2rem;

  span {
    display: block;
    font-size: 3rem;
    font-weight: bold;
    margin-bottom: 0.5rem;
  }

}
.current small {
 font-style: italic;
  color: #888;
}

form {
  display: flex;
  margin-bottom: 2rem;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  overflow: hidden;
  transition: 200ms linear;

}

form:focus-within, form:hover {
  border-color: hotpink;
  border-width: 2px;
}
form input[type="number"] {
  appearance: none;
  outline: none;
  border: none;

  flex: 1 1 0%;
  padding: 1.5rem 1.5rem;
  font-size: 1.25rem;
}

form input[type="submit"] {
  appearance: none;
  outline: none;
  border: none;
  background-color: hotpink;
  color: white;

  padding: 0.5rem 1rem;
  font-size: 1.25rem;
  font-weight: bold;
  transition: 200ms linear;
  cursor: pointer;
}

form input[type="submit"]:hover {
  background-color: white;
  color: hotpink;
  border-left-color: hotpink;
}

.canvas-box {
  width: 100%;
  max-width: 720px;

  margin-bottom: 2rem;
  padding: 1rem;

  background-color: white;
  border-radius: 0.5rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  transition: 200ms linear;
}
table {
  width: 100%;
  border-collapse: collapse;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  overflow: hidden;
  transition: 200ms linear;


}
th {
    background-color: hotpink;
    color: white;
    padding: 0.5rem 1rem;
    text-align: left;
  }

td {
  padding: 0.5rem 1rem;
  text-align: left;
  border-bottom: 1px solid #ccc;
  font-weight: bold;
}

</style>
