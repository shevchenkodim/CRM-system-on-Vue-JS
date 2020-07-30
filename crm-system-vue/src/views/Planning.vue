<template>
  <div>
    <div class="page-title">
      <h3>Планирование</h3>
      <h4>{{info.bill | currency}}</h4>
    </div>

    <Loader v-if="loading" />

    <p class="center" v-else-if="!categories.length">
      Категорий пока нет.
      <router-link to="/categories">Добавить новую категорию</router-link>
    </p>

    <section v-else>
      <div>
        <p>
          <strong>Девушка:</strong>
          12 122 из 14 0000
        </p>
        <div class="progress">
          <div class="determinate green" style="width:40%"></div>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  name: "planing",
  data: () => ({
    loading: true,
    categories: [],
  }),
  computed: {
    ...mapGetters(["info"]),
  },
  async mounted() {
    const records = await this.$store.dispatch("fetchRecords");
    const categories = await this.$store.dispatch("fetchCategories");

    this.categories = categories.map((cat) => {
      const spend = records
        .filter((r) => r.categoryID === cat.id)
        .filter((r) => r.type === "outcome")
        .reduce((total, record) => {
          return (total += +record.amount);
        }, 0);

      const persent = (100 * spend) / cat.limit;
      const progressPersent = persent > 100 ? 100 : persent;
      const progressColor =
        persent < 60 ? "green" : persent < 100 ? "yellow" : "red";

      return {
        ...cat,
        progressPersent,
        progressColor,
        spend,
      };
    });

    this.loading = false;
  },
};
</script>