<template>
  <div id="app">
    {{colAttrs}}
    <table class="table">

      <thead>
        <tr
          v-for="(colAttr, j) in colAttrs"
          :key="j"
        >
          <th
            v-if="j === 0 && rowAttrs.length !== 0"
            :colSpan="rowAttrs.length"
            :rowSpan="colAttrs.length"
          >

          </th>
          <template v-for="(colKey, i) in pivot.getColKeys()">
            <th
              v-if="spanSize(pivot.getColKeys(),i,j) !== -1"
              :key="`colKey${i}`"
              :colSpan="spanSize(pivot.getColKeys(),i,j)"
              :rowSpan="[j === colAttrs.length - 1 && rowAttrs.length !== 0 ? 2 : 1]"
            >
              {{colKey[j]}}
            </th>
          </template>
          <!-- <th
            v-for="(rowLabel, j) in rowKey"
            :key="`rowKeyLabel${i}-${j}`"
          >
            {{rowLabel}}
          </th> -->

        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(rowKey, i) in pivot.getRowKeys()"
          :key="`rowKeyRow${i}`"
        >
          <!-- <th
            v-for="(rowLabel, j) in rowKey"
            :key="`rowKeyLabel${i}-${j}`"
            :rowSpan=""
            :colSpan=""
          >
            {{rowLabel}}
          </th>
           -->
          <template v-for="(rowLabel, j) in rowKey">
            <th
              v-if="spanSize(pivot.getRowKeys(),i,j) !== -1"
              :key="`rowKeyLabel${i}-${j}`"
              :rowSpan="spanSize(pivot.getRowKeys(),i,j)"
              :colSpan="[j === rowAttrs.length - 1 && colAttrs.length !== 0 ? 1 : 1]"
            >
              {{rowLabel}}
            </th>
          </template>
          <td
            v-for="(colKey, i) in pivot.getColKeys()"
            :key="`pvtVal${i}-${j}`"
          >
            {{pivot.getAggregator(rowKey, colKey).value()}}
          </td>
          <td>Annotation Slot</td>
        </tr>

      </tbody>
    </table>
    {{pivot}}
  </div>
</template>

<script>
import Tips from './components/tips'
import { PivotData } from './components/PivotData'
import defaultProps from './components/helpers/defaultProps'
export default {
  mounted () {
    this.init()
  },
  computed: {
    colAttrs () {
      return this.pivot.props.cols
    },
    rowAttrs () {
      return this.pivot.props.rows
    }
  },
  data () {
    return {
      data: Tips,
      pivot: null
    }
  },
  methods: {
    spanSize (arr, i, j) {
      // helper function for setting row/col-span in pivotTableRenderer
      let x
      if (i !== 0) {
        let asc, end
        let noDraw = true
        for (
          x = 0, end = j, asc = end >= 0;
          asc ? x <= end : x >= end;
          asc ? x++ : x--
        ) {
          if (arr[i - 1][x] !== arr[i][x]) {
            noDraw = false
          }
        }
        if (noDraw) {
          return -1
        }
      }
      let len = 0
      while (i + len < arr.length) {
        let asc1, end1
        let stop = false
        for (
          x = 0, end1 = j, asc1 = end1 >= 0;
          asc1 ? x <= end1 : x >= end1;
          asc1 ? x++ : x--
        ) {
          if (arr[i][x] !== arr[i + len][x]) {
            stop = true
          }
        }
        if (stop) {
          break
        }
        len++
      }
      return len
    },
    init () {
      console.log(this.data)
      const attrValues = {}
      const materializedInput = []
      let recordsProcessed = 0
      var inputProps = defaultProps
      inputProps.data = this.data
      inputProps.cols = ['Day']

      inputProps.rows = ['Party Size', 'Meal']

      const pivot = new PivotData(inputProps)
      this.pivot = pivot
      // pivot.forEachRecord(this.data, this.derivedAttributes, function (record) {
      //   materializedInput.push(record)
      //   for (const attr of Object.keys(record)) {
      //     if (!(attr in attrValues)) {
      //       attrValues[attr] = {}
      //       if (recordsProcessed > 0) {
      //         attrValues[attr].null = recordsProcessed
      //       }
      //     }
      //   }
      //   for (const attr in attrValues) {
      //     const value = attr in record ? record[attr] : 'null'
      //     if (!(value in attrValues[attr])) {
      //       attrValues[attr][value] = 0
      //     }
      //     attrValues[attr][value]++
      //   }
      //   recordsProcessed++
      // })
      // console.log(pivot)
    }
  },
  watch: {
    pivot () {

    }
  }
}
</script>

<style>
table,
th,
td {
  border: 1px solid black;
}
</style>
