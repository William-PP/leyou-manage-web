<template>
  <div>
    <v-layout row class="px-3">
      <v-flex>
        <v-btn color="blue" dark>新增品牌</v-btn>
      </v-flex>
      <v-flex xs4>
        <v-text-field label="搜索" @blur="loadData" append-icon="search" v-model="key"></v-text-field>
      </v-flex>
    </v-layout>
    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <tr>
          <td class="text-xs-center">{{ props.item.id }}</td>
          <td class="text-xs-center">{{ props.item.name }}</td>
          <td class="text-xs-center">{{ props.item.letter }}</td>
          <td class="text-xs-center"><img :src="props.item.image" alt=""></td>
          <td class="text-xs-center">
            <v-btn flat icon color="green" small>
              <v-icon>edit</v-icon>
            </v-btn>

            <v-btn flat icon color="deep-orange" small>
              <v-icon dark>delete</v-icon>
            </v-btn>
          </td>
        </tr>
      </template>
    </v-data-table>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        totalBrands: 15,
        brands: [],
        loading: false,
        pagination: {},
        headers: [
          { text: '品牌ID', align: 'center', value: 'id'},
          { text: '品牌名称', align: 'center', sortable: false, value: 'name' },
          { text: '品牌LOGO', align: 'center', sortable: false,value: 'image' },
          { text: '首字母', align: 'center', value: 'letter' },
          { text: '操作', align: 'center', sortable: false, value: '' },
        ],
        key: "",
      }
    },
    created(){
      //  页面加载，应该去查询数据
      this.loadData();
    },
    watch:{
      pagination:{
        deep: true,
        handler(){
          this.loadData();
        }
      }
    },
    methods:{
      // 开启进度条
      loadData(){
        //  TODO 发起ajax
        this.loading= true;
        this.$http.get('/item/brand/page', {
          params: {
            page: this.pagination.page,
            rows: this.pagination.rowsPerPage,
            sortBy: this.pagination.sortBy,
            desc: this.pagination.descending,
            key: this.key
          }
        }).then(response => {
            console.log(response.data);
          this.brands = response.data.items;
          this.totalBrands = response.data.total;
          //关闭进度条
          this.loading = false;
        }).catch(error => {
            console.log(error);
          //关闭进度条
          this.loading = false;
        });
        /*setTimeout(() => {
          this.totalBrands = 15;
          this.brands = [
            {id:1,name:"小米",letter:"J",image:"https://img30.360buyimg.com/popshop/jfs/t1/21713/1/5027/5175/5c38740eE4ccfc381/0bedaba1e05119ac.jpg"}
          ]
          //关闭进度条
          this.loading = false;
        },1500)*/
      }
    }
  }
</script>
