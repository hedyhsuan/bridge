<template>
    <div>
      <div class="pro_bg">
        <div class="col-12 p-0">
          <div class="productBanner">
              <img class="bannerImg w-100" src="https://imgur.com/s76vcQO.jpg" alt="">
          </div>
        </div>
      <div class="container product-wrap">
        <div class="row justify-content-center" >
                  <!-- breadcrumb -->
          <div class="col-12 breadcrumb_group">
             <nav aria-label="breadcrumb">
               <ol class="breadcrumb">
                 <li class="breadcrumb-item">
                   <router-link :to="{name:'Category',params:{category:'allproduct'}}">所有商品</router-link>
                   </li>
                 <li class="breadcrumb-item" aria-current="page">
                  <router-link :to="{name:'Category',params:{category:category}}">{{category}}</router-link>
                   </li>
               </ol>
             </nav>
          </div>

          <div class="sidebar col-md-3" >
            <CategorySidebar @getProduct="getProducts" :category="category" ></CategorySidebar>
            <!-- 點擊後更新頁面 -->
          </div>
          <div class="col-md-9 row">
            <productlist
              class="col-md-4 col-sm-6 col-12"
              v-for="item in products" :key="item.id"  
              :selectitem="item"
              v-on="$listeners"
              ></productlist>
              <!-- $listeners傳遞productlist中我的最愛更新至此父層dashboard更新數量 -->
            <div class="col-12" v-show="pagination">
              <pagination  :page= pagination @switchPage="getProducts"></pagination>
              <!-- 將此頁的data pagination動態的存進page中,子頁面再用prop接收 -->
              <!-- @switchPage是pagination這個component向外觸發此實體getProduct()的媒介小鈴鐺 --> 
            </div>
          </div>

        </div>
       </div>
      </div>
    </div>

</template>

<script>
// import $ from 'jquery';
import pagination from "../../pagination.vue";
import productlist from "../../productlist";
import CategorySidebar from "../../category_sidebar"
import {getProductsAPI,getAllProductsAPI} from "../../../api/api"

export default {

    data() {
        return {
            allProducts:[],
            //所有商品資料
            products:[],
            //分頁的商品資料
            pagination:{},
            category:"",
            //產品類別
        }
    },
    components:{
        pagination,
        productlist,
        CategorySidebar
      },

    methods: {
      getProducts(page){
        this.category=this.$route.params.category
        if (this.category==='allproduct'){
          console.log("getAll")
           getProductsAPI(page).then((res)=>{
               this.products=res.data.products;
               this.pagination=res.data.pagination;
            
           })
        }else{
          this.SortProduct()
        }
      },

    SortProduct(){
      getAllProductsAPI().then((res)=>{
          this.allProducts=res.data.products;
          this.products=[];
         //把原本的資料清空
           this.pagination=res.data.pagination;
          this.allProducts.forEach((item)=>{

          if(item.category == this.category){
            console.log(item.category)
   
            this.products.push(item)
          }else if(this.category ==='sale' ){
            if(item.original_price !== item.price){
              console.log("getSale")
              this.products.push(item)
              }
          }
        })

      })
    }


    },

    
    created() { 
      this.getProducts()
        
    },

}

</script>

<style scoped>
.product-wrap{
 margin-top: 70px;
 margin-bottom: 30px;
}
@media (max-width:767px){
  .product-wrap{
    margin-top: 70px;
   }
}
@media (max-width:575px){
  .product-wrap{
    margin-top: 30px;
   }
}

.product_list{
  margin-top: 110px;
}
.products{
  margin-top: 100px;
}
#productImg{
  overflow: hidden;
  height: 40vh;
}

.productBanner{
  height: 400px;
  overflow: hidden;

}
@media(max-width:767px){
  .productBanner{
    height: 250px;
  }
}
.bannerImg{
  object-position: center -100px
} 


@media(max-width:767px){
  .sidebar{
    display: none;
  }
}


.breadcrumb{
  background-color:rgb(0,0,0,0);
  /* padding: 0 15px; */
  
}
.breadcrumb_group{
  display: none;
}
@media(max-width:767px){
  .breadcrumb_group{
    display: block;
  }
}
  
</style>