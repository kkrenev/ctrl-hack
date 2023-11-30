<template>
  <div id="app">
    <div class="cols">
      <base-input
          v-model="form.price"
          label="Цена"
          placeholder="Введите цену"
          @input="updateFields('price', $event)"
      />
      <base-input
          v-model="form.qty"
          label="Кол-во"
          placeholder="Введите кол-во"
          @input="updateFields('qty', $event)"
      />
      <base-input
          v-model="form.amount"
          label="Сумма"
          placeholder="Введите сумму"
          @input="updateFields('amount', $event)"
      />
      <base-button @click.native="handleSubmit">
        <template #label></template>
        <template #default>Рассчитать</template>
      </base-button>
    </div>
    <div class="cols mt-10">
      <base-label>{{ form.price }}</base-label>
      <base-label>{{ form.qty }}</base-label>
      <base-label>{{ form.amount }}</base-label>
      <base-label>{{ localStorageData }}</base-label>
    </div>
    <base-container class="mt-10">
      <template #default>
        <the-information :events="reversedEvents"></the-information>
      </template>
    </base-container>
  </div>
</template>

<script>
import BaseButton from '@/components/Base/BaseButton.vue'
import BaseContainer from '@/components/Base/BaseContainer.vue'
import BaseInput from '@/components/Base/BaseInput.vue'
import BaseLabel from '@/components/Base/BaseLabel.vue'
import TheInformation from '@/components/TheInformation.vue'
import {MESSAGE_TYPES, MESSAGES} from '@/constants'

export default {
  name: 'App',
  components: {
    BaseContainer,
    TheInformation,
    BaseLabel,
    BaseButton,
    BaseInput
  },

  data () {
    return {
      form: {
        price: 0,
        qty: 0,
        amount: 0
      },
      nonce: 0,
      events: [],
      localStorageData: '',
      firstFieldChanged: ''
    }
  },

  computed: {
    reversedEvents () {
      return [...this.events].reverse()
    }
  },

  methods: {
    updateFields (changedField, val) {
      if (!this.firstFieldChanged) {
        this.firstFieldChanged = changedField
      }
      this.debounceCalculation(changedField, val)
    },
    calculateFields (changedField, val) {
      if (this.firstFieldChanged) {
        const {price, qty} = this.form

        switch (this.firstFieldChanged) {
          case 'price':
            if (price && qty) this.form.amount = price * qty
            break
          case 'qty':
            if (qty && price) this.form.amount = price * qty
            break
        }

        if (changedField === 'amount') {
          const amount = Number(val)
          if (this.firstFieldChanged === 'price') {
            this.form.price = amount / qty
          } else {
            this.form.qty = amount / price
          }
          this.form.amount = amount
        }
      }
      this.logEvent({message: MESSAGES.FIELDS_UPDATE, data: this.form, type: MESSAGE_TYPES.success})
    },
    debounceCalculation (changedField, val) {
      clearTimeout(this.debounceTimer)
      this.debounceTimer = setTimeout(this.calculateFields(changedField, val), 300)
    },
    handleSubmit () {
      this.logEvent({message: MESSAGES.CLICK_ON_SAVE, data: this.form, type: MESSAGE_TYPES.success})
      this.saveToLocalStorage()
    },
    saveToLocalStorage () {
      setTimeout(() => {
        if (this.form.amount % 2 === 0) {
          this.nonce++
          const dataToSave = {...this.form, nonce: this.nonce}
          localStorage.setItem('formData', JSON.stringify(dataToSave))
          this.localStorageData = JSON.stringify(dataToSave)
          this.logEvent({message: MESSAGES.RESULT_IS_SAVE, data: dataToSave, type: MESSAGE_TYPES.success})
        } else {
          this.logEvent({message: MESSAGES.SUM_MUST_BE_EVEN, data: this.form, type: MESSAGE_TYPES.error})
        }
      }, 1000)
    },
    logEvent ({message, data, type}) {
      this.events.push({
        date: new Date().toLocaleTimeString(),
        message,
        data: JSON.stringify(data),
        type
      })
    }
  },
  created () {
    this.localStorageData = localStorage.getItem('formData') || ''
    if (this.localStorageData) {
      const {price, qty, amount, nonce} = JSON.parse(this.localStorageData)
      this.form.price = price
      this.form.qty = qty
      this.form.amount = amount
      this.nonce = nonce
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 60px 20px;
  max-width: 960px;
  background-color: #ccc;
}

.cols {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
}

.mt-10 {
  margin-top: 40px;
}

.cursor-pointer {
  cursor: pointer;
}
</style>
