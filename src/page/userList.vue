<template>
    <div class="fillcontain">
        <head-top></head-top>
        <el-form label-width="110px" class="demo-formData" :inline="true">
            <el-form-item label="旷工id">
                <el-input v-model="minerId"></el-input>
            </el-form-item>
            <el-form-item label="上链时间">
                <el-input v-model="cochainTime"></el-input>
            </el-form-item>
            <el-form-item>
                <el-button type="primary" @click="getData">查询</el-button>
            </el-form-item>
        </el-form>
        <div class="table_container">
            <el-table
                :data="winingList"
                highlight-current-row
                style="width: 100%">
                <el-table-column
                    label="序号"
                  type="index"
                  width="100">
                </el-table-column>
                <el-table-column
                  property="minerId"
                  label="旷工id"
                  width="220">
                </el-table-column>
                <el-table-column
                  property="blockId"
                  label="区块id">
                </el-table-column>
                <el-table-column
                    property="cochain"
                    label="是否上链"
                    width="120">
                </el-table-column>
                <el-table-column
                  property="cochainTime"
                  label="上链时间"
                  width="220">
                </el-table-column>
            </el-table>
            <div class="Pagination" style="text-align: left;margin-top: 10px;">
                <el-pagination
                  @size-change="handleSizeChange"
                  @current-change="handleCurrentChange"
                  :current-page="currentPage"
                  :page-size="20"
                  layout="total, prev, pager, next"
                  :total="count">
                </el-pagination>
            </div>
        </div>
    </div>
</template>

<script>
    import headTop from '../components/headTop'
    import {getUserList, getUserCount} from '@/api/getData'
    import axios from 'axios'
    export default {
        data(){
            return {
                currentRow: null,
                offset: 0,
                limit: 20,
                count: 0,
                currentPage: 1,
                winingList:[],
                websock:{},
                minerId:'',
                cochainTime:''
            }
        },
    	components: {
    		headTop,
    	},
        created(){
            this.initData();
        },
        init(){
            let url = 'wss://localhost:8090/websocket/123'
            // 创建websocket连接
            this.websock = new WebSocket(url);
            // 监听打开
            this.websock.onopen= this.websockOpen;
            // 监听关闭
            this.websock.onclose = this.websockClose;
            //监听异常
            this.websock.onerror = this.websockError;
            //监听服务器发送的消息
            this.websock.onmessage = this.websockMessage;
        },
        methods: {
            async initData(){
                let pageInfo = JSON.stringify(
                    {
                        currentPage:this.currentPage,
                        limit:this.limit,
                        wini:{}
                    }
                );
                this.$http.get("/api/api/wining/all", {params:{
                        pageInfo:pageInfo
                    }}).then(response => {
                    this.winingList = response.data.list;
                    this.count = response.data.total;
                }, response => {
                    console.log("error");
                });
            },
            handleSizeChange(val) {
                console.log(`每页 ${val} 条`);
            },
            handleCurrentChange(val) {
                this.currentPage = val;
                this.offset = (val - 1)*this.limit;
                this.getData()
            },

            getData(){
                console.log(this.formData)
                let pageInfo = JSON.stringify(
                    {
                        currentPage:this.currentPage,
                        limit:this.limit,
                        wini:{
                            minerId: this.minerId,
                            cochainTime: this.cochainTime
                        }
                    }
                );
                this.$http.get("/api/api/wining/all", {params:{
                        pageInfo:pageInfo
                    }}).then(response => {
                    this.winingList = response.data.list;
                    this.count = response.data.total;
                }, response => {
                    console.log("error");
                });
            },
            websockOpen() {
                console.log('监听连接建立')
                // 渲染操作
            },
            websockClose() {
                console.log('监听连接关闭')
                // 渲染操作
            },
            websockError() {
                console.log('监听异常')
                // 渲染操作
            },
            websockMessage(e) {
                console.log('监听服务器发送的消息',e.data)
                // 渲染操作
            }
        },
    }
</script>

<style lang="less">
	@import '../style/mixin';
    .table_container{
        padding: 20px;
    }
</style>
