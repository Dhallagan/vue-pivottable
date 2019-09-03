<template>
  <div id="app">

    <pivot-table
      :data="data"
      :cols="['period_end_d_ate_key']"
      :rows="['matter_name','record_type']"
    >
      <span
        slot="valueCell"
        slot-scope="cell"
      >
        {{cell}}
      </span>
      <template slot="annotation">
        <textarea
          name="Text1"
          cols="40"
          rows="2"
        ></textarea>
      </template>
    </pivot-table>
  </div>
</template>

<script>
import Inv from './components/inv'
import PivotTable from './components/PivotTable'
export default {
  components: {
    PivotTable
  },
  data () {
    return {
      data: Inv
    }
  }
}
</script>
