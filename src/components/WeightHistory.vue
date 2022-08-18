<template>
  <tr class="bg-white border-b transition duration-300 ease-in-out hover:bg-blue-200">
    <td class="table-body">
      {{ index }}
    </td>
    <td class="table-body">
      {{ weight }} kg
    </td>
    <td class="table-body">
      {{ gender }}
    </td>
    <td class="table-body">
      {{ date }}
    </td>
  </tr>
</template>

<script>
export default {
  name: "WeightHistory",
  props: {
    index: {
      type: Number,
      default: ''
    },
    weight: {
      type: String,
      default: ''
    },
    gender: {
      type: String,
      default: ''
    },
    date: {
      type: Date,
      default: ''
    }
  }
}
</script>

<style scoped>

</style>
