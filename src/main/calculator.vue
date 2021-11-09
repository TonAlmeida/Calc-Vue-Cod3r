<template>
  <div class="calculator">
      <Display :value='displayValue' />
      <Button label="AC" @onBtnClick='clearMemory' triple />
      <Button label="/" @onBtnClick='setOperation' operation />
      <Button label="7" @onBtnClick='addDigit' />
      <Button label="8" @onBtnClick='addDigit' />
      <Button label="9" @onBtnClick='addDigit' />
      <Button label="*" @onBtnClick='setOperation' operation />
      <Button label="4" @onBtnClick='addDigit' />
      <Button label="5" @onBtnClick='addDigit' />
      <Button label="6" @onBtnClick='addDigit' />
      <Button label="-" @onBtnClick='setOperation' operation />
      <Button label="1" @onBtnClick='addDigit' />
      <Button label="2" @onBtnClick='addDigit' />
      <Button label="3" @onBtnClick='addDigit' />
      <Button label="+" @onBtnClick='setOperation' operation />
      <Button label="0" @onBtnClick='addDigit' double />
      <Button label="." @onBtnClick='addDigit' />
      <Button label="=" @onBtnClick='setOperation' operation />
  </div>
</template>

<script>
import Display from '../components/display.vue'
import Button from '../components/button.vue'

export default {
    data: function () {
        return {
            displayValue: '0',
            clearDisplay: false,
            operation: null,
            values: [0, 0],
            current: 0
        }
    },
    components: {
        Button,
        Display
    },
    methods:{
        clearMemory() {
            Object.assign(this.$data, this.$options.data())
        },
        setOperation(op) {
            if(this.current === 0) {
                this.operation = op
                this.current = 1
                this.clearDisplay = true
            } else {
                const equals = op === '='
                const currentOperation = this.operation
                
                try{
                    this.values[0] = eval(
                        `${this.values[0]} ${currentOperation} ${this.values[1]}`
                    )
                    if (isNaN(this.values[0]) || !isFinite(this.values[0])) {
                    this.clearMemory()
                    return
                    }
                } catch (e) {
                    this.$emit('onError', e)
                }
                
                this.values[1] = 0
                this.displayValue = this.values[0]
                this.operation = equals ? null : op
                this.current = equals ? 0 : 1
                this.clearDisplay = !equals
            }
        },
        addDigit(n) {
            if(n === '.' && this.displayValue.includes('.')) return 
            
            const clearDisplay = this.displayValue === '0' ||
                this.clearDisplay
            const currentValue = clearDisplay ? '' : this.displayValue
            const displayValue = currentValue + n
            
            this.displayValue = displayValue
            this.clearDisplay = false
            this.values[this.current] = displayValue
        }
    }
}
</script>

<style>
.calculator {
    height: 320px;
    width: 235px;
    border-radius: 5px;
    overflow: hidden;
    
    display: grid;
    grid-template-columns: repeat(4, 25%);
    grid-template-rows: 1fr, repeat(5, 48px);
}
</style>