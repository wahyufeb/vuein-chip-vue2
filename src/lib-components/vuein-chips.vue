<script lang="ts">
import Vue from 'vue';

type Chip = { value: string, valid: boolean }
type Chips = Array<Chip>
interface ChipMeta {
  chips: Chips,
  currentInput: string,
  wrongEmail: boolean,
}

export default /*#__PURE__*/Vue.extend({
  name: 'VueinChipsSample', // vue component name
  props: {
    value: {
      type: Array,
      default: () => [],
    },
    label: {
      type: String,
      default: null,
    },
    errorMessage: {
      type: String,
      default: null,
    },
    regexValidation: {
      type: RegExp,
      default: null,
    },
    validate: {
      type: Function,
      default: null,
    }
  },
  data(): ChipMeta {
    return {
      chips: [],
      currentInput: '',
      wrongEmail: false
    }
  },
  computed: {
    isInvalidField() {
      const filterInvalid: Chips = this.chips.filter((chip: Chip) => !chip.valid);
      return filterInvalid.length > 0 ? true : false;
    },
    getErrorField() {
      if (this.errorMessage) {
        return this.errorMessage;
      } else {
        return "Invalid value"
      }
    },
    /**
     * Function to validate chips
     * Validate using validate function if validate is set
     * Validate using regex if regexValidation is set
     */
    validationProcess() :boolean {
      if (this.validate) {
        return !this.validate(this.currentInput);
      } 
      if (this.regexValidation){
        return this.regexValidation ? this.regexValidation.test(this.currentInput) : true;
      }
      return true
    }
  },
  methods: {
    saveChip() {
      !(this.chips.some((chip: Chip) => chip.value === this.currentInput)) && this.chips.push({
        value: this.currentInput,
        valid: this.validationProcess
      });
      this.currentInput = '';
      this.$emit('input', this.chips);
    },
    deleteChip(index: number) {
      this.chips.splice(index, 1);
      this.$emit('input', this.chips);
    },
    backspaceDelete(params: any) {
      params.which === 8 && this.currentInput === '' && this.chips.splice(this.chips.length - 1) && this.$emit('input', this.chips);
    },
    onKeyup(e: any) {
      if (e.keyCode === 188) {
        this.currentInput = this.currentInput.replace(',', '')
        if (this.currentInput !== '') {
          this.saveChip()
        }
      }
    }
  },
});
</script>

<template>
  <div class="app">
    <label class="chip-label" :class="{'chip-label--invalid':isInvalidField}" v-if="label" for="chip">{{label}}</label>
    <div class="chips" :class="{ 'chips--invalid': isInvalidField }">
      <div class="chip__item chip--valid" :class="{ 'chip--invalid': !chip.valid }" v-for="(chip, i) of chips" :key="i">
        <p>{{ chip.value }}</p>
        <div class="chip__item__close" @click="deleteChip(i)">
          <i class="close-icon"></i>
        </div>
      </div>
      <input v-model="currentInput" @keypress.enter="saveChip" @keydown.delete="backspaceDelete" @keyup="onKeyup">
    </div>
    <span class="chip-message-error" v-if="isInvalidField">{{ getErrorField }}</span>
  </div>
</template>

<style scoped>
.app {
  --danger-color: #ff0000;
}

.chip-label--invalid {
  color: var(--danger-color);
  margin-bottom: 20px;
}
.chips {
  width: 400px;
  border: 1px solid #ccc;
  display: flex;
  flex-wrap: wrap;
  align-content: space-between;
  padding: 2px;
  border-radius: 10px;
  margin: 5px 0;
}

.chips--invalid {
  border-color: var(--danger-color);
}

.chip__item {
  padding: 4px 8px;
  border: 1px solid #ccc;
  border-radius: 10px;
  display: flex;
  align-items: center;
  margin: 3px 0;
  margin-right: 4px;
}

.chip__item p {
  margin: 0;
}

.chip__item .chip__item__close {
  cursor: pointer;
  margin-left: 6px;
  text-transform: lowercase;
  font-family: 'Arial';
  font-weight: bold;
}

.chip--valid {
  background: #e0e0e0;
  color: #000;
}

.chip--invalid {
  background: var(--danger-color);
  color: #fff;
}

input {
  flex: 1;
  width: 30px;
  border: none;
  outline: none;
  padding: 10px;
}

.chip-message-error {
  color: var(--danger-color);
}

/* Icon */
.close-icon {
  box-sizing: border-box;
  position: relative;
  display: block;
  width: 22px;
  height: 22px;
  border: 2px solid transparent;
  border-radius: 40px
}

.close-icon::after,
.close-icon::before {
  content: "";
  display: block;
  box-sizing: border-box;
  position: absolute;
  width: 16px;
  height: 2px;
  background: currentColor;
  transform: rotate(45deg);
  border-radius: 5px;
  top: 8px;
  left: 1px
}

.close-icon::after {
  transform: rotate(-45deg)
}
</style>
