<template>
    <div>
      <div class="pro_bg">
        <div class="container product_list ">
          <div class="row pt-4 d-flex justify-content-between" >
            <div>
              <button >
                <router-link :to="{name: 'Bsproduct'}" >產品管理</router-link>
              </button>
              <button class="btn-dark">
                <router-link :to="{name: 'Coupon'}">優惠券</router-link>
              </button>
              <button class="btn-dark">
                 <router-link :to="{name:'Orderlist'}">訂單管理</router-link>
              </button>
            </div >
            <div>
              <button class="btn-dark" @click="openModal(true)">建立新產品</button>
              <button class="btn-dark" @click="logout()">登出</button>
            </div>

          </div>

          <table class="table mt-4">
            <thead>
                <tr>
                    <th width="100">分類</th>
                    <th>名稱</th>
                    <th width="80">原價</th>
                    <th width="80">售價</th>
                    <th width="80">啟用</th>
                    <th width="100">編輯</th>
                    <th width="100">刪除</th>
                </tr>
            </thead> 
           
           <tbody>
               <tr v-for="item in products" :key="item.id">
                   <td>{{item.category}}</td>
                   <td>{{item.title}}</td>
                   <td>{{item.original_price|currency}}</td>
                   <td>{{item.price | currency}}</td>
                   <td v-if="item.is_enabled" class="text-success">啟用 </td>
                   <td v-else>未啟用</td> 
                   <td>
                     <button class="btn btn-outline-dark btn-sm" @click="openModal(false,item)">編輯</button>
                   </td>
                   <td>
                     <button class="btn btn-outline-dark btn-sm" @click="openDel(item)">刪除</button>
                   </td>
               </tr>
           </tbody>

          </table>
          <pagination :page= pagination @switchPage="getProducts"></pagination>
      <!-- @switchPage是pagination這個component向外觸發此實體getProduct()的媒介小鈴鐺 -->
      
        </div>
       </div>

<!-- 單一商品 -->
       <div class="modal fade" id="productModal" tabindex="-1" role="dialog"
       aria-labelledby="exampleModalLabel" aria-hidden="true">
          <div class="modal-dialog modal-lg" role="document">
            <div class="modal-content border-0">
              <div class="modal-header bg-dark text-white">
                <h5 class="modal-title" id="exampleModalLabel">
                  <span>新增產品</span>
                </h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                   <span aria-hidden="true">&times;</span>
                </button>
              </div>
              <div class="modal-body">
                <div class="row">
                  <div class="col-sm-4">
                    <div class="form-group">
                      <label for="image">輸入圖片網址</label>
                      <input type="text" class="form-control" id="image"
                       placeholder="請輸入圖片連結" v-model="tempProduct.imageUrl">
                    </div>
                    <div class="form-group">
                      <label for="customFile">或 上傳圖片
                         <i class="fas fa-spinner fa-spin" v-if="status.fileUploading"></i>
                      </label>
                      <input type="file" id="customFile" class="form-control"
                     ref="files" @change="uploadFile">
                       <!-- **ref=”files”這行如果沒有的話會找不到檔案** -->
                    </div>
                    <img :src="tempProduct.imageUrl"
                      class="img-fluid" alt="">
                  </div>
                  <div class="col-sm-8">
                    <div class="form-group">
                      <label for="title" >標題</label>
                      <input type="text" class="form-control" id="title"
                        placeholder="請輸入標題" v-model="tempProduct.title">
                    </div>

                    <div class="form-row">
                      <div class="form-group col-md-6">
                        <label for="category">分類</label>
                        <input type="text" class="form-control" id="category"
                          placeholder="請輸入分類" v-model="tempProduct.category"> 
                      </div>
                      <div class="form-group col-md-6">
                        <label for="price">單位</label>
                        <input type="unit" class="form-control" id="unit"
                          placeholder="請輸入單位" v-model="tempProduct.unit">
                      </div>
                   </div>

                   <div class="form-row">
                    <div class="form-group col-md-6">
                    <label for="origin_price">原價</label>
                      <input type="number" class="form-control" id="origin_price"
                        placeholder="請輸入原價" v-model="tempProduct.original_price">
                    </div>
                    <div class="form-group col-md-6">
                      <label for="price">售價</label>
                      <input type="number" class="form-control" id="price"
                        placeholder="請輸入售價" v-model="tempProduct.price">
                    </div>
                   </div>
                   <hr>

                    <div class="form-group">
                      <label for="description">產品描述</label>
                      <textarea type="text" class="form-control" id="description"
                        placeholder="請輸入產品描述" v-model="tempProduct.description"></textarea>
                    </div>
                    <div class="form-group">
                      <label for="content">說明內容</label>
                      <textarea type="text" class="form-control" id="content"
                        placeholder="請輸入產品說明內容" v-model="tempProduct.content"></textarea>
                    </div>
                    <div class="form-group">
                      <div class="form-check">
                        <input class="form-check-input" type="checkbox"
                          id="is_enabled" v-model="tempProduct.is_enabled"
                          :true_value="1"
                          :false_value="0">
                          <!-- 後端data中0跟為false跟true -->
                        <label class="form-check-label" for="is_enabled">
                          是否啟用
                        </label>
                      </div>
                    </div>
                 </div>
               </div>
             </div>
              <div class="modal-footer">
                <button type="button" class="btn btn-outline-secondary" data-dismiss="modal">取消</button>
                <button type="button" class="btn btn-primary" @click="updateProduct()">確認</button>
              </div>
           </div>
         </div>
  
      </div>

       <!-- 確認刪除視窗 -->
      <div class="modal fade" id="delProductModal" tabindex="-1" role="dialog"
        aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
          <div class="modal-content border-0">
            <div class="modal-header bg-danger text-white">
              <h5 class="modal-title" id="exampleModalLabel">
                <span>刪除產品</span>
              </h5>
              <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              是否刪除 <strong class="text-danger"> {{tempProduct.title }}</strong> 商品(刪除後將無法恢復)。
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-outline-secondary" data-dismiss="modal(item)">取消</button>
              <button type="button" class="btn btn-danger" @click="deleteProduct">確認刪除</button>
            </div>
          </div>
        </div>

      </div>



    </div>
</template>


<script>
import pagination from "../../pagination"
import {getBsProductsAPI,logoutAPI,addBsProductAPI,updateBsProductAPI,deleteBsProductAPI,fileUploadAPI} from "../../../api/api"
export default {
    components:{
      pagination
    },
    data() {
        return {
            products:[],
            tempProduct:{},
            //新增或存放單筆要編輯的資料，綁定的內容格式要參考後端的API
            isNew:false,
            //判定是新增或是修改資料
            pagination:{},
            status:{
              fileUploading:false,
            },
            page:1,

        }
    },
    methods: {
      getProducts(page){
        getBsProductsAPI(page).then((res)=>{
           this.page=page
           //將頁面訊息儲存，編輯完後回到該頁面
            this.products=res.data.products;
            this.pagination=res.data.pagination;
 
        })

      },
    logout(){
       logoutAPI().then((res)=>{
          this.$router.push("/admin/login")
       })
    },

    openModal(isNew,item){
      //若是現有的資料則先帶入內容
      if(isNew){
        this.tempProduct={},
        this.isNew=true

      }else{
        this.tempProduct=Object.assign({},item);
          //ES6寫法，淺拷貝，避免item資料重疊
      }
      $("#productModal").modal("show");
    },

    openDel(item){
      this.tempProduct=Object.assign({},item);
      $("#delProductModal").modal("show")
    },

    updateProduct(){
      //打開modal的時候已先判定isNew
      if(this.isNew){
        addBsProductAPI({data:this.tempProduct}).then((res)=>{
             $("#productModal").modal("hide");
             this.getProducts();

        })
      }else{
        updateBsProductAPI(this.tempProduct.id,{data:this.tempProduct}).then((res)=>{
             $("#productModal").modal("hide");
             this.getProducts(this.page);
        })

      }

    },

    deleteProduct(){
      deleteBsProductAPI(this.tempProduct.id).then((res)=>{
          $("#delProductModal").modal("hide");
          this.getProducts();

      })

    },

    uploadFile(){
      console.log(this);
      //檔案被儲存的位置
      const uploadedFile =this.$refs.files.files[0];
      fileUploadAPI(uploadedFile).then((res)=>{
        console.log(res.data);
        if(res.data.success){
          
          this.$set(this.tempProduct,'imageUrl',res.data.imageUrl)
          }
      })

    }
    },
    created() {
    // const token = document.cookie.replace(
    //  /(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/,
    //  "$1"
    //  );
    // if (token === "") return
    //  //如果token沒有內容就直接return結束，若有就往下寫
    //  this.axios.defaults.headers.common["Authorization"] = token;
     this.getProducts();
 },

}
</script>

<style scoped>
.img-fluid{
  object-position: center center;
}
.btn-dark>a{
  color: white;
}

</style>
