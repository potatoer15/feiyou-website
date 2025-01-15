<template>
    <div class="recharge-detail">
        <!--海报-->
        <section class="banner container-fluid">
            <img src="../../../static/img/game-bg.jpg" alt="">
            <div class="title">
                <p class="p1">Games</p>
                <p class="p2">做最出色的遊戲</p>
            </div>
        </section>
        <div class="recharge-container">
            <div class="recharge-content">
                <div class="thank-you-message">
                    选择商品
                </div>

                <div class="recharge-options">
                    <div class="recharge-tier" v-for="tier in rechargeTiers" :key="tier.id">
                        <div class="product-info">
                            <img :src="tier.image" alt="" class="product-image">
                            <div class="product-name">{{ tier.name }}</div>
                            <div class="price">¥ {{ tier.price }}</div>
                            <button class="recharge-button" @click="handleRecharge(tier)">
                                购买
                            </button>
                        </div>
                    </div>
                </div>
            </div>

            <!-- 支付弹窗 -->
            <a-modal v-model:visible="isModalVisible" title="支付订单" @cancel="handleCancel" width="700px">
                <div class="order-info">
                    <div class="order-header">
                        <div class="order-no">订单编号 #{{ currentOrder.orderId }}</div>
                        <div class="countdown">订单支付倒计时：{{ countdown }}</div>
                    </div>
                    <div class="order-details">
                        <div class="detail-item">
                            <span>订单金额</span>
                            <span>¥ {{ currentOrder.amount }} 元</span>
                        </div>
                        <div class="detail-item">
                            <span>购买点数</span>
                            <span class="points-value">
                                {{ currentOrder.points }}
                            </span>
                        </div>
                    </div>
                    <div class="payment-frame">
                        <iframe :src="paymentUrl" frameborder="0" width="100%" height="500px"></iframe>
                    </div>
                </div>
            </a-modal>
        </div>
    </div>
</template>

<script>
export default {
    name: 'RechargeDetail',
    data() {
        return {
            gameId: '',
            rechargeTiers: [
                {
                    id: 1,
                    name: '无名客的荣勋',
                    price: 68,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 68
                },
                {
                    id: 2,
                    name: '无名客的奖章',
                    price: 128,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 128
                },
                {
                    id: 3,
                    name: '列车补给凭证',
                    price: 30,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 30
                },
                {
                    id: 4,
                    name: '60古老梦华',
                    price: 6,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 60
                },
                {
                    id: 5,
                    name: '300古老梦华',
                    price: 30,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',

                    points: 300
                },
                {
                    id: 6,
                    name: '980古老梦华',
                    price: 98,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 980
                },
                {
                    id: 7,
                    name: '1980古老梦华',
                    price: 198,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 1980
                },
                {
                    id: 8,
                    name: '3280古老梦华',
                    price: 328,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 3280
                },
                {
                    id: 9,
                    name: '6480古老梦华',
                    price: 648,
                    image: 'https://sdk-webstatic.mihoyo.com/sdk-payment-upload/2025/01/14/2bb54956e1672f78ee3ff07bfef29bc8_434004644719114646.png',
                    points: 6480
                }
            ],
            isModalVisible: false,
            currentOrder: {
                orderId: '',
                amount: 0,
                points: 0
            },
            countdown: '',
            paymentUrl: '',
            timer: null
        }
    },
    created() {
        this.gameId = this.$route.params.gameId
    },
    methods: {
        handleRecharge(tier) {
            this.currentOrder = {
                orderId: 'ORDER' + Date.now(),
                amount: tier.price,
                points: tier.points
            }
            this.paymentUrl = '支付页面URL' // 实际项目中替换为真实的支付URL
            this.isModalVisible = true
            this.startCountdown(15 * 60) // 15分钟倒计时
        },
        startCountdown(seconds) {
            if (this.timer) clearInterval(this.timer)

            const updateCountdown = () => {
                const minutes = Math.floor(seconds / 60)
                const remainingSeconds = seconds % 60
                this.countdown = `${minutes}:${remainingSeconds.toString().padStart(2, '0')}`

                if (seconds <= 0) {
                    clearInterval(this.timer)
                    this.handleCancel()
                }
                seconds--
            }

            updateCountdown()
            this.timer = setInterval(updateCountdown, 1000)
        },
        handleCancel() {
            this.isModalVisible = false
            if (this.timer) {
                clearInterval(this.timer)
                this.timer = null
            }
        }
    },
    beforeDestroy() {
        if (this.timer) {
            clearInterval(this.timer)
        }
    }
}
</script>

<style scoped lang="less">
.recharge-detail {
    min-height: 100vh;

    // 海报
    .banner {
        margin: 0 auto;
        max-width: 1920px;
        padding: 0;
        position: relative;

        .title {
            position: absolute;
            text-align: center;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: #fff;
            font-size: 38px;

            .p2 {
                font-size: 24px;
            }

            @media screen and (max-width:767px) {
                font-size: 4vw;

                .p2 {
                    font-size: 3vw;
                }
            }
        }
    }

    .recharge-container {
        padding: 20px;
        margin: 0 auto;

        .recharge-content {
            background: #fff;
            border-radius: 12px;
            padding: 30px;

            .thank-you-message {
                font-size: 24px;
                font-weight: bold;
                margin-bottom: 30px;
            }

            .recharge-options {
                display: grid;
                grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
                gap: 20px;

                .recharge-tier {
                    position: relative;
                    border: 1px solid #e8e8e8;
                    border-radius: 8px;
                    padding: 15px;
                    text-align: center;
                    transition: all 0.3s;

                    &:hover {
                        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
                    }

                    .tag {
                        position: absolute;
                        top: 10px;
                        right: 10px;
                        background: #ff9800;
                        color: white;
                        padding: 2px 8px;
                        border-radius: 4px;
                        font-size: 12px;
                    }

                    .product-image {
                        width: 80px;
                        height: 80px;
                        margin-bottom: 10px;
                    }

                    .product-name {
                        font-size: 16px;
                        margin-bottom: 5px;
                    }

                    .product-extra {
                        color: #666;
                        font-size: 14px;
                        margin-bottom: 10px;
                    }

                    .price {
                        font-size: 20px;
                        font-weight: bold;
                        color: #f85415;
                        margin-bottom: 15px;
                    }

                    .recharge-button {
                        width: 100%;
                        padding: 8px;
                        background: #00b4ce;
                        color: white;
                        border: none;
                        border-radius: 4px;
                        cursor: pointer;

                        &:hover {
                            background: #02a6c0;
                        }
                    }
                }
            }
        }
    }
}

.order-info {
    .order-header {
        display: flex;
        justify-content: space-between;
        margin-bottom: 20px;
        padding-bottom: 15px;
        border-bottom: 1px solid #e8e8e8;
    }

    .detail-item {
        display: flex;
        justify-content: space-between;
        margin-bottom: 15px;

        .points-value {
            color: #f85415;
            font-weight: bold;
        }
    }
}

/* Modal 样式 */
/deep/ .ant-modal-content {
    border-radius: 8px;
}

/deep/ .ant-modal-header {
    border-radius: 8px 8px 0 0;
}

/deep/ .ant-modal-body {
    padding: 24px;
}
</style>