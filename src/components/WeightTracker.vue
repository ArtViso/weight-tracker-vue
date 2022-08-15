<template>
  <main class="container">
    <div class="flex justify-center items-center">
      <img src="../assets/logo.png" class="w-[35px] h-[35px] m-[0] mr-[10px]" alt="">
      <h1 class="text-center text-4xl mt-5 mb-5 uppercase">Weight Tracker</h1>
    </div>
    <div
        class="flex flex-col justify-center items-center text-center rounded-full w-[200px] h-[200px] m-[auto] mb-5 border-2 border-blue-500 bg-white shadow-lg shadow-blue-500/50">
      <span class="text-4xl font-bold">{{ currentWeight.weight }}</span>
      <small class="text-2xl font-bold">Current Weight (kg)</small>
    </div>

    <form class="mb-5 text-center" @submit.prevent="addWeight">
      <input
          class="mr-[10px] shadow appearance-none border rounded w-[85%] py-2 px-3 text-gray-700 transition-all hover:ring-1 hover:border-blue-500 focus:outline-none focus:border-blue-500 focus:ring-1 focus:border-blue-500"
          type="number" step="0.1" v-model="weightInput"/>
      <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
      >Add weight
      </button>
    </form>

    <div v-if="weights && weights.length > 0">
      <h1 class="text-center text-2xl mb-5 uppercase">Last 7 days:</h1>

      <div class="w-[100%] h-[400px] m-[auto]">
        <canvas ref="weightChartEl"></canvas>
      </div>

      <div class="weight-history">
        <h1 class="text-center text-2xl mt-5 mb-5 uppercase">Weight History:</h1>
        <div class="flex flex-col">
          <div class="overflow-x-auto sm:-mx-6 lg:-mx-8">
            <div class="py-2 inline-block min-w-full sm:px-6 lg:px-8">
              <div class="overflow-hidden">
                <table class="min-w-full text-center">
                  <thead class="bg-white border-b">
                  <tr>
                    <th scope="col" class="table-head">
                      #
                    </th>
                    <th scope="col" class="table-head">
                      Weight
                    </th>
                    <th scope="col" class="table-head">
                      Gender
                    </th>
                    <th scope="col" class="table-head">
                      Date
                    </th>
                  </tr>
                  </thead>
                  <tbody>
                  <tr v-for="(weight, index) in weights"
                      class="bg-white border-b transition duration-300 ease-in-out hover:bg-blue-200">
                    <td class="table-body">
                      {{ index + 1 }}
                    </td>
                    <td class="table-body">
                      {{ weight.weight }} kg
                    </td>
                    <td class="table-body">
                      M&W
                    </td>
                    <td class="table-body">
                      {{ new Date(weight.date).toLocaleDateString() }}
                    </td>
                  </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup>
import {ref, shallowRef, computed, watch, nextTick} from 'vue'
import Chart from 'chart.js/auto'

const weights = ref([]);
const weightInput = ref(0);
const weightChartEl = ref(null);
const weightChart = shallowRef(null);
const currentWeight = computed(() => {
  return weights.value.sort((a, b) => b.date - a.date)[0] || {weight: 0}
})

const addWeight = () => {
  if (!weightInput.value) {
    return;
  }
  weights.value.push({
    weight: weightInput.value,
    date: new Date().getTime()
  })
}
watch(weights, (newWeights) => {
  const ws = [...newWeights]
  if (weightChart.value) {
    weightChart.value.data.labels = ws
        .sort((a, b) => a.date - b.date)
        .map(weight => new Date(weight.date).toLocaleDateString())
        .slice(-7)
    weightChart.value.data.datasets[0].data = ws
        .sort((a, b) => a.date - b.date)
        .map(weight => weight.weight)
        .slice(-7)
    weightChart.value.update()
    return
  }
  nextTick(() => {
    weightChart.value = new Chart(weightChartEl.value.getContext('2d'), {
      type: 'line',
      data: {
        labels: ws
            .sort((a, b) => a.date - b.date)
            .map(weight => new Date(weight.date).toLocaleDateString()),
        datasets: [
          {
            label: 'WEIGHT',
            data: ws
                .sort((a, b) => a.date - b.date)
                .map(weight => weight.weight),
            backgroundColor: 'rgba(59, 130, 246, 0.2)',
            borderColor: 'rgba(0, 97, 255, 1)',
            borderWidth: 1,
            fill: true
          }
        ]
      },
      options: {
        responsive: true,
        maintainAspectRatio: false
      }
    })
  })
}, {deep: true})
</script>
<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Avenir, Helvetica, Arial, sans-serif;
}

body {
  background-color: #ededed;
}
</style>
