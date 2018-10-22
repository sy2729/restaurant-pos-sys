<template>
 <div class="pos">
    <div>
        <el-row >
            <el-col :span='7' class="pos-order">
                <el-tabs>
                    <el-tab-pane label='Check Out'>
                        <el-table style="width: 100%;" :data='tableData'>
                            <el-table-column prop='name' label='Good Name'></el-table-column>
                            <el-table-column prop='count' label='Count' width='100'></el-table-column>
                            <el-table-column prop='price' label='price' width='100'></el-table-column>
                            <el-table-column label='Action' width='100' fixed='right'>
                                <template scope="scope">
                                    <el-button type='text' size='small' @click='deleteItem(scope.row)'>Delete</el-button>
                                    <el-button type='text' size='small' @click='addOrder(scope.row)'>Add</el-button>
                                </template>
                            </el-table-column>
                        </el-table>
                        <div class="total-info">
                            <p class="amount">
                                <span>amount:</span>
                                <span class="total-number">{{amount}}</span>
                            </p>
                            <p class="price">
                                <span>price:</span>
                                <span class="total-number">{{price}}</span>
                            </p>
                        </div>
                        <div class="button-group">
                            <el-button type="success" @click='checkOut'>Check Out</el-button>
                            <el-button type="warning">Resting Order</el-button>
                            <el-button type="danger" @click="deleteAll">Delete</el-button>
                        </div>
                    </el-tab-pane>
                    <el-tab-pane label='Resting order'>
                        Resting Order
                    </el-tab-pane>
                    <el-tab-pane label='Take out'>
                        Take Out
                    </el-tab-pane>
                </el-tabs>
            </el-col>

            <!--商品展示-->
            <el-col :span="17">
             <div class="regular-items">
                <div class="regular-items-title section-title">Regular Item</div>
                <div class="regular-item-list">
                    <ul class="clearfix">
                        <li v-for="i in regularItem" :key='i.id' @click="addOrder(i)">
                            <span class="regular-item-name">{{i.name}}</span>
                            <span class="regular-item-price">${{i.price}}</span>
                        </li>
                    </ul>
                </div>
             </div>

             <div class="item-type">
                 <!-- <div class="item-type-title section-title">Food Type</div> -->
                 <el-tabs>
                     <el-tab-pane :label='i.name' v-for="i in typeFoodData" :key='i.name'>
                         <ul class="food-list clearfix">
                             <li class="food-item" v-for="j in i.details" :key='j.id' @click="addOrder(j)">
                                 <span class="food-image" :style="`background-image: url(${j.img})`"></span>
                                 <div class="food-info">
                                    <p class="food-name">{{j.name}}</p>
                                    <p class="food-price">${{j.price}}</p>
                                 </div>
                             </li>
                         </ul>
                     </el-tab-pane>
                 </el-tabs>
             </div>
            </el-col>
        </el-row>
    </div>
  </div>
</template>


<script>
import axios from 'axios';
export default {
    name: 'Pos',
    data(){
        return {
            tableData: [],
            regularItem: [],
            typeFoodData: [],
        }
    },
    computed: {
        amount(){
            var result = 0;
            this.tableData.forEach((i)=>{
                result += i.count;
            })
            return result;
        },
        price(){
            var result = 0;
            this.tableData.forEach((i)=>{
                result += i.count * i.price;
            })
            return result;
        },
    },
    methods: {
        addOrder(data) {
            let alreadyExist = false;
            this.tableData.forEach((i)=>{
                if(i.id === data.id) {
                    alreadyExist = true;
                }
            });

            if(alreadyExist) {
                let arr = this.tableData.filter((i)=>{
                    return i.id === data.id
                });
                arr[0].count++;
            }else {
                let newData = {count: 1};
                Object.assign(newData, data)
                this.tableData.push(newData)
            }
        },

        deleteItem(data) {
            let id = data.id;
            this.tableData.filter((i, index)=>{
                if(i.id === id && i.count > 1) {
                    i.count--
                }else if (i.id === id && i.count === 1) {
                    this.tableData.splice(index, 1)
                }
            })
        },
        deleteAll(){
            this.tableData = [];
        },
        checkOut(){
            if(this.price === 0) {
                this.$message.error('Nothing Purchased') 
                return
            }
            this.deleteAll();
            this.$message({
                message: 'Checkout Successfully',
                type: 'success',
            })
        }
    },
    created() {
        axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/oftenGoods')
            .then((i)=>{
                var newData = i.data.map((i)=>{
                    return {
                        "id": i.goodsId,
                        "name": i.goodsName,
                        "price": i.price
                    }
                })
                this.regularItem = newData;
            })

        axios.get('https://www.easy-mock.com/mock/5b8b30dbf032f03c5e71de7f/kuaican/typeGoods')
            .then((i)=>{
                // console.log(i.data)
                var categories = ['Burger', 'Snack', 'Drink', 'Combo'];
                var newData = i.data.map((i, index)=>{
                    var details = i.map((i)=>{
                        return {
                                id: i.goodsId,
                                img: i.goodsImg,
                                name: i.goodsName,
                                price: i.price
                            }
                    })
                    return {
                        name: categories[index],
                        details: details
                    }
                })
                this.typeFoodData = newData;
            })
        
    }
    
}
</script>

<style lang="scss" scoped>
.section-title {
    padding: 15px 14px;
    border-bottom: 1px solid #ddd;
    background: #fff;
    text-align: left; 
}

.pos-order {
    background: #fff;
    height: 100vh;

    .total-info {
        margin: 30px 0;
        display: flex;
        justify-content: space-around;    

        .total-number {
            margin-left: 15px;
            font-weight: bold;
        }    
    }

    .button-group {
        margin-top: 10px;
    }
}

.regular-items {
    // .regular-items-title {

    // }

    .regular-item-list > ul{
        li {
            float: left;
            border: 1px solid #ddd;
            padding: 5px 10px;
            margin: 10px;
            background: #fff;
            border-radius: 4px;
            cursor: pointer;

            .regular-item-price {
                margin-left: 10px;
                color: rgb(47, 91, 148);
            }

            &:hover {
                color: #60A3F8;
                border-color: #60A3F8;
            }
        }
    }
}

.item-type {
    margin-top: 30px;

    .item-type-title {
        background: #fff;
    }
    .food-list {
        .food-item {
            float: left;
            width: 300px;
            height: 100px;
            background: #fff;
            display: flex;
            // align-items: center;
            margin: 10px;
            box-shadow: 2px 2px 10px 0 rgba(100, 100, 100, 0.2);
            cursor: pointer;

            .food-image {
                width: 100px;
                height: 100%;
                background: transparent center no-repeat;
                background-size: cover;
            }

            .food-info {
                display: flex;
                flex-direction: column;
                align-items: flex-start;
                padding: 10px;

                .food-name {
                    color: rgb(47, 91, 148);
                    font-weight: bold;
                }
            }

        }
    }
}


</style>
