<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="filteredOrders"
      hide-default-footer
      disable-sort
      :loading="isLoading"
    >
      <template v-slot:[`item.create_at`]="{ item }">
        <span v-if="item.create_at">{{ item.create_at | date }}</span>
      </template>
      <template v-slot:[`item[user.email]`]="{ item }">
        {{ item.user.email }}
      </template>
      <template v-slot:[`item.title`]="{ item }">
        <ul>
          <li
            v-for="(product, index) in item.products"
            :key="index"
          >
            {{ product.product.title }}
          </li>
        </ul>
      </template>
      <template v-slot:[`item.num`]="{ item }">
        <ul>
          <li
            v-for="(product, index) in item.products"
            :key="index"
          >
            {{ product.product.num }}  {{ product.product.unit }}
          </li>
        </ul>
      </template>
      <template v-slot:[`item.total`]="{ item }">
        {{ item.total | currency }}
      </template>
      <template v-slot:[`item.is_paid`]="{ item }">
        <span
          v-if="item.is_paid"
          class="green--text"
        >
          已付款
        </span>
        <span
          v-else
          class="grey--text"
        >
          尚未付款
        </span>
      </template>
    </v-data-table>
    <v-pagination
      v-if="pagination"
      v-model="pagination.current_page"
      :length="pagination.total_pages"
      :total-visible="5"
      class="mt-5"
      circle
      color="warning"
      @input="getOrders"
    />
  </div>
</template>

<script>
export default {
  name: 'AdminOrders',
  middleware: 'routerAuth',
  meta: {
    requiresAuth: true,
  },
  async asyncData({ $axios, $config: { apiPath, customPath } }, page = 1) {
    const res = await $axios.$get(`${apiPath}/api/${customPath
    }/admin/orders?page=${page}`);

    return {
      orders: res.orders,
      pagination: res.pagination,
      isLoading: false,
      modalIsLoading: false,
    };
  },
  data() {
    return {
      isLoading: false,
      modalIsLoading: false,
      headers: [
        { text: '訂購時間', value: 'create_at' },
        { text: 'Email', value: 'user.email' },
        { text: '商品名稱', value: 'title' },
        { text: '觀看期限', value: 'num' },
        { text: '訂單金額', value: 'total' },
        { text: '備註', value: 'message' },
        { text: '付款狀態', value: 'is_paid' },
      ],
      orders: [],
      pagination: {},
    };
  },
  computed: {
    filteredOrders() {
      return this.orders.filter((el) => {
        if (el.create_at) {
          return el;
        }
      });
    },
  },
  methods: {
    async getOrders(page) {
      this.isLoading = true;
      const res = await this.$axios.$get(`${this.$config.apiPath
      }/api/${this.$config.customPath}/admin/orders?page=${page}`);

      this.orders = res.orders;
      this.pagination = res.pagination;
      this.isLoading = false;
    },
  },
};
</script>

<style>

</style>
