<template>
  <div class="backup-keystore">

    <div class="x-header">
      <x-header :title="$t('backupKeystore')" :left-options="{preventGoBack:true, backText: ''}"
                @on-click-back="back"></x-header>
    </div>

    <div class="backup-content">
      <div class="backup-info warning-bg tl pd-40">
        <ul>
          <li class="danger-text">
            {{ $t('savedOffline') }}
            <p class="warning-text mt-20">{{ $t('backupWalletWarning1') }}</p>
          </li>
          <li class="danger-text mt-20">
            {{ $t('donNotUseNetworkTransmission') }}
            <p class="warning-text mt-20">{{ $t('backupWalletWarning2') }}</p>
          </li>
          <li class="danger-text mt-20">
            {{ $t('donNotCapture') }}
            <p class="warning-text mt-20">{{ $t('backupWalletWarning3') }}</p>
          </li>
        </ul>
      </div>

      <div class="copy-content">{{ copyContent }}</div>

      <div class="mt-20 pd-40">
        <x-button type="primary" v-clipboard:copy="copyContent" v-clipboard:success="onCopy">{{ $t('copy') }}</x-button>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "BackupKeystore",
    props: ['address'],
    data() {
      return {
        copyContent: ``,
      }
    },
    methods: {
      back() {
        this.$emit('onBack')
      },
      onCopy() {
        this.$vux.toast.text(this.$t('copySucc'))
        this.isCopy = true
        this.loggedOutText = this.$t('submitLoggedOut')
      }
    },
    created() {
      let walletJson = localStorage.getItem('web3js_wallet')
      walletJson = JSON.parse(walletJson)
      let myAddr = this.address.toLowerCase()

      for (let i = 0; i < walletJson.length; i++) {
        let address = '0x' + walletJson[i].address
        if (myAddr == address) {
          this.copyContent = JSON.stringify(walletJson[i])
        }
      }
    }
  }
</script>

<style lang="less" scoped>
  .backup-keystore {

    .backup-content {
      margin-top: 120px;

      .backup-info {
        padding-left: 80px;
        ul li {
          list-style: disc outside none;
          line-height: 50px;
        }
      }
      .copy-content {
        margin: 0 40px;
        margin-top: 60px;
        border: 1px solid #4389F5;
        max-height: 430px;
        overflow-y: auto;
        padding: 40px;
        word-break: break-word;
        border-radius: 20px;
        text-align: left;
      }
    }
  }
</style>
