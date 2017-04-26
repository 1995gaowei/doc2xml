<template>
    <div style="text-align: center;">
    <h1>doc2xml</h1>
    <el-form ref="transferForm" :model="transferForm" :rules="transferRules" label-width="120px" style="width: 600px; display: inline-block; margin-top:30px;">
        <el-form-item label="源路径" prop="inputPath">
            <el-input v-model="transferForm.inputPath"></el-input>
        </el-form-item>
        <el-form-item label="目标路径" prop="outputPath">
            <el-input v-model="transferForm.outputPath"></el-input>
        </el-form-item>
        <el-button type="primary" @click="onTransfer">开始转换</el-button>
    </el-form>
    <br>
    <div style="display: inline-block;margin-top: 30px;">
        <el-tag type="gray">总共：{{total}}</el-tag>
        <el-tag type="success">成功：{{transferred}}</el-tag>
        <el-tag type="danger">失败：{{error}}</el-tag>
    </div>
    </div>
</template>

<script>
import Vue from 'vue'

export default {
    data () {
        return {
            transferred: 0,
            total: 0,
            error: 0,
            transferForm: {
                inputPath: '',
                outputPath: ''
            },
            transferRules: {
                inputPath: [
                    { required: true, message: '请输入源路径' }
                ],
                outputPath: [
                    { required: true, message: '请输入目标路径' }
                ]
            }
        }
    },
    beforeDestroy: function() {
        clearInterval(this.getProcessInterval);
    },
    methods: {
        onTransfer () {
            this.$refs['transferForm'].validate((valid) => {
                if (valid) {
                    Vue.http.get('http://127.0.0.1:8080/transfer?inputPath='+this.transferForm.inputPath+'&outputPath='+this.transferForm.outputPath).then(res => {
                        console.log(res);
                    }, res => {
                        console.log(res);
                    });

                    this.getProcess();
                    console.log('transfer');
                } else {
                    console.log('error');
                }
            });
        },
        getProcess () {
            this.getProcessInterval = setInterval(() => {
                console.log('hhhhh')
                Vue.http.get('http://127.0.0.1:8080/getProcess?inputPath='+this.transferForm.inputPath+'&outputPath='+this.transferForm.outputPath).then(res => {
                    this.transferred = res.body.data.transferred;
                    this.total = res.body.data.total;
                    this.error = res.body.data.error;
                    if (this.transferred + this.error == this.total) {
                        clearInterval(this.getProcessInterval);
                    }
                }, res => {
                    console.log(res);
                });
            }, 50);
        }
    }
}
</script>