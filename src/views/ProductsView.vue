<template>
  <Loading :active="isLoading"></Loading>
  <div class="text-end">
    <button
      class="btn btn-primary"
      type="button"
      @click="openModal(true)"
    >
      增加一個產品
    </button>
  </div>
  <table class="table mt-4">
  <thead>
    <tr>
      <th width="120">分類</th>
      <th>產品名稱</th>
      <th width="120">原價</th>
      <th width="120">售價</th>
      <th width="100">是否啟用</th>
      <th width="200">編輯</th>
    </tr>
  </thead>
  <tbody>
    <tr v-for ="item in products" :key ="item.id">
      <td>{{item.category}}</td>
      <td>{{item.title}}</td>
      <td class="text-right">
        {{ $filters.currency(item.origin_price) }}
      </td>
      <td class="text-right">
        {{ $filters.currency(item.price) }}
      </td>
      <td>
        <span class="text-success" v-if="item.is_enabled">啟用</span>
        <span class="text-muted" v-else>未啟用</span>
      </td>
      <td>
        <div class="btn-group">
          <button class="btn btn-outline-primary btn-sm"
          @click="openModal(false,item)">編輯</button>
          <button class="btn btn-outline-danger btn-sm"
          @click="delProductModal(item)">刪除</button>
        </div>
      </td>
    </tr>
  </tbody>
</table>
<pagination
  :pages="pagination"
  @emit-pages="getProducts"
></pagination>
<ProductModal
  ref="productModal"
  :product="tempProduct"
  @update-product="updateProduct"
>
</ProductModal>
<DelModal
  ref="delModal"
  :product="tempProduct"
  @delete-product="deleteProduct"
></DelModal>
</template>

<script>
import Pagination from '@/components/Pagination.vue';
import ProductModal from '../components/ProductModal.vue';
import DelModal from '../components/DelModal.vue';

export default {
  components: {
    ProductModal,
    DelModal,
    Pagination,
  },
  inject: ['emitter'],
  data() {
    return {
      products: [],
      pagination: {},
      tempProduct: {},
      isNew: false,
      isLoading: false,
    };
  },
  methods: {
    getProducts(page = 1) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/products/?page=${page}`;
      this.isLoading = true;
      this.$http.get(api)
        .then((res) => {
          this.isLoading = false;
          if (res.data.success) {
            this.products = res.data.products;
            this.pagination = res.data.pagination;
            console.log(res.data);
          }
        });
    },
    openModal(isNew, item) {
      if (isNew) {
        this.tempProduct = {};
      } else {
        this.tempProduct = { ...item };
      }
      this.isNew = isNew;
      const productComponent = this.$refs.productModal;
      productComponent.showModal();
    },
    updateProduct(item) {
      console.log(item);
      this.tempProduct = item;
      // 新增
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product`;
      let httpMethod = 'post';
      if (!this.isNew) {
        // 修改
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`;
        httpMethod = 'put';
      }
      const productComponent = this.$refs.productModal;
      this.$http[httpMethod](api, { data: this.tempProduct })
        .then((res) => {
          console.log(res);
          productComponent.hideModal();
          if (res.data.success) {
            this.getProducts();
            this.emitter.emit('push-message', {
              style: 'success',
              title: '更新成功',
            });
          } else {
            this.$httpMessageState(res, '產品更新');
          }
        });
    },
    delProductModal(item) {
      this.tempProduct = { ...item };
      const productComponent = this.$refs.delModal;
      productComponent.showModal();
    },
    deleteProduct(item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`;
      this.$http.delete(api)
        .then((res) => {
          console.log(api, res);
          if (res.data.success) {
            const productComponent = this.$refs.delModal;
            productComponent.hideModal();
            this.getProducts();
          }
        });
    },
  },
  created() {
    this.getProducts();
  },
};
</script>
