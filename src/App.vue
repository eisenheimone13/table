<template>
  <div id="app">
    <div class="container">
      <div class="items">
        <div class="table-filters">
          <form @submit.prevent="filterProd()">
            <div class="table-filter">
              <select
                name="column"
                class="form-control"
                v-model="selectedField"
              >
                <option value="" selected disabled>Choose field</option>
                <option
                  v-for="(field, i) in formFields"
                  :key="i"
                  :value="field"
                >
                  {{ field[0] }}
                </option>
              </select>
            </div>
            <div class="table-filter">
              <select
                name="condition"
                class="form-control"
                v-model="selectedCondition"
              >
                <option value="" selected disabled>Choose condition</option>
                <option v-for="(cond, i) in filters" :key="i">
                  {{ cond }}
                </option>
              </select>
            </div>
            <div class="table-filter">
              <input placeholder="Условие" type="text" v-model="value" />
            </div>
            <div class="table-filter">
              <button type="submit">Поиск</button>
              <button @click.prevent="clearFilter">Сбросить</button>
            </div>
          </form>
        </div>
        <div class="table">
          <table style="width: 100%">
            <thead>
              <tr class="table-head">
                <th>Дата</th>
                <th>Название</th>
                <th>Количество</th>
                <th>Расстояние</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, i) in paginationProduct" :key="i">
                <td>{{ item.date }}</td>
                <td>{{ item.title }}</td>
                <td>{{ item.pcs }}</td>
                <td>{{ item.distance }}</td>
              </tr>
            </tbody>
          </table>
          <div class="pagination-options">
            <button @click="prevPage">Previous</button>
            <button @click="nextPage" :disabled="!nextPage">Next</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// не работал с БД SQL, в моем опыте базы были только noSQL
// если есть необходимоть можно сэмулировать локальный сервер для выдачи данных и так же сделать таблицу из компонентов и пропсов
export default {
  name: "App",
  data() {
    return {
      products: [
        { date: "22.02.2020", title: "Vanea", pcs: 3, distance: 254 },
        { date: "22.05.2021", title: "Jhon", pcs: 7, distance: 234 },
        { date: "12.03.2020", title: "Walker", pcs: 3, distance: 456 },
        { date: "28.02.2022", title: "Haker", pcs: 2, distance: 231 },
        { date: "24.05.2019", title: "Appro", pcs: 8, distance: 145 },
        { date: "12.03.2019", title: "Elan", pcs: 4, distance: 768 },
        { date: "28.09.2021", title: "Andro", pcs: 15, distance: 850 },
        { date: "05.05.2020", title: "Petro", pcs: 23, distance: 496 },
        { date: "02.07.2022", title: "Valeriko", pcs: 17, distance: 542 },
        { date: "18.11.2018", title: "Josan", pcs: 4, distance: 855 },
      ],
      filters: ["greater", "less", "contains", "equal"],
      selectedField: "",
      selectedCondition: "",
      value: "",
      pageSize: 6,
      currentPage: 1,
    };
  },
  computed: {
    formFields() {
      const fields = [];
      let arr = this.products;
      for (const key in arr[0]) {
        let temp = arr[0][key];
        let type = typeof temp;
        fields.push([key, type]);
      }
      return fields;
    },
    filteredProducts() {
      let filter = this.selectedField;
      let condition = this.selectedCondition;
      let value = this.value;

      if (filter && condition && value) {
        return filter[1] === "number"
          ? this.products.filter((item) => {
              let val = item[filter[0]];
              let cond = Number(value);

              return condition === "equal"
                ? val === cond
                : condition === "greater"
                ? val > cond
                : condition === "less"
                ? val < cond
                : "";
            })
          : this.products.filter((p) =>
              condition === "contains"
                ? p[filter[0]].toLowerCase().includes(value.toLowerCase())
                : ""
            );
      } else {
        return this.products;
      }
    },
    paginationProduct() {
      return this.filteredProducts.filter((row, index) => {
        let start = (this.currentPage - 1) * this.pageSize;
        let end = this.currentPage * this.pageSize;
        if (index >= start && index < end) return true;
      });
    },
  },
  methods: {
    nextPage() {
      if (this.currentPage * this.pageSize < this.filteredProducts.length)
        this.currentPage++;
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--;
    },
    clearFilter() {
      this.selectedField = "";
      this.selectedCondition = "";
      this.value = "";
    },

  },
};
</script>

<style lang="scss">
@import "@/assets/styles/variables.scss";
#app {
  background-color: $main-background;
}
body {
  background-color: $main-background;
}

*,
*:after,
*:before {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
  outline: 0;
}
.container {
  max-width: 1300px;
  margin: 0 auto;
}
.items {
  margin-top: 60px;
  display: flex;
  flex-direction: row;
}
.table-filters {
  width: 100%;
  flex: 30%;
  background-color: #fff;
  margin-right: 6px;
  padding: 16px;
}
.table-filter {
  margin-bottom: 16px;
  input,
  select {
    min-width: 216px;
    height: 26px;
    border: none;
    background-color: #fff;
    color: #1b1b1b;
    box-shadow: 0 0 5px 0 rgba($color: #000000, $alpha: 0.2);
  }
  input {
    padding-left: 5px;
  }
}
.table {
  display: flex;
  flex-direction: column;
  flex: 70%;
  background-color: #fff;
  box-shadow: 0 0 5px 0 rgba($color: #000000, $alpha: 0.2);
  padding: 16px;
  table,
  th,
  td {
    padding: 5px;
    text-align: left;
    height: 26px;
    border: 1px solid #cfcfcf;
    border-collapse: collapse;
  }
  .pagination-options {
    margin-top: 16px;
  }
}
.table-head {
  font-size: 18px;
}
button {
  cursor: pointer;
  min-width: 100px;
  margin-right: 16px;
  height: 31px;
  border-radius: 3px;
  border: none;
  font-size: 14px;
  background-color: #fff;
  color: #1b1b1b;
  box-shadow: 0 0 5px 0 rgba($color: #000000, $alpha: 0.2);
  &:disabled {
    background-color: #1b1b1b;
  }
}
</style>
