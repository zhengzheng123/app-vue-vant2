<template>
  <div>
    <van-nav-bar
      title="设置金额"
      left-text="返回"
      right-text=""
      left-arrow
      @click-left="onClickLeft"
    />
    <van-cell-group>
      <van-field
        ref="moneyInput"
        v-model="money"
        clearable
        label="金额"
        type="Number"
        @focus="moneyInputFocus"
        @input="handleAmountInput"
      />
      <van-field
        ref="reasonInput"
        v-model="reason"
        clearable
        label="理由"
        v-if="reasonShow"
      />
    </van-cell-group>
    <div class="rsn" v-if="!reasonShow">
      <span @click="handleReasonShow">添加收款理由</span>
    </div>
    <div class="btn">
      <van-button type="primary" :disabled="btnDisabled" size="large" @click="handleOk">确定</van-button>
    </div>
  </div>
</template>

<script>
  import {
    NavBar,
    Field,
    CellGroup,
    Button,
    NumberKeyboard
  } from 'vant'

  export default {
    name: "SetMoney",
    components: {
      [NavBar.name]: NavBar,
      [Field.name]: Field,
      [CellGroup.name]: CellGroup,
      [Button.name]: Button,
      [NumberKeyboard.name]: NumberKeyboard
    },
    data() {
      return {
        money: '',
        reason: '',
        reasonShow: false,
        reasonFocus:false,
        show: false, // 控制数字键盘显示
        btnDisabled:true, // 控制按钮禁用状态
        numConfig: {
          num: '', // 小数点前的数字
          dec: '', // 小数点后的数字
          step: 0, // 小数点后第几位
          point: false, // 是否点击小数点
        }
      }
    },
    mounted(){
      this.$refs.moneyInput.focus();
    },
    methods: {
      onClickLeft() {
        this.$router.push('/collect');
      },
      moneyInputFocus(){
        this.reasonFocus = false;

      },
      handleAmountInput(e){
        // 判断位数是否超过8位
        if (this.money.length > 8)
          return;
        if(! /^\d*(?:.\d{0,2})?$/.test(e)){
          this.money = this.money.substr(0,this.money.length-1);
        }else{
          this.money = this.money;
        }
        // 判断temp是否大于0，控制按钮禁用状态
        if(Number(this.money) <= 0){
          this.btnDisabled = true;
        }else{
          this.btnDisabled = false;
        }
      },
      formatMoney() {
        let mid = '';
        if(this.money.indexOf('.') !== -1){
          // 有点
          const arr = this.money.split('.')
          let [num,dec] = arr;
          while (dec.length < 2) {
            dec += '0'
          }
          mid += `${num}.${dec}`
        }else{
          // 无点
          mid = `${parseInt(this.money).toString()}.00`;
        }
        return mid;
      },
      handleReasonShow(){
        this.reasonShow=true;
        this.reasonFocus=true;
      },
      handleOk() {
        this.$router.push({
          path: '/collect',
          query: {
            money: this.formatMoney(),
            reason: this.reason
          }
        })
      }
    },
    updated(){
      this.reasonFocus && this.$refs.reasonInput.focus();
    }
    // directives:{
    //   focus:{
    //     inserted:function (el) {
    //       el.focus();
    //     }
    //   }
    // },
  }
</script>

<style scoped lang="less">
  .van-cell-group {
    margin-top: 10px;
  }

  .btn {
    padding: 16px 10px;
  }

  .rsn {
    text-align: right;
    padding: 10px;
    > span {
      color: #2d8cf0;
      font-size: 14px;
    }
  }
</style>
