<template>
    <div id="app">
        <canvas id="khqr" ref="khqr"></canvas>
        <button @click="generateQR">Generate</button>
        <button @click="checkTransaction">CheckTransction</button>
    </div>
</template>

<script>
import QRCode from "qrcode";
import { BakongKHQR, khqrData, IndividualInfo } from "bakong-khqr";
import axios from "axios";

export default {
    name: "App",
    data() {
        return {
            md5: ""
        }
    },
    methods: {
        generateQR() {
            const QRCodeCanvas = this.$refs.khqr;
            const optionalData = {
                currency: khqrData.currency.khr,
                amount: 1000,
            };

            const merchantInfo = new IndividualInfo(
                "test_user_acleda@aclb",
                "Test User",
                "Battambang",
                optionalData
            );

            const khqr = new BakongKHQR();
            const response = khqr.generateIndividual(merchantInfo);

            this.md5 = response.data.md5
            QRCode.toCanvas(QRCodeCanvas, response.data.qr);
        },
        async checkTransaction() {
            const token = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MzQ3ODg1NjYsImlhdCI6MTYyNjc1MzM2NiwiZGF0YSI6eyJpZCI6IjM0YzM5ZGUwZThhNTQ5NyJ9fQ.I2uQigYPjNAb9i3DX4xzCy0Z9R17MUz10uSXavdLTOU"
            const config = {
                headers: { Authorization: `Bearer ${token}` },
            };
            const response = await axios.post(
                "https://sit-api-bakong.nbc.org.kh/v1/check_transaction_by_md5",
                { md5: this.md5 },
                config
            );
            console.log(response.data);
        },
    },
    mounted() {
        this.generateQR();
    },
};
</script>

<style>
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
