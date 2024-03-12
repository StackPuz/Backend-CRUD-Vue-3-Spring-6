<template>
  <div class="container">
    <div class="row">
      <div class="col">
        <form method="post" @submit.prevent="create()">
          <div class="row">
            <div class="form-group col-md-6 col-lg-4">
              <label for="order_detail_order_id">Order Id</label>
              <input id="order_detail_order_id" name="id.orderId" class="form-control form-control-sm" v-model="orderDetail.id.orderId" type="number" required />
              <span v-if="errors.id" class="text-danger">{{errors.id}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="order_detail_no">No</label>
              <input id="order_detail_no" name="id.no" class="form-control form-control-sm" v-model="orderDetail.id.no" type="number" required />
              <span v-if="errors.id" class="text-danger">{{errors.id}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="order_detail_product_id">Product</label>
              <select id="order_detail_product_id" name="product.id" class="form-control form-control-sm" v-model="orderDetail.product.id" required>
                <option v-for="product in products" :key="product" :value="product.id" :selected="orderDetail.product && orderDetail.product.id == product.id">{{product.name}}</option>
              </select>
              <span v-if="errors.product" class="text-danger">{{errors.product}}</span>
            </div>
            <div class="form-group col-md-6 col-lg-4">
              <label for="order_detail_qty">Qty</label>
              <input id="order_detail_qty" name="qty" class="form-control form-control-sm" v-model="orderDetail.qty" type="number" required />
              <span v-if="errors.qty" class="text-danger">{{errors.qty}}</span>
            </div>
            <div class="col-12">
              <router-link class="btn btn-sm btn-secondary" :to="getRef('/orderDetail')">Cancel</router-link>
              <button class="btn btn-sm btn-primary">Submit</button>
            </div>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>
<script>
import Service from './Service'
import Util from"../../util"

export default {
  name: 'OrderDetailCreate',
  data() {
    return {
      orderDetail: {product:{},id:{}},
      products: [],
      errors: {}
    }
  },
  mounted() {
    this.get().finally(() => {
      this.initView(true)
    })
  },
  methods: {
    ...Util,
    get() {
      return Service.create().then(response => {
        this.products = response.data.products
      })
    },
    create() {
      Service.create(this.orderDetail).then(() => {
        this.$router.push(this.getRef('/orderDetail'))
      }).catch((e) => {
        if (e.response.data.errors) {
          this.errors = e.response.data.errors
        }
        else {
          alert(e.response.data.message)
        }
      })
    }
  }
}
</script>
