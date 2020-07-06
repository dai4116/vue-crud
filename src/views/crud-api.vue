<template>
  <div>
    <div class="container">
      <h3 class="text-center mt-3 mb-3">Address book</h3>
      <button type="button" class="btn btn-primary btn-sm mb-3" @click="addBtn()" data-toggle="modal"
        data-target="#addModal">
        Add
        contact
      </button>
      <h6 class="d-inline-block ml-3">篩選地區</h6>
      <select v-model="selected" class="form-control filterSelect d-inline-block" style="width:200px">
        <option value="" disabled>--請選擇--</option>
        <option :value="item" v-for="(item,i) in selectData" :key="i">{{ item.text }}</option>
      </select>
      <div class="filters-container">
        <div class="form-check form-check-inline">
          <input v-model="checkAll" class="form-check-input" type="checkbox" id="checkAll" value="all">
          <label class="form-check-label" for="all">all</label>
        </div>
        <div v-for="(item,i) in checkData" :key="i" class="form-check form-check-inline">
          <input v-model="checkname" :value="item" class="form-check-input" type="checkbox">
          <label :for="item.text" class="form-check-label">{{item.text}}</label>
        </div>
      </div>
      <!-- add contact彈窗-->
      <div class="modal fade" id="addModal" tabindex="-1" role="dialog" aria-labelledby="addModalTitle"
        aria-hidden="true">
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
                  <input v-model="addItems.data.name" type="text" value class="form-control" aria-describedby="nameHelp"
                    placeholder="Enter Name" />
                </div>
                <div class="form-group">
                  <label for="addInputTel">Tel</label>
                  <input v-model="addItems.data.tel" type="text" value class="form-control" placeholder="Enter Tel" />
                </div>
                <div class="form-group">
                  <label for="addInputEmail">Email</label>
                  <select v-model="addSelected" class="form-control addSelect">
                    <option value="" disabled>--請選擇--</option>
                    <option :value="item" v-for="(item,i) in selectData" :key="i">{{ item.text }}</option>
                  </select>
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
          <tr v-for="(item, i) in items" :key="i">
            <!-- key是陣列索引位置 -->

            <th>{{ item[0] }}</th>
            <td class="name">{{ item[1] }}</td>
            <td class="tel">{{ item[2] }}</td>
            <td class="email">{{ item[3] }}</td>
            <td>
              <button type="button" class="btn btn-primary btn-sm mr-2" data-toggle="modal" data-target="#editModal"
                @click="editBtn(item[0])">Edit</button>
              <button type="button" class="btn btn-primary btn-sm delete" @click="deleteBtn(i)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
      <!-- 編輯彈窗 -->
      <div class="modal fade" id="editModal" tabindex="-1" role="dialog" aria-labelledby="editModalCenterTitle"
        aria-hidden="true">
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
                  <input v-model="editItems.data.name" type="text" value class="form-control"
                    aria-describedby="nameHelp" placeholder="Enter Name" />
                </div>
                <div class="form-group">
                  <label for="EditInputTel">Tel</label>
                  <input v-model="editItems.data.tel" type="text" value class="form-control" placeholder="Enter Tel" />
                </div>
                <div class="form-group">
                  <label for="EditInputEmail">Email</label>
                  <select v-model="editSelected" class="form-control addSelect">
                    <option value="" disabled>--請選擇--</option>
                    <option :value="item" v-for="(item,i) in selectData" :key="i">{{ item.text }}</option>
                  </select>
                </div>
              </form>
            </div>
            <div class="modal-footer">
              <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
              <button type="submit" class="btn btn-primary" data-dismiss="modal"
                @click="editSubmit(editItems.data.id)">Submit</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // import $ from "jquery";
  import axios from "axios";
  import qs from "qs";
  import _ from "lodash"
  export default {
    name: "",
    props: {},
    data: function () {
      // 資料
      return {
        addItems: {
          data: {
            name: "",
            tel: "",
          }
        },
        editItems: {
          data: {
            id: "",
            name: "",
            tel: "",
          }
        },
        items: [],
        itemsAll: [], //新的全部的資料 select用
        selected: "",
        checkname: [],
        addSelected: "",
        editSelected: "",
        checkData: [{
            text: "Kaohsiung",
            value: "Kaohsiung"
          },
          {
            text: "Taichung",
            value: "Taichung"
          },
          {
            text: "Taipei",
            value: "Taipei"
          }
        ],
        selectData: [{
            text: "Kaohsiung",
            value: "Kaohsiung"
          },
          {
            text: "Taichung",
            value: "Taichung"
          },
          {
            text: "Taipei",
            value: "Taipei"
          }
        ],
        src: "" //追蹤 store用
      };
    },
    watch: {
      //監聽值
      'selected'(val) {
        console.log(">>", val, this.items)
        var selectItem = []
        this.itemsAll.forEach(el => {
          //  console.log(el)
          if (val.value === el[3]) {
            selectItem.push(el)
          }
        });
        this.items = selectItem
      },
      // 'selected.value'(val){
      //   console.log("1111",val)
      // },
      'checkname'(val) {
        var checkItem = []
        val.forEach(el => {
          this.itemsAll.forEach(el2 => {
            if (el.value === el2[3]) {
              checkItem.push(el2)
            }
            // console.log(el2)
          });
        })
        this.items = checkItem
      },
      // 'checkAll'(val) {
      //   console.log(val)
      //   if (val === true) {
      //     this.items = this.itemsAll
      //   } else {
      //     this.items = []
      //   }
      // }
    },
    computed: {
      //相依的資料改變時才做計算方法
      //參考 https://jsfiddle.net/littlebookboy/q5gLww88/
      //全選
      // 綁定在全選的 checkbox 的名稱
      // 1. 頁面讀取時會觸發 get 取回被綁定的物件應屬於什麼值（這邊就是看你要預設全選還是不全選）
      // 2. 當勾選時會變動值，觸發 computed 執行重算
      checkAll: {
        get: function () {
          // 當「已經打勾的選項」跟「可以打勾的選項」一樣多，把全選的值修正成 true or false
          // 下面這個判斷是說，當「可以用來打勾的選項」this.checkData 是存在的話
          // ？ 回傳（「已經打勾的選項」this.checkname.length 跟「可以打勾的選項」this.checkData.length 一樣多嗎）
          // ： 否則回傳 false （不存在可以打勾的選項）
          return this.checkData ? this.checkname.length === this.checkData.length : false
        },
        // 當 v-model 觸發 狀態改變，執行 set 
        set: function (value) {
          console.log(value)
          // value 是 v-model 在全選 checkbox 上的勾選狀態，打勾時為 true
          // 暫存用陣列，用來存放被打勾的 checkbox value
          let selected = []
          // 如果全選打勾
          if (value) {
            // 把可以勾的 checkbox dom 跑一輪
            this.checkData.forEach((action) => {
              // 寫入暫存陣列
              selected.push(action)
            })
          }
          // 將暫存陣列的值寫到已經勾選的容器陣列，表示全都勾（true），false 時會被清空
          this.checkname = selected
          console.log(">>>", checkname)
        }
      }
    },
    methods: {
      // 初始
      //撈整筆資料
      getList: function () {
        axios.get("http://127.0.0.1:880/api/list.php").then(res => {
          this.items = res.data.data;
          this.itemsAll = res.data.data;
        });
      },

      // 新增鈕
      addBtn() {
        axios.get("http://127.0.0.1:880/api/list.php").then(res => {
          console.log(res);
          this.addItems.data.name = "",
            this.addItems.data.tel = ""
          //清空input
        })
      },
      // 新增完成
      submitBtn() {
        let data = qs.stringify({
          name: this.addItems.data.name,
          tel: this.addItems.data.tel,
          email: this.addSelected.value
        });
        axios.post("http://127.0.0.1:880/api/add.php", data).then(res => {
          console.log(res.data);
          this.items.push()
          this.getList()
        });
      },

      // 編輯鈕 編輯頁input顯示新增的資料
      editBtn(id) {
        //取到點擊items.id的值
        this.items.forEach(el => {
          if (id === el[0]) {
            this.editItems.data.id = el[0];
            this.editItems.data.name = el[1];
            this.editItems.data.tel = el[2];
            this.editItems.data.email = el[3];
            //console.log("==>>", this.editItems);
          }
        });
      },
      // 編輯完成
      editSubmit(id) {
        let data = qs.stringify({
          id: id,
          name: this.editItems.data.name,
          tel: this.editItems.data.tel,
          email: this.editSelected.value
        });
        axios.post("http://127.0.0.1:880/api/update.php", data).then(res => {
          console.log(res);
          let obj = _.cloneDeep(this.items); //需拷貝舊的值來用
          obj.forEach((el, i) => {
            //i是位置
            if (id !== el[0]) return;
            //console.log("xxx>>>>", this.items[i]);
            obj[i] = [
              id,
              this.editItems.data.name,
              this.editItems.data.tel,
              this.editSelected.value
            ];
            this.items = obj;
            //console.log(">>>", this.items[i]);
          });
        });
      },
      // 刪除鈕
      deleteBtn: function (i) {
        let data = qs.stringify({
          id: this.items[i][0] //items第幾筆的第1個位置>>id
        });
        //  console.log("id" , this.items[i][0])
        axios.post("http://127.0.0.1:880/api/del.php", data).then(res => {
          console.log(res);
          this.items.splice(i, 1);
        });
      },


    },
    //BEGIN--生命週期
    beforeCreate: function () {
      //實體初始化
    },
    created: function () {
      //實體建立完成。資料 $data 已可取得，但 $el 屬性還未被建立。
    },
    beforeMount: function () {
      //執行元素掛載之前。
    },
    mounted: function () {
      //元素已掛載， $el 被建立。
      this.getList()
    },
    beforeUpdate: function () {
      //當資料變化時被呼叫，還不會描繪 View。
    },
    updated: function () {
      //當資料變化時被呼叫，還不會描繪 View。
    },
    beforeDestroy: function () {
      //實體還可使用。
    },
    destroyed: function () {
      //實體銷毀。
    }
    //END--生命週期
  };
</script>

<style lang="scss" scoped>
</style>