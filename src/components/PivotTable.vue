<template>
  <div id="app">

    <table class="table table-bordered">
      <thead class="thead-light">
        <tr
          v-for="(colAttr, j) in pivot.props.cols"
          :key="j"
        >
          <th
            class="thead-light"
            v-if="j === 0 && pivot.props.rows.length !== 0"
            :colSpan="pivot.props.rows.length"
            :rowSpan="pivot.props.cols.length"
          >
          </th>
          <template v-for="(colKey, i) in pivot.getColKeys()">
            <th
              v-if="spanSize(pivot.getColKeys(),i,j) !== -1"
              :key="`colKey${i}`"
              :colSpan="spanSize(pivot.getColKeys(),i,j)"
              :rowSpan="[j === pivot.props.cols.length - 1 && pivot.props.rows.length !== 0 ? 2 : 1]"
            >
              {{colKey[j]}}
            </th>
          </template>
          <th v-if="1 === j">Comment</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(rowKey, i) in pivot.getRowKeys()"
          :key="`rowKeyRow${i}`"
        >

          <template v-for="(rowLabel, j) in rowKey">
            <th
              class="thead-light"
              v-if="spanSize(pivot.getRowKeys(),i,j) !== -1"
              :key="`rowKeyLabel${i}-${j}`"
              :rowSpan="spanSize(pivot.getRowKeys(),i,j)"
              :colSpan="[j === pivot.props.rows.length - 1 && pivot.props.cols.length !== 0 ? 1 : 1]"
            >
              {{rowLabel}}
            </th>
          </template>
          <td
            v-for="(colKey, j) in pivot.getColKeys()"
            :key="`pvtVal${i}-${j}`"
          >
            <slot
              name="valueCell"
              :cell="pivot.getAggregator(rowKey, colKey)"
            />
            {{pivot.getAggregator(rowKey, colKey).value()}}
          </td>
          <td>
            <slot name="annotation" />
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</template>

<script>
// import Tips from './tips'
import { PivotData } from './PivotData'
import defaultProps from './helpers/defaultProps'
export default {
  name: 'pivot-table',
  props: {
    data: [Array],
    cols: [Array],
    rows: [Array]
  },

  data () {
    return {
      pivot: null
    }
  },
  mounted () {
    this.init()
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
      // console.log(this.data)
      // const attrValues = {}
      // const materializedInput = []
      // // let recordsProcessed = 0
      console.log(1)
      var inputProps = defaultProps
      console.log(2)
      inputProps.data = this.data
      inputProps.cols = this.cols
      inputProps.rows = this.rows
      console.log(3)
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
    data () {
      console.log('Prop changed')
    },
    cols () {
      console.log('Cols changed')
      this.init()
    }
  }
}
</script>
