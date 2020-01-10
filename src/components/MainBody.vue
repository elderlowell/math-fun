<template>
  <div class="p-3 text-dark d-flex flex-column justify-content-center">
    <b-card
      title="Math Speed Test Generator"
    >
      <b-card-text>
        <b-form @submit="onGenerate" @reset="onReset" v-if="show">
          <b-form-group label="Operators to Use:">
            <b-form-checkbox-group
            v-model="operatorsSelected"
            :options="operators"
            stacked
            ></b-form-checkbox-group>
            <small class="text-danger" v-if="operatorsError">* You must select at least one operator.</small>
          </b-form-group>
          <b-form-group label="Number of Questions:">
            <b-form-select
            v-model="numberOfQuestions"
            :options="questions"
            ></b-form-select>
          </b-form-group>
          <b-form-group label="Numbers to Use:">
            <b-row class="m-0">
              <b-col cols="6" class="p-0">
                <b-form-group label="Left Side">
                  <b-form-checkbox-group
                  v-model="leftSide"
                  :options="numbersToUse"
                  stacked
                  ></b-form-checkbox-group>
                  <b-button variant="dark" class="mt-2" @click="addAllLeft()">{{ leftSide === numbersToUse ? 'Remove All' : 'Add All' }}</b-button>
                </b-form-group>
              </b-col>
              <b-col cols="6" class="p-0">
                <b-form-group label="Right Side">
                  <b-form-checkbox-group
                  v-model="rightSide"
                  :options="numbersToUse"
                  stacked
                  ></b-form-checkbox-group>
                  <b-button variant="dark" class="mt-2" @click="addAllRight()">{{ rightSide === numbersToUse ? 'Remove All' : 'Add All' }}</b-button>
                </b-form-group>
              </b-col>
            </b-row>
            <small class="text-danger" v-if="numbersError">* You must select at least one number to use for each side.</small>
          </b-form-group>
          <b-form-group>
            <b-form-checkbox
              v-model="solution"
            >Print Solution Sheet
            </b-form-checkbox>
          </b-form-group>
          <b-button type="reset" variant="danger">Reset</b-button>
          <b-button type="submit" variant="warning" class="ml-2">Generate Quiz</b-button>
        </b-form>
      </b-card-text>
    </b-card>
    <b-card  v-if="showPrint" class="mt-3">
      <b-card-text>
        <b-form @submit="onPrint" @reset="onClose">
          <div id="print-form">
            <div>
              <div class="title-box">Name:</div>
              <div class="title-box">Date:</div>
            </div>
            <div class="problem-box" v-for="p in problems" :key="p.number">
              {{ p.number }}. {{ p.left }} <span v-html="p.operator"></span> {{ p.right }} =
            </div>
          </div>
          <b-button type="reset" variant="danger">Close</b-button>
          <b-button type="submit" variant="warning" class="ml-2">Print</b-button>
        </b-form>
      </b-card-text>
    </b-card>
  </div>
</template>

<script>
import print from 'print-js'
export default {
  name: 'MainBody',
  data () {
    return {
      show: true,
      operators: [
        { text: '+ Addition', value: '&#43;' },
        { text: '- Subtraction', value: '&#8722;' },
        { text: 'x Multiplication', value: '&#215;' },
        { text: '/ Division', value: '&#247;' }
      ],
      operatorsSelected: [],
      operatorsError: false,
      numberOfQuestions: 20,
      questions: [20, 40, 60, 80],
      numbersToUse: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
      leftSide: [],
      rightSide: [],
      numbersError: false,
      solution: true,
      showPrint: false
    }
  },
  methods: {
    print,
    addAllLeft () {
      this.leftSide === this.numbersToUse ? this.leftSide = [] : this.leftSide = this.numbersToUse
    },
    addAllRight () {
      if (this.rightSide === this.numbersToUse) {
       this.rightSide = []
     } else {
       this.rightSide = this.numbersToUse
       if (this.operatorsSelected.includes('&#247;')) {
         this.rightSide.shift('&#247;')
       }
     }
    },
    onGenerate (evt) {
      evt.preventDefault()
      this.operatorsError = false
      this.numbersError = false
      if (this.operatorsSelected.length === 0) {
        this.operatorsError = true
      } else if (this.leftSide.length === 0 || this.rightSide.length === 0) {
        this.numbersError = true
      } else {
        this.operatorsError = false
        this.numbersError = false
        this.showPrint = true
      }
    },
    onReset (evt) {
      evt.preventDefault()
      this.show = false
      this.operatorsSelected = []
      this.operatorsError = false
      this.numberOfQuestions = 20
      this.leftSide = []
      this.rightSide = []
      this.numbersError = false
      this.solution = true
      this.show = true
      this.showPrint = false
    },
    onPrint (evt) {
      evt.preventDefault()
      this.print('print-form', 'html')
    },
    onClose (evt) {
      evt.preventDefault()
      this.showPrint = false
    }
  },
  computed: {
    problems () {
      let temp = []
      for (var i = 0; i < this.numberOfQuestions; i++) {
        let left = this.leftSide[Math.floor(Math.random() * this.leftSide.length)]
        let operator = this.operatorsSelected[Math.floor(Math.random() * this.operatorsSelected.length)]
        let right = this.rightSide[Math.floor(Math.random() * this.rightSide.length)]
        switch (operator) {
          case '&#8722;':
            temp.push({ number: i + 1, left: left + right, operator: operator, right: left })
            break
          case '&#247;':
            temp.push({ number: i + 1, left: left * right, operator: operator, right: left })
            break
          default:
            temp.push({ number: i + 1, left: left, operator: operator, right: right })
        }
      }
      return temp
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.title-box {
  width: 24em;
  height: 3em;
  display: inline-block;
}
.problem-box {
  width: 12em;
  height: 3em;
  display: inline-block;
}
@media print {
  #print-form {
    height: 100%;
    width: 100%;
    text-align: right;
  }
  .title-box {
    width: 24em;
    height: 3em;
    display: inline-block;
  }
  .problem-box {
    width: 12em;
    height: 3em;
    display: inline-block;
    margin: 0 4em 4em 0;
  }
}
</style>
