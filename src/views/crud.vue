<template>
<div>
  <div class="container">
        <h3 class="text-center mt-3 mb-3">Address book</h3>
        <button type="button" class="btn btn-primary btn-sm mb-3" @click="addBtn()" data-toggle="modal"
            data-target="#addModal">Add
            contact</button>
        <!-- add contact彈窗-->
        <div class="modal fade" id="addModal" tabindex="-1" role="dialog"
            aria-labelledby="addModalTitle" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="addModalTitle">Add contact</h5>
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>
                    <div class="modal-body">
                        <form>
                            <div class="form-group">
                                <label for="addInputName">Name</label>
                                <input v-model="addItems.data.name" type="text" value="" class="form-control" aria-describedby="nameHelp" placeholder="Enter Name" >
                            </div>
                            <div class="form-group">
                                <label for="addInputTel">Tel</label>
                                <input v-model="addItems.data.tel" type="text" value="" class="form-control" placeholder="Enter Tel">
                            </div>
                            <div class="form-group">
                                <label for="addInputEmail">Email</label>
                                <input v-model="addItems.data.email" type="text" value="" class="form-control" placeholder="Enter email">
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                        <button type="submit" class="btn btn-primary" data-dismiss="modal" @click="submitBtn">Submit</button>
                    </div>
                </div>
            </div>
        </div>
        <table class="table table-bordered text-center">
            <thead class="thead-dark">
                <tr>
                    <th scope="col">#</th>
                    <th scope="col">Name</th>
                    <th scope="col">Tel</th>
                    <th scope="col">E-mail</th>
                    <th scope="col">Edit</th>
                </tr>
            </thead>
            <tbody id="contacts">
                <tr v-for="(item, i) in items" :key="i"> <!-- key是陣列索引位置 -->
               
                    <th>{{ item[0] }}</th>
                    <td class="name">{{ item[1] }}</td>
                    <td class="tel">{{ item[2] }}</td>
                    <td class="email">{{ item[3] }}</td>
                    <td>
                        <button type="button" class="btn btn-primary btn-sm mr-2" data-toggle="modal"
                            data-target="#editModal" @click="editBtn(item.id)">Edit</button>
                        <button type="button" class="btn btn-primary btn-sm delete" @click="deleteBtn(i)">Delete</button>
                    </td>
                </tr>
            </tbody>
        </table>
        <!-- 編輯彈窗 -->
                <div class="modal fade" id="editModal" tabindex="-1" role="dialog"
                    aria-labelledby="editModalCenterTitle" aria-hidden="true">
                    <div class="modal-dialog modal-dialog-centered" role="document">
                        <div class="modal-content">
                            <div class="modal-header">
                                <h5 class="modal-title" id="editModalCenterTitle">Add contact</h5>
                                <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                                    <span aria-hidden="true">&times;</span>
                                </button>
                            </div>
                            <div class="modal-body">
                                <form>
                                    <div class="form-group">
                                        <label for="EditInputName">Name</label>
                                        <input v-model="editItems.data.name" type="text" value="" class="form-control"
                                            aria-describedby="nameHelp" placeholder="Enter Name">
                                    </div>
                                    <div class="form-group">
                                        <label for="EditInputTel">Tel</label>
                                        <input v-model="editItems.data.tel" type="text" value="" class="form-control"
                                            placeholder="Enter Tel">
                                    </div>
                                    <div class="form-group">
                                        <label for="EditInputEmail">Email</label>
                                        <input v-model="editItems.data.email" type="text" value="" class="form-control"
                                            placeholder="Enter email">
                                    </div>
                                </form>
                            </div>
                            <div class="modal-footer">
                                <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                                <button type="submit" class="btn btn-primary" data-dismiss="modal" @click="editSubmit(editItems.data.id)">Submit</button>
                            </div>
                        </div>
                    </div>
                </div>
    </div>

</div>
</template>

<script>
import $ from 'jquery';
import axios from 'axios';
export default {
  name: "",
  props: {},
  data: function() {
    // 資料
    return {
      addItems: {
        data: {
          name:'',
          tel:'',
          email:'',
        }
      },
      editItems: {
        data: {
          id: '',
          name:'',
          tel:'',
          email:'',
        }
      },
      items: [],
      src: "" //追蹤 store用
    };
  },
  watch: {
    //監聽值
  },
  computed: {
    //相依的資料改變時才做計算方法
  },
  methods: {
    // 初始

    // 新增鈕
    addBtn() {
      $.ajax({
        url: "http://127.0.0.1:880/api/list.php",
        type: "GET",
        data: {}
      });
      this.addItems.data.name = '',
      this.addItems.data.tel = '',
      this.addItems.data.email = ''
    },
    // 新增完成
    submitBtn() {
      // var data = {
      //   name: this.addItems.data.name,
      //   tel: this.addItems.data.tel,
      //   email: this.addItems.data.email
      // };
      // $.ajax({
      //   url: "http://127.0.0.1:880/api/add.php",
      //   type: "POST",
      //   data: data,
      //   success: function() {
      //     alert("新增完成");
      //   }
      // });
      
       axios.post('http://127.0.0.1:880/api/add.php',{
          name: this.addItems.data.name,
          tel: this.addItems.data.tel,
          email: this.addItems.data.email,
        })
        .then((res) => {
          console.log(res)
          this.items.push(res.data)
        })
      // this.items.push({
      //   id: this.items.length + 1,
      //   name: this.addItems.data.name,
      //   tel: this.addItems.data.tel,
      //   email: this.addItems.data.email
      // });
    },

    // 編輯鈕 編輯頁input顯示新增的資料
    editBtn(id) {
      //取到點擊items.id的值

      this.items.forEach(el => {
        if (id === el.id) {
          this.editItems.data.id = el.id;
          this.editItems.data.name = el.name;
          this.editItems.data.tel = el.tel;
          this.editItems.data.email = el.email;
          // console.log('>>',id)
        }
      });
    },
    // 編輯完成
    editSubmit(id) {
      // console.log(this.items)
      // console.log('id:',this.id)
      this.items.forEach((el, i) => {
        //i是位置
        if (id !== el.id) return;
        //  if(id === el.id) {
        this.items[i].name = this.editItems.data.name; //i取得第幾個
        this.items[i].tel = this.editItems.data.tel;
        this.items[i].email = this.editItems.data.email;
        console.log(">>", el, i);
        //  }
      });
      var data = {
        id: this.editItems.data.id,
        name: this.editItems.data.name,
        tel: this.editItems.data.tel,
        email: this.editItems.data.email
      };
      $.ajax({
        url: "http://127.0.0.1:880/api/update.php",
        type: "POST",
        data: data,
        success: function() {
          //var a = JSON.parse(result);
          //console.log('>>',a.code)
          alert("編輯完成");
        }
      });
    },
    // 刪除鈕
    deleteBtn: function(i) {
      var data = {
        id: this.items.id
      }
      $.ajax({
        url: "http://127.0.0.1:880/api/del.php",
        type: 'POST',
        data: data,
        success: function(){
          //var a = JSON.parse(result);
          alert("刪除成功")
        }
      })
      this.items.splice(i, 1);
    }
  },
  //BEGIN--生命週期
  beforeCreate: function() {
    //實體初始化
  },
  created: function() {
    //實體建立完成。資料 $data 已可取得，但 $el 屬性還未被建立。
  },
  beforeMount: function() {
    //執行元素掛載之前。
  },
  mounted: function() {
    //元素已掛載， $el 被建立。
    // $.ajax({
    //   url: "http://127.0.0.1:880/api/list.php",
    //   type: "GET",
    //   data: {},
    //   success: result => {
    //     console.log(result);
    //     alert("ok");
    //   }
    // });
    axios.get('http://127.0.0.1:880/api/list.php').then((res) => {
        this.items = res.data.data
        console.log('>>',res.data)
      })
  },
  beforeUpdate: function() {
    //當資料變化時被呼叫，還不會描繪 View。
  },
  updated: function() {
    //當資料變化時被呼叫，還不會描繪 View。
  },
  beforeDestroy: function() {
    //實體還可使用。
  },
  destroyed: function() {
    //實體銷毀。
  }
  //END--生命週期
};
</script>

<style lang="scss" scoped>
</style>