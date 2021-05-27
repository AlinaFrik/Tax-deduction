<template>
  <div class="popup">
    <div class="popup__card">
      <h3 class="popup__card__header">Налоговый вычет</h3>
      <div class="popup__card__close" @click="closePopup">
        <svg
          width="12"
          height="12"
          viewBox="0 0 12 12"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M7.6151 6.00057L11.6514 1.96311C11.7605 1.85778 11.8475 1.73179 11.9073 1.59248C11.9671 1.45318 11.9986 1.30335 12 1.15174C12.0013 1.00013 11.9724 0.849774 11.915 0.709449C11.8576 0.569124 11.7728 0.441638 11.6656 0.33443C11.5584 0.227222 11.4309 0.142438 11.2905 0.0850268C11.1502 0.0276153 10.9999 -0.00127433 10.8483 4.31116e-05C10.6967 0.00136056 10.5468 0.032859 10.4075 0.0927004C10.2682 0.152542 10.1422 0.239527 10.0369 0.348583L5.99943 4.3849L1.96311 0.348583C1.85778 0.239527 1.73179 0.152542 1.59248 0.0927004C1.45318 0.032859 1.30335 0.00136056 1.15174 4.31116e-05C1.00013 -0.00127433 0.849774 0.0276153 0.709449 0.0850268C0.569124 0.142438 0.441638 0.227222 0.33443 0.33443C0.227222 0.441638 0.142438 0.569124 0.0850268 0.709449C0.0276153 0.849774 -0.00127433 1.00013 4.31116e-05 1.15174C0.00136056 1.30335 0.032859 1.45318 0.0927004 1.59248C0.152542 1.73179 0.239527 1.85778 0.348583 1.96311L4.3849 5.99943L0.348583 10.0369C0.239527 10.1422 0.152542 10.2682 0.0927004 10.4075C0.032859 10.5468 0.00136056 10.6967 4.31116e-05 10.8483C-0.00127433 10.9999 0.0276153 11.1502 0.0850268 11.2905C0.142438 11.4309 0.227222 11.5584 0.33443 11.6656C0.441638 11.7728 0.569124 11.8576 0.709449 11.915C0.849774 11.9724 1.00013 12.0013 1.15174 12C1.30335 11.9986 1.45318 11.9671 1.59248 11.9073C1.73179 11.8475 1.85778 11.7605 1.96311 11.6514L5.99943 7.6151L10.0369 11.6514C10.1422 11.7605 10.2682 11.8475 10.4075 11.9073C10.5468 11.9671 10.6967 11.9986 10.8483 12C10.9999 12.0013 11.1502 11.9724 11.2905 11.915C11.4309 11.8576 11.5584 11.7728 11.6656 11.6656C11.7728 11.5584 11.8576 11.4309 11.915 11.2905C11.9724 11.1502 12.0013 10.9999 12 10.8483C11.9986 10.6967 11.9671 10.5468 11.9073 10.4075C11.8475 10.2682 11.7605 10.1422 11.6514 10.0369L7.6151 5.99943V6.00057Z"
            fill="#EA0029"
          />
        </svg>
      </div>
      <p class="popup__card__text text-grey">
        Используйте налоговый вычет чтобы погасить ипотеку досрочно. Размер
        налогового вычета составляет: не более 13% от своего официального
        годового дохода.
      </p>
      <form class="popup__card__form" @submit.prevent="addTaxDeduction">
        <b>Ваша зарплата в месяц</b>
        <input
          v-model="formattedSalary"
          class="popup__card__form__input input"
          :class="{ input_error: errorSalary }"
          placeholder="Введите данные"
          @blur="focusOut"
          @focus="focusIn"
        />
        <p v-if="errorSalary" class="input__error">
          Поле обязательно для заполнения
        </p>
        <p class="popup__card__form__text-btn text-btn" @click="calculateTax">
          Расcчитать
        </p>
        <div
          v-if="resultsTaxDeduction.length"
          class="popup__card__form__checkboxes"
        >
          <b>Итого можете внести в качестве досрочных:</b>
          <div
            v-for="resultTaxDeduction in resultsTaxDeduction"
            :key="resultTaxDeduction.year"
            class="popup__card__form__checkboxes__checkbox"
          >
            <input
              type="checkbox"
              v-model="choosenTaxDeduction"
              :value="resultTaxDeduction"
              :id="resultTaxDeduction.year"
              class="checkbox"
            />
            <label :for="resultTaxDeduction.year"
              >{{ resultTaxDeduction.sum }} рублей
              <span class="text-grey"
                >&nbsp;в {{ resultTaxDeduction.year }} год</span
              ></label
            >
          </div>
        </div>
        <div class="popup__card__form__options">
          <b class="popup__card__form__options__title">Что уменьшаем?</b>
          <div class="popup__card__form__options__tags">
            <span
              class="popup__card__form__options__tags__tag tag"
              :class="{ tag_active: optionToReduce === 'payment' }"
              @click="changeOptionToReduce('payment')"
              >Платёж</span
            >
            <span
              class="popup__card__form__options__tags__tag tag"
              :class="{ tag_active: optionToReduce === 'time' }"
              @click="changeOptionToReduce('time')"
              >Срок</span
            >
          </div>
        </div>
        <button type="submit" class="popup__card__form__btn btn">Добавить</button>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  name: "TaxPopup",
  data() {
    return {
      salary: null,
      formattedSalary: null,
      optionToReduce: "payment",
      errorSalary: false,
      resultsTaxDeduction: [],
      choosenTaxDeduction: [],
      percentageOfCostApartament: 260000,
    };
  },
  methods: {
    closePopup() {
      this.$emit("close");
    },
    addTaxDeduction() {
      // to add tax deduction
    },
    changeOptionToReduce(option) {
      this.optionToReduce = option;
      // to add logic of changed option
    },
    calculateTax() {
      if (!this.salary) return (this.errorSalary = true);
      this.resultsTaxDeduction = [];
      const deductionYear = this.salary * 12 * 0.13;
      const fullYears = Math.floor(this.percentageOfCostApartament / deductionYear);
      for (let i = 1; i <= fullYears; i++) this.resultsTaxDeduction.push({ year: i, sum: deductionYear });
      const residue = this.percentageOfCostApartament - deductionYear * fullYears;
      if (residue) this.resultsTaxDeduction.push({ year: fullYears + 1, sum: residue });
    },
    inputSalary() {},
    focusOut: function () {
      this.salary = this.formattedSalary.replace(/[^\d.]/g, "");
      if (!this.salary) this.salary = 0;
      this.formattedSalary = this.salary.toString().replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1 ") + " ₽";
    },
    focusIn: function () {
      this.errorSalary = false;
      this.formattedSalary = this.salary?.toString() || "";
    },
  },
};
</script>

<style scoped lang="scss"></style>
