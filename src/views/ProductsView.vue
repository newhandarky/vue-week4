<template>
  <div class="container">
    <div class="text-end mt-4">
      <button class="btn btn-primary" @click="addModel">建立新的產品</button>
    </div>
    <table class="table mt-4 table table-hover">
      <thead>
        <tr>
          <th width="120">分類</th>
          <th>產品名稱</th>
          <th width="120">原價</th>
          <th width="120">售價</th>
          <th width="100">是否啟用</th>
          <th width="120">編輯</th>
        </tr>
      </thead>
      <tbody>
        <!-- 改綁商品標題 -->
        <tr v-for="(product) in products" :key="product.title">
          <td>{{ product.category }}</td>
          <td>{{ product.title }}</td>
          <td class="text-end">{{ product.origin_price }}</td>
          <td class="text-end">{{ product.price }}</td>
          <td>
            <span v-if="product.is_enabled === 1" class="text-success">啟用</span>
            <span v-else class="text-danger">未啟用</span>
          </td>
          <td>
            <div class="btn-group">
              <button type="button" class="btn btn-outline-primary btn-sm" @click="editModel(product)">編輯</button>
              <button type="button" class="btn btn-outline-danger btn-sm" @click="delModel(product)">刪除</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  <!-- Modal -->
  <div id="productModal" ref="productModal" class="modal fade" tabindex="-1" aria-labelledby="productModalLabel"
    aria-hidden="true">
    <div class="modal-dialog modal-xl">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 id="productModalLabel" class="modal-title"><span>新增產品</span></h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-sm-4">
              <div class="mb-2">
                <div class="mb-3">
                  <label for="imageUrl" class="form-label">輸入圖片網址</label>
                  <input type="text" class="form-control" v-model="tempProduct.imageUrl" placeholder="請輸入圖片連結">
                </div>
                <img class="img-fluid" :src="tempProduct.imageUrl" alt="商品主圖">
              </div>
              <h3>多圖新增</h3>
              <!-- 若是產品物件存在imagesUrl陣列, 空陣列也算 -->
              <div v-if="Array.isArray(tempProduct.imagesUrl)">
                <!-- 以該陣列跑迴圈, key值改綁圖片路徑 -->
                <div class="mb-2" v-for="(image, key) in tempProduct.imagesUrl" :key="image">
                  <div class="mb-3">
                    <label for="imageUrl" class="form-label">輸入圖片網址</label>
                    <input type="text" class="form-control" v-model="tempProduct.imagesUrl[key]" placeholder="請輸入圖片連結">
                  </div>
                  <img v-if="image" class="img-fluid" :src="image">
                  <img v-else class="img-fluid" :src="defaultImageUrl">
                </div>
                <!-- 判斷圖片陣列長度是否為0 或 最後的值是否為空 -->
                <div v-if="!tempProduct.imagesUrl.length || tempProduct.imagesUrl[tempProduct.imagesUrl.length - 1]">
                  <!-- 若是完全沒圖片或是沒有空白的圖片則顯示新增按鈕 -->
                  <button class="btn btn-outline-primary btn-sm d-block w-100"
                    @click="tempProduct.imagesUrl.push('')">新增圖片 </button>
                </div>
                <div v-else>
                  <button class="btn btn-outline-danger btn-sm d-block w-100"
                    @click="tempProduct.imagesUrl.pop()">刪除圖片</button>
                </div>
              </div>
              <div v-else>
                <!-- 若是 product 物件沒有 imagesUrl 屬性時顯示 -->
                <button class="btn btn-outline-primary btn-sm d-block w-100" type="button"
                  @click="createImages">新增圖片</button>
              </div>

            </div>
            <div class="col-sm-8">
              <div class="mb-3">
                <label for="title" class="form-label">標題</label>
                <input id="title" type="text" class="form-control" placeholder="請輸入標題" v-model="tempProduct.title">
              </div>

              <div class="row">
                <div class="mb-3 col-md-6">
                  <label for="category" class="form-label">分類</label>
                  <input id="category" type="text" class="form-control" placeholder="請輸入分類"
                    v-model="tempProduct.category">
                </div>
                <div class="mb-3 col-md-6">
                  <label for="unit" class="form-label">單位</label>
                  <input id="unit" type="text" class="form-control" placeholder="請輸入單位" v-model="tempProduct.unit">
                </div>
              </div>

              <div class="row">
                <div class="mb-3 col-md-6">
                  <label for="origin_price" class="form-label">原價</label>
                  <input id="origin_price" type="number" min="0" class="form-control" placeholder="請輸入原價"
                    v-model.number="tempProduct.origin_price">
                </div>
                <div class="mb-3 col-md-6">
                  <label for="price" class="form-label">售價</label>
                  <input id="price" type="number" min="0" class="form-control" placeholder="請輸入售價"
                    v-model.number="tempProduct.price">
                </div>
              </div>
              <hr>

              <div class="mb-3">
                <label for="description" class="form-label">產品描述</label>
                <textarea id="description" type="text" class="form-control" placeholder="請輸入產品描述"
                  v-model="tempProduct.description"></textarea>
              </div>
              <div class="mb-3">
                <label for="content" class="form-label">說明內容</label>
                <textarea id="content" type="text" class="form-control" placeholder="請輸入說明內容"
                  v-model="tempProduct.content"></textarea>
              </div>
              <div class="mb-3">
                <div class="form-check">
                  <input id="is_enabled" class="form-check-input" type="checkbox" v-model="tempProduct.is_enabled"
                    :true-value="1" :false-value="0">
                  <label class="form-check-label" for="is_enabled">是否啟用</label>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">取消</button>
          <button type="button" class="btn btn-primary" @click="addProduct">確認</button>
        </div>
      </div>
    </div>
  </div>
  <div id="delProductModal" ref="delProductModal" class="modal fade" tabindex="-1" aria-labelledby="delProductModalLabel"
    aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content border-0">
        <div class="modal-header bg-danger text-white">
          <h5 id="delProductModalLabel" class="modal-title"><span>刪除產品</span></h5>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          是否刪除<strong class="text-danger"></strong> 商品(刪除後將無法恢復)。
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">取消</button>
          <button type="button" class="btn btn-danger" @click="deleteProduct">確認刪除</button>
        </div>
      </div>
    </div>
  </div>
  <!-- Modal -->
</template>

<script type="module">
import * as bootstrap from 'bootstrap/dist/js/bootstrap.min.js'
import axios from 'axios'
import Swal from 'sweetalert2'

// 先將model變數放最外層以便取用
let productModal = null
let delProductModal = null

export default {
  data () {
    return {
      apiUrl: 'https://vue3-course-api.hexschool.io/v2',
      apiPath: 'newhandarky',
      products: [],
      tempProduct: {
        imagesUrl: []
      },
      defaultImageUrl: 'https://www.ils.com.tw/zh/Up_files/webskin/RWD/include/images/type3album_blank.png'
    }
  },
  methods: {
    checkAdmin () {
      const url = `${this.apiUrl}/api/user/check`
      axios.post(url).then((res) => {
        // 驗證完畢後取得產品列表
        this.getData()
      }).catch((err) => {
        Swal.fire({
          title: `${err.data.message}`,
          icon: 'error'
        }).then(() => {
          location = './login.html'
        })
      })
    },

    getData () {
      const url = `${this.apiUrl}/api/${this.apiPath}/admin/products`
      axios.get(url).then((res) => {
        this.products = res.data.products
      }).catch((err) => {
        Swal.fire({
          title: `${err.data.message}`,
          icon: 'error'
        })
      })
    },

    toggleUpdateModel (str) {
      str === 'show' ? productModal.show() : productModal.hide()
    },

    toggleDeleteModel (str) {
      str === 'show' ? delProductModal.show() : delProductModal.hide()
    },

    addModel () {
      this.tempProduct = {
        imagesUrl: []
      }
      this.toggleUpdateModel('show')
    },

    editModel (product) {
      this.tempProduct = { ...product }
      this.toggleUpdateModel('show')
    },

    delModel (product) {
      this.tempProduct = { ...product }
      this.toggleDeleteModel('show')
    },

    addProduct () {
      const url = `${this.apiUrl}/api/${this.apiPath}/admin/product`
      // 若是沒有ID改呼叫編輯
      if (this.tempProduct.id) {
        this.updateProduct()
      } else {
        axios.post(url, { data: this.tempProduct }).then((res) => {
          Swal.fire({
            title: '新增成功',
            icon: 'success'
          }).then(() => {
            this.getData()
            this.toggleUpdateModel()
          })
        }).catch((err) => {
          Swal.fire({
            title: `${err.data.message}`,
            icon: 'error'
          })
        })
      }
    },

    deleteProduct () {
      const url = `${this.apiUrl}/api/${this.apiPath}/admin/product/${this.tempProduct.id}`
      axios.delete(url).then((res) => {
        Swal.fire({
          title: '刪除成功',
          icon: 'success'
        }).then(() => {
          this.getData()
          this.toggleDeleteModel()
        })
      }).catch((err) => {
        Swal.fire({
          title: `${err.data.message}`,
          icon: 'error'
        })
      })
    },

    updateProduct () {
      const url = `${this.apiUrl}/api/${this.apiPath}/admin/product/${this.tempProduct.id}`
      axios.put(url, { data: this.tempProduct }).then((res) => {
        Swal.fire({
          title: '修改成功',
          icon: 'success'
        }).then(() => {
          this.getData()
          this.toggleUpdateModel()
        })
      }).catch((err) => {
        Swal.fire({
          title: `${err.data.message}`,
          icon: 'error'
        })
      })
    },

    createImages () {
      this.tempProduct.imagesUrl = ['']
    }
  },
  mounted () {
    // 綁定編輯的model
    productModal = new bootstrap.Modal(document.getElementById('productModal'), {
      keyboard: false
    })
    // 綁定刪除的model
    delProductModal = new bootstrap.Modal(document.getElementById('delProductModal'), {
      keyboard: false
    })
    // 從cookie取出登入時存入的token
    const token = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/, '$1')
    // 將token設定到axios的預設header裡
    axios.defaults.headers.common.Authorization = token
    // 確認登入狀態
    this.checkAdmin()
  }
}
</script>

<style scoped>
  body {
    display: block;
  }
</style>
