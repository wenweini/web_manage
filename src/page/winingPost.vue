<template>
    <div class="fillcontain">
        <head-top></head-top>
        <el-row>
            <el-col :span="24">
                <div class="grid-content bg-purple-dark">
                    <el-table
                        ref="filterTable"
                        :data="tableData"
                        style="width: 100%">
                        <el-table-column
                        prop="minerNo"
                        label="节点号"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="yxslP"
                        label="有效算力(P)"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="yxslT"
                        label="有效算力(T)"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="kyye"
                        label="可用余额"
                        width="180">
                        </el-table-column>
                        <el-table-column
                        prop="sqdy"
                        label="抵押"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="preCommitDeposits"
                        label="预存款"
                        width="80">
                        </el-table-column>
                        <el-table-column
                        prop="workerBalance"
                        label="worker余额"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="controlBalance"
                        label="post余额"
                        width="120">
                        </el-table-column>
                        <el-table-column
                        prop="allSectors"
                        label="全部扇区"
                        width="100">
                        </el-table-column>
                        <el-table-column
                        prop="availableSectors"
                        label="有效扇区"
                        width="100">
                        </el-table-column>
                        <el-table-column
                        prop="errorSectors"
                        label="错误扇区"
                        width="100">
                        </el-table-column>
                        <el-table-column
                        prop="inserttime"
                        label="更新日期"
                        :formatter="dateFormat"
                        sortable
                        column-key="inserttime"
                        >
                        </el-table-column>
                    </el-table>
                </div>
            </el-col>
        </el-row>
    </div>
</template>

<script>
import headTop from '../components/headTop'
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
            cochainTime:'',
            tableData:[]
        }
    },
    components: {
        headTop,
    },
    created(){

    },
    mounted(){
        // WebSocket
        if ('WebSocket' in window) {
            this.websocket = new WebSocket('ws://192.168.1.47:8090/push/websocket')
            // alert('连接浏览器')
            this.initWebSocket()
        } else {
            alert('当前浏览器 不支持')
        }
        this.getData();
    },
    beforeDestroy () {
        this.onbeforeunload()
    },
    methods: {
        initWebSocket () {
            // 连接错误
            this.websocket.onerror = this.setErrorMessage

            // 连接成功
            this.websocket.onopen = this.setOnopenMessage

            // 收到消息的回调
            this.websocket.onmessage = this.setOnmessageMessage

            // 连接关闭的回调
            this.websocket.onclose = this.setOncloseMessage

            // 监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
            window.onbeforeunload = this.onbeforeunload
        },
        setErrorMessage () {
            console.log('WebSocket连接发生错误 状态码：' + this.websocket.readyState)
        },
        setOnopenMessage () {
            console.log('WebSocket连接成功 状态码：' + this.websocket.readyState)
        },
        setOnmessageMessage (event) {
            // 根据服务器推送的消息做自己的业务处理
            console.log('服务端返回：' + event.data)
            this.tableData = JSON.parse(event.data)
        },
        setOncloseMessage () {
            console.log('WebSocket连接关闭 状态码：' + this.websocket.readyState)
        },
        onbeforeunload () {
            this.closeWebSocket()
        },
        closeWebSocket () {
            this.websocket.close()
        },
        getData () {
            this.$http.get("/api/api/miner/info", {params:{}}).then(response => {
                    this.tableData = JSON.parse(response.data);
                }, response => {
                    console.log("error");
                });
            
        },
        dateFormat(row, column){
            let date = new Date(row.inserttime);
            let y = date.getFullYear();
            let MM = date.getMonth() + 1;
            MM = MM < 10 ? ('0' + MM) : MM;
            let d = date.getDate();
            d = d < 10 ? ('0' + d) : d;
            let h = date.getHours();
            h = h < 10 ? ('0' + h) : h;
            let m = date.getMinutes();
            m = m < 10 ? ('0' + m) : m;
            let s = date.getSeconds();
            s = s < 10 ? ('0' + s) : s;
            return y + '-' + MM + '-' + d + ' ' + h + ':' + m + ':' + s;
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
